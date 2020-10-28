---
title: '安全 ASP.NET Core :::no-loc(Blazor WebAssembly):::'
author: guardrex
description: '瞭解如何將 :::no-loc(Blazor)::: WebAssemlby 應用程式保護為單一頁面應用程式， (spa) 。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/security/webassembly/index
ms.openlocfilehash: 2c160f21ccccb44f9047cf23c67bc191ad1b2b3d
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690369"
---
# <a name="secure-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="1259d-103">安全 ASP.NET Core :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="1259d-103">Secure ASP.NET Core :::no-loc(Blazor WebAssembly):::</span></span>

<span data-ttu-id="1259d-104">[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="1259d-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="1259d-105">:::no-loc(Blazor WebAssembly)::: 應用程式的保護方式與單一頁面應用程式 (Spa) 相同。</span><span class="sxs-lookup"><span data-stu-id="1259d-105">:::no-loc(Blazor WebAssembly)::: apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="1259d-106">有幾種方法可以驗證使用者的 Spa，但最常見且最完整的方法是使用以 [OAuth 2.0 通訊協定](https://oauth.net/)為基礎的執行，例如 [OpenID Connect (OIDC) ](https://openid.net/connect/)。</span><span class="sxs-lookup"><span data-stu-id="1259d-106">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [OAuth 2.0 protocol](https://oauth.net/), such as [OpenID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="1259d-107">驗證程式庫</span><span class="sxs-lookup"><span data-stu-id="1259d-107">Authentication library</span></span>

<span data-ttu-id="1259d-108">:::no-loc(Blazor WebAssembly)::: 支援透過程式庫使用 OIDC 來驗證和授權應用程式 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 。</span><span class="sxs-lookup"><span data-stu-id="1259d-108">:::no-loc(Blazor WebAssembly)::: supports authenticating and authorizing apps using OIDC via the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library.</span></span> <span data-ttu-id="1259d-109">此程式庫提供一組基本類型，可針對 ASP.NET Core 後端進行順暢的驗證。</span><span class="sxs-lookup"><span data-stu-id="1259d-109">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="1259d-110">此程式庫會 :::no-loc(ASP.NET Core Identity)::: 與以[ :::no-loc(Identity)::: 伺服器](https://identityserver.io/)為基礎的 API 授權支援整合。</span><span class="sxs-lookup"><span data-stu-id="1259d-110">The library integrates :::no-loc(ASP.NET Core Identity)::: with API authorization support built on top of [:::no-loc(Identity)::: Server](https://identityserver.io/).</span></span> <span data-ttu-id="1259d-111">此程式庫可以針對支援 OIDC 的任何協力廠商 :::no-loc(Identity)::: 提供者進行驗證 (IP) ，這稱為 OpenID 提供者 (OP) 。</span><span class="sxs-lookup"><span data-stu-id="1259d-111">The library can authenticate against any third-party :::no-loc(Identity)::: Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="1259d-112">中的驗證支援 :::no-loc(Blazor WebAssembly)::: 是建立在程式庫的上層 `oidc-client.js` ，用來處理基礎驗證通訊協定的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="1259d-112">The authentication support in :::no-loc(Blazor WebAssembly)::: is built on top of the `oidc-client.js` library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="1259d-113">有其他驗證 Spa 的選項存在，例如使用 SameSite :::no-loc(cookie)::: s。</span><span class="sxs-lookup"><span data-stu-id="1259d-113">Other options for authenticating SPAs exist, such as the use of SameSite :::no-loc(cookie):::s.</span></span> <span data-ttu-id="1259d-114">但是，的工程設計 :::no-loc(Blazor WebAssembly)::: 會在 OAuth 和 OIDC 上進行結算，作為應用程式中驗證的最佳選項 :::no-loc(Blazor WebAssembly)::: 。</span><span class="sxs-lookup"><span data-stu-id="1259d-114">However, the engineering design of :::no-loc(Blazor WebAssembly)::: is settled on OAuth and OIDC as the best option for authentication in :::no-loc(Blazor WebAssembly)::: apps.</span></span> <span data-ttu-id="1259d-115">以 JSON Web 權杖為基礎的[權杖型驗證](xref:security/anti-request-forgery#token-based-authentication) [ (jwt) ](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)基於功能和安全性理由選擇了 [以超過[ :::no-loc(cookie)::: 基礎的驗證](xref:security/anti-request-forgery#:::no-loc(cookie):::-based-authentication)]：</span><span class="sxs-lookup"><span data-stu-id="1259d-115">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [:::no-loc(cookie):::-based authentication](xref:security/anti-request-forgery#:::no-loc(cookie):::-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="1259d-116">使用以權杖為基礎的通訊協定提供較小的攻擊面區，因為權杖不會在所有要求中傳送。</span><span class="sxs-lookup"><span data-stu-id="1259d-116">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="1259d-117">伺服器端點不需要保護 [跨網站偽造要求 (CSRF) ](xref:security/anti-request-forgery) ，因為權杖是明確傳送的。</span><span class="sxs-lookup"><span data-stu-id="1259d-117">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="1259d-118">這可讓您將 :::no-loc(Blazor WebAssembly)::: 應用程式與 MVC 或 :::no-loc(Razor)::: pages 應用程式一起裝載。</span><span class="sxs-lookup"><span data-stu-id="1259d-118">This allows you to host :::no-loc(Blazor WebAssembly)::: apps alongside MVC or :::no-loc(Razor)::: pages apps.</span></span>
* <span data-ttu-id="1259d-119">權杖的許可權比 :::no-loc(cookie)::: s 窄。</span><span class="sxs-lookup"><span data-stu-id="1259d-119">Tokens have narrower permissions than :::no-loc(cookie):::s.</span></span> <span data-ttu-id="1259d-120">例如，除非明確執行這類功能，否則無法使用權杖來管理使用者帳戶或變更使用者的密碼。</span><span class="sxs-lookup"><span data-stu-id="1259d-120">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="1259d-121">權杖有短暫的存留期（預設為一小時），這會限制攻擊時間範圍。</span><span class="sxs-lookup"><span data-stu-id="1259d-121">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="1259d-122">您也可以隨時撤銷權杖。</span><span class="sxs-lookup"><span data-stu-id="1259d-122">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="1259d-123">獨立的 Jwt 供應專案可保證用戶端和伺服器有關驗證程式。</span><span class="sxs-lookup"><span data-stu-id="1259d-123">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="1259d-124">例如，用戶端的方法是偵測並驗證其所接收的權杖是否合法，並在指定的驗證程式中發出。</span><span class="sxs-lookup"><span data-stu-id="1259d-124">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part of a given authentication process.</span></span> <span data-ttu-id="1259d-125">如果協力廠商嘗試在驗證程式的過程中切換權杖，用戶端可以偵測到切換的權杖，並避免使用它。</span><span class="sxs-lookup"><span data-stu-id="1259d-125">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="1259d-126">OAuth 和 OIDC 的權杖不依賴使用者代理程式正確運作，以確保應用程式的安全。</span><span class="sxs-lookup"><span data-stu-id="1259d-126">Tokens with OAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="1259d-127">以權杖為基礎的通訊協定（例如 OAuth 和 OIDC）可讓您使用相同的安全性特性集來驗證和授權託管和獨立應用程式。</span><span class="sxs-lookup"><span data-stu-id="1259d-127">Token-based protocols, such as OAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="1259d-128">使用 OIDC 進行驗證流程</span><span class="sxs-lookup"><span data-stu-id="1259d-128">Authentication process with OIDC</span></span>

<span data-ttu-id="1259d-129">此連結 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 庫提供數個基本專案，以使用 OIDC 來執行驗證和授權。</span><span class="sxs-lookup"><span data-stu-id="1259d-129">The [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="1259d-130">大致來說，驗證的運作方式如下：</span><span class="sxs-lookup"><span data-stu-id="1259d-130">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="1259d-131">當匿名使用者選取登入按鈕或要求套用屬性的頁面時 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) ，系統會將使用者重新導向至應用程式的登入頁面， (`/authentication/login`) 。</span><span class="sxs-lookup"><span data-stu-id="1259d-131">When an anonymous user selects the login button or requests a page with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="1259d-132">在 [登入] 頁面中，驗證程式庫會準備重新導向至授權端點。</span><span class="sxs-lookup"><span data-stu-id="1259d-132">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="1259d-133">授權端點位於 :::no-loc(Blazor WebAssembly)::: 應用程式之外，而且可以裝載于不同的來源。</span><span class="sxs-lookup"><span data-stu-id="1259d-133">The authorization endpoint is outside of the :::no-loc(Blazor WebAssembly)::: app and can be hosted at a separate origin.</span></span> <span data-ttu-id="1259d-134">端點負責判斷使用者是否已通過驗證，並在回應中發出一或多個權杖。</span><span class="sxs-lookup"><span data-stu-id="1259d-134">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="1259d-135">驗證程式庫會提供登入回呼，以接收驗證回應。</span><span class="sxs-lookup"><span data-stu-id="1259d-135">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="1259d-136">如果使用者未經過驗證，系統會將使用者重新導向至基礎驗證系統，這通常是 :::no-loc(ASP.NET Core Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="1259d-136">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually :::no-loc(ASP.NET Core Identity):::.</span></span>
  * <span data-ttu-id="1259d-137">如果使用者已通過驗證，授權端點會產生適當的權杖，並將瀏覽器重新導向回登入回呼端點 (`/authentication/login-callback`) 。</span><span class="sxs-lookup"><span data-stu-id="1259d-137">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="1259d-138">當 :::no-loc(Blazor WebAssembly)::: 應用程式載入登入回呼端點 (`/authentication/login-callback`) 時，會處理驗證回應。</span><span class="sxs-lookup"><span data-stu-id="1259d-138">When the :::no-loc(Blazor WebAssembly)::: app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="1259d-139">如果驗證程式順利完成，就會驗證使用者，並選擇性地將其傳回給使用者要求的原始受保護 URL。</span><span class="sxs-lookup"><span data-stu-id="1259d-139">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="1259d-140">如果驗證程式因任何原因而失敗，則會將使用者傳送至登入失敗頁面 (`/authentication/login-failed`) ，並顯示錯誤。</span><span class="sxs-lookup"><span data-stu-id="1259d-140">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>

## <a name="authentication-component"></a><span data-ttu-id="1259d-141">`Authentication` 元件</span><span class="sxs-lookup"><span data-stu-id="1259d-141">`Authentication` component</span></span>

<span data-ttu-id="1259d-142">`Authentication`元件 (`Pages/Authentication.razor`) 會處理遠端驗證作業，並允許應用程式：</span><span class="sxs-lookup"><span data-stu-id="1259d-142">The `Authentication` component (`Pages/Authentication.razor`) handles remote authentication operations and permits the app to:</span></span>

* <span data-ttu-id="1259d-143">設定驗證狀態的應用程式路由。</span><span class="sxs-lookup"><span data-stu-id="1259d-143">Configure app routes for authentication states.</span></span>
* <span data-ttu-id="1259d-144">設定驗證狀態的 UI 內容。</span><span class="sxs-lookup"><span data-stu-id="1259d-144">Set UI content for authentication states.</span></span>
* <span data-ttu-id="1259d-145">管理驗證狀態。</span><span class="sxs-lookup"><span data-stu-id="1259d-145">Manage authentication state.</span></span>

<span data-ttu-id="1259d-146">驗證動作（例如註冊或登入使用者）會傳遞至 :::no-loc(Blazor)::: 架構的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> 元件，以保存並控制驗證作業之間的狀態。</span><span class="sxs-lookup"><span data-stu-id="1259d-146">Authentication actions, such as registering or signing in a user, are passed to the :::no-loc(Blazor)::: framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> component, which persists and controls state across authentication operations.</span></span>

<span data-ttu-id="1259d-147">如需詳細資訊和範例，請參閱 <xref:blazor/security/webassembly/additional-scenarios>。</span><span class="sxs-lookup"><span data-stu-id="1259d-147">For more information and examples, see <xref:blazor/security/webassembly/additional-scenarios>.</span></span>

## <a name="authorization"></a><span data-ttu-id="1259d-148">授權</span><span class="sxs-lookup"><span data-stu-id="1259d-148">Authorization</span></span>

<span data-ttu-id="1259d-149">在 :::no-loc(Blazor WebAssembly)::: 應用程式中，可以略過授權檢查，因為使用者可以修改所有用戶端程式代碼。</span><span class="sxs-lookup"><span data-stu-id="1259d-149">In :::no-loc(Blazor WebAssembly)::: apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="1259d-150">這同樣也適用於所有的用戶端應用程式技術，包括 JavaScript SPA 架構或任何作業系統的原生應用程式。</span><span class="sxs-lookup"><span data-stu-id="1259d-150">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="1259d-151">**請一律在由您用戶端應用程式所存取之任何 API 端點內的伺服器上執行授權檢查。**</span><span class="sxs-lookup"><span data-stu-id="1259d-151">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="require-authorization-for-the-entire-app"></a><span data-ttu-id="1259d-152">需要整個應用程式的授權</span><span class="sxs-lookup"><span data-stu-id="1259d-152">Require authorization for the entire app</span></span>

<span data-ttu-id="1259d-153">使用下列其中一種方法，將 ([API 檔](xref:System.Web.Mvc.AuthorizeAttribute)) 的[ `[Authorize]` 屬性](xref:blazor/security/index#authorize-attribute)套用至應用程式的每個 :::no-loc(Razor)::: 元件：</span><span class="sxs-lookup"><span data-stu-id="1259d-153">Apply the [`[Authorize]` attribute](xref:blazor/security/index#authorize-attribute) ([API documentation](xref:System.Web.Mvc.AuthorizeAttribute)) to each :::no-loc(Razor)::: component of the app using one of the following approaches:</span></span>

* <span data-ttu-id="1259d-154">使用檔案中的指示詞 [`@attribute`](xref:mvc/views/razor#attribute) `_Imports.razor` ：</span><span class="sxs-lookup"><span data-stu-id="1259d-154">Use the [`@attribute`](xref:mvc/views/razor#attribute) directive in the `_Imports.razor` file:</span></span>

  ```razor
  @using Microsoft.AspNetCore.Authorization
  @attribute [Authorize]
  ```

* <span data-ttu-id="1259d-155">將屬性新增至 :::no-loc(Razor)::: 資料夾中的每個元件 `Pages` 。</span><span class="sxs-lookup"><span data-stu-id="1259d-155">Add the attribute to each :::no-loc(Razor)::: component in the `Pages` folder.</span></span>

> [!NOTE]
> <span data-ttu-id="1259d-156"><xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType>不支援使用將設定為 <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> 原則 **not** 。</span><span class="sxs-lookup"><span data-stu-id="1259d-156">Setting an <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> to a policy with <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> is **not** supported.</span></span>

## <a name="refresh-tokens"></a><span data-ttu-id="1259d-157">重新整理權杖</span><span class="sxs-lookup"><span data-stu-id="1259d-157">Refresh tokens</span></span>

<span data-ttu-id="1259d-158">重新整理權杖無法在應用程式中保護用戶端 :::no-loc(Blazor WebAssembly)::: 。</span><span class="sxs-lookup"><span data-stu-id="1259d-158">Refresh tokens can't be secured client-side in :::no-loc(Blazor WebAssembly)::: apps.</span></span> <span data-ttu-id="1259d-159">因此，不應將重新整理權杖傳送至應用程式以供直接使用。</span><span class="sxs-lookup"><span data-stu-id="1259d-159">Therefore, refresh tokens shouldn't be sent to the app for direct use.</span></span>

<span data-ttu-id="1259d-160">重新整理權杖可由裝載解決方案中的伺服器端應用程式維護及使用， :::no-loc(Blazor WebAssembly)::: 以存取協力廠商 api。</span><span class="sxs-lookup"><span data-stu-id="1259d-160">Refresh tokens can be maintained and used by the server-side app in a Hosted :::no-loc(Blazor WebAssembly)::: solution to access third-party APIs.</span></span> <span data-ttu-id="1259d-161">如需詳細資訊，請參閱<xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>。</span><span class="sxs-lookup"><span data-stu-id="1259d-161">For more information, see <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.</span></span>

## <a name="establish-claims-for-users"></a><span data-ttu-id="1259d-162">為使用者建立宣告</span><span class="sxs-lookup"><span data-stu-id="1259d-162">Establish claims for users</span></span>

<span data-ttu-id="1259d-163">應用程式通常需要以伺服器的 web API 呼叫為基礎的使用者宣告。</span><span class="sxs-lookup"><span data-stu-id="1259d-163">Apps often require claims for users based on a web API call to a server.</span></span> <span data-ttu-id="1259d-164">例如，宣告經常用來在應用程式中 [建立授權](xref:blazor/security/index#authorization) 。</span><span class="sxs-lookup"><span data-stu-id="1259d-164">For example, claims are frequently used to [establish authorization](xref:blazor/security/index#authorization) in an app.</span></span> <span data-ttu-id="1259d-165">在這些情況下，應用程式會要求存取權杖以存取服務，並使用權杖來取得宣告的使用者資料。</span><span class="sxs-lookup"><span data-stu-id="1259d-165">In these scenarios, the app requests an access token to access the service and uses the token to obtain the user data for the claims.</span></span> <span data-ttu-id="1259d-166">如需範例，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="1259d-166">For examples, see the following resources:</span></span>

* [<span data-ttu-id="1259d-167">其他案例：自訂使用者</span><span class="sxs-lookup"><span data-stu-id="1259d-167">Additional scenarios: Customize the user</span></span>](xref:blazor/security/webassembly/additional-scenarios#customize-the-user)
* <xref:blazor/security/webassembly/aad-groups-roles>

## <a name="implementation-guidance"></a><span data-ttu-id="1259d-168">實作指引</span><span class="sxs-lookup"><span data-stu-id="1259d-168">Implementation guidance</span></span>

<span data-ttu-id="1259d-169">本 *總覽* 底下的文章提供在 :::no-loc(Blazor WebAssembly)::: 應用程式中針對特定提供者驗證使用者的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="1259d-169">Articles under this *Overview* provide information on authenticating users in :::no-loc(Blazor WebAssembly)::: apps against specific providers.</span></span>

<span data-ttu-id="1259d-170">獨立 :::no-loc(Blazor WebAssembly)::: 應用程式：</span><span class="sxs-lookup"><span data-stu-id="1259d-170">Standalone :::no-loc(Blazor WebAssembly)::: apps:</span></span>

* [<span data-ttu-id="1259d-171">OIDC 提供者和 WebAssembly Authentication Library 的一般指引</span><span class="sxs-lookup"><span data-stu-id="1259d-171">General guidance for OIDC providers and the WebAssembly Authentication Library</span></span>](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [<span data-ttu-id="1259d-172">Microsoft 帳戶</span><span class="sxs-lookup"><span data-stu-id="1259d-172">Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="1259d-173">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="1259d-173">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="1259d-174">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="1259d-174">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

<span data-ttu-id="1259d-175">託管 :::no-loc(Blazor WebAssembly)::: 應用程式：</span><span class="sxs-lookup"><span data-stu-id="1259d-175">Hosted :::no-loc(Blazor WebAssembly)::: apps:</span></span>

* [<span data-ttu-id="1259d-176">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="1259d-176">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [<span data-ttu-id="1259d-177">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="1259d-177">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [<span data-ttu-id="1259d-178">:::no-loc(Identity)::: 伺服器</span><span class="sxs-lookup"><span data-stu-id="1259d-178">:::no-loc(Identity)::: Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="1259d-179">您可以在下列文章中找到進一步的設定指引：</span><span class="sxs-lookup"><span data-stu-id="1259d-179">Further configuration guidance is found in the following articles:</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* <xref:blazor/security/webassembly/graph-api>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="1259d-180">如需進一步的設定指引，請參閱 <xref:blazor/security/webassembly/additional-scenarios> 。</span><span class="sxs-lookup"><span data-stu-id="1259d-180">For further configuration guidance, see <xref:blazor/security/webassembly/additional-scenarios>.</span></span>

::: moniker-end
