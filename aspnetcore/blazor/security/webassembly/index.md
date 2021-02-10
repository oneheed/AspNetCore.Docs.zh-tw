---
title: 安全 ASP.NET Core Blazor WebAssembly
author: guardrex
description: 瞭解如何將 Blazor WebAssemlby 應用程式保護為單一頁面應用程式， (spa) 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/index
ms.openlocfilehash: fc2ebae6e88e312aafec790229f978c3130e64de
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106683"
---
# <a name="secure-aspnet-core-blazor-webassembly"></a><span data-ttu-id="6d121-103">安全 ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="6d121-103">Secure ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="6d121-104">[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="6d121-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="6d121-105">Blazor WebAssembly 應用程式的保護方式與單一頁面應用程式 (Spa) 相同。</span><span class="sxs-lookup"><span data-stu-id="6d121-105">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="6d121-106">有幾種方法可以驗證使用者的 Spa，但最常見且最完整的方法是使用以 [OAuth 2.0 通訊協定](https://oauth.net/)為基礎的執行，例如 [OpenID Connect (OIDC) ](https://openid.net/connect/)。</span><span class="sxs-lookup"><span data-stu-id="6d121-106">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [OAuth 2.0 protocol](https://oauth.net/), such as [OpenID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="6d121-107">驗證程式庫</span><span class="sxs-lookup"><span data-stu-id="6d121-107">Authentication library</span></span>

<span data-ttu-id="6d121-108">Blazor WebAssembly 支援透過程式庫使用 OIDC 來驗證和授權應用程式 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 。</span><span class="sxs-lookup"><span data-stu-id="6d121-108">Blazor WebAssembly supports authenticating and authorizing apps using OIDC via the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library.</span></span> <span data-ttu-id="6d121-109">此程式庫提供一組基本類型，可針對 ASP.NET Core 後端進行順暢的驗證。</span><span class="sxs-lookup"><span data-stu-id="6d121-109">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="6d121-110">此程式庫會 ASP.NET Core Identity 與以[ Identity 伺服器](https://identityserver.io/)為基礎的 API 授權支援整合。</span><span class="sxs-lookup"><span data-stu-id="6d121-110">The library integrates ASP.NET Core Identity with API authorization support built on top of [Identity Server](https://identityserver.io/).</span></span> <span data-ttu-id="6d121-111">此程式庫可以針對支援 OIDC 的任何協力廠商 Identity 提供者進行驗證 (IP) ，這稱為 OpenID 提供者 (OP) 。</span><span class="sxs-lookup"><span data-stu-id="6d121-111">The library can authenticate against any third-party Identity Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="6d121-112">中的驗證支援 Blazor WebAssembly 是建立在程式庫的上層 `oidc-client.js` ，用來處理基礎驗證通訊協定的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="6d121-112">The authentication support in Blazor WebAssembly is built on top of the `oidc-client.js` library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="6d121-113">有其他驗證 Spa 的選項存在，例如使用 SameSite cookie s。</span><span class="sxs-lookup"><span data-stu-id="6d121-113">Other options for authenticating SPAs exist, such as the use of SameSite cookies.</span></span> <span data-ttu-id="6d121-114">但是，的工程設計 Blazor WebAssembly 會在 OAuth 和 OIDC 上進行結算，作為應用程式中驗證的最佳選項 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="6d121-114">However, the engineering design of Blazor WebAssembly is settled on OAuth and OIDC as the best option for authentication in Blazor WebAssembly apps.</span></span> <span data-ttu-id="6d121-115">以 JSON Web 權杖為基礎的[權杖型驗證](xref:security/anti-request-forgery#token-based-authentication) [ (jwt) ](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)基於功能和安全性理由選擇了 [以超過[ cookie 基礎的驗證](xref:security/anti-request-forgery#cookie-based-authentication)]：</span><span class="sxs-lookup"><span data-stu-id="6d121-115">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [cookie-based authentication](xref:security/anti-request-forgery#cookie-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="6d121-116">使用以權杖為基礎的通訊協定提供較小的攻擊面區，因為權杖不會在所有要求中傳送。</span><span class="sxs-lookup"><span data-stu-id="6d121-116">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="6d121-117">伺服器端點不需要保護 [跨網站偽造要求 (CSRF) ](xref:security/anti-request-forgery) ，因為權杖是明確傳送的。</span><span class="sxs-lookup"><span data-stu-id="6d121-117">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="6d121-118">這可讓您將 Blazor WebAssembly 應用程式與 MVC 或 Razor pages 應用程式一起裝載。</span><span class="sxs-lookup"><span data-stu-id="6d121-118">This allows you to host Blazor WebAssembly apps alongside MVC or Razor pages apps.</span></span>
* <span data-ttu-id="6d121-119">權杖的許可權比 cookie s 窄。</span><span class="sxs-lookup"><span data-stu-id="6d121-119">Tokens have narrower permissions than cookies.</span></span> <span data-ttu-id="6d121-120">例如，除非明確執行這類功能，否則無法使用權杖來管理使用者帳戶或變更使用者的密碼。</span><span class="sxs-lookup"><span data-stu-id="6d121-120">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="6d121-121">權杖有短暫的存留期（預設為一小時），這會限制攻擊時間範圍。</span><span class="sxs-lookup"><span data-stu-id="6d121-121">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="6d121-122">您也可以隨時撤銷權杖。</span><span class="sxs-lookup"><span data-stu-id="6d121-122">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="6d121-123">獨立的 Jwt 供應專案可保證用戶端和伺服器有關驗證程式。</span><span class="sxs-lookup"><span data-stu-id="6d121-123">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="6d121-124">例如，用戶端的方法是偵測並驗證其所接收的權杖是否合法，並在指定的驗證程式中發出。</span><span class="sxs-lookup"><span data-stu-id="6d121-124">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part of a given authentication process.</span></span> <span data-ttu-id="6d121-125">如果協力廠商嘗試在驗證程式的過程中切換權杖，用戶端可以偵測到切換的權杖，並避免使用它。</span><span class="sxs-lookup"><span data-stu-id="6d121-125">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="6d121-126">OAuth 和 OIDC 的權杖不依賴使用者代理程式正確運作，以確保應用程式的安全。</span><span class="sxs-lookup"><span data-stu-id="6d121-126">Tokens with OAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="6d121-127">以權杖為基礎的通訊協定（例如 OAuth 和 OIDC）可讓您使用相同的安全性特性集來驗證和授權託管和獨立應用程式。</span><span class="sxs-lookup"><span data-stu-id="6d121-127">Token-based protocols, such as OAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="6d121-128">使用 OIDC 進行驗證流程</span><span class="sxs-lookup"><span data-stu-id="6d121-128">Authentication process with OIDC</span></span>

<span data-ttu-id="6d121-129">此連結 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 庫提供數個基本專案，以使用 OIDC 來執行驗證和授權。</span><span class="sxs-lookup"><span data-stu-id="6d121-129">The [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="6d121-130">大致來說，驗證的運作方式如下：</span><span class="sxs-lookup"><span data-stu-id="6d121-130">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="6d121-131">當匿名使用者選取登入按鈕或要求套用[ `[Authorize]` 屬性](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)的頁面時，系統會將使用者重新導向至應用程式的登入頁面， (`/authentication/login`) 。</span><span class="sxs-lookup"><span data-stu-id="6d121-131">When an anonymous user selects the login button or requests a page with the [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="6d121-132">在 [登入] 頁面中，驗證程式庫會準備重新導向至授權端點。</span><span class="sxs-lookup"><span data-stu-id="6d121-132">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="6d121-133">授權端點位於 Blazor WebAssembly 應用程式之外，而且可以裝載于不同的來源。</span><span class="sxs-lookup"><span data-stu-id="6d121-133">The authorization endpoint is outside of the Blazor WebAssembly app and can be hosted at a separate origin.</span></span> <span data-ttu-id="6d121-134">端點負責判斷使用者是否已通過驗證，並在回應中發出一或多個權杖。</span><span class="sxs-lookup"><span data-stu-id="6d121-134">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="6d121-135">驗證程式庫會提供登入回呼，以接收驗證回應。</span><span class="sxs-lookup"><span data-stu-id="6d121-135">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="6d121-136">如果使用者未經過驗證，系統會將使用者重新導向至基礎驗證系統，這通常是 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="6d121-136">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="6d121-137">如果使用者已通過驗證，授權端點會產生適當的權杖，並將瀏覽器重新導向回登入回呼端點 (`/authentication/login-callback`) 。</span><span class="sxs-lookup"><span data-stu-id="6d121-137">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="6d121-138">當 Blazor WebAssembly 應用程式載入登入回呼端點 (`/authentication/login-callback`) 時，會處理驗證回應。</span><span class="sxs-lookup"><span data-stu-id="6d121-138">When the Blazor WebAssembly app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="6d121-139">如果驗證程式順利完成，就會驗證使用者，並選擇性地將其傳回給使用者要求的原始受保護 URL。</span><span class="sxs-lookup"><span data-stu-id="6d121-139">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="6d121-140">如果驗證程式因任何原因而失敗，則會將使用者傳送至登入失敗頁面 (`/authentication/login-failed`) ，並顯示錯誤。</span><span class="sxs-lookup"><span data-stu-id="6d121-140">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>

## <a name="authentication-component"></a><span data-ttu-id="6d121-141">`Authentication` 元件</span><span class="sxs-lookup"><span data-stu-id="6d121-141">`Authentication` component</span></span>

<span data-ttu-id="6d121-142">`Authentication`元件 (`Pages/Authentication.razor`) 會處理遠端驗證作業，並允許應用程式：</span><span class="sxs-lookup"><span data-stu-id="6d121-142">The `Authentication` component (`Pages/Authentication.razor`) handles remote authentication operations and permits the app to:</span></span>

* <span data-ttu-id="6d121-143">設定驗證狀態的應用程式路由。</span><span class="sxs-lookup"><span data-stu-id="6d121-143">Configure app routes for authentication states.</span></span>
* <span data-ttu-id="6d121-144">設定驗證狀態的 UI 內容。</span><span class="sxs-lookup"><span data-stu-id="6d121-144">Set UI content for authentication states.</span></span>
* <span data-ttu-id="6d121-145">管理驗證狀態。</span><span class="sxs-lookup"><span data-stu-id="6d121-145">Manage authentication state.</span></span>

<span data-ttu-id="6d121-146">驗證動作（例如註冊或登入使用者）會傳遞至 Blazor 架構的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> 元件，以保存並控制驗證作業之間的狀態。</span><span class="sxs-lookup"><span data-stu-id="6d121-146">Authentication actions, such as registering or signing in a user, are passed to the Blazor framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> component, which persists and controls state across authentication operations.</span></span>

<span data-ttu-id="6d121-147">如需詳細資訊和範例，請參閱 <xref:blazor/security/webassembly/additional-scenarios>。</span><span class="sxs-lookup"><span data-stu-id="6d121-147">For more information and examples, see <xref:blazor/security/webassembly/additional-scenarios>.</span></span>

## <a name="authorization"></a><span data-ttu-id="6d121-148">授權</span><span class="sxs-lookup"><span data-stu-id="6d121-148">Authorization</span></span>

<span data-ttu-id="6d121-149">在 Blazor WebAssembly 應用程式中，可以略過授權檢查，因為使用者可以修改所有用戶端程式代碼。</span><span class="sxs-lookup"><span data-stu-id="6d121-149">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="6d121-150">這同樣也適用於所有的用戶端應用程式技術，包括 JavaScript SPA 架構或任何作業系統的原生應用程式。</span><span class="sxs-lookup"><span data-stu-id="6d121-150">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="6d121-151">**請一律在由您用戶端應用程式所存取之任何 API 端點內的伺服器上執行授權檢查。**</span><span class="sxs-lookup"><span data-stu-id="6d121-151">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="require-authorization-for-the-entire-app"></a><span data-ttu-id="6d121-152">需要整個應用程式的授權</span><span class="sxs-lookup"><span data-stu-id="6d121-152">Require authorization for the entire app</span></span>

<span data-ttu-id="6d121-153">使用下列其中一種方法，將 ([API 檔](xref:System.Web.Mvc.AuthorizeAttribute)) 的[ `[Authorize]` 屬性](xref:blazor/security/index#authorize-attribute)套用至應用程式的每個 Razor 元件：</span><span class="sxs-lookup"><span data-stu-id="6d121-153">Apply the [`[Authorize]` attribute](xref:blazor/security/index#authorize-attribute) ([API documentation](xref:System.Web.Mvc.AuthorizeAttribute)) to each Razor component of the app using one of the following approaches:</span></span>

* <span data-ttu-id="6d121-154">使用檔案中的指示詞 [`@attribute`](xref:mvc/views/razor#attribute) `_Imports.razor` ：</span><span class="sxs-lookup"><span data-stu-id="6d121-154">Use the [`@attribute`](xref:mvc/views/razor#attribute) directive in the `_Imports.razor` file:</span></span>

  ```razor
  @using Microsoft.AspNetCore.Authorization
  @attribute [Authorize]
  ```

* <span data-ttu-id="6d121-155">將屬性新增至 Razor 資料夾中的每個元件 `Pages` 。</span><span class="sxs-lookup"><span data-stu-id="6d121-155">Add the attribute to each Razor component in the `Pages` folder.</span></span>

> [!NOTE]
> <span data-ttu-id="6d121-156"><xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType>不支援使用將設定為 <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> 原則 。</span><span class="sxs-lookup"><span data-stu-id="6d121-156">Setting an <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> to a policy with <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> is **not** supported.</span></span>

## <a name="refresh-tokens"></a><span data-ttu-id="6d121-157">重新整理權杖</span><span class="sxs-lookup"><span data-stu-id="6d121-157">Refresh tokens</span></span>

<span data-ttu-id="6d121-158">重新整理權杖無法在應用程式中保護用戶端 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="6d121-158">Refresh tokens can't be secured client-side in Blazor WebAssembly apps.</span></span> <span data-ttu-id="6d121-159">因此，不應將重新整理權杖傳送至應用程式以供直接使用。</span><span class="sxs-lookup"><span data-stu-id="6d121-159">Therefore, refresh tokens shouldn't be sent to the app for direct use.</span></span>

<span data-ttu-id="6d121-160">重新整理權杖可由裝載解決方案中的伺服器端應用程式維護及使用， Blazor WebAssembly 以存取協力廠商 api。</span><span class="sxs-lookup"><span data-stu-id="6d121-160">Refresh tokens can be maintained and used by the server-side app in a Hosted Blazor WebAssembly solution to access third-party APIs.</span></span> <span data-ttu-id="6d121-161">如需詳細資訊，請參閱<xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>。</span><span class="sxs-lookup"><span data-stu-id="6d121-161">For more information, see <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.</span></span>

## <a name="establish-claims-for-users"></a><span data-ttu-id="6d121-162">為使用者建立宣告</span><span class="sxs-lookup"><span data-stu-id="6d121-162">Establish claims for users</span></span>

<span data-ttu-id="6d121-163">應用程式通常需要以伺服器的 web API 呼叫為基礎的使用者宣告。</span><span class="sxs-lookup"><span data-stu-id="6d121-163">Apps often require claims for users based on a web API call to a server.</span></span> <span data-ttu-id="6d121-164">例如，宣告經常用來在應用程式中 [建立授權](xref:blazor/security/index#authorization) 。</span><span class="sxs-lookup"><span data-stu-id="6d121-164">For example, claims are frequently used to [establish authorization](xref:blazor/security/index#authorization) in an app.</span></span> <span data-ttu-id="6d121-165">在這些情況下，應用程式會要求存取權杖以存取服務，並使用權杖來取得宣告的使用者資料。</span><span class="sxs-lookup"><span data-stu-id="6d121-165">In these scenarios, the app requests an access token to access the service and uses the token to obtain the user data for the claims.</span></span> <span data-ttu-id="6d121-166">如需範例，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="6d121-166">For examples, see the following resources:</span></span>

* [<span data-ttu-id="6d121-167">其他案例：自訂使用者</span><span class="sxs-lookup"><span data-stu-id="6d121-167">Additional scenarios: Customize the user</span></span>](xref:blazor/security/webassembly/additional-scenarios#customize-the-user)
* <xref:blazor/security/webassembly/aad-groups-roles>

## <a name="azure-app-service-on-linux-with-identity-server"></a><span data-ttu-id="6d121-168">使用 Identity 伺服器 Linux 上的 Azure App Service</span><span class="sxs-lookup"><span data-stu-id="6d121-168">Azure App Service on Linux with Identity Server</span></span>

<span data-ttu-id="6d121-169">當部署至與伺服器 Linux 上的 Azure App Service 時，請明確指定簽發者 Identity 。</span><span class="sxs-lookup"><span data-stu-id="6d121-169">Specify the issuer explicitly when deploying to Azure App Service on Linux with Identity Server.</span></span> <span data-ttu-id="6d121-170">如需詳細資訊，請參閱<xref:security/authentication/identity/spa#azure-app-service-on-linux>。</span><span class="sxs-lookup"><span data-stu-id="6d121-170">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

## <a name="windows-authentication"></a><span data-ttu-id="6d121-171">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="6d121-171">Windows Authentication</span></span>

<span data-ttu-id="6d121-172">我們不建議使用 Windows 驗證搭配 Blazor Webassembly 或其他任何 SPA 架構。</span><span class="sxs-lookup"><span data-stu-id="6d121-172">We don't recommend using Windows Authentication with Blazor Webassembly or with any other SPA framework.</span></span> <span data-ttu-id="6d121-173">建議使用以權杖為基礎的通訊協定，而不是使用 Windows 驗證，例如搭配 Active Directory 同盟服務 (ADFS) 的 OIDC。</span><span class="sxs-lookup"><span data-stu-id="6d121-173">We recommend using token-based protocols instead of Windows Authentication, such as OIDC with Active Directory Federation Services (ADFS).</span></span>

<span data-ttu-id="6d121-174">如果 Windows 驗證與 Webassembly 搭配使用 Blazor ，或與任何其他 SPA 架構搭配使用，則需要額外的量值，以保護應用程式免于受到跨網站要求偽造 (CSRF) 權杖。</span><span class="sxs-lookup"><span data-stu-id="6d121-174">If Windows Authentication is used with Blazor Webassembly or with any other SPA framework, additional measures are required to protect the app from cross-site request forgery (CSRF) tokens.</span></span> <span data-ttu-id="6d121-175">適用于的相同考慮也 cookie 適用于 Windows 驗證，因為 Windows 驗證不提供任何機制來防止跨原始來源共用驗證內容。</span><span class="sxs-lookup"><span data-stu-id="6d121-175">The same concerns that apply to cookies apply to Windows Authentication with the addition that Windows Authentication doesn't offer any mechanism to prevent sharing of the authentication context across origins.</span></span> <span data-ttu-id="6d121-176">使用 Windows 驗證的應用程式若沒有額外的 CSRF 防護，則至少應限制為組織的內部網路，而不會在網際網路上使用。</span><span class="sxs-lookup"><span data-stu-id="6d121-176">Apps using Windows Authentication without additional protection from CSRF should at least be restricted to an organization's intranet and not be used on the Internet.</span></span>

<span data-ttu-id="6d121-177">如需詳細資訊，請參閱 <xref:security/anti-request-forgery> 。</span><span class="sxs-lookup"><span data-stu-id="6d121-177">For for information, see <xref:security/anti-request-forgery>.</span></span>

## <a name="implementation-guidance"></a><span data-ttu-id="6d121-178">實作指引</span><span class="sxs-lookup"><span data-stu-id="6d121-178">Implementation guidance</span></span>

<span data-ttu-id="6d121-179">本 *總覽* 底下的文章提供在 Blazor WebAssembly 應用程式中針對特定提供者驗證使用者的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="6d121-179">Articles under this *Overview* provide information on authenticating users in Blazor WebAssembly apps against specific providers.</span></span>

<span data-ttu-id="6d121-180">獨立 Blazor WebAssembly 應用程式：</span><span class="sxs-lookup"><span data-stu-id="6d121-180">Standalone Blazor WebAssembly apps:</span></span>

* [<span data-ttu-id="6d121-181">OIDC 提供者和 WebAssembly Authentication Library 的一般指引</span><span class="sxs-lookup"><span data-stu-id="6d121-181">General guidance for OIDC providers and the WebAssembly Authentication Library</span></span>](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [<span data-ttu-id="6d121-182">Microsoft 帳戶</span><span class="sxs-lookup"><span data-stu-id="6d121-182">Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="6d121-183">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="6d121-183">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="6d121-184">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="6d121-184">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

<span data-ttu-id="6d121-185">託管 Blazor WebAssembly 應用程式：</span><span class="sxs-lookup"><span data-stu-id="6d121-185">Hosted Blazor WebAssembly apps:</span></span>

* [<span data-ttu-id="6d121-186">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="6d121-186">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [<span data-ttu-id="6d121-187">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="6d121-187">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [<span data-ttu-id="6d121-188">Identity 伺服器</span><span class="sxs-lookup"><span data-stu-id="6d121-188">Identity Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6d121-189">您可以在下列文章中找到進一步的設定指引：</span><span class="sxs-lookup"><span data-stu-id="6d121-189">Further configuration guidance is found in the following articles:</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* <xref:blazor/security/webassembly/graph-api>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="6d121-190">如需進一步的設定指引，請參閱 <xref:blazor/security/webassembly/additional-scenarios> 。</span><span class="sxs-lookup"><span data-stu-id="6d121-190">For further configuration guidance, see <xref:blazor/security/webassembly/additional-scenarios>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="6d121-191">其他資源</span><span class="sxs-lookup"><span data-stu-id="6d121-191">Additional resources</span></span>

* <span data-ttu-id="6d121-192"><xref:host-and-deploy/proxy-load-balancer>：包含下列相關指引：</span><span class="sxs-lookup"><span data-stu-id="6d121-192"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="6d121-193">使用轉送的標頭中介軟體，在 proxy 伺服器和內部網路之間保留 HTTPS 配置資訊。</span><span class="sxs-lookup"><span data-stu-id="6d121-193">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="6d121-194">其他案例和使用案例，包括手動設定設定、正確要求路由的要求路徑變更，以及轉送 Linux 和非 IIS 反向 proxy 的要求配置。</span><span class="sxs-lookup"><span data-stu-id="6d121-194">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
