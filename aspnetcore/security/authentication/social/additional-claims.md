---
title: 在 ASP.NET Core 中保存外部提供者的其他宣告和權杖
author: rick-anderson
description: 瞭解如何從外部提供者建立其他宣告和權杖。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 4503291ff887f79b1ad6eacd4e56943ce23335bc
ms.sourcegitcommit: 5156eab2118584405eb663e1fcd82f8bd7764504
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2020
ms.locfileid: "93141504"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="78a9a-103">在 ASP.NET Core 中保存外部提供者的其他宣告和權杖</span><span class="sxs-lookup"><span data-stu-id="78a9a-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="78a9a-104">ASP.NET Core 的應用程式可以從外部驗證提供者（例如 Facebook、Google、Microsoft 和 Twitter）建立額外的宣告和權杖。</span><span class="sxs-lookup"><span data-stu-id="78a9a-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="78a9a-105">每個提供者都會顯示其平臺上使用者的不同資訊，但是接收和轉換使用者資料到其他宣告的模式相同。</span><span class="sxs-lookup"><span data-stu-id="78a9a-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="78a9a-106">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="78a9a-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="78a9a-107">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="78a9a-107">Prerequisites</span></span>

<span data-ttu-id="78a9a-108">決定要在應用程式中支援的外部驗證提供者。</span><span class="sxs-lookup"><span data-stu-id="78a9a-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="78a9a-109">針對每個提供者，註冊應用程式並取得用戶端識別碼和用戶端密碼。</span><span class="sxs-lookup"><span data-stu-id="78a9a-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="78a9a-110">如需詳細資訊，請參閱<xref:security/authentication/social/index>。</span><span class="sxs-lookup"><span data-stu-id="78a9a-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="78a9a-111">範例應用程式會使用 [Google 驗證提供者](xref:security/authentication/google-logins)。</span><span class="sxs-lookup"><span data-stu-id="78a9a-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="78a9a-112">設定用戶端識別碼和用戶端密碼</span><span class="sxs-lookup"><span data-stu-id="78a9a-112">Set the client ID and client secret</span></span>

<span data-ttu-id="78a9a-113">OAuth 驗證提供者會使用用戶端識別碼和用戶端密碼，建立與應用程式之間的信任關係。</span><span class="sxs-lookup"><span data-stu-id="78a9a-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="78a9a-114">當應用程式向提供者註冊時，外部驗證提供者會為應用程式建立用戶端識別碼和用戶端秘密值。</span><span class="sxs-lookup"><span data-stu-id="78a9a-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="78a9a-115">應用程式使用的每個外部提供者都必須與提供者的用戶端識別碼和用戶端密碼分開設定。</span><span class="sxs-lookup"><span data-stu-id="78a9a-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="78a9a-116">如需詳細資訊，請參閱適用于您案例的外部驗證提供者主題：</span><span class="sxs-lookup"><span data-stu-id="78a9a-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="78a9a-117">Facebook 驗證</span><span class="sxs-lookup"><span data-stu-id="78a9a-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="78a9a-118">Google 驗證</span><span class="sxs-lookup"><span data-stu-id="78a9a-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="78a9a-119">Microsoft 驗證</span><span class="sxs-lookup"><span data-stu-id="78a9a-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="78a9a-120">Twitter 驗證</span><span class="sxs-lookup"><span data-stu-id="78a9a-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="78a9a-121">其他驗證提供者</span><span class="sxs-lookup"><span data-stu-id="78a9a-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="78a9a-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="78a9a-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="78a9a-123">範例應用程式會使用 Google 提供的用戶端識別碼和用戶端密碼來設定 Google 驗證提供者：</span><span class="sxs-lookup"><span data-stu-id="78a9a-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="78a9a-124">建立驗證範圍</span><span class="sxs-lookup"><span data-stu-id="78a9a-124">Establish the authentication scope</span></span>

<span data-ttu-id="78a9a-125">指定，以指定要從提供者取出的許可權清單 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="78a9a-126">下表顯示常見外部提供者的驗證範圍。</span><span class="sxs-lookup"><span data-stu-id="78a9a-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="78a9a-127">提供者</span><span class="sxs-lookup"><span data-stu-id="78a9a-127">Provider</span></span>  | <span data-ttu-id="78a9a-128">影響範圍</span><span class="sxs-lookup"><span data-stu-id="78a9a-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="78a9a-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="78a9a-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="78a9a-130">Google</span><span class="sxs-lookup"><span data-stu-id="78a9a-130">Google</span></span>    | <span data-ttu-id="78a9a-131">`profile`, `email`, `openid`</span><span class="sxs-lookup"><span data-stu-id="78a9a-131">`profile`, `email`, `openid`</span></span>                                     |
| <span data-ttu-id="78a9a-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="78a9a-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="78a9a-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="78a9a-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="78a9a-134">在範例應用程式中， `profile` 當在 `email` `openid` 上呼叫時，架構會自動加入 Google 的、和範圍 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-134">In the sample app, Google's `profile`, `email`, and `openid` scopes are automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="78a9a-135">如果應用程式需要其他範圍，請將它們新增至選項。</span><span class="sxs-lookup"><span data-stu-id="78a9a-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="78a9a-136">在下列範例中， `https://www.googleapis.com/auth/user.birthday.read` 會新增 Google 範圍來取得使用者的生日：</span><span class="sxs-lookup"><span data-stu-id="78a9a-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="78a9a-137">對應使用者資料索引鍵和建立宣告</span><span class="sxs-lookup"><span data-stu-id="78a9a-137">Map user data keys and create claims</span></span>

<span data-ttu-id="78a9a-138">在提供者的選項中， <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> 針對外部提供者的 JSON 使用者資料中的每個金鑰/子機碼指定或，以在登入時讀取應用程式身分識別。</span><span class="sxs-lookup"><span data-stu-id="78a9a-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="78a9a-139">如需宣告類型的詳細資訊，請參閱 <xref:System.Security.Claims.ClaimTypes> 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="78a9a-140">範例應用程式會建立地區設定 (`urn:google:locale`) 和圖片 (`urn:google:picture` 從 `locale` `picture` Google 使用者資料中的和金鑰) 宣告：</span><span class="sxs-lookup"><span data-stu-id="78a9a-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="78a9a-141">在中 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` ， <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) 會使用來登入應用程式 <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-141">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="78a9a-142">在登入程式中， <xref:Microsoft.AspNetCore.Identity.UserManager%601> 可以 `ApplicationUser` 為提供的使用者資料儲存宣告 <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-142">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="78a9a-143">在範例應用程式中， `OnPostConfirmationAsync` ( *Account/ExternalLogin* ]) 會 `urn:google:locale` 為已登入 () 和圖片 () 宣告建立地區設定 `urn:google:picture` `ApplicationUser` ，包括下列的宣告 <xref:System.Security.Claims.ClaimTypes.GivenName> ：</span><span class="sxs-lookup"><span data-stu-id="78a9a-143">In the sample app, `OnPostConfirmationAsync` ( *Account/ExternalLogin.cshtml.cs* ) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="78a9a-144">依預設，使用者的宣告會儲存在驗證中 cookie 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-144">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="78a9a-145">如果驗證 cookie 過大，可能會導致應用程式失敗，因為：</span><span class="sxs-lookup"><span data-stu-id="78a9a-145">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="78a9a-146">瀏覽器偵測到 cookie 標頭太長。</span><span class="sxs-lookup"><span data-stu-id="78a9a-146">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="78a9a-147">要求的整體大小太大。</span><span class="sxs-lookup"><span data-stu-id="78a9a-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="78a9a-148">如果處理使用者要求需要大量的使用者資料：</span><span class="sxs-lookup"><span data-stu-id="78a9a-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="78a9a-149">將要求處理的使用者宣告數目和大小限制為僅限應用程式所需的數目和大小。</span><span class="sxs-lookup"><span data-stu-id="78a9a-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="78a9a-150">使用自訂 <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> 的 Cookie 驗證中介軟體 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> 來跨要求儲存身分識別。</span><span class="sxs-lookup"><span data-stu-id="78a9a-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="78a9a-151">在伺服器上保留大量的身分識別資訊，同時只將小型會話識別碼金鑰傳送給用戶端。</span><span class="sxs-lookup"><span data-stu-id="78a9a-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="78a9a-152">儲存存取權杖</span><span class="sxs-lookup"><span data-stu-id="78a9a-152">Save the access token</span></span>

<span data-ttu-id="78a9a-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 定義在成功授權之後，是否應將存取和重新整理權杖儲存在中 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="78a9a-154">`SaveTokens` 預設會設定為， `false` 以縮減最終驗證的大小 cookie 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="78a9a-155">範例應用程式會在中將的值設定 `SaveTokens` 為 `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> ：</span><span class="sxs-lookup"><span data-stu-id="78a9a-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="78a9a-156">當 `OnPostConfirmationAsync` 執行時，請在的外部提供者中儲存存取權杖 ([ExternalLoginInfo AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) `ApplicationUser` 。 `AuthenticationProperties`</span><span class="sxs-lookup"><span data-stu-id="78a9a-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="78a9a-157">範例應用程式會將存取權杖儲存在 `OnPostConfirmationAsync` (新的使用者註冊) ，並 `OnGetCallbackAsync` (先前在 *Account/ExternalLogin* 中註冊的使用者) ：</span><span class="sxs-lookup"><span data-stu-id="78a9a-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs* :</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="78a9a-158">如何新增其他自訂權杖</span><span class="sxs-lookup"><span data-stu-id="78a9a-158">How to add additional custom tokens</span></span>

<span data-ttu-id="78a9a-159">為了示範如何新增作為一部分儲存的自訂權杖， `SaveTokens` 範例應用程式會將 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> 包含目前的 <xref:System.DateTime> [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) 加入至 `TicketCreated` ：</span><span class="sxs-lookup"><span data-stu-id="78a9a-159">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="78a9a-160">建立和新增宣告</span><span class="sxs-lookup"><span data-stu-id="78a9a-160">Creating and adding claims</span></span>

<span data-ttu-id="78a9a-161">架構會提供用來建立宣告並將其新增至集合的一般動作和擴充方法。</span><span class="sxs-lookup"><span data-stu-id="78a9a-161">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="78a9a-162">如需詳細資訊，請參閱 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 和 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>。</span><span class="sxs-lookup"><span data-stu-id="78a9a-162">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="78a9a-163">使用者可以藉由衍生自訂動作 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> 並執行抽象方法，來定義自訂動作 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-163">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="78a9a-164">如需詳細資訊，請參閱<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>。</span><span class="sxs-lookup"><span data-stu-id="78a9a-164">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="78a9a-165">移除宣告動作和宣告</span><span class="sxs-lookup"><span data-stu-id="78a9a-165">Removal of claim actions and claims</span></span>

<span data-ttu-id="78a9a-166">[ClaimActionCollection 移除 (字串) ](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) 移除集合中指定的所有宣告動作 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-166">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="78a9a-167">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection，String) ](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) 會從身分識別中刪除指定的宣告 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-167">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="78a9a-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> 主要用於搭配 [OpenID Connect (OIDC) ](/azure/active-directory/develop/v2-protocols-oidc) 來移除通訊協定產生的宣告。</span><span class="sxs-lookup"><span data-stu-id="78a9a-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="78a9a-169">範例應用程式輸出</span><span class="sxs-lookup"><span data-stu-id="78a9a-169">Sample app output</span></span>

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

<span data-ttu-id="78a9a-170">ASP.NET Core 的應用程式可以從外部驗證提供者（例如 Facebook、Google、Microsoft 和 Twitter）建立額外的宣告和權杖。</span><span class="sxs-lookup"><span data-stu-id="78a9a-170">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="78a9a-171">每個提供者都會顯示其平臺上使用者的不同資訊，但是接收和轉換使用者資料到其他宣告的模式相同。</span><span class="sxs-lookup"><span data-stu-id="78a9a-171">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="78a9a-172">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="78a9a-172">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="78a9a-173">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="78a9a-173">Prerequisites</span></span>

<span data-ttu-id="78a9a-174">決定要在應用程式中支援的外部驗證提供者。</span><span class="sxs-lookup"><span data-stu-id="78a9a-174">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="78a9a-175">針對每個提供者，註冊應用程式並取得用戶端識別碼和用戶端密碼。</span><span class="sxs-lookup"><span data-stu-id="78a9a-175">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="78a9a-176">如需詳細資訊，請參閱<xref:security/authentication/social/index>。</span><span class="sxs-lookup"><span data-stu-id="78a9a-176">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="78a9a-177">範例應用程式會使用 [Google 驗證提供者](xref:security/authentication/google-logins)。</span><span class="sxs-lookup"><span data-stu-id="78a9a-177">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="78a9a-178">設定用戶端識別碼和用戶端密碼</span><span class="sxs-lookup"><span data-stu-id="78a9a-178">Set the client ID and client secret</span></span>

<span data-ttu-id="78a9a-179">OAuth 驗證提供者會使用用戶端識別碼和用戶端密碼，建立與應用程式之間的信任關係。</span><span class="sxs-lookup"><span data-stu-id="78a9a-179">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="78a9a-180">當應用程式向提供者註冊時，外部驗證提供者會為應用程式建立用戶端識別碼和用戶端秘密值。</span><span class="sxs-lookup"><span data-stu-id="78a9a-180">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="78a9a-181">應用程式使用的每個外部提供者都必須與提供者的用戶端識別碼和用戶端密碼分開設定。</span><span class="sxs-lookup"><span data-stu-id="78a9a-181">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="78a9a-182">如需詳細資訊，請參閱適用于您案例的外部驗證提供者主題：</span><span class="sxs-lookup"><span data-stu-id="78a9a-182">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="78a9a-183">Facebook 驗證</span><span class="sxs-lookup"><span data-stu-id="78a9a-183">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="78a9a-184">Google 驗證</span><span class="sxs-lookup"><span data-stu-id="78a9a-184">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="78a9a-185">Microsoft 驗證</span><span class="sxs-lookup"><span data-stu-id="78a9a-185">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="78a9a-186">Twitter 驗證</span><span class="sxs-lookup"><span data-stu-id="78a9a-186">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="78a9a-187">其他驗證提供者</span><span class="sxs-lookup"><span data-stu-id="78a9a-187">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="78a9a-188">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="78a9a-188">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="78a9a-189">範例應用程式會使用 Google 提供的用戶端識別碼和用戶端密碼來設定 Google 驗證提供者：</span><span class="sxs-lookup"><span data-stu-id="78a9a-189">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="78a9a-190">建立驗證範圍</span><span class="sxs-lookup"><span data-stu-id="78a9a-190">Establish the authentication scope</span></span>

<span data-ttu-id="78a9a-191">指定，以指定要從提供者取出的許可權清單 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-191">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="78a9a-192">下表顯示常見外部提供者的驗證範圍。</span><span class="sxs-lookup"><span data-stu-id="78a9a-192">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="78a9a-193">提供者</span><span class="sxs-lookup"><span data-stu-id="78a9a-193">Provider</span></span>  | <span data-ttu-id="78a9a-194">影響範圍</span><span class="sxs-lookup"><span data-stu-id="78a9a-194">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="78a9a-195">Facebook</span><span class="sxs-lookup"><span data-stu-id="78a9a-195">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="78a9a-196">Google</span><span class="sxs-lookup"><span data-stu-id="78a9a-196">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="78a9a-197">Microsoft</span><span class="sxs-lookup"><span data-stu-id="78a9a-197">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="78a9a-198">Twitter</span><span class="sxs-lookup"><span data-stu-id="78a9a-198">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="78a9a-199">在範例應用程式中， `userinfo.profile` 當在上呼叫時，架構會自動加入 Google 的範圍 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-199">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="78a9a-200">如果應用程式需要其他範圍，請將它們新增至選項。</span><span class="sxs-lookup"><span data-stu-id="78a9a-200">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="78a9a-201">下列範例 `https://www.googleapis.com/auth/user.birthday.read` 會新增 Google 範圍，以取得使用者的生日：</span><span class="sxs-lookup"><span data-stu-id="78a9a-201">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="78a9a-202">對應使用者資料索引鍵和建立宣告</span><span class="sxs-lookup"><span data-stu-id="78a9a-202">Map user data keys and create claims</span></span>

<span data-ttu-id="78a9a-203">在提供者的選項中， <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> 針對外部提供者的 JSON 使用者資料中的每個金鑰/子機碼指定或，以在登入時讀取應用程式身分識別。</span><span class="sxs-lookup"><span data-stu-id="78a9a-203">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="78a9a-204">如需宣告類型的詳細資訊，請參閱 <xref:System.Security.Claims.ClaimTypes> 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-204">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="78a9a-205">範例應用程式會建立地區設定 (`urn:google:locale`) 和圖片 (`urn:google:picture` 從 `locale` `picture` Google 使用者資料中的和金鑰) 宣告：</span><span class="sxs-lookup"><span data-stu-id="78a9a-205">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="78a9a-206">在中 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` ， <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) 會使用來登入應用程式 <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-206">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="78a9a-207">在登入程式中， <xref:Microsoft.AspNetCore.Identity.UserManager%601> 可以 `ApplicationUser` 為提供的使用者資料儲存宣告 <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-207">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="78a9a-208">在範例應用程式中， `OnPostConfirmationAsync` ( *Account/ExternalLogin* ]) 會 `urn:google:locale` 為已登入 () 和圖片 () 宣告建立地區設定 `urn:google:picture` `ApplicationUser` ，包括下列的宣告 <xref:System.Security.Claims.ClaimTypes.GivenName> ：</span><span class="sxs-lookup"><span data-stu-id="78a9a-208">In the sample app, `OnPostConfirmationAsync` ( *Account/ExternalLogin.cshtml.cs* ) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="78a9a-209">依預設，使用者的宣告會儲存在驗證中 cookie 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-209">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="78a9a-210">如果驗證 cookie 過大，可能會導致應用程式失敗，因為：</span><span class="sxs-lookup"><span data-stu-id="78a9a-210">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="78a9a-211">瀏覽器偵測到 cookie 標頭太長。</span><span class="sxs-lookup"><span data-stu-id="78a9a-211">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="78a9a-212">要求的整體大小太大。</span><span class="sxs-lookup"><span data-stu-id="78a9a-212">The overall size of the request is too large.</span></span>

<span data-ttu-id="78a9a-213">如果處理使用者要求需要大量的使用者資料：</span><span class="sxs-lookup"><span data-stu-id="78a9a-213">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="78a9a-214">將要求處理的使用者宣告數目和大小限制為僅限應用程式所需的數目和大小。</span><span class="sxs-lookup"><span data-stu-id="78a9a-214">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="78a9a-215">使用自訂 <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> 的 Cookie 驗證中介軟體 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> 來跨要求儲存身分識別。</span><span class="sxs-lookup"><span data-stu-id="78a9a-215">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="78a9a-216">在伺服器上保留大量的身分識別資訊，同時只將小型會話識別碼金鑰傳送給用戶端。</span><span class="sxs-lookup"><span data-stu-id="78a9a-216">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="78a9a-217">儲存存取權杖</span><span class="sxs-lookup"><span data-stu-id="78a9a-217">Save the access token</span></span>

<span data-ttu-id="78a9a-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 定義在成功授權之後，是否應將存取和重新整理權杖儲存在中 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="78a9a-219">`SaveTokens` 預設會設定為， `false` 以縮減最終驗證的大小 cookie 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-219">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="78a9a-220">範例應用程式會在中將的值設定 `SaveTokens` 為 `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> ：</span><span class="sxs-lookup"><span data-stu-id="78a9a-220">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="78a9a-221">當 `OnPostConfirmationAsync` 執行時，請在的外部提供者中儲存存取權杖 ([ExternalLoginInfo AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) `ApplicationUser` 。 `AuthenticationProperties`</span><span class="sxs-lookup"><span data-stu-id="78a9a-221">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="78a9a-222">範例應用程式會將存取權杖儲存在 `OnPostConfirmationAsync` (新的使用者註冊) ，並 `OnGetCallbackAsync` (先前在 *Account/ExternalLogin* 中註冊的使用者) ：</span><span class="sxs-lookup"><span data-stu-id="78a9a-222">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs* :</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="78a9a-223">如何新增其他自訂權杖</span><span class="sxs-lookup"><span data-stu-id="78a9a-223">How to add additional custom tokens</span></span>

<span data-ttu-id="78a9a-224">為了示範如何新增作為一部分儲存的自訂權杖， `SaveTokens` 範例應用程式會將 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> 包含目前的 <xref:System.DateTime> [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) 加入至 `TicketCreated` ：</span><span class="sxs-lookup"><span data-stu-id="78a9a-224">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="78a9a-225">建立和新增宣告</span><span class="sxs-lookup"><span data-stu-id="78a9a-225">Creating and adding claims</span></span>

<span data-ttu-id="78a9a-226">架構會提供用來建立宣告並將其新增至集合的一般動作和擴充方法。</span><span class="sxs-lookup"><span data-stu-id="78a9a-226">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="78a9a-227">如需詳細資訊，請參閱 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 和 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>。</span><span class="sxs-lookup"><span data-stu-id="78a9a-227">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="78a9a-228">使用者可以藉由衍生自訂動作 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> 並執行抽象方法，來定義自訂動作 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-228">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="78a9a-229">如需詳細資訊，請參閱<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>。</span><span class="sxs-lookup"><span data-stu-id="78a9a-229">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="78a9a-230">移除宣告動作和宣告</span><span class="sxs-lookup"><span data-stu-id="78a9a-230">Removal of claim actions and claims</span></span>

<span data-ttu-id="78a9a-231">[ClaimActionCollection 移除 (字串) ](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) 移除集合中指定的所有宣告動作 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-231">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="78a9a-232">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection，String) ](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) 會從身分識別中刪除指定的宣告 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-232">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="78a9a-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> 主要用於搭配 [OpenID Connect (OIDC) ](/azure/active-directory/develop/v2-protocols-oidc) 來移除通訊協定產生的宣告。</span><span class="sxs-lookup"><span data-stu-id="78a9a-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="78a9a-234">範例應用程式輸出</span><span class="sxs-lookup"><span data-stu-id="78a9a-234">Sample app output</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="78a9a-235">其他資源</span><span class="sxs-lookup"><span data-stu-id="78a9a-235">Additional resources</span></span>

* <span data-ttu-id="78a9a-236">[dotnet/AspNetCore 工程 SocialSample 應用程式](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample)：連結的範例應用程式位於 [Dotnet/AspNetCore GitHub 存放庫的](https://github.com/dotnet/AspNetCore) `master` 工程分支上。</span><span class="sxs-lookup"><span data-stu-id="78a9a-236">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="78a9a-237">`master`分支包含下一版 ASP.NET Core 的主動式開發程式碼。</span><span class="sxs-lookup"><span data-stu-id="78a9a-237">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="78a9a-238">若要查看 ASP.NET Core 發行版本的範例應用程式版本，請使用 [ **分支** ] 下拉式清單來選取發行分支 (例如 `release/{X.Y}`) 。</span><span class="sxs-lookup"><span data-stu-id="78a9a-238">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
