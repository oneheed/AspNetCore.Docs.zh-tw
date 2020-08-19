---
title: 使用 cookie 驗證但不使用 ASP.NET Core Identity
author: rick-anderson
description: 瞭解如何在 cookie 不使用驗證的情況下使用 ASP.NET Core Identity 。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
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
uid: security/authentication/cookie
ms.openlocfilehash: 2e9eb58837d74343d8de6903372146570b43f330
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627139"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="9fb54-103">使用 cookie 驗證但不使用 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="9fb54-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="9fb54-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9fb54-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9fb54-105">ASP.NET Core Identity 是完整的完整功能驗證提供者，可用於建立和維護登入。</span><span class="sxs-lookup"><span data-stu-id="9fb54-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="9fb54-106">但是， cookie 不能使用不使用的驗證提供者 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="9fb54-107">如需詳細資訊，請參閱<xref:security/authentication/identity>。</span><span class="sxs-lookup"><span data-stu-id="9fb54-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="9fb54-108">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="9fb54-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9fb54-109">為了示範範例應用程式中的目的，假設使用者的使用者帳戶（Maria Rodriguez）會硬式編碼到應用程式中。</span><span class="sxs-lookup"><span data-stu-id="9fb54-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="9fb54-110">使用 **電子郵件** 位址 `maria.rodriguez@contoso.com` 和任何密碼來登入使用者。</span><span class="sxs-lookup"><span data-stu-id="9fb54-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="9fb54-111">使用者會在 `AuthenticateUser` *頁面/帳戶/登入 .cs* 檔案的方法中進行驗證。</span><span class="sxs-lookup"><span data-stu-id="9fb54-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="9fb54-112">在真實世界的範例中，使用者會針對資料庫進行驗證。</span><span class="sxs-lookup"><span data-stu-id="9fb54-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="9fb54-113">組態</span><span class="sxs-lookup"><span data-stu-id="9fb54-113">Configuration</span></span>

<span data-ttu-id="9fb54-114">如果應用程式不使用 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app)，請在專案檔中建立 AspNetCore 的封裝參考 [。 Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) 套件。</span><span class="sxs-lookup"><span data-stu-id="9fb54-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="9fb54-115">在 `Startup.ConfigureServices` 方法中，使用和方法建立驗證中介軟體 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 服務 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ：</span><span class="sxs-lookup"><span data-stu-id="9fb54-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="9fb54-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> 傳遞以 `AddAuthentication` 設定應用程式的預設驗證配置。</span><span class="sxs-lookup"><span data-stu-id="9fb54-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="9fb54-117">`AuthenticationScheme` 當有多個 cookie 驗證實例，而且您想要 [使用特定配置來授權](xref:security/authorization/limitingidentitybyscheme)時，這會很有用。</span><span class="sxs-lookup"><span data-stu-id="9fb54-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="9fb54-118">將設定 `AuthenticationScheme` 為[ Cookie AuthenticationDefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)時，AuthenticationScheme 會為配置提供值 " Cookie s"。</span><span class="sxs-lookup"><span data-stu-id="9fb54-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="9fb54-119">您可以提供任何區分配置的字串值。</span><span class="sxs-lookup"><span data-stu-id="9fb54-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="9fb54-120">應用程式的驗證配置不同于應用程式的 cookie 驗證配置。</span><span class="sxs-lookup"><span data-stu-id="9fb54-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="9fb54-121">當 cookie 未提供驗證配置時 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ，它會使用 `CookieAuthenticationDefaults.AuthenticationScheme` ( " Cookie s" ) 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="9fb54-122">驗證 cookie 的 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 屬性預設會設定為 `true` 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="9fb54-123">cookie當網站訪客未同意資料收集時，允許驗證。</span><span class="sxs-lookup"><span data-stu-id="9fb54-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="9fb54-124">如需詳細資訊，請參閱<xref:security/gdpr#essential-cookies>。</span><span class="sxs-lookup"><span data-stu-id="9fb54-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="9fb54-125">在中 `Startup.Configure` ，呼叫 `UseAuthentication` 和 `UseAuthorization` 來設定 `HttpContext.User` 屬性，並針對要求執行授權中介軟體。</span><span class="sxs-lookup"><span data-stu-id="9fb54-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="9fb54-126">呼叫 `UseAuthentication` 和方法之前，請先呼叫和 `UseAuthorization` 方法 `UseEndpoints` ：</span><span class="sxs-lookup"><span data-stu-id="9fb54-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="9fb54-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>類別是用來設定驗證提供者選項。</span><span class="sxs-lookup"><span data-stu-id="9fb54-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="9fb54-128">在 `CookieAuthenticationOptions` 服務設定中，于方法中設定驗證 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="9fb54-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="9fb54-129">Cookie 原則中介軟體</span><span class="sxs-lookup"><span data-stu-id="9fb54-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="9fb54-130">[ Cookie 原則中介軟體](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)可啟用 cookie 原則功能。</span><span class="sxs-lookup"><span data-stu-id="9fb54-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="9fb54-131">將中介軟體新增至應用程式處理管線的順序是機密的， &mdash; 它只會影響在管線中註冊的下游元件。</span><span class="sxs-lookup"><span data-stu-id="9fb54-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="9fb54-132"><xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> Cookie cookie cookie 當附加或刪除時，請使用提供給原則中介軟體來控制處理與處理處理常式的全域特性 cookie 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="9fb54-133">預設 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 值是 `SameSiteMode.Lax` 允許 OAuth2 authentication。</span><span class="sxs-lookup"><span data-stu-id="9fb54-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="9fb54-134">若要嚴格地強制執行相同的網站原則 `SameSiteMode.Strict` ，請設定 `MinimumSameSitePolicy` 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="9fb54-135">雖然這項設定會中斷 OAuth2 和其他的跨原始來源驗證配置，但它會提高 cookie 不依賴跨原始來源要求處理的其他類型應用程式的安全性層級。</span><span class="sxs-lookup"><span data-stu-id="9fb54-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="9fb54-136">的 Cookie 原則中介軟體設定， `MinimumSameSitePolicy` 可能會影響 `Cookie.SameSite` 下列矩陣中的設定 `CookieAuthenticationOptions` 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="9fb54-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="9fb54-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="9fb54-138">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="9fb54-138">Cookie.SameSite</span></span> | <span data-ttu-id="9fb54-139">結果 Cookie 。SameSite 設定</span><span class="sxs-lookup"><span data-stu-id="9fb54-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="9fb54-140">SameSiteMode。無</span><span class="sxs-lookup"><span data-stu-id="9fb54-140">SameSiteMode.None</span></span>     | <span data-ttu-id="9fb54-141">SameSiteMode。無</span><span class="sxs-lookup"><span data-stu-id="9fb54-141">SameSiteMode.None</span></span><br><span data-ttu-id="9fb54-142">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="9fb54-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="9fb54-143">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="9fb54-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="9fb54-144">SameSiteMode。無</span><span class="sxs-lookup"><span data-stu-id="9fb54-144">SameSiteMode.None</span></span><br><span data-ttu-id="9fb54-145">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="9fb54-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="9fb54-146">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="9fb54-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="9fb54-147">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="9fb54-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="9fb54-148">SameSiteMode。無</span><span class="sxs-lookup"><span data-stu-id="9fb54-148">SameSiteMode.None</span></span><br><span data-ttu-id="9fb54-149">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="9fb54-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="9fb54-150">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="9fb54-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="9fb54-151">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="9fb54-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="9fb54-152">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="9fb54-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="9fb54-153">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="9fb54-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="9fb54-154">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="9fb54-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="9fb54-155">SameSiteMode。無</span><span class="sxs-lookup"><span data-stu-id="9fb54-155">SameSiteMode.None</span></span><br><span data-ttu-id="9fb54-156">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="9fb54-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="9fb54-157">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="9fb54-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="9fb54-158">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="9fb54-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="9fb54-159">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="9fb54-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="9fb54-160">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="9fb54-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="9fb54-161">建立驗證 cookie</span><span class="sxs-lookup"><span data-stu-id="9fb54-161">Create an authentication cookie</span></span>

<span data-ttu-id="9fb54-162">若要建立 cookie 保存使用者資訊，請建立 <xref:System.Security.Claims.ClaimsPrincipal> 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="9fb54-163">使用者資訊會序列化並儲存在中 cookie 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="9fb54-164"><xref:System.Security.Claims.ClaimsIdentity>使用任何必要的 <xref:System.Security.Claims.Claim> 來建立，並呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 以登入使用者：</span><span class="sxs-lookup"><span data-stu-id="9fb54-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="9fb54-165">`SignInAsync` 建立加密的 cookie ，並將它新增至目前的回應。</span><span class="sxs-lookup"><span data-stu-id="9fb54-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="9fb54-166">如果 `AuthenticationScheme` 未指定，則會使用預設配置。</span><span class="sxs-lookup"><span data-stu-id="9fb54-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="9fb54-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> 預設只會用於一些特定的路徑，例如登入路徑和登出路徑。</span><span class="sxs-lookup"><span data-stu-id="9fb54-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="9fb54-168">如需詳細資訊，請參閱[ Cookie AuthenticationHandler 來源](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334)。</span><span class="sxs-lookup"><span data-stu-id="9fb54-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="9fb54-169">ASP.NET Core 的 [資料保護](xref:security/data-protection/using-data-protection) 系統用於加密。</span><span class="sxs-lookup"><span data-stu-id="9fb54-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="9fb54-170">若為裝載于多部電腦上的應用程式、跨應用程式的負載平衡，或使用 web 伺服陣列，請 [將資料保護設定](xref:security/data-protection/configuration/overview) 為使用相同的金鑰通道和應用程式識別碼。</span><span class="sxs-lookup"><span data-stu-id="9fb54-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="9fb54-171">登出</span><span class="sxs-lookup"><span data-stu-id="9fb54-171">Sign out</span></span>

<span data-ttu-id="9fb54-172">若要登出目前的使用者並刪除其 cookie ，請呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ：</span><span class="sxs-lookup"><span data-stu-id="9fb54-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="9fb54-173">如果 `CookieAuthenticationDefaults.AuthenticationScheme` 未使用 (或 " Cookie s" ) 作為配置 (例如 "Contoso Cookie " ) ，請提供設定驗證提供者時所使用的配置。</span><span class="sxs-lookup"><span data-stu-id="9fb54-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="9fb54-174">否則，會使用預設配置。</span><span class="sxs-lookup"><span data-stu-id="9fb54-174">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="9fb54-175">回應後端變更</span><span class="sxs-lookup"><span data-stu-id="9fb54-175">React to back-end changes</span></span>

<span data-ttu-id="9fb54-176">一旦 cookie 建立之後， cookie 就會是身分識別的單一來源。</span><span class="sxs-lookup"><span data-stu-id="9fb54-176">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="9fb54-177">如果在後端系統中停用使用者帳戶：</span><span class="sxs-lookup"><span data-stu-id="9fb54-177">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="9fb54-178">應用程式的 cookie 驗證系統會根據驗證繼續處理要求 cookie 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-178">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="9fb54-179">只要驗證有效，使用者就會一直登入應用程式 cookie 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-179">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="9fb54-180"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>事件可以用來攔截和覆寫身分識別的驗證 cookie 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-180">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="9fb54-181">cookie在每個要求上進行驗證，可降低撤銷存取應用程式之使用者的風險。</span><span class="sxs-lookup"><span data-stu-id="9fb54-181">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="9fb54-182">驗證的其中一個方法 cookie 是以追蹤使用者資料庫變更的時間為基礎。</span><span class="sxs-lookup"><span data-stu-id="9fb54-182">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="9fb54-183">如果自使用者發出後資料庫尚未變更，則 cookie 不需要重新驗證使用者（如果其 cookie 仍然有效）。</span><span class="sxs-lookup"><span data-stu-id="9fb54-183">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="9fb54-184">在範例應用程式中，資料庫會在中執行， `IUserRepository` 並儲存 `LastChanged` 值。</span><span class="sxs-lookup"><span data-stu-id="9fb54-184">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="9fb54-185">當使用者在資料庫中更新時， `LastChanged` 值會設定為目前的時間。</span><span class="sxs-lookup"><span data-stu-id="9fb54-185">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="9fb54-186">若要使 cookie 資料庫根據值變更時失效 `LastChanged` ，請 cookie 使用 `LastChanged` 包含 `LastChanged` 資料庫目前值的宣告來建立。</span><span class="sxs-lookup"><span data-stu-id="9fb54-186">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="9fb54-187">若要執行事件的覆寫 `ValidatePrincipal` ，請在衍生自的類別中撰寫具有下列簽章的方法 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> ：</span><span class="sxs-lookup"><span data-stu-id="9fb54-187">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="9fb54-188">以下是的範例執行 `CookieAuthenticationEvents` ：</span><span class="sxs-lookup"><span data-stu-id="9fb54-188">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="9fb54-189">cookie在方法的服務註冊期間註冊事件實例 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-189">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9fb54-190">為您的類別提供 [範圍服務註冊](xref:fundamentals/dependency-injection#service-lifetimes) `CustomCookieAuthenticationEvents` ：</span><span class="sxs-lookup"><span data-stu-id="9fb54-190">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="9fb54-191">假設有一種情況，使用者的名稱會以 &mdash; 任何方式來更新不會影響安全性的決策。</span><span class="sxs-lookup"><span data-stu-id="9fb54-191">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="9fb54-192">如果您想要以非破壞性的更新使用者主體，請呼叫 `context.ReplacePrincipal` 並將 `context.ShouldRenew` 屬性設為 `true` 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-192">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="9fb54-193">此處所述的方法會在每個要求上觸發。</span><span class="sxs-lookup"><span data-stu-id="9fb54-193">The approach described here is triggered on every request.</span></span> <span data-ttu-id="9fb54-194">針對 cookie 每個要求的所有使用者驗證驗證，可能會導致應用程式的效能大幅下降。</span><span class="sxs-lookup"><span data-stu-id="9fb54-194">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="9fb54-195">持續性 cookie s</span><span class="sxs-lookup"><span data-stu-id="9fb54-195">Persistent cookies</span></span>

<span data-ttu-id="9fb54-196">您可能想要在 cookie 瀏覽器會話之間保存。</span><span class="sxs-lookup"><span data-stu-id="9fb54-196">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="9fb54-197">只有在登入或類似的機制中，以明確的使用者同意來啟用此持續性，才能使用 [記住我] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="9fb54-197">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="9fb54-198">下列程式碼片段會建立身分識別，並 cookie 透過瀏覽器關閉來對應繼續生存。</span><span class="sxs-lookup"><span data-stu-id="9fb54-198">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="9fb54-199">系統會接受先前設定的任何滑動到期設定。</span><span class="sxs-lookup"><span data-stu-id="9fb54-199">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="9fb54-200">如果在 cookie 瀏覽器關閉時過期，則瀏覽器會在 cookie 重新開機後清除。</span><span class="sxs-lookup"><span data-stu-id="9fb54-200">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="9fb54-201">設定 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> 為 `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ：</span><span class="sxs-lookup"><span data-stu-id="9fb54-201">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="9fb54-202">絕對 cookie 到期</span><span class="sxs-lookup"><span data-stu-id="9fb54-202">Absolute cookie expiration</span></span>

<span data-ttu-id="9fb54-203">您可以使用設定絕對到期時間 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-203">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="9fb54-204">若要建立持續性 cookie ， `IsPersistent` 也必須設定。</span><span class="sxs-lookup"><span data-stu-id="9fb54-204">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="9fb54-205">否則， cookie 會以會話為基礎的存留期建立，而且可能會在其保留的驗證票證之前或之後過期。</span><span class="sxs-lookup"><span data-stu-id="9fb54-205">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="9fb54-206">當 `ExpiresUtc` 設定時，它會覆寫之 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> 選項的值 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> （如果有設定的話）。</span><span class="sxs-lookup"><span data-stu-id="9fb54-206">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="9fb54-207">下列程式碼片段會建立一個 cookie 會持續20分鐘的身分識別。</span><span class="sxs-lookup"><span data-stu-id="9fb54-207">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="9fb54-208">這會忽略先前設定的任何滑動到期設定。</span><span class="sxs-lookup"><span data-stu-id="9fb54-208">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9fb54-209">ASP.NET Core Identity 是完整的完整功能驗證提供者，可用於建立和維護登入。</span><span class="sxs-lookup"><span data-stu-id="9fb54-209">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="9fb54-210">不過，您 cookie 不能使用不使用的驗證驗證提供者 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-210">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="9fb54-211">如需詳細資訊，請參閱<xref:security/authentication/identity>。</span><span class="sxs-lookup"><span data-stu-id="9fb54-211">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="9fb54-212">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="9fb54-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9fb54-213">為了示範範例應用程式中的目的，假設使用者的使用者帳戶（Maria Rodriguez）會硬式編碼到應用程式中。</span><span class="sxs-lookup"><span data-stu-id="9fb54-213">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="9fb54-214">使用 **電子郵件** 位址 `maria.rodriguez@contoso.com` 和任何密碼來登入使用者。</span><span class="sxs-lookup"><span data-stu-id="9fb54-214">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="9fb54-215">使用者會在 `AuthenticateUser` *頁面/帳戶/登入 .cs* 檔案的方法中進行驗證。</span><span class="sxs-lookup"><span data-stu-id="9fb54-215">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="9fb54-216">在真實世界的範例中，使用者會針對資料庫進行驗證。</span><span class="sxs-lookup"><span data-stu-id="9fb54-216">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="9fb54-217">組態</span><span class="sxs-lookup"><span data-stu-id="9fb54-217">Configuration</span></span>

<span data-ttu-id="9fb54-218">如果應用程式不使用 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app)，請在專案檔中建立 AspNetCore 的封裝參考 [。 Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) 套件。</span><span class="sxs-lookup"><span data-stu-id="9fb54-218">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="9fb54-219">在 `Startup.ConfigureServices` 方法中，使用和方法建立驗證中介軟體 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 服務 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ：</span><span class="sxs-lookup"><span data-stu-id="9fb54-219">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="9fb54-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> 傳遞以 `AddAuthentication` 設定應用程式的預設驗證配置。</span><span class="sxs-lookup"><span data-stu-id="9fb54-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="9fb54-221">`AuthenticationScheme` 當有多個 cookie 驗證實例，而且您想要 [使用特定配置來授權](xref:security/authorization/limitingidentitybyscheme)時，這會很有用。</span><span class="sxs-lookup"><span data-stu-id="9fb54-221">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="9fb54-222">將設定 `AuthenticationScheme` 為[ Cookie AuthenticationDefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)時，AuthenticationScheme 會為配置提供值 " Cookie s"。</span><span class="sxs-lookup"><span data-stu-id="9fb54-222">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="9fb54-223">您可以提供任何區分配置的字串值。</span><span class="sxs-lookup"><span data-stu-id="9fb54-223">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="9fb54-224">應用程式的驗證配置不同于應用程式的 cookie 驗證配置。</span><span class="sxs-lookup"><span data-stu-id="9fb54-224">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="9fb54-225">當 cookie 未提供驗證配置時 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ，它會使用 `CookieAuthenticationDefaults.AuthenticationScheme` ( " Cookie s" ) 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-225">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="9fb54-226">驗證 cookie 的 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 屬性預設會設定為 `true` 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-226">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="9fb54-227">cookie當網站訪客未同意資料收集時，允許驗證。</span><span class="sxs-lookup"><span data-stu-id="9fb54-227">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="9fb54-228">如需詳細資訊，請參閱<xref:security/gdpr#essential-cookies>。</span><span class="sxs-lookup"><span data-stu-id="9fb54-228">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="9fb54-229">在 `Startup.Configure` 方法中，呼叫 `UseAuthentication` 方法以叫用設定屬性的驗證中介軟體 `HttpContext.User` 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-229">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="9fb54-230">呼叫 `UseAuthentication` 或之前，請先呼叫方法 `UseMvcWithDefaultRoute` `UseMvc` ：</span><span class="sxs-lookup"><span data-stu-id="9fb54-230">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="9fb54-231"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>類別是用來設定驗證提供者選項。</span><span class="sxs-lookup"><span data-stu-id="9fb54-231">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="9fb54-232">在 `CookieAuthenticationOptions` 服務設定中，于方法中設定驗證 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="9fb54-232">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="9fb54-233">Cookie 原則中介軟體</span><span class="sxs-lookup"><span data-stu-id="9fb54-233">Cookie Policy Middleware</span></span>

<span data-ttu-id="9fb54-234">[ Cookie 原則中介軟體](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)可啟用 cookie 原則功能。</span><span class="sxs-lookup"><span data-stu-id="9fb54-234">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="9fb54-235">將中介軟體新增至應用程式處理管線的順序是機密的， &mdash; 它只會影響在管線中註冊的下游元件。</span><span class="sxs-lookup"><span data-stu-id="9fb54-235">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="9fb54-236"><xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> Cookie cookie cookie 當附加或刪除時，請使用提供給原則中介軟體來控制處理與處理處理常式的全域特性 cookie 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-236">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="9fb54-237">預設 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 值是 `SameSiteMode.Lax` 允許 OAuth2 authentication。</span><span class="sxs-lookup"><span data-stu-id="9fb54-237">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="9fb54-238">若要嚴格地強制執行相同的網站原則 `SameSiteMode.Strict` ，請設定 `MinimumSameSitePolicy` 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-238">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="9fb54-239">雖然這項設定會中斷 OAuth2 和其他的跨原始來源驗證配置，但它會提高 cookie 不依賴跨原始來源要求處理的其他類型應用程式的安全性層級。</span><span class="sxs-lookup"><span data-stu-id="9fb54-239">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="9fb54-240">的 Cookie 原則中介軟體設定， `MinimumSameSitePolicy` 可能會影響 `Cookie.SameSite` 下列矩陣中的設定 `CookieAuthenticationOptions` 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-240">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="9fb54-241">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="9fb54-241">MinimumSameSitePolicy</span></span> | <span data-ttu-id="9fb54-242">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="9fb54-242">Cookie.SameSite</span></span> | <span data-ttu-id="9fb54-243">結果 Cookie 。SameSite 設定</span><span class="sxs-lookup"><span data-stu-id="9fb54-243">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="9fb54-244">SameSiteMode。無</span><span class="sxs-lookup"><span data-stu-id="9fb54-244">SameSiteMode.None</span></span>     | <span data-ttu-id="9fb54-245">SameSiteMode。無</span><span class="sxs-lookup"><span data-stu-id="9fb54-245">SameSiteMode.None</span></span><br><span data-ttu-id="9fb54-246">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="9fb54-246">SameSiteMode.Lax</span></span><br><span data-ttu-id="9fb54-247">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="9fb54-247">SameSiteMode.Strict</span></span> | <span data-ttu-id="9fb54-248">SameSiteMode。無</span><span class="sxs-lookup"><span data-stu-id="9fb54-248">SameSiteMode.None</span></span><br><span data-ttu-id="9fb54-249">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="9fb54-249">SameSiteMode.Lax</span></span><br><span data-ttu-id="9fb54-250">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="9fb54-250">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="9fb54-251">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="9fb54-251">SameSiteMode.Lax</span></span>      | <span data-ttu-id="9fb54-252">SameSiteMode。無</span><span class="sxs-lookup"><span data-stu-id="9fb54-252">SameSiteMode.None</span></span><br><span data-ttu-id="9fb54-253">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="9fb54-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="9fb54-254">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="9fb54-254">SameSiteMode.Strict</span></span> | <span data-ttu-id="9fb54-255">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="9fb54-255">SameSiteMode.Lax</span></span><br><span data-ttu-id="9fb54-256">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="9fb54-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="9fb54-257">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="9fb54-257">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="9fb54-258">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="9fb54-258">SameSiteMode.Strict</span></span>   | <span data-ttu-id="9fb54-259">SameSiteMode。無</span><span class="sxs-lookup"><span data-stu-id="9fb54-259">SameSiteMode.None</span></span><br><span data-ttu-id="9fb54-260">SameSiteMode 不嚴格</span><span class="sxs-lookup"><span data-stu-id="9fb54-260">SameSiteMode.Lax</span></span><br><span data-ttu-id="9fb54-261">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="9fb54-261">SameSiteMode.Strict</span></span> | <span data-ttu-id="9fb54-262">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="9fb54-262">SameSiteMode.Strict</span></span><br><span data-ttu-id="9fb54-263">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="9fb54-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="9fb54-264">SameSiteMode 嚴謹</span><span class="sxs-lookup"><span data-stu-id="9fb54-264">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="9fb54-265">建立驗證 cookie</span><span class="sxs-lookup"><span data-stu-id="9fb54-265">Create an authentication cookie</span></span>

<span data-ttu-id="9fb54-266">若要建立 cookie 保存使用者資訊，請建立 <xref:System.Security.Claims.ClaimsPrincipal> 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-266">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="9fb54-267">使用者資訊會序列化並儲存在中 cookie 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-267">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="9fb54-268"><xref:System.Security.Claims.ClaimsIdentity>使用任何必要的 <xref:System.Security.Claims.Claim> 來建立，並呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 以登入使用者：</span><span class="sxs-lookup"><span data-stu-id="9fb54-268">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="9fb54-269">`SignInAsync` 建立加密的 cookie ，並將它新增至目前的回應。</span><span class="sxs-lookup"><span data-stu-id="9fb54-269">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="9fb54-270">如果 `AuthenticationScheme` 未指定，則會使用預設配置。</span><span class="sxs-lookup"><span data-stu-id="9fb54-270">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="9fb54-271">ASP.NET Core 的 [資料保護](xref:security/data-protection/using-data-protection) 系統用於加密。</span><span class="sxs-lookup"><span data-stu-id="9fb54-271">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="9fb54-272">若為裝載于多部電腦上的應用程式、跨應用程式的負載平衡，或使用 web 伺服陣列，請 [將資料保護設定](xref:security/data-protection/configuration/overview) 為使用相同的金鑰通道和應用程式識別碼。</span><span class="sxs-lookup"><span data-stu-id="9fb54-272">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="9fb54-273">登出</span><span class="sxs-lookup"><span data-stu-id="9fb54-273">Sign out</span></span>

<span data-ttu-id="9fb54-274">若要登出目前的使用者並刪除其 cookie ，請呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ：</span><span class="sxs-lookup"><span data-stu-id="9fb54-274">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="9fb54-275">如果 `CookieAuthenticationDefaults.AuthenticationScheme` 未使用 (或 " Cookie s" ) 作為配置 (例如 "Contoso Cookie " ) ，請提供設定驗證提供者時所使用的配置。</span><span class="sxs-lookup"><span data-stu-id="9fb54-275">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="9fb54-276">否則，會使用預設配置。</span><span class="sxs-lookup"><span data-stu-id="9fb54-276">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="9fb54-277">回應後端變更</span><span class="sxs-lookup"><span data-stu-id="9fb54-277">React to back-end changes</span></span>

<span data-ttu-id="9fb54-278">一旦 cookie 建立之後， cookie 就會是身分識別的單一來源。</span><span class="sxs-lookup"><span data-stu-id="9fb54-278">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="9fb54-279">如果在後端系統中停用使用者帳戶：</span><span class="sxs-lookup"><span data-stu-id="9fb54-279">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="9fb54-280">應用程式的 cookie 驗證系統會根據驗證繼續處理要求 cookie 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-280">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="9fb54-281">只要驗證有效，使用者就會一直登入應用程式 cookie 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-281">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="9fb54-282"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>事件可以用來攔截和覆寫身分識別的驗證 cookie 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-282">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="9fb54-283">cookie在每個要求上進行驗證，可降低撤銷存取應用程式之使用者的風險。</span><span class="sxs-lookup"><span data-stu-id="9fb54-283">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="9fb54-284">驗證的其中一個方法 cookie 是以追蹤使用者資料庫變更的時間為基礎。</span><span class="sxs-lookup"><span data-stu-id="9fb54-284">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="9fb54-285">如果自使用者發出後資料庫尚未變更，則 cookie 不需要重新驗證使用者（如果其 cookie 仍然有效）。</span><span class="sxs-lookup"><span data-stu-id="9fb54-285">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="9fb54-286">在範例應用程式中，資料庫會在中執行， `IUserRepository` 並儲存 `LastChanged` 值。</span><span class="sxs-lookup"><span data-stu-id="9fb54-286">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="9fb54-287">當使用者在資料庫中更新時， `LastChanged` 值會設定為目前的時間。</span><span class="sxs-lookup"><span data-stu-id="9fb54-287">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="9fb54-288">若要使 cookie 資料庫根據值變更時失效 `LastChanged` ，請 cookie 使用 `LastChanged` 包含 `LastChanged` 資料庫目前值的宣告來建立。</span><span class="sxs-lookup"><span data-stu-id="9fb54-288">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="9fb54-289">若要執行事件的覆寫 `ValidatePrincipal` ，請在衍生自的類別中撰寫具有下列簽章的方法 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> ：</span><span class="sxs-lookup"><span data-stu-id="9fb54-289">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="9fb54-290">以下是的範例執行 `CookieAuthenticationEvents` ：</span><span class="sxs-lookup"><span data-stu-id="9fb54-290">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="9fb54-291">cookie在方法的服務註冊期間註冊事件實例 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-291">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9fb54-292">為您的類別提供 [範圍服務註冊](xref:fundamentals/dependency-injection#service-lifetimes) `CustomCookieAuthenticationEvents` ：</span><span class="sxs-lookup"><span data-stu-id="9fb54-292">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="9fb54-293">假設有一種情況，使用者的名稱會以 &mdash; 任何方式來更新不會影響安全性的決策。</span><span class="sxs-lookup"><span data-stu-id="9fb54-293">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="9fb54-294">如果您想要以非破壞性的更新使用者主體，請呼叫 `context.ReplacePrincipal` 並將 `context.ShouldRenew` 屬性設為 `true` 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-294">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="9fb54-295">此處所述的方法會在每個要求上觸發。</span><span class="sxs-lookup"><span data-stu-id="9fb54-295">The approach described here is triggered on every request.</span></span> <span data-ttu-id="9fb54-296">針對 cookie 每個要求的所有使用者驗證驗證，可能會導致應用程式的效能大幅下降。</span><span class="sxs-lookup"><span data-stu-id="9fb54-296">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="9fb54-297">持續性 cookie s</span><span class="sxs-lookup"><span data-stu-id="9fb54-297">Persistent cookies</span></span>

<span data-ttu-id="9fb54-298">您可能想要在 cookie 瀏覽器會話之間保存。</span><span class="sxs-lookup"><span data-stu-id="9fb54-298">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="9fb54-299">只有在登入或類似的機制中，以明確的使用者同意來啟用此持續性，才能使用 [記住我] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="9fb54-299">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="9fb54-300">下列程式碼片段會建立身分識別，並 cookie 透過瀏覽器關閉來對應繼續生存。</span><span class="sxs-lookup"><span data-stu-id="9fb54-300">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="9fb54-301">系統會接受先前設定的任何滑動到期設定。</span><span class="sxs-lookup"><span data-stu-id="9fb54-301">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="9fb54-302">如果在 cookie 瀏覽器關閉時過期，則瀏覽器會在 cookie 重新開機後清除。</span><span class="sxs-lookup"><span data-stu-id="9fb54-302">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="9fb54-303">設定 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> 為 `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ：</span><span class="sxs-lookup"><span data-stu-id="9fb54-303">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="9fb54-304">絕對 cookie 到期</span><span class="sxs-lookup"><span data-stu-id="9fb54-304">Absolute cookie expiration</span></span>

<span data-ttu-id="9fb54-305">您可以使用設定絕對到期時間 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> 。</span><span class="sxs-lookup"><span data-stu-id="9fb54-305">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="9fb54-306">若要建立持續性 cookie ， `IsPersistent` 也必須設定。</span><span class="sxs-lookup"><span data-stu-id="9fb54-306">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="9fb54-307">否則， cookie 會以會話為基礎的存留期建立，而且可能會在其保留的驗證票證之前或之後過期。</span><span class="sxs-lookup"><span data-stu-id="9fb54-307">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="9fb54-308">當 `ExpiresUtc` 設定時，它會覆寫之 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> 選項的值 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> （如果有設定的話）。</span><span class="sxs-lookup"><span data-stu-id="9fb54-308">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="9fb54-309">下列程式碼片段會建立一個 cookie 會持續20分鐘的身分識別。</span><span class="sxs-lookup"><span data-stu-id="9fb54-309">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="9fb54-310">這會忽略先前設定的任何滑動到期設定。</span><span class="sxs-lookup"><span data-stu-id="9fb54-310">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9fb54-311">其他資源</span><span class="sxs-lookup"><span data-stu-id="9fb54-311">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="9fb54-312">以原則為基礎的角色檢查</span><span class="sxs-lookup"><span data-stu-id="9fb54-312">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
