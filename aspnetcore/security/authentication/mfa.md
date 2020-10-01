---
title: ASP.NET Core 中的多重要素驗證
author: damienbod
description: 瞭解如何在 ASP.NET Core 應用程式中設定 (MFA) 的多重要素驗證。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
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
uid: security/authentication/mfa
ms.openlocfilehash: 76a11aa7b89b3ce60ed11bd7553a7e5898f661f4
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606805"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a><span data-ttu-id="9dad7-103">ASP.NET Core 中的多重要素驗證</span><span class="sxs-lookup"><span data-stu-id="9dad7-103">Multi-factor authentication in ASP.NET Core</span></span>

<span data-ttu-id="9dad7-104">依 [Damien Bowden](https://github.com/damienbod)</span><span class="sxs-lookup"><span data-stu-id="9dad7-104">By [Damien Bowden](https://github.com/damienbod)</span></span>

[<span data-ttu-id="9dad7-105">查看或下載 (damienbod/AspNetCoreHybridFlowWithApi GitHub 存放庫) 的範例程式碼 </span><span class="sxs-lookup"><span data-stu-id="9dad7-105">View or download sample code (damienbod/AspNetCoreHybridFlowWithApi GitHub repository)</span></span>](https://github.com/damienbod/AspNetCoreHybridFlowWithApi)

<span data-ttu-id="9dad7-106">多重要素驗證 (MFA) 是在登入事件期間要求使用者以進行其他形式的識別的進程。</span><span class="sxs-lookup"><span data-stu-id="9dad7-106">Multi-factor authentication (MFA) is a process in which a user is requested during a sign-in event for additional forms of identification.</span></span> <span data-ttu-id="9dad7-107">此提示可能是從行動電話輸入代碼、使用 FIDO2 金鑰，或提供指紋掃描。</span><span class="sxs-lookup"><span data-stu-id="9dad7-107">This prompt could be to enter a code from a cellphone, use a FIDO2 key, or to provide a fingerprint scan.</span></span> <span data-ttu-id="9dad7-108">當您需要第二種形式的驗證時，會增強安全性。</span><span class="sxs-lookup"><span data-stu-id="9dad7-108">When you require a second form of authentication, security is enhanced.</span></span> <span data-ttu-id="9dad7-109">攻擊者無法輕易取得或複製其他因素。</span><span class="sxs-lookup"><span data-stu-id="9dad7-109">The additional factor isn't easily obtained or duplicated by an attacker.</span></span>

<span data-ttu-id="9dad7-110">本文涵蓋下列領域：</span><span class="sxs-lookup"><span data-stu-id="9dad7-110">This article covers the following areas:</span></span>

* <span data-ttu-id="9dad7-111">何謂 MFA，以及建議使用哪些 MFA 流程</span><span class="sxs-lookup"><span data-stu-id="9dad7-111">What is MFA and what MFA flows are recommended</span></span>
* <span data-ttu-id="9dad7-112">使用管理頁面的設定 MFA ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="9dad7-112">Configure MFA for administration pages using ASP.NET Core Identity</span></span>
* <span data-ttu-id="9dad7-113">將 MFA 登入需求傳送至 OpenID Connect server</span><span class="sxs-lookup"><span data-stu-id="9dad7-113">Send MFA sign-in requirement to OpenID Connect server</span></span>
* <span data-ttu-id="9dad7-114">強制 ASP.NET Core OpenID Connect 用戶端要求 MFA</span><span class="sxs-lookup"><span data-stu-id="9dad7-114">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

## <a name="mfa-2fa"></a><span data-ttu-id="9dad7-115">MFA、2FA</span><span class="sxs-lookup"><span data-stu-id="9dad7-115">MFA, 2FA</span></span>

<span data-ttu-id="9dad7-116">MFA 需要至少兩種或更多類型的身分識別，例如您知道的某些資訊、您擁有的內容，或使用者要驗證的生物識別驗證。</span><span class="sxs-lookup"><span data-stu-id="9dad7-116">MFA requires at least two or more types of proof for an identity like something you know, something you possess, or biometric validation for the user to authenticate.</span></span>

<span data-ttu-id="9dad7-117">雙因素驗證 (2FA) 就像是 MFA 的子集，但不同之處在于 MFA 可能需要兩個以上的因素來證明身分識別。</span><span class="sxs-lookup"><span data-stu-id="9dad7-117">Two-factor authentication (2FA) is like a subset of MFA, but the difference being that MFA can require two or more factors to prove the identity.</span></span>

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a><span data-ttu-id="9dad7-118">MFA TOTP (以時間為基礎的一次性密碼演算法) </span><span class="sxs-lookup"><span data-stu-id="9dad7-118">MFA TOTP (Time-based One-time Password Algorithm)</span></span>

<span data-ttu-id="9dad7-119">使用 TOTP 的 MFA 是使用支援的實作為 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="9dad7-119">MFA using TOTP is a supported implementation using ASP.NET Core Identity.</span></span> <span data-ttu-id="9dad7-120">這可以與任何符合規範的驗證器應用程式搭配使用，包括：</span><span class="sxs-lookup"><span data-stu-id="9dad7-120">This can be used together with any compliant authenticator app, including:</span></span>

* <span data-ttu-id="9dad7-121">Microsoft Authenticator 應用程式</span><span class="sxs-lookup"><span data-stu-id="9dad7-121">Microsoft Authenticator App</span></span>
* <span data-ttu-id="9dad7-122">Google 驗證器應用程式</span><span class="sxs-lookup"><span data-stu-id="9dad7-122">Google Authenticator App</span></span>

<span data-ttu-id="9dad7-123">請參閱下列連結以取得執行詳細資料：</span><span class="sxs-lookup"><span data-stu-id="9dad7-123">See the following link for implementation details:</span></span>

[<span data-ttu-id="9dad7-124">在 ASP.NET Core 中啟用 TOTP 驗證器應用程式的 QR 代碼產生</span><span class="sxs-lookup"><span data-stu-id="9dad7-124">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a><span data-ttu-id="9dad7-125">MFA FIDO2 或無密碼</span><span class="sxs-lookup"><span data-stu-id="9dad7-125">MFA FIDO2 or passwordless</span></span>

<span data-ttu-id="9dad7-126">FIDO2 目前為：</span><span class="sxs-lookup"><span data-stu-id="9dad7-126">FIDO2 is currently:</span></span>

* <span data-ttu-id="9dad7-127">達到 MFA 最安全的方式。</span><span class="sxs-lookup"><span data-stu-id="9dad7-127">The most secure way of achieving MFA.</span></span>
* <span data-ttu-id="9dad7-128">唯一可防範網路釣魚攻擊的 MFA 流程。</span><span class="sxs-lookup"><span data-stu-id="9dad7-128">The only MFA flow that protects against phishing attacks.</span></span>

<span data-ttu-id="9dad7-129">目前，ASP.NET Core 不會直接支援 FIDO2。</span><span class="sxs-lookup"><span data-stu-id="9dad7-129">At present, ASP.NET Core doesn't support FIDO2 directly.</span></span> <span data-ttu-id="9dad7-130">FIDO2 可用於 MFA 或無密碼流程。</span><span class="sxs-lookup"><span data-stu-id="9dad7-130">FIDO2 can be used for MFA or passwordless flows.</span></span>

<span data-ttu-id="9dad7-131">Azure Active Directory 提供 FIDO2 和無密碼流程的支援。</span><span class="sxs-lookup"><span data-stu-id="9dad7-131">Azure Active Directory provides support for FIDO2 and passwordless flows.</span></span> <span data-ttu-id="9dad7-132">如需詳細資訊，請參閱 [Azure Active Directory 的無密碼 authentication 選項](/azure/active-directory/authentication/concept-authentication-passwordless)。</span><span class="sxs-lookup"><span data-stu-id="9dad7-132">For more information, see [Passwordless authentication options for Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span></span>

### <a name="mfa-sms"></a><span data-ttu-id="9dad7-133">MFA SMS</span><span class="sxs-lookup"><span data-stu-id="9dad7-133">MFA SMS</span></span>

<span data-ttu-id="9dad7-134">使用 SMS 的 MFA 可大幅提高安全性，相較于密碼驗證 (單一因素) 。</span><span class="sxs-lookup"><span data-stu-id="9dad7-134">MFA with SMS increases security massively compared with password authentication (single factor).</span></span> <span data-ttu-id="9dad7-135">不過，不再建議使用 SMS 做為第二個因素。</span><span class="sxs-lookup"><span data-stu-id="9dad7-135">However, using SMS as a second factor is no longer recommended.</span></span> <span data-ttu-id="9dad7-136">這種類型的執行有太多已知的攻擊媒介。</span><span class="sxs-lookup"><span data-stu-id="9dad7-136">Too many known attack vectors exist for this type of implementation.</span></span>

[<span data-ttu-id="9dad7-137">NIST 指導方針</span><span class="sxs-lookup"><span data-stu-id="9dad7-137">NIST guidelines</span></span>](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-no-locaspnet-core-identity"></a><span data-ttu-id="9dad7-138">使用管理頁面的設定 MFA ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="9dad7-138">Configure MFA for administration pages using ASP.NET Core Identity</span></span>

<span data-ttu-id="9dad7-139">MFA 可能會強制使用者存取應用程式內的敏感頁面 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="9dad7-139">MFA could be forced on users to access sensitive pages within an ASP.NET Core Identity app.</span></span> <span data-ttu-id="9dad7-140">針對不同的身分識別存在不同存取層級的應用程式，這可能會很有用。</span><span class="sxs-lookup"><span data-stu-id="9dad7-140">This could be useful for apps where different levels of access exist for the different identities.</span></span> <span data-ttu-id="9dad7-141">例如，使用者或許可以使用密碼登入來查看設定檔資料，但系統管理員必須使用 MFA 來存取系統管理頁面。</span><span class="sxs-lookup"><span data-stu-id="9dad7-141">For example, users might be able to view the profile data using a password login, but an administrator would be required to use MFA to access the administrative pages.</span></span>

### <a name="extend-the-login-with-an-mfa-claim"></a><span data-ttu-id="9dad7-142">使用 MFA 宣告擴充登入</span><span class="sxs-lookup"><span data-stu-id="9dad7-142">Extend the login with an MFA claim</span></span>

<span data-ttu-id="9dad7-143">示範程式碼是使用 ASP.NET Core 搭配 Identity 和頁面進行設定 Razor 。</span><span class="sxs-lookup"><span data-stu-id="9dad7-143">The demo code is setup using ASP.NET Core with Identity and Razor Pages.</span></span> <span data-ttu-id="9dad7-144">`AddIdentity`使用方法，而不是使用方法 `AddDefaultIdentity` ，因此 `IUserClaimsPrincipalFactory` 可以在成功登入之後，使用執行將宣告加入至身分識別。</span><span class="sxs-lookup"><span data-stu-id="9dad7-144">The `AddIdentity` method is used instead of `AddDefaultIdentity` one, so an `IUserClaimsPrincipalFactory` implementation can be used to add claims to the identity after a successful login.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    
    services.AddIdentity<IdentityUser, IdentityRole>(
            options => options.SignIn.RequireConfirmedAccount = false)
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddSingleton<IEmailSender, EmailSender>();
    services.AddScoped<IUserClaimsPrincipalFactory<IdentityUser>, 
        AdditionalUserClaimsPrincipalFactory>();

    services.AddAuthorization(options =>
        options.AddPolicy("TwoFactorEnabled",
            x => x.RequireClaim("amr", "mfa")));

    services.AddRazorPages();
}
```

<span data-ttu-id="9dad7-145">`AdditionalUserClaimsPrincipalFactory`類別只會在 `amr` 成功登入之後，將宣告新增至使用者宣告。</span><span class="sxs-lookup"><span data-stu-id="9dad7-145">The `AdditionalUserClaimsPrincipalFactory` class adds the `amr` claim to the user claims only after a successful login.</span></span> <span data-ttu-id="9dad7-146">宣告的值是從資料庫讀取的。</span><span class="sxs-lookup"><span data-stu-id="9dad7-146">The claim's value is read from the database.</span></span> <span data-ttu-id="9dad7-147">宣告會新增到此處，因為如果身分識別已使用 MFA 登入，則使用者應該只存取較高的受保護的檢視。</span><span class="sxs-lookup"><span data-stu-id="9dad7-147">The claim is added here because the user should only access the higher protected view if the identity has logged in with MFA.</span></span> <span data-ttu-id="9dad7-148">如果您直接從資料庫讀取資料庫檢視，而不是使用宣告，則在啟用 MFA 後，就可以直接存取沒有 MFA 的視圖。</span><span class="sxs-lookup"><span data-stu-id="9dad7-148">If the database view is read from the database directly instead of using the claim, it's possible to access the view without MFA directly after activating the MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Options;
using System.Collections.Generic;
using System.Security.Claims;
using System.Threading.Tasks;

namespace IdentityStandaloneMfa
{
    public class AdditionalUserClaimsPrincipalFactory : 
        UserClaimsPrincipalFactory<IdentityUser, IdentityRole>
    {
        public AdditionalUserClaimsPrincipalFactory( 
            UserManager<IdentityUser> userManager,
            RoleManager<IdentityRole> roleManager, 
            IOptions<IdentityOptions> optionsAccessor) 
            : base(userManager, roleManager, optionsAccessor)
        {
        }

        public async override Task<ClaimsPrincipal> CreateAsync(IdentityUser user)
        {
            var principal = await base.CreateAsync(user);
            var identity = (ClaimsIdentity)principal.Identity;

            var claims = new List<Claim>();

            if (user.TwoFactorEnabled)
            {
                claims.Add(new Claim("amr", "mfa"));
            }
            else
            {
                claims.Add(new Claim("amr", "pwd"));
            }

            identity.AddClaims(claims);
            return principal;
        }
    }
}
```

<span data-ttu-id="9dad7-149">因為 Identity 服務設定在類別中有所變更 `Startup` ，所以需要更新的版面配置 Identity 。</span><span class="sxs-lookup"><span data-stu-id="9dad7-149">Because the Identity service setup changed in the `Startup` class, the layouts of the Identity need to be updated.</span></span> <span data-ttu-id="9dad7-150">將 Identity 頁面 Scaffold 到應用程式中。</span><span class="sxs-lookup"><span data-stu-id="9dad7-150">Scaffold the Identity pages into the app.</span></span> <span data-ttu-id="9dad7-151">在\* Identity /Account/Manage/_Layout cshtml\*檔案中定義版面配置。</span><span class="sxs-lookup"><span data-stu-id="9dad7-151">Define the layout in the *Identity/Account/Manage/_Layout.cshtml* file.</span></span>

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

<span data-ttu-id="9dad7-152">此外，請從頁面指派所有管理頁面的版面配置 Identity ：</span><span class="sxs-lookup"><span data-stu-id="9dad7-152">Also assign the layout for all the manage pages from the Identity pages:</span></span>

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a><span data-ttu-id="9dad7-153">在 [系統管理] 頁面中驗證 MFA 需求</span><span class="sxs-lookup"><span data-stu-id="9dad7-153">Validate the MFA requirement in the administration page</span></span>

<span data-ttu-id="9dad7-154">系統管理 Razor 頁面會驗證使用者是否已使用 MFA 登入。</span><span class="sxs-lookup"><span data-stu-id="9dad7-154">The administration Razor Page validates that the user has logged in using MFA.</span></span> <span data-ttu-id="9dad7-155">在 `OnGet` 方法中，會使用身分識別來存取使用者宣告。</span><span class="sxs-lookup"><span data-stu-id="9dad7-155">In the `OnGet` method, the identity is used to access the user claims.</span></span> <span data-ttu-id="9dad7-156">檢查宣告的 `amr` 值 `mfa` 。</span><span class="sxs-lookup"><span data-stu-id="9dad7-156">The `amr` claim is checked for the value `mfa`.</span></span> <span data-ttu-id="9dad7-157">如果身分識別缺少此宣告或為 `false` ，則頁面會重新導向至 [啟用 MFA] 頁面。</span><span class="sxs-lookup"><span data-stu-id="9dad7-157">If the identity is missing this claim or is `false`, the page redirects to the Enable MFA page.</span></span> <span data-ttu-id="9dad7-158">這是可能的，因為使用者已登入，但沒有 MFA。</span><span class="sxs-lookup"><span data-stu-id="9dad7-158">This is possible because the user has logged in already, but without MFA.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace IdentityStandaloneMfa
{
    public class AdminModel : PageModel
    {
        public IActionResult OnGet()
        {
            var claimTwoFactorEnabled = 
                User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (claimTwoFactorEnabled != null && 
                "mfa".Equals(claimTwoFactorEnabled.Value))
            {
                // You logged in with MFA, do the administrative stuff
            }
            else
            {
                return Redirect(
                    "/Identity/Account/Manage/TwoFactorAuthentication");
            }

            return Page();
        }
    }
}
```

### <a name="ui-logic-to-toggle-user-login-information"></a><span data-ttu-id="9dad7-159">切換使用者登入資訊的 UI 邏輯</span><span class="sxs-lookup"><span data-stu-id="9dad7-159">UI logic to toggle user login information</span></span>

<span data-ttu-id="9dad7-160">系統會在啟動時新增授權原則。</span><span class="sxs-lookup"><span data-stu-id="9dad7-160">An authorization policy was added at startup.</span></span> <span data-ttu-id="9dad7-161">原則需要 `amr` 具有值的宣告 `mfa` 。</span><span class="sxs-lookup"><span data-stu-id="9dad7-161">The policy requires the `amr` claim with the value `mfa`.</span></span>

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

<span data-ttu-id="9dad7-162">然後，您可以在此窗格中使用此原則 `_Layout` 顯示或隱藏 [ **管理** ] 功能表，並顯示警告：</span><span class="sxs-lookup"><span data-stu-id="9dad7-162">This policy can then be used in the `_Layout` view to show or hide the **Admin** menu with the warning:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="9dad7-163">如果身分識別已使用 MFA 登入，則會顯示系統 **管理** 功能表，而不會出現工具提示警告。</span><span class="sxs-lookup"><span data-stu-id="9dad7-163">If the identity has logged in using MFA, the **Admin** menu is displayed without the tooltip warning.</span></span> <span data-ttu-id="9dad7-164">當使用者登入但未啟用 MFA 時，系統會顯示 [系統 \*\*管理員 (未啟用]) \*\* 功能表和工具提示，告知使用者 (說明警告) 。</span><span class="sxs-lookup"><span data-stu-id="9dad7-164">When the user has logged in without MFA, the **Admin (Not Enabled)** menu is displayed along with the tooltip that informs the user (explaining the warning).</span></span>

```cshtml
@if (SignInManager.IsSignedIn(User))
{
    @if ((AuthorizationService.AuthorizeAsync(User, "TwoFactorEnabled")).Result.Succeeded)
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin">Admin</a>
        </li>
    }
    else
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin" 
               id="tooltip-demo"  
               data-toggle="tooltip" 
               data-placement="bottom" 
               title="MFA is NOT enabled. This is required for the Admin Page. If you have activated MFA, then logout, login again.">
                Admin (Not Enabled)
            </a>
        </li>
    }
}
```

<span data-ttu-id="9dad7-165">如果使用者在沒有 MFA 的情況下登入，則會顯示警告：</span><span class="sxs-lookup"><span data-stu-id="9dad7-165">If the user logs in without MFA, the warning is displayed:</span></span>

![系統管理員 MFA 驗證](mfa/_static/identitystandalonemfa_01.png)

<span data-ttu-id="9dad7-167">當您按一下 [系統 **管理員** ] 連結時，系統會將使用者重新導向至 MFA enable view：</span><span class="sxs-lookup"><span data-stu-id="9dad7-167">The user is redirected to the MFA enable view when clicking the **Admin** link:</span></span>

![系統管理員啟用 MFA 驗證](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a><span data-ttu-id="9dad7-169">將 MFA 登入需求傳送至 OpenID Connect server</span><span class="sxs-lookup"><span data-stu-id="9dad7-169">Send MFA sign-in requirement to OpenID Connect server</span></span> 

<span data-ttu-id="9dad7-170">`acr_values`參數可以用來 `mfa` 在驗證要求中，將必要的值從用戶端傳遞至伺服器。</span><span class="sxs-lookup"><span data-stu-id="9dad7-170">The `acr_values` parameter can be used to pass the `mfa` required value from the client to the server in an authentication request.</span></span>

> [!NOTE]
> <span data-ttu-id="9dad7-171">`acr_values`參數必須在 OpenID Connect 伺服器上處理，才能運作。</span><span class="sxs-lookup"><span data-stu-id="9dad7-171">The `acr_values` parameter needs to be handled on the OpenID Connect server for this to work.</span></span>

### <a name="openid-connect-aspnet-core-client"></a><span data-ttu-id="9dad7-172">OpenID Connect ASP.NET Core 用戶端</span><span class="sxs-lookup"><span data-stu-id="9dad7-172">OpenID Connect ASP.NET Core client</span></span>

<span data-ttu-id="9dad7-173">Razor用戶端應用程式 OpenID Connect 的 ASP.NET Core 頁面會使用 `AddOpenIdConnect` 方法來登入 OpenID Connect 伺服器。</span><span class="sxs-lookup"><span data-stu-id="9dad7-173">The ASP.NET Core Razor Pages OpenID Connect client app uses the `AddOpenIdConnect` method to login to the OpenID Connect server.</span></span> <span data-ttu-id="9dad7-174">`acr_values`參數會以 `mfa` 值設定，並與驗證要求一起傳送。</span><span class="sxs-lookup"><span data-stu-id="9dad7-174">The `acr_values` parameter is set with the `mfa` value and sent with the authentication request.</span></span> <span data-ttu-id="9dad7-175">`OpenIdConnectEvents`用來加入這個。</span><span class="sxs-lookup"><span data-stu-id="9dad7-175">The `OpenIdConnectEvents` is used to add this.</span></span>

<span data-ttu-id="9dad7-176">如需建議的 `acr_values` 參數值，請參閱 [驗證方法參考值](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08)。</span><span class="sxs-lookup"><span data-stu-id="9dad7-176">For recommended `acr_values` parameter values, see [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "<OpenID Connect server URL>";
        options.RequireHttpsMetadata = true;
        options.ClientId = "<OpenID Connect client ID>";
        options.ClientSecret = "<>";
        // Code with PKCE can also be used here
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
        options.Events = new OpenIdConnectEvents
        {
            OnRedirectToIdentityProvider = context =>
            {
                context.ProtocolMessage.SetParameter("acr_values", "mfa");
                return Task.FromResult(0);
            }
        };
    });
```

### <a name="example-openid-connect-no-locidentityserver-4-server-with-no-locaspnet-core-identity"></a><span data-ttu-id="9dad7-177">範例 OpenID Connect Identity server 4 伺服器 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="9dad7-177">Example OpenID Connect IdentityServer 4 server with ASP.NET Core Identity</span></span>

<span data-ttu-id="9dad7-178">在使用搭配 MVC views 來執行的 OpenID Connect 伺服器上 ASP.NET Core Identity ，會建立名為 *ErrorEnable2FA* 的新視圖。</span><span class="sxs-lookup"><span data-stu-id="9dad7-178">On the OpenID Connect server, which is implemented using ASP.NET Core Identity with MVC views, a new view named *ErrorEnable2FA.cshtml* is created.</span></span> <span data-ttu-id="9dad7-179">檢視：</span><span class="sxs-lookup"><span data-stu-id="9dad7-179">The view:</span></span>

* <span data-ttu-id="9dad7-180">如果 Identity 來自需要 MFA 但使用者未在中啟用此功能的應用程式，則會顯示 Identity 。</span><span class="sxs-lookup"><span data-stu-id="9dad7-180">Displays if the Identity comes from an app that requires MFA but the user hasn't activated this in Identity.</span></span>
* <span data-ttu-id="9dad7-181">通知使用者，並新增用來啟用此的連結。</span><span class="sxs-lookup"><span data-stu-id="9dad7-181">Informs the user and adds a link to activate this.</span></span>

```cshtml
@{
    ViewData["Title"] = "ErrorEnable2FA";
}

<h1>The client application requires you to have MFA enabled. Enable this, try login again.</h1>

<br />

You can enable MFA to login here:

<br />

<a asp-controller="Manage" asp-action="TwoFactorAuthentication">Enable MFA</a>
```

<span data-ttu-id="9dad7-182">在 `Login` 方法中， `IIdentityServerInteractionService` 會使用介面實作為 `_interaction` 存取 OpenID Connect 要求參數。</span><span class="sxs-lookup"><span data-stu-id="9dad7-182">In the `Login` method, the `IIdentityServerInteractionService` interface implementation `_interaction` is used to access the OpenID Connect request parameters.</span></span> <span data-ttu-id="9dad7-183">您 `acr_values` 可以使用屬性來存取參數 `AcrValues` 。</span><span class="sxs-lookup"><span data-stu-id="9dad7-183">The `acr_values` parameter is accessed using the `AcrValues` property.</span></span> <span data-ttu-id="9dad7-184">當用戶端使用 set 傳送此 `mfa` 設定時，就可以檢查。</span><span class="sxs-lookup"><span data-stu-id="9dad7-184">As the client sent this with `mfa` set, this can then be checked.</span></span>

<span data-ttu-id="9dad7-185">如果需要 MFA，且中的使用者 ASP.NET Core Identity 已啟用 mfa，則會繼續登入。</span><span class="sxs-lookup"><span data-stu-id="9dad7-185">If MFA is required, and the user in ASP.NET Core Identity has MFA enabled, then the login continues.</span></span> <span data-ttu-id="9dad7-186">當使用者未啟用 MFA 時，會將使用者重新導向至自訂視圖*ErrorEnable2FA。*</span><span class="sxs-lookup"><span data-stu-id="9dad7-186">When the user has no MFA enabled, the user is redirected to the custom view *ErrorEnable2FA.cshtml*.</span></span> <span data-ttu-id="9dad7-187">然後 ASP.NET Core Identity 將使用者登入。</span><span class="sxs-lookup"><span data-stu-id="9dad7-187">Then ASP.NET Core Identity signs the user in.</span></span>

```csharp
//
// POST: /Account/Login
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Login(LoginInputModel model)
{
    var returnUrl = model.ReturnUrl;
    var context = 
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa = 
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    var user = await _userManager.FindByNameAsync(model.Email);
    if (user != null && !user.TwoFactorEnabled && requires2Fa)
    {
        return RedirectToAction(nameof(ErrorEnable2FA));
    }

    // code omitted for brevity
```

<span data-ttu-id="9dad7-188">`ExternalLoginCallback`方法的運作方式類似本機 Identity 登入。</span><span class="sxs-lookup"><span data-stu-id="9dad7-188">The `ExternalLoginCallback` method works like the local Identity login.</span></span> <span data-ttu-id="9dad7-189">`AcrValues`會檢查屬性的 `mfa` 值。</span><span class="sxs-lookup"><span data-stu-id="9dad7-189">The `AcrValues` property is checked for the `mfa` value.</span></span> <span data-ttu-id="9dad7-190">如果 `mfa` 有此值，則會在登入完成之前強制 MFA (例如，重新導向至 `ErrorEnable2FA` view) 。</span><span class="sxs-lookup"><span data-stu-id="9dad7-190">If the `mfa` value is present, MFA is forced before the login completes (for example, redirected to the `ErrorEnable2FA` view).</span></span>

```csharp
//
// GET: /Account/ExternalLoginCallback
[HttpGet]
[AllowAnonymous]
public async Task<IActionResult> ExternalLoginCallback(
    string returnUrl = null,
    string remoteError = null)
{
    var context =
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa =
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    if (remoteError != null)
    {
        ModelState.AddModelError(
            string.Empty,
            _sharedLocalizer["EXTERNAL_PROVIDER_ERROR", 
            remoteError]);
        return View(nameof(Login));
    }
    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        return RedirectToAction(nameof(Login));
    }

    var email = info.Principal.FindFirstValue(ClaimTypes.Email);

    if (!string.IsNullOrEmpty(email))
    {
        var user = await _userManager.FindByNameAsync(email);
        if (user != null && !user.TwoFactorEnabled && requires2Fa)
        {
            return RedirectToAction(nameof(ErrorEnable2FA));
        }
    }

    // Sign in the user with this external login provider if the user already has a login.
    var result = await _signInManager
        .ExternalLoginSignInAsync(
            info.LoginProvider, 
            info.ProviderKey, 
            isPersistent: 
            false);

    // code omitted for brevity
```

<span data-ttu-id="9dad7-191">如果使用者已經登入，用戶端應用程式：</span><span class="sxs-lookup"><span data-stu-id="9dad7-191">If the user is already logged in, the client app:</span></span>

* <span data-ttu-id="9dad7-192">仍會驗證宣告 `amr` 。</span><span class="sxs-lookup"><span data-stu-id="9dad7-192">Still validates the `amr` claim.</span></span>
* <span data-ttu-id="9dad7-193">可以使用視圖的連結來設定 MFA ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="9dad7-193">Can set up the MFA with a link to the ASP.NET Core Identity view.</span></span>

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a><span data-ttu-id="9dad7-195">強制 ASP.NET Core OpenID Connect 用戶端要求 MFA</span><span class="sxs-lookup"><span data-stu-id="9dad7-195">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

<span data-ttu-id="9dad7-196">此範例示範如何使用 Razor OpenID Connect 登入 ASP.NET Core 頁面應用程式，可以要求使用者使用 MFA 進行驗證。</span><span class="sxs-lookup"><span data-stu-id="9dad7-196">This example shows how an ASP.NET Core Razor Page app, which uses OpenID Connect to sign in, can require that users have authenticated using MFA.</span></span>

<span data-ttu-id="9dad7-197">若要驗證 MFA 需求， `IAuthorizationRequirement` 會建立一個需求。</span><span class="sxs-lookup"><span data-stu-id="9dad7-197">To validate the MFA requirement, an `IAuthorizationRequirement` requirement is created.</span></span> <span data-ttu-id="9dad7-198">這會使用需要 MFA 的原則新增到頁面。</span><span class="sxs-lookup"><span data-stu-id="9dad7-198">This will be added to the pages using a policy that requires MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

<span data-ttu-id="9dad7-199">會實作為 `AuthorizationHandler` ，它會使用宣告 `amr` 並檢查值 `mfa` 。</span><span class="sxs-lookup"><span data-stu-id="9dad7-199">An `AuthorizationHandler` is implemented that will use the `amr` claim and check for the value `mfa`.</span></span> <span data-ttu-id="9dad7-200">`amr`會在驗證成功時傳回， `id_token` 而且可以有許多不同的值，如[驗證方法參考值](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08)規格中所定義。</span><span class="sxs-lookup"><span data-stu-id="9dad7-200">The `amr` is returned in the `id_token` of a successful authentication and can have many different values as defined in the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="9dad7-201">傳回的值取決於身分識別如何進行驗證，以及如何在 OpenID Connect 伺服器上執行。</span><span class="sxs-lookup"><span data-stu-id="9dad7-201">The returned value depends on how the identity authenticated and on the OpenID Connect server implementation.</span></span>

<span data-ttu-id="9dad7-202">會 `AuthorizationHandler` 使用 `RequireMfa` 需求並驗證宣告 `amr` 。</span><span class="sxs-lookup"><span data-stu-id="9dad7-202">The `AuthorizationHandler` uses the `RequireMfa` requirement and validates the `amr` claim.</span></span> <span data-ttu-id="9dad7-203">OpenID Connect 伺服器可以搭配使用 Server4 來執行 Identity ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="9dad7-203">The OpenID Connect server can be implemented using IdentityServer4 with ASP.NET Core Identity.</span></span> <span data-ttu-id="9dad7-204">當使用者使用 TOTP 進行登入時， `amr` 會傳回具有 MFA 值的宣告。</span><span class="sxs-lookup"><span data-stu-id="9dad7-204">When a user logs in using TOTP, the `amr` claim is returned with an MFA value.</span></span> <span data-ttu-id="9dad7-205">如果使用不同的 OpenID Connect server 執行或不同的 MFA 類型，宣告 `amr` 將會有不同的值，或可以有不同的值。</span><span class="sxs-lookup"><span data-stu-id="9dad7-205">If using a different OpenID Connect server implementation or a different MFA type, the `amr` claim will, or can, have a different value.</span></span> <span data-ttu-id="9dad7-206">您也必須延伸程式碼，才能接受此程式碼。</span><span class="sxs-lookup"><span data-stu-id="9dad7-206">The code must be extended to accept this as well.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfaHandler : AuthorizationHandler<RequireMfa>
    {
        protected override Task HandleRequirementAsync(
            AuthorizationHandlerContext context, 
            RequireMfa requirement)
        {
            if (context == null)
                throw new ArgumentNullException(nameof(context));
            if (requirement == null)
                throw new ArgumentNullException(nameof(requirement));

            var amrClaim =
                context.User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (amrClaim != null && amrClaim.Value == Amr.Mfa)
            {
                context.Succeed(requirement);
            }

            return Task.CompletedTask;
        }
    }
}
```

<span data-ttu-id="9dad7-207">在 `Startup.ConfigureServices` 方法中， `AddOpenIdConnect` 會使用方法做為預設的挑戰配置。</span><span class="sxs-lookup"><span data-stu-id="9dad7-207">In the `Startup.ConfigureServices` method, the `AddOpenIdConnect` method is used as the default challenge scheme.</span></span> <span data-ttu-id="9dad7-208">用來檢查宣告的授權處理常式 `amr` 會新增至控制反轉容器。</span><span class="sxs-lookup"><span data-stu-id="9dad7-208">The authorization handler, which is used to check the `amr` claim, is added to the Inversion of Control container.</span></span> <span data-ttu-id="9dad7-209">接著會建立原則，以新增 `RequireMfa` 需求。</span><span class="sxs-lookup"><span data-stu-id="9dad7-209">A policy is then created which adds the `RequireMfa` requirement.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.ConfigureApplicationCookie(options =>
        options.Cookie.SecurePolicy =
            CookieSecurePolicy.Always);

    services.AddSingleton<IAuthorizationHandler, RequireMfaHandler>();

    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "https://localhost:44352";
        options.RequireHttpsMetadata = true;
        options.ClientId = "AspNetCoreRequireMfaOidc";
        options.ClientSecret = "AspNetCoreRequireMfaOidcSecret";
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
    });

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireMfa", policyIsAdminRequirement =>
        {
            policyIsAdminRequirement.Requirements.Add(new RequireMfa());
        });
    });

    services.AddRazorPages();
}
```

<span data-ttu-id="9dad7-210">然後，此原則會 Razor 視需要在頁面中使用。</span><span class="sxs-lookup"><span data-stu-id="9dad7-210">This policy is then used in the Razor page as required.</span></span> <span data-ttu-id="9dad7-211">您也可以全域為整個應用程式新增原則。</span><span class="sxs-lookup"><span data-stu-id="9dad7-211">The policy could be added globally for the entire app as well.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Extensions.Logging;

namespace AspNetCoreRequireMfaOidc.Pages
{
    [Authorize(Policy= "RequireMfa")]
    public class IndexModel : PageModel
    {
        private readonly ILogger<IndexModel> _logger;

        public IndexModel(ILogger<IndexModel> logger)
        {
            _logger = logger;
        }

        public void OnGet()
        {
        }
    }
}
```

<span data-ttu-id="9dad7-212">如果使用者在沒有 MFA 的情況下進行驗證，則宣告 `amr` 可能會有 `pwd` 值。</span><span class="sxs-lookup"><span data-stu-id="9dad7-212">If the user authenticates without MFA, the `amr` claim will probably have a `pwd` value.</span></span> <span data-ttu-id="9dad7-213">要求不會獲得存取頁面的授權。</span><span class="sxs-lookup"><span data-stu-id="9dad7-213">The request won't be authorized to access the page.</span></span> <span data-ttu-id="9dad7-214">使用預設值時，系統會將使用者重新導向至 *帳戶/AccessDenied* 頁面。</span><span class="sxs-lookup"><span data-stu-id="9dad7-214">Using the default values, the user will be redirected to the *Account/AccessDenied* page.</span></span> <span data-ttu-id="9dad7-215">您可以變更此行為，也可以在這裡執行您自己的自訂邏輯。</span><span class="sxs-lookup"><span data-stu-id="9dad7-215">This behavior can be changed or you can implement your own custom logic here.</span></span> <span data-ttu-id="9dad7-216">在此範例中，會新增連結，讓有效的使用者可以為其帳戶設定 MFA。</span><span class="sxs-lookup"><span data-stu-id="9dad7-216">In this example, a link is added so that the valid user can set up MFA for their account.</span></span>

```cshtml
@page
@model AspNetCoreRequireMfaOidc.AccessDeniedModel
@{
    ViewData["Title"] = "AccessDenied";
    Layout = "~/Pages/Shared/_Layout.cshtml";
}

<h1>AccessDenied</h1>

You require MFA to login here

<a href="https://localhost:44352/Manage/TwoFactorAuthentication">Enable MFA</a>
```

<span data-ttu-id="9dad7-217">現在只有使用 MFA 進行驗證的使用者才能存取頁面或網站。</span><span class="sxs-lookup"><span data-stu-id="9dad7-217">Now only users that authenticate with MFA can access the page or website.</span></span> <span data-ttu-id="9dad7-218">如果使用不同的 MFA 類型或2FA 可以，宣告 `amr` 將會有不同的值，且必須正確處理。</span><span class="sxs-lookup"><span data-stu-id="9dad7-218">If different MFA types are used or if 2FA is okay, the `amr` claim will have different values and needs to be processed correctly.</span></span> <span data-ttu-id="9dad7-219">不同的 OpenID Connect 伺服器也會傳回此宣告的不同值，而且可能不會遵循 [驗證方法參考值](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) 的規格。</span><span class="sxs-lookup"><span data-stu-id="9dad7-219">Different OpenID Connect servers also return different values for this claim and might not follow the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="9dad7-220">不使用 MFA 登入時 (例如，只使用密碼) ：</span><span class="sxs-lookup"><span data-stu-id="9dad7-220">When logging in without MFA (for example, using just a password):</span></span>

* <span data-ttu-id="9dad7-221">`amr`具有 `pwd` 值：</span><span class="sxs-lookup"><span data-stu-id="9dad7-221">The `amr` has the `pwd` value:</span></span>

    ![require_mfa_oidc_02.png](mfa/_static/require_mfa_oidc_02.png)

* <span data-ttu-id="9dad7-223">拒絕存取：</span><span class="sxs-lookup"><span data-stu-id="9dad7-223">Access is denied:</span></span>

    ![require_mfa_oidc_03.png](mfa/_static/require_mfa_oidc_03.png)

<span data-ttu-id="9dad7-225">或者，使用 OTP 登入 Identity ：</span><span class="sxs-lookup"><span data-stu-id="9dad7-225">Alternatively, logging in using OTP with Identity:</span></span>

![require_mfa_oidc_01.png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a><span data-ttu-id="9dad7-227">其他資源</span><span class="sxs-lookup"><span data-stu-id="9dad7-227">Additional resources</span></span>

* [<span data-ttu-id="9dad7-228">在 ASP.NET Core 中啟用 TOTP 驗證器應用程式的 QR 代碼產生</span><span class="sxs-lookup"><span data-stu-id="9dad7-228">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="9dad7-229">Azure Active Directory 的無密碼 authentication 選項</span><span class="sxs-lookup"><span data-stu-id="9dad7-229">Passwordless authentication options for Azure Active Directory</span></span>](/azure/active-directory/authentication/concept-authentication-passwordless)
* [<span data-ttu-id="9dad7-230">使用 .NET FIDO2 適用于 FIDO2/WebAuthn 證明和判斷提示的 .NET 程式庫</span><span class="sxs-lookup"><span data-stu-id="9dad7-230">FIDO2 .NET library for FIDO2 / WebAuthn Attestation and Assertion using .NET</span></span>](https://github.com/abergs/fido2-net-lib)
* [<span data-ttu-id="9dad7-231">很棒</span><span class="sxs-lookup"><span data-stu-id="9dad7-231">WebAuthn Awesome</span></span>](https://github.com/herrjemand/awesome-webauthn)
