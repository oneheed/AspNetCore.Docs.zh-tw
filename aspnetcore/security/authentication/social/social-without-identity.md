---
title: 不 ASP.NET Core 的 Facebook、Google 及外部提供者驗證Identity
author: rick-anderson
description: 使用 Facebook、Google、Twitter 等的說明，而不 ASP.NET Core 的帳戶使用者驗證 Identity 。
ms.author: riande
ms.date: 12/10/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 73055a262ac69c0fd6a7f59e77d23121e71ea3dd
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021662"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-no-locidentity"></a><span data-ttu-id="5e985-103">使用不 ASP.NET Core 的社交登入提供者驗證Identity</span><span class="sxs-lookup"><span data-stu-id="5e985-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="5e985-104">By [Kirk Larkin](https://twitter.com/serpent5)和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5e985-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5e985-105"><xref:security/authentication/social/index>說明如何讓使用者使用 OAuth 2.0 搭配外部驗證提供者的認證來登入。</span><span class="sxs-lookup"><span data-stu-id="5e985-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="5e985-106">該主題中所述的方法包含 ASP.NET Core Identity 做為驗證提供者。</span><span class="sxs-lookup"><span data-stu-id="5e985-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="5e985-107">這個範例會示範如何在不 ASP.NET Core 的**情況下**使用外部驗證提供者 Identity 。</span><span class="sxs-lookup"><span data-stu-id="5e985-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="5e985-108">這適用于不需要所有 ASP.NET Core 功能的應用程式 Identity ，但仍需要與信任的外部驗證提供者整合。</span><span class="sxs-lookup"><span data-stu-id="5e985-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="5e985-109">這個範例會使用[Google 驗證](xref:security/authentication/google-logins)來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="5e985-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="5e985-110">使用 Google authentication 會將管理登入程式的許多複雜性轉移到 Google。</span><span class="sxs-lookup"><span data-stu-id="5e985-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="5e985-111">若要與不同的外部驗證提供者整合，請參閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="5e985-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="5e985-112">Facebook 驗證</span><span class="sxs-lookup"><span data-stu-id="5e985-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="5e985-113">Microsoft 驗證</span><span class="sxs-lookup"><span data-stu-id="5e985-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="5e985-114">Twitter 驗證</span><span class="sxs-lookup"><span data-stu-id="5e985-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="5e985-115">其他提供者</span><span class="sxs-lookup"><span data-stu-id="5e985-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="5e985-116">組態</span><span class="sxs-lookup"><span data-stu-id="5e985-116">Configuration</span></span>

<span data-ttu-id="5e985-117">在 `ConfigureServices` 方法中，使用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 、 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 和方法來設定應用程式的驗證配置 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> ：</span><span class="sxs-lookup"><span data-stu-id="5e985-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="5e985-118">的呼叫會 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 設定應用程式的 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> 。</span><span class="sxs-lookup"><span data-stu-id="5e985-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="5e985-119">`DefaultScheme`是下列驗證擴充方法所使用的預設配置 `HttpContext` ：</span><span class="sxs-lookup"><span data-stu-id="5e985-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="5e985-120">將應用程式的設 `DefaultScheme` 為[ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ( " Cookie s" ) 會將應用程式設定為使用，做為 Cookie 這些擴充方法的預設配置。</span><span class="sxs-lookup"><span data-stu-id="5e985-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="5e985-121">將應用程式的設定 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> 為[GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ( "Google" ) 會將應用程式設為使用 Google 做為呼叫的預設配置 `ChallengeAsync` 。</span><span class="sxs-lookup"><span data-stu-id="5e985-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="5e985-122">`DefaultChallengeScheme`覆寫 `DefaultScheme` 。</span><span class="sxs-lookup"><span data-stu-id="5e985-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="5e985-123"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>如需在設定時覆寫的其他屬性，請參閱 `DefaultScheme` 。</span><span class="sxs-lookup"><span data-stu-id="5e985-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="5e985-124">在中，在呼叫 `Startup.Configure` `UseAuthentication` `UseAuthorization` 和之間呼叫和 `UseRouting` `UseEndpoints` 。</span><span class="sxs-lookup"><span data-stu-id="5e985-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="5e985-125">這會設定 `HttpContext.User` 屬性，並執行要求的授權中介軟體：</span><span class="sxs-lookup"><span data-stu-id="5e985-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="5e985-126">若要深入瞭解驗證配置，請參閱[驗證概念](xref:security/authentication/index#authentication-concepts)。</span><span class="sxs-lookup"><span data-stu-id="5e985-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="5e985-127">若要深入瞭解 cookie 驗證，請參閱 <xref:security/authentication/cookie> 。</span><span class="sxs-lookup"><span data-stu-id="5e985-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="5e985-128">套用授權</span><span class="sxs-lookup"><span data-stu-id="5e985-128">Apply authorization</span></span>

<span data-ttu-id="5e985-129">藉由將 `AuthorizeAttribute` 屬性套用至控制器、動作或頁面，測試應用程式的驗證設定。</span><span class="sxs-lookup"><span data-stu-id="5e985-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="5e985-130">下列程式碼會將*隱私權*頁面的存取限制為已驗證的使用者：</span><span class="sxs-lookup"><span data-stu-id="5e985-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="5e985-131">登出</span><span class="sxs-lookup"><span data-stu-id="5e985-131">Sign out</span></span>

<span data-ttu-id="5e985-132">若要登出目前的使用者並刪除其 cookie ，請呼叫[SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)。</span><span class="sxs-lookup"><span data-stu-id="5e985-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="5e985-133">下列程式碼會將 `Logout` 頁面處理常式新增至 [*索引*] 頁面：</span><span class="sxs-lookup"><span data-stu-id="5e985-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="5e985-134">請注意，對的呼叫不 `SignOutAsync` 會指定驗證配置。</span><span class="sxs-lookup"><span data-stu-id="5e985-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="5e985-135">的應用程式 `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` 會用來做為回復。</span><span class="sxs-lookup"><span data-stu-id="5e985-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5e985-136">其他資源</span><span class="sxs-lookup"><span data-stu-id="5e985-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5e985-137"><xref:security/authentication/social/index>說明如何讓使用者使用 OAuth 2.0 搭配外部驗證提供者的認證來登入。</span><span class="sxs-lookup"><span data-stu-id="5e985-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="5e985-138">該主題中所述的方法包含 ASP.NET Core Identity 做為驗證提供者。</span><span class="sxs-lookup"><span data-stu-id="5e985-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="5e985-139">這個範例會示範如何在不 ASP.NET Core 的**情況下**使用外部驗證提供者 Identity 。</span><span class="sxs-lookup"><span data-stu-id="5e985-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="5e985-140">這適用于不需要所有 ASP.NET Core 功能的應用程式 Identity ，但仍需要與信任的外部驗證提供者整合。</span><span class="sxs-lookup"><span data-stu-id="5e985-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="5e985-141">這個範例會使用[Google 驗證](xref:security/authentication/google-logins)來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="5e985-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="5e985-142">使用 Google authentication 會將管理登入程式的許多複雜性轉移到 Google。</span><span class="sxs-lookup"><span data-stu-id="5e985-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="5e985-143">若要與不同的外部驗證提供者整合，請參閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="5e985-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="5e985-144">Facebook 驗證</span><span class="sxs-lookup"><span data-stu-id="5e985-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="5e985-145">Microsoft 驗證</span><span class="sxs-lookup"><span data-stu-id="5e985-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="5e985-146">Twitter 驗證</span><span class="sxs-lookup"><span data-stu-id="5e985-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="5e985-147">其他提供者</span><span class="sxs-lookup"><span data-stu-id="5e985-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="5e985-148">組態</span><span class="sxs-lookup"><span data-stu-id="5e985-148">Configuration</span></span>

<span data-ttu-id="5e985-149">在 `ConfigureServices` 方法中，使用 `AddAuthentication` 、 `AddCookie` 和方法來設定應用程式的驗證配置 `AddGoogle` ：</span><span class="sxs-lookup"><span data-stu-id="5e985-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="5e985-150">對[AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__)的呼叫會設定應用程式的[DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)。</span><span class="sxs-lookup"><span data-stu-id="5e985-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="5e985-151">`DefaultScheme`是下列驗證擴充方法所使用的預設配置 `HttpContext` ：</span><span class="sxs-lookup"><span data-stu-id="5e985-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="5e985-152">將應用程式的設 `DefaultScheme` 為[ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ( " Cookie s" ) 會將應用程式設定為使用，做為 Cookie 這些擴充方法的預設配置。</span><span class="sxs-lookup"><span data-stu-id="5e985-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="5e985-153">將應用程式的設定 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> 為[GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ( "Google" ) 會將應用程式設為使用 Google 做為呼叫的預設配置 `ChallengeAsync` 。</span><span class="sxs-lookup"><span data-stu-id="5e985-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="5e985-154">`DefaultChallengeScheme`覆寫 `DefaultScheme` 。</span><span class="sxs-lookup"><span data-stu-id="5e985-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="5e985-155"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>如需在設定時覆寫的其他屬性，請參閱 `DefaultScheme` 。</span><span class="sxs-lookup"><span data-stu-id="5e985-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="5e985-156">在 `Configure` 方法中，呼叫 `UseAuthentication` 方法來叫用設定屬性的驗證中介軟體 `HttpContext.User` 。</span><span class="sxs-lookup"><span data-stu-id="5e985-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="5e985-157">呼叫 `UseAuthentication` 或之前，請先呼叫方法 `UseMvcWithDefaultRoute` `UseMvc` ：</span><span class="sxs-lookup"><span data-stu-id="5e985-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="5e985-158">若要深入瞭解驗證配置，請參閱[驗證概念](xref:security/authentication/index#authentication-concepts)。</span><span class="sxs-lookup"><span data-stu-id="5e985-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="5e985-159">若要深入瞭解 cookie 驗證，請參閱 <xref:security/authentication/cookie> 。</span><span class="sxs-lookup"><span data-stu-id="5e985-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="5e985-160">套用授權</span><span class="sxs-lookup"><span data-stu-id="5e985-160">Apply authorization</span></span>

<span data-ttu-id="5e985-161">藉由將 `AuthorizeAttribute` 屬性套用至控制器、動作或頁面，測試應用程式的驗證設定。</span><span class="sxs-lookup"><span data-stu-id="5e985-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="5e985-162">下列程式碼會將*隱私權*頁面的存取限制為已驗證的使用者：</span><span class="sxs-lookup"><span data-stu-id="5e985-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="5e985-163">登出</span><span class="sxs-lookup"><span data-stu-id="5e985-163">Sign out</span></span>

<span data-ttu-id="5e985-164">若要登出目前的使用者並刪除其 cookie ，請呼叫[SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)。</span><span class="sxs-lookup"><span data-stu-id="5e985-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="5e985-165">下列程式碼會將 `Logout` 頁面處理常式新增至 [*索引*] 頁面：</span><span class="sxs-lookup"><span data-stu-id="5e985-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="5e985-166">請注意，對的呼叫不 `SignOutAsync` 會指定驗證配置。</span><span class="sxs-lookup"><span data-stu-id="5e985-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="5e985-167">的應用程式 `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` 會用來做為回復。</span><span class="sxs-lookup"><span data-stu-id="5e985-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5e985-168">其他資源</span><span class="sxs-lookup"><span data-stu-id="5e985-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
