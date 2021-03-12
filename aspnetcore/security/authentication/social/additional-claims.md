---
title: 從 ASP.NET Core 中的外部提供者保存其他宣告和權杖
author: rick-anderson
description: 瞭解如何從外部提供者建立其他宣告和權杖。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2021
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
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 513de6133455e26552b79de0f07cf135e8b36825
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605564"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="c556d-103">從 ASP.NET Core 中的外部提供者保存其他宣告和權杖</span><span class="sxs-lookup"><span data-stu-id="c556d-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c556d-104">ASP.NET Core 應用程式可以從外部驗證提供者（例如 Facebook、Google、Microsoft 和 Twitter）建立額外的宣告和權杖。</span><span class="sxs-lookup"><span data-stu-id="c556d-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="c556d-105">每個提供者都會顯示其平臺上使用者的不同資訊，但是接收和轉換使用者資料到其他宣告的模式相同。</span><span class="sxs-lookup"><span data-stu-id="c556d-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="c556d-106">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/social/additional-claims/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="c556d-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c556d-107">必要條件</span><span class="sxs-lookup"><span data-stu-id="c556d-107">Prerequisites</span></span>

<span data-ttu-id="c556d-108">決定要在應用程式中支援的外部驗證提供者。</span><span class="sxs-lookup"><span data-stu-id="c556d-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="c556d-109">針對每個提供者，註冊應用程式並取得用戶端識別碼和用戶端密碼。</span><span class="sxs-lookup"><span data-stu-id="c556d-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="c556d-110">如需詳細資訊，請參閱<xref:security/authentication/social/index>。</span><span class="sxs-lookup"><span data-stu-id="c556d-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="c556d-111">範例應用程式會使用 [Google 驗證提供者](xref:security/authentication/google-logins)。</span><span class="sxs-lookup"><span data-stu-id="c556d-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="c556d-112">設定用戶端識別碼和用戶端密碼</span><span class="sxs-lookup"><span data-stu-id="c556d-112">Set the client ID and client secret</span></span>

<span data-ttu-id="c556d-113">OAuth 驗證提供者會使用用戶端識別碼和用戶端密碼，建立與應用程式之間的信任關係。</span><span class="sxs-lookup"><span data-stu-id="c556d-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="c556d-114">當應用程式向提供者註冊時，外部驗證提供者會為應用程式建立用戶端識別碼和用戶端秘密值。</span><span class="sxs-lookup"><span data-stu-id="c556d-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="c556d-115">應用程式使用的每個外部提供者都必須與提供者的用戶端識別碼和用戶端密碼分開設定。</span><span class="sxs-lookup"><span data-stu-id="c556d-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="c556d-116">如需詳細資訊，請參閱適用于您案例的外部驗證提供者主題：</span><span class="sxs-lookup"><span data-stu-id="c556d-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="c556d-117">Facebook 驗證</span><span class="sxs-lookup"><span data-stu-id="c556d-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="c556d-118">Google 驗證</span><span class="sxs-lookup"><span data-stu-id="c556d-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="c556d-119">Microsoft 驗證</span><span class="sxs-lookup"><span data-stu-id="c556d-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="c556d-120">Twitter 驗證</span><span class="sxs-lookup"><span data-stu-id="c556d-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="c556d-121">其他驗證提供者</span><span class="sxs-lookup"><span data-stu-id="c556d-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="c556d-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="c556d-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="c556d-123">範例應用程式會使用 Google 提供的用戶端識別碼和用戶端密碼來設定 Google 驗證提供者：</span><span class="sxs-lookup"><span data-stu-id="c556d-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="c556d-124">建立驗證範圍</span><span class="sxs-lookup"><span data-stu-id="c556d-124">Establish the authentication scope</span></span>

<span data-ttu-id="c556d-125">指定，以指定要從提供者取出的許可權清單 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> 。</span><span class="sxs-lookup"><span data-stu-id="c556d-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="c556d-126">下表顯示常見外部提供者的驗證範圍。</span><span class="sxs-lookup"><span data-stu-id="c556d-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="c556d-127">提供者</span><span class="sxs-lookup"><span data-stu-id="c556d-127">Provider</span></span>  | <span data-ttu-id="c556d-128">影響範圍</span><span class="sxs-lookup"><span data-stu-id="c556d-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="c556d-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="c556d-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="c556d-130">Google</span><span class="sxs-lookup"><span data-stu-id="c556d-130">Google</span></span>    | <span data-ttu-id="c556d-131">`profile`, `email`, `openid`</span><span class="sxs-lookup"><span data-stu-id="c556d-131">`profile`, `email`, `openid`</span></span>                                     |
| <span data-ttu-id="c556d-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="c556d-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="c556d-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="c556d-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="c556d-134">在範例應用程式中， `profile` 當在 `email` `openid` 上呼叫時，架構會自動加入 Google 的、和範圍 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> 。</span><span class="sxs-lookup"><span data-stu-id="c556d-134">In the sample app, Google's `profile`, `email`, and `openid` scopes are automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="c556d-135">如果應用程式需要其他範圍，請將它們新增至選項。</span><span class="sxs-lookup"><span data-stu-id="c556d-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="c556d-136">在下列範例中， `https://www.googleapis.com/auth/user.birthday.read` 會新增 Google 範圍來取得使用者的生日：</span><span class="sxs-lookup"><span data-stu-id="c556d-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="c556d-137">對應使用者資料索引鍵和建立宣告</span><span class="sxs-lookup"><span data-stu-id="c556d-137">Map user data keys and create claims</span></span>

<span data-ttu-id="c556d-138">在提供者的選項中， <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> 針對外部提供者的 JSON 使用者資料中的每個金鑰/子機碼指定或，以在登入時讀取應用程式身分識別。</span><span class="sxs-lookup"><span data-stu-id="c556d-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="c556d-139">如需宣告類型的詳細資訊，請參閱 <xref:System.Security.Claims.ClaimTypes> 。</span><span class="sxs-lookup"><span data-stu-id="c556d-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="c556d-140">範例應用程式會建立地區設定 (`urn:google:locale`) 和圖片 (`urn:google:picture` 從 `locale` `picture` Google 使用者資料中的和金鑰) 宣告：</span><span class="sxs-lookup"><span data-stu-id="c556d-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="c556d-141">在中 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` ， <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) 會使用來登入應用程式 <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> 。</span><span class="sxs-lookup"><span data-stu-id="c556d-141">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="c556d-142">在登入程式中， <xref:Microsoft.AspNetCore.Identity.UserManager%601> 可以 `ApplicationUser` 為提供的使用者資料儲存宣告 <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> 。</span><span class="sxs-lookup"><span data-stu-id="c556d-142">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="c556d-143">在範例應用程式中， `OnPostConfirmationAsync` (*Account/ExternalLogin* ]) 會 `urn:google:locale` 為已登入 () 和圖片 () 宣告建立地區設定 `urn:google:picture` `ApplicationUser` ，包括下列的宣告 <xref:System.Security.Claims.ClaimTypes.GivenName> ：</span><span class="sxs-lookup"><span data-stu-id="c556d-143">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="c556d-144">依預設，使用者的宣告會儲存在驗證中 cookie 。</span><span class="sxs-lookup"><span data-stu-id="c556d-144">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="c556d-145">如果驗證 cookie 過大，可能會導致應用程式失敗，因為：</span><span class="sxs-lookup"><span data-stu-id="c556d-145">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="c556d-146">瀏覽器偵測到 cookie 標頭太長。</span><span class="sxs-lookup"><span data-stu-id="c556d-146">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="c556d-147">要求的整體大小太大。</span><span class="sxs-lookup"><span data-stu-id="c556d-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="c556d-148">如果處理使用者要求需要大量的使用者資料：</span><span class="sxs-lookup"><span data-stu-id="c556d-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="c556d-149">將要求處理的使用者宣告數目和大小限制為僅限應用程式所需的數目和大小。</span><span class="sxs-lookup"><span data-stu-id="c556d-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="c556d-150">使用自訂 <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> 的 Cookie 驗證中介軟體 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> 來跨要求儲存身分識別。</span><span class="sxs-lookup"><span data-stu-id="c556d-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="c556d-151">在伺服器上保留大量的身分識別資訊，同時只將小型會話識別碼金鑰傳送給用戶端。</span><span class="sxs-lookup"><span data-stu-id="c556d-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="c556d-152">儲存存取權杖</span><span class="sxs-lookup"><span data-stu-id="c556d-152">Save the access token</span></span>

<span data-ttu-id="c556d-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 定義在成功授權之後，是否應將存取和重新整理權杖儲存在中 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> 。</span><span class="sxs-lookup"><span data-stu-id="c556d-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="c556d-154">`SaveTokens` 預設會設定為， `false` 以縮減最終驗證的大小 cookie 。</span><span class="sxs-lookup"><span data-stu-id="c556d-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="c556d-155">範例應用程式會在中將的值設定 `SaveTokens` 為 `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> ：</span><span class="sxs-lookup"><span data-stu-id="c556d-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="c556d-156">當 `OnPostConfirmationAsync` 執行時，請在的外部提供者中儲存存取權杖 ([ExternalLoginInfo AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) `ApplicationUser` 。 `AuthenticationProperties`</span><span class="sxs-lookup"><span data-stu-id="c556d-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="c556d-157">範例應用程式會將存取權杖儲存在 `OnPostConfirmationAsync` (新的使用者註冊) ，並 `OnGetCallbackAsync` (先前在 *Account/ExternalLogin* 中註冊的使用者) ：</span><span class="sxs-lookup"><span data-stu-id="c556d-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

> [!NOTE]
> <span data-ttu-id="c556d-158">如需將權杖傳遞至 Razor 應用程式元件的詳細資訊 Blazor Server ，請參閱 <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app> 。</span><span class="sxs-lookup"><span data-stu-id="c556d-158">For information on passing tokens to the Razor components of a Blazor Server app, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="c556d-159">如何新增其他自訂權杖</span><span class="sxs-lookup"><span data-stu-id="c556d-159">How to add additional custom tokens</span></span>

<span data-ttu-id="c556d-160">為了示範如何新增作為一部分儲存的自訂權杖， `SaveTokens` 範例應用程式會將 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> 包含目前的 <xref:System.DateTime> [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) 加入至 `TicketCreated` ：</span><span class="sxs-lookup"><span data-stu-id="c556d-160">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="c556d-161">建立和新增宣告</span><span class="sxs-lookup"><span data-stu-id="c556d-161">Creating and adding claims</span></span>

<span data-ttu-id="c556d-162">架構會提供用來建立宣告並將其新增至集合的一般動作和擴充方法。</span><span class="sxs-lookup"><span data-stu-id="c556d-162">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="c556d-163">如需詳細資訊，請參閱 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 和 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>。</span><span class="sxs-lookup"><span data-stu-id="c556d-163">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="c556d-164">使用者可以藉由衍生自訂動作 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> 並執行抽象方法，來定義自訂動作 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> 。</span><span class="sxs-lookup"><span data-stu-id="c556d-164">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="c556d-165">如需詳細資訊，請參閱<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>。</span><span class="sxs-lookup"><span data-stu-id="c556d-165">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="add-and-update-user-claims"></a><span data-ttu-id="c556d-166">新增和更新使用者宣告</span><span class="sxs-lookup"><span data-stu-id="c556d-166">Add and update user claims</span></span>

<span data-ttu-id="c556d-167">宣告會在第一次註冊時從外部提供者複製到使用者資料庫，而不是在登入時複製。</span><span class="sxs-lookup"><span data-stu-id="c556d-167">Claims are copied from external providers to the user database on first registration, not on sign in.</span></span> <span data-ttu-id="c556d-168">如果在使用者註冊以使用應用程式之後，在應用程式中啟用其他宣告，請在使用者上呼叫 [使用 RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) ，以強制產生新的驗證 cookie 。</span><span class="sxs-lookup"><span data-stu-id="c556d-168">If additional claims are enabled in an app after a user registers to use the app, call [SignInManager.RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) on a user to force the generation of a new authentication cookie.</span></span>

<span data-ttu-id="c556d-169">在使用測試使用者帳戶的開發環境中，您可以直接刪除並重新建立使用者帳戶。</span><span class="sxs-lookup"><span data-stu-id="c556d-169">In the Development environment working with test user accounts, you can simply delete and recreate the user account.</span></span> <span data-ttu-id="c556d-170">針對生產系統，加入至應用程式的新宣告可回填至使用者帳戶。</span><span class="sxs-lookup"><span data-stu-id="c556d-170">For production systems, new claims added to the app can be backfilled into user accounts.</span></span> <span data-ttu-id="c556d-171">將 [ `ExternalLogin` 頁面的](xref:security/authentication/scaffold-identity) 程式碼放入應用程式 `Areas/Pages/Identity/Account/Manage` 中之後，將下列程式碼新增至檔案 `ExternalLoginModel` 中的 `ExternalLogin.cshtml.cs` 。</span><span class="sxs-lookup"><span data-stu-id="c556d-171">After [scaffolding the `ExternalLogin` page](xref:security/authentication/scaffold-identity) into the app at `Areas/Pages/Identity/Account/Manage`, add the following code to the `ExternalLoginModel` in the `ExternalLogin.cshtml.cs` file.</span></span>

<span data-ttu-id="c556d-172">新增已新增之宣告的字典。</span><span class="sxs-lookup"><span data-stu-id="c556d-172">Add a dictionary of added claims.</span></span> <span data-ttu-id="c556d-173">使用字典索引鍵來保存宣告類型，並使用這些值來保留預設值。</span><span class="sxs-lookup"><span data-stu-id="c556d-173">Use the dictionary keys to hold the claim types, and use the values to hold a default value.</span></span> <span data-ttu-id="c556d-174">將下列這一行加入至類別的頂端。</span><span class="sxs-lookup"><span data-stu-id="c556d-174">Add the following line to the top of the class.</span></span> <span data-ttu-id="c556d-175">下列範例假設為使用者的 Google 圖片新增一個宣告，並將一般大頭照影像作為預設值：</span><span class="sxs-lookup"><span data-stu-id="c556d-175">The following example assumes that one claim is added for the user's Google picture with a generic headshot image as the default value:</span></span>

```csharp
private readonly IReadOnlyDictionary<string, string> _claimsToSync = 
    new Dictionary<string, string>()
    {
        { "urn:google:picture", "https://localhost:5001/headshot.png" },
    };
```

<span data-ttu-id="c556d-176">以下列程式碼取代方法的預設程式碼 `OnGetCallbackAsync` 。</span><span class="sxs-lookup"><span data-stu-id="c556d-176">Replace the default code of the `OnGetCallbackAsync` method with the following code.</span></span> <span data-ttu-id="c556d-177">程式碼會在宣告字典中執行迴圈。</span><span class="sxs-lookup"><span data-stu-id="c556d-177">The code loops through the claims dictionary.</span></span> <span data-ttu-id="c556d-178">系統會為每個使用者新增宣告 (回填) 或更新。</span><span class="sxs-lookup"><span data-stu-id="c556d-178">Claims are added (backfilled) or updated for each user.</span></span> <span data-ttu-id="c556d-179">新增或更新宣告時，會使用來重新整理使用者登入，並 <xref:Microsoft.AspNetCore.Identity.SignInManager%601> 保留現有的驗證屬性 (`AuthenticationProperties`) 。</span><span class="sxs-lookup"><span data-stu-id="c556d-179">When claims are added or updated, the user sign-in is refreshed using the <xref:Microsoft.AspNetCore.Identity.SignInManager%601>, preserving the existing authentication properties (`AuthenticationProperties`).</span></span>

```csharp
public async Task<IActionResult> OnGetCallbackAsync(
    string returnUrl = null, string remoteError = null)
{
    returnUrl = returnUrl ?? Url.Content("~/");

    if (remoteError != null)
    {
        ErrorMessage = $"Error from external provider: {remoteError}";

        return RedirectToPage("./Login", new {ReturnUrl = returnUrl });
    }

    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        ErrorMessage = "Error loading external login information.";
        return RedirectToPage("./Login", new { ReturnUrl = returnUrl });
    }

    // Sign in the user with this external login provider if the user already has a 
    // login.
    var result = await _signInManager.ExternalLoginSignInAsync(info.LoginProvider, 
        info.ProviderKey, isPersistent: false, bypassTwoFactor : true);

    if (result.Succeeded)
    {
        _logger.LogInformation("{Name} logged in with {LoginProvider} provider.", 
            info.Principal.Identity.Name, info.LoginProvider);

        if (_claimsToSync.Count > 0)
        {
            var user = await _userManager.FindByLoginAsync(info.LoginProvider, 
                info.ProviderKey);
            var userClaims = await _userManager.GetClaimsAsync(user);
            bool refreshSignIn = false;

            foreach (var addedClaim in _claimsToSync)
            {
                var userClaim = userClaims
                    .FirstOrDefault(c => c.Type == addedClaim.Key);

                if (info.Principal.HasClaim(c => c.Type == addedClaim.Key))
                {
                    var externalClaim = info.Principal.FindFirst(addedClaim.Key);

                    if (userClaim == null)
                    {
                        await _userManager.AddClaimAsync(user, 
                            new Claim(addedClaim.Key, externalClaim.Value));
                        refreshSignIn = true;
                    }
                    else if (userClaim.Value != externalClaim.Value)
                    {
                        await _userManager
                            .ReplaceClaimAsync(user, userClaim, externalClaim);
                        refreshSignIn = true;
                    }
                }
                else if (userClaim == null)
                {
                    // Fill with a default value
                    await _userManager.AddClaimAsync(user, new Claim(addedClaim.Key, 
                        addedClaim.Value));
                    refreshSignIn = true;
                }
            }

            if (refreshSignIn)
            {
                await _signInManager.RefreshSignInAsync(user);
            }
        }

        return LocalRedirect(returnUrl);
    }

    if (result.IsLockedOut)
    {
        return RedirectToPage("./Lockout");
    }
    else
    {
        // If the user does not have an account, then ask the user to create an 
        // account.
        ReturnUrl = returnUrl;
        ProviderDisplayName = info.ProviderDisplayName;

        if (info.Principal.HasClaim(c => c.Type == ClaimTypes.Email))
        {
            Input = new InputModel
            {
                Email = info.Principal.FindFirstValue(ClaimTypes.Email)
            };
        }

        return Page();
    }
}
```

<span data-ttu-id="c556d-180">當使用者登入時宣告變更，但不需要回填步驟時，會採用類似的方法。</span><span class="sxs-lookup"><span data-stu-id="c556d-180">A similar approach is taken when claims change while a user is signed in but a backfill step isn't required.</span></span> <span data-ttu-id="c556d-181">若要更新使用者的宣告，請在使用者上呼叫下列內容：</span><span class="sxs-lookup"><span data-stu-id="c556d-181">To update a user's claims, call the following on the user:</span></span>

* <span data-ttu-id="c556d-182">[UserManager：](xref:Microsoft.AspNetCore.Identity.UserManager%601) 在使用者上 ReplaceClaimAsync 儲存在身分識別資料庫中的宣告。</span><span class="sxs-lookup"><span data-stu-id="c556d-182">[UserManager.ReplaceClaimAsync](xref:Microsoft.AspNetCore.Identity.UserManager%601) on the user for claims stored in the identity database.</span></span>
* <span data-ttu-id="c556d-183">[使用](xref:Microsoft.AspNetCore.Identity.SignInManager%601) 在使用者上 RefreshSignInAsync，以強制產生新的驗證 cookie 。</span><span class="sxs-lookup"><span data-stu-id="c556d-183">[SignInManager.RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) on the user to force the generation of a new authentication cookie.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="c556d-184">移除宣告動作和宣告</span><span class="sxs-lookup"><span data-stu-id="c556d-184">Removal of claim actions and claims</span></span>

<span data-ttu-id="c556d-185">[ClaimActionCollection 移除 (字串) ](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) 移除集合中指定的所有宣告動作 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 。</span><span class="sxs-lookup"><span data-stu-id="c556d-185">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="c556d-186">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection，String) ](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) 會從身分識別中刪除指定的宣告 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 。</span><span class="sxs-lookup"><span data-stu-id="c556d-186">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="c556d-187"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> 主要與 [OpenID connect 搭配使用 (OIDC) ](/azure/active-directory/develop/v2-protocols-oidc) 移除通訊協定產生的宣告。</span><span class="sxs-lookup"><span data-stu-id="c556d-187"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="c556d-188">範例應用程式輸出</span><span class="sxs-lookup"><span data-stu-id="c556d-188">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c556d-189">ASP.NET Core 應用程式可以從外部驗證提供者（例如 Facebook、Google、Microsoft 和 Twitter）建立額外的宣告和權杖。</span><span class="sxs-lookup"><span data-stu-id="c556d-189">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="c556d-190">每個提供者都會顯示其平臺上使用者的不同資訊，但是接收和轉換使用者資料到其他宣告的模式相同。</span><span class="sxs-lookup"><span data-stu-id="c556d-190">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="c556d-191">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/social/additional-claims/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="c556d-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c556d-192">必要條件</span><span class="sxs-lookup"><span data-stu-id="c556d-192">Prerequisites</span></span>

<span data-ttu-id="c556d-193">決定要在應用程式中支援的外部驗證提供者。</span><span class="sxs-lookup"><span data-stu-id="c556d-193">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="c556d-194">針對每個提供者，註冊應用程式並取得用戶端識別碼和用戶端密碼。</span><span class="sxs-lookup"><span data-stu-id="c556d-194">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="c556d-195">如需詳細資訊，請參閱<xref:security/authentication/social/index>。</span><span class="sxs-lookup"><span data-stu-id="c556d-195">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="c556d-196">範例應用程式會使用 [Google 驗證提供者](xref:security/authentication/google-logins)。</span><span class="sxs-lookup"><span data-stu-id="c556d-196">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="c556d-197">設定用戶端識別碼和用戶端密碼</span><span class="sxs-lookup"><span data-stu-id="c556d-197">Set the client ID and client secret</span></span>

<span data-ttu-id="c556d-198">OAuth 驗證提供者會使用用戶端識別碼和用戶端密碼，建立與應用程式之間的信任關係。</span><span class="sxs-lookup"><span data-stu-id="c556d-198">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="c556d-199">當應用程式向提供者註冊時，外部驗證提供者會為應用程式建立用戶端識別碼和用戶端秘密值。</span><span class="sxs-lookup"><span data-stu-id="c556d-199">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="c556d-200">應用程式使用的每個外部提供者都必須與提供者的用戶端識別碼和用戶端密碼分開設定。</span><span class="sxs-lookup"><span data-stu-id="c556d-200">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="c556d-201">如需詳細資訊，請參閱適用于您案例的外部驗證提供者主題：</span><span class="sxs-lookup"><span data-stu-id="c556d-201">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="c556d-202">Facebook 驗證</span><span class="sxs-lookup"><span data-stu-id="c556d-202">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="c556d-203">Google 驗證</span><span class="sxs-lookup"><span data-stu-id="c556d-203">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="c556d-204">Microsoft 驗證</span><span class="sxs-lookup"><span data-stu-id="c556d-204">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="c556d-205">Twitter 驗證</span><span class="sxs-lookup"><span data-stu-id="c556d-205">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="c556d-206">其他驗證提供者</span><span class="sxs-lookup"><span data-stu-id="c556d-206">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="c556d-207">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="c556d-207">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="c556d-208">範例應用程式會使用 Google 提供的用戶端識別碼和用戶端密碼來設定 Google 驗證提供者：</span><span class="sxs-lookup"><span data-stu-id="c556d-208">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="c556d-209">建立驗證範圍</span><span class="sxs-lookup"><span data-stu-id="c556d-209">Establish the authentication scope</span></span>

<span data-ttu-id="c556d-210">指定，以指定要從提供者取出的許可權清單 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> 。</span><span class="sxs-lookup"><span data-stu-id="c556d-210">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="c556d-211">下表顯示常見外部提供者的驗證範圍。</span><span class="sxs-lookup"><span data-stu-id="c556d-211">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="c556d-212">提供者</span><span class="sxs-lookup"><span data-stu-id="c556d-212">Provider</span></span>  | <span data-ttu-id="c556d-213">影響範圍</span><span class="sxs-lookup"><span data-stu-id="c556d-213">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="c556d-214">Facebook</span><span class="sxs-lookup"><span data-stu-id="c556d-214">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="c556d-215">Google</span><span class="sxs-lookup"><span data-stu-id="c556d-215">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="c556d-216">Microsoft</span><span class="sxs-lookup"><span data-stu-id="c556d-216">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="c556d-217">Twitter</span><span class="sxs-lookup"><span data-stu-id="c556d-217">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="c556d-218">在範例應用程式中， `userinfo.profile` 當在上呼叫時，架構會自動加入 Google 的範圍 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> 。</span><span class="sxs-lookup"><span data-stu-id="c556d-218">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="c556d-219">如果應用程式需要其他範圍，請將它們新增至選項。</span><span class="sxs-lookup"><span data-stu-id="c556d-219">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="c556d-220">下列範例 `https://www.googleapis.com/auth/user.birthday.read` 會新增 Google 範圍，以取得使用者的生日：</span><span class="sxs-lookup"><span data-stu-id="c556d-220">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="c556d-221">對應使用者資料索引鍵和建立宣告</span><span class="sxs-lookup"><span data-stu-id="c556d-221">Map user data keys and create claims</span></span>

<span data-ttu-id="c556d-222">在提供者的選項中， <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> 針對外部提供者的 JSON 使用者資料中的每個金鑰/子機碼指定或，以在登入時讀取應用程式身分識別。</span><span class="sxs-lookup"><span data-stu-id="c556d-222">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="c556d-223">如需宣告類型的詳細資訊，請參閱 <xref:System.Security.Claims.ClaimTypes> 。</span><span class="sxs-lookup"><span data-stu-id="c556d-223">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="c556d-224">範例應用程式會建立地區設定 (`urn:google:locale`) 和圖片 (`urn:google:picture` 從 `locale` `picture` Google 使用者資料中的和金鑰) 宣告：</span><span class="sxs-lookup"><span data-stu-id="c556d-224">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="c556d-225">在中 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` ， <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) 會使用來登入應用程式 <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> 。</span><span class="sxs-lookup"><span data-stu-id="c556d-225">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="c556d-226">在登入程式中， <xref:Microsoft.AspNetCore.Identity.UserManager%601> 可以 `ApplicationUser` 為提供的使用者資料儲存宣告 <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> 。</span><span class="sxs-lookup"><span data-stu-id="c556d-226">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="c556d-227">在範例應用程式中， `OnPostConfirmationAsync` (*Account/ExternalLogin* ]) 會 `urn:google:locale` 為已登入 () 和圖片 () 宣告建立地區設定 `urn:google:picture` `ApplicationUser` ，包括下列的宣告 <xref:System.Security.Claims.ClaimTypes.GivenName> ：</span><span class="sxs-lookup"><span data-stu-id="c556d-227">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="c556d-228">依預設，使用者的宣告會儲存在驗證中 cookie 。</span><span class="sxs-lookup"><span data-stu-id="c556d-228">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="c556d-229">如果驗證 cookie 過大，可能會導致應用程式失敗，因為：</span><span class="sxs-lookup"><span data-stu-id="c556d-229">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="c556d-230">瀏覽器偵測到 cookie 標頭太長。</span><span class="sxs-lookup"><span data-stu-id="c556d-230">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="c556d-231">要求的整體大小太大。</span><span class="sxs-lookup"><span data-stu-id="c556d-231">The overall size of the request is too large.</span></span>

<span data-ttu-id="c556d-232">如果處理使用者要求需要大量的使用者資料：</span><span class="sxs-lookup"><span data-stu-id="c556d-232">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="c556d-233">將要求處理的使用者宣告數目和大小限制為僅限應用程式所需的數目和大小。</span><span class="sxs-lookup"><span data-stu-id="c556d-233">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="c556d-234">使用自訂 <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> 的 Cookie 驗證中介軟體 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> 來跨要求儲存身分識別。</span><span class="sxs-lookup"><span data-stu-id="c556d-234">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="c556d-235">在伺服器上保留大量的身分識別資訊，同時只將小型會話識別碼金鑰傳送給用戶端。</span><span class="sxs-lookup"><span data-stu-id="c556d-235">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="c556d-236">儲存存取權杖</span><span class="sxs-lookup"><span data-stu-id="c556d-236">Save the access token</span></span>

<span data-ttu-id="c556d-237"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 定義在成功授權之後，是否應將存取和重新整理權杖儲存在中 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> 。</span><span class="sxs-lookup"><span data-stu-id="c556d-237"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="c556d-238">`SaveTokens` 預設會設定為， `false` 以縮減最終驗證的大小 cookie 。</span><span class="sxs-lookup"><span data-stu-id="c556d-238">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="c556d-239">範例應用程式會在中將的值設定 `SaveTokens` 為 `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> ：</span><span class="sxs-lookup"><span data-stu-id="c556d-239">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="c556d-240">當 `OnPostConfirmationAsync` 執行時，請在的外部提供者中儲存存取權杖 ([ExternalLoginInfo AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) `ApplicationUser` 。 `AuthenticationProperties`</span><span class="sxs-lookup"><span data-stu-id="c556d-240">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="c556d-241">範例應用程式會將存取權杖儲存在 `OnPostConfirmationAsync` (新的使用者註冊) ，並 `OnGetCallbackAsync` (先前在 *Account/ExternalLogin* 中註冊的使用者) ：</span><span class="sxs-lookup"><span data-stu-id="c556d-241">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="c556d-242">如何新增其他自訂權杖</span><span class="sxs-lookup"><span data-stu-id="c556d-242">How to add additional custom tokens</span></span>

<span data-ttu-id="c556d-243">為了示範如何新增作為一部分儲存的自訂權杖， `SaveTokens` 範例應用程式會將 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> 包含目前的 <xref:System.DateTime> [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) 加入至 `TicketCreated` ：</span><span class="sxs-lookup"><span data-stu-id="c556d-243">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="c556d-244">建立和新增宣告</span><span class="sxs-lookup"><span data-stu-id="c556d-244">Creating and adding claims</span></span>

<span data-ttu-id="c556d-245">架構會提供用來建立宣告並將其新增至集合的一般動作和擴充方法。</span><span class="sxs-lookup"><span data-stu-id="c556d-245">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="c556d-246">如需詳細資訊，請參閱 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 和 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>。</span><span class="sxs-lookup"><span data-stu-id="c556d-246">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="c556d-247">使用者可以藉由衍生自訂動作 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> 並執行抽象方法，來定義自訂動作 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> 。</span><span class="sxs-lookup"><span data-stu-id="c556d-247">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="c556d-248">如需詳細資訊，請參閱<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>。</span><span class="sxs-lookup"><span data-stu-id="c556d-248">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="c556d-249">移除宣告動作和宣告</span><span class="sxs-lookup"><span data-stu-id="c556d-249">Removal of claim actions and claims</span></span>

<span data-ttu-id="c556d-250">[ClaimActionCollection 移除 (字串) ](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) 移除集合中指定的所有宣告動作 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 。</span><span class="sxs-lookup"><span data-stu-id="c556d-250">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="c556d-251">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection，String) ](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) 會從身分識別中刪除指定的宣告 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 。</span><span class="sxs-lookup"><span data-stu-id="c556d-251">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="c556d-252"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> 主要與 [OpenID connect 搭配使用 (OIDC) ](/azure/active-directory/develop/v2-protocols-oidc) 移除通訊協定產生的宣告。</span><span class="sxs-lookup"><span data-stu-id="c556d-252"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="c556d-253">範例應用程式輸出</span><span class="sxs-lookup"><span data-stu-id="c556d-253">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c556d-254">其他資源</span><span class="sxs-lookup"><span data-stu-id="c556d-254">Additional resources</span></span>

* <span data-ttu-id="c556d-255">[dotnet/AspNetCore 工程 SocialSample 應用程式](https://github.com/dotnet/AspNetCore/tree/main/src/Security/Authentication/samples/SocialSample)：連結的範例應用程式位於 [Dotnet/AspNetCore GitHub 存放庫的](https://github.com/dotnet/AspNetCore) `main` 工程分支上。</span><span class="sxs-lookup"><span data-stu-id="c556d-255">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/main/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `main` engineering branch.</span></span> <span data-ttu-id="c556d-256">`main`分支包含下一版 ASP.NET Core 的主動式開發程式碼。</span><span class="sxs-lookup"><span data-stu-id="c556d-256">The `main` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="c556d-257">若要查看 ASP.NET Core 發行版本的範例應用程式版本，請使用 [ **分支** ] 下拉式清單來選取發行分支 (例如 `release/{X.Y}`) 。</span><span class="sxs-lookup"><span data-stu-id="c556d-257">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
