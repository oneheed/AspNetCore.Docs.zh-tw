---
title: cookie在 ASP.NET Core 中使用 SameSite
author: rick-anderson
description: 瞭解如何 cookie 在 ASP.NET Core 中使用 SameSite s
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
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
- Electron
uid: security/samesite
ms.openlocfilehash: c95952face8763dc9f2dd12312cab1a1bc07528a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632339"
---
# <a name="work-with-samesite-no-loccookies-in-aspnet-core"></a><span data-ttu-id="afd82-103">cookie在 ASP.NET Core 中使用 SameSite</span><span class="sxs-lookup"><span data-stu-id="afd82-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="afd82-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="afd82-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="afd82-105">SameSite 是一項 [IETF](https://ietf.org/about/) 草稿標準，其設計目的是為了提供某些保護，以防止跨網站偽造要求 (CSRF) 攻擊。</span><span class="sxs-lookup"><span data-stu-id="afd82-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="afd82-106">最初是在 [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07)中繪製草稿標準，已于 [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00)更新。</span><span class="sxs-lookup"><span data-stu-id="afd82-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="afd82-107">更新的標準與先前的標準沒有回溯相容性，但以下是最顯著的差異：</span><span class="sxs-lookup"><span data-stu-id="afd82-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="afd82-108">Cookie預設會將不含 SameSite 標頭的 s 視為 `SameSite=Lax` 。</span><span class="sxs-lookup"><span data-stu-id="afd82-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="afd82-109">`SameSite=None` 必須用來允許跨網站 cookie 使用。</span><span class="sxs-lookup"><span data-stu-id="afd82-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="afd82-110">Cookie判斷提示的 `SameSite=None` 也必須標示為 `Secure` 。</span><span class="sxs-lookup"><span data-stu-id="afd82-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="afd82-111">使用的應用程式 [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) 可能會遇到或的問題， `sameSite=Lax` `sameSite=Strict` cookie 因為 `<iframe>` 會被視為跨網站案例。</span><span class="sxs-lookup"><span data-stu-id="afd82-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="afd82-112">`SameSite=None` [2016 標準](https://tools.ietf.org/html/draft-west-first-party-cookies-07)不允許此值，而且會導致某些執行將這類視為 cookie `SameSite=Strict` 。</span><span class="sxs-lookup"><span data-stu-id="afd82-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="afd82-113">請參閱本檔中的 [支援舊版瀏覽器](#sob) 。</span><span class="sxs-lookup"><span data-stu-id="afd82-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="afd82-114">此 `SameSite=Lax` 設定適用于大部分的應用程式 cookie 。</span><span class="sxs-lookup"><span data-stu-id="afd82-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="afd82-115">某些形式的驗證，例如 [OpenID Connect](https://openid.net/connect/) (OIDC) 和 [WS-同盟](https://auth0.com/docs/protocols/ws-fed) 預設為以 POST 為基礎的重新導向。</span><span class="sxs-lookup"><span data-stu-id="afd82-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="afd82-116">以 POST 為基礎的重新導向會觸發 SameSite 瀏覽器保護，因此這些元件會停用 SameSite。</span><span class="sxs-lookup"><span data-stu-id="afd82-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="afd82-117">由於要求流程的差異，大部分的 [OAuth](https://oauth.net/) 登入不會受到影響。</span><span class="sxs-lookup"><span data-stu-id="afd82-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="afd82-118">每個發出的 ASP.NET Core 元件都 cookie 需要決定 SameSite 是否適當。</span><span class="sxs-lookup"><span data-stu-id="afd82-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-and-no-locidentity"></a><span data-ttu-id="afd82-119">SameSite 和 Identity</span><span class="sxs-lookup"><span data-stu-id="afd82-119">SameSite and Identity</span></span>

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="samesite-test-sample-code"></a><span data-ttu-id="afd82-120">SameSite 測試範例程式碼</span><span class="sxs-lookup"><span data-stu-id="afd82-120">SameSite test sample code</span></span>

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="afd82-121">您可以下載並測試下列範例：</span><span class="sxs-lookup"><span data-stu-id="afd82-121">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="afd82-122">範例</span><span class="sxs-lookup"><span data-stu-id="afd82-122">Sample</span></span>               | <span data-ttu-id="afd82-123">Document</span><span class="sxs-lookup"><span data-stu-id="afd82-123">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="afd82-124">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="afd82-124">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="afd82-125">[.NET Core Razor 頁面](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="afd82-125">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="afd82-126">您可以下載並測試下列範例：</span><span class="sxs-lookup"><span data-stu-id="afd82-126">The following sample can be downloaded and tested:</span></span>


| <span data-ttu-id="afd82-127">範例</span><span class="sxs-lookup"><span data-stu-id="afd82-127">Sample</span></span>               | <span data-ttu-id="afd82-128">Document</span><span class="sxs-lookup"><span data-stu-id="afd82-128">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="afd82-129">[.NET Core Razor 頁面](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="afd82-129">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="afd82-130">SameSite 屬性的 .NET Core 支援</span><span class="sxs-lookup"><span data-stu-id="afd82-130">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="afd82-131">自12月2019起的更新發行之後，.NET Core 2.2 支援 SameSite 的 2019 draft 標準。</span><span class="sxs-lookup"><span data-stu-id="afd82-131">.NET Core 2.2 supports the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="afd82-132">開發人員可以使用屬性，以程式設計方式控制 sameSite 屬性的值 `HttpCookie.SameSite` 。</span><span class="sxs-lookup"><span data-stu-id="afd82-132">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="afd82-133">將屬性設為 [嚴格]、[不嚴格] 或 [無]，會 `SameSite` 導致這些值使用來寫入至網路 cookie 。</span><span class="sxs-lookup"><span data-stu-id="afd82-133">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="afd82-134">將它設定為等於 (SameSiteMode) # A2-1) 表示網路上沒有任何 sameSite 屬性應該包含在 cookie</span><span class="sxs-lookup"><span data-stu-id="afd82-134">Setting it equal to (SameSiteMode)(-1) indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="afd82-135">.NET Core 3.0 支援更新的 SameSite 值，並將額外的列舉值新增 `SameSiteMode.Unspecified` 至 `SameSiteMode` 列舉。</span><span class="sxs-lookup"><span data-stu-id="afd82-135">.NET Core 3.0 supports the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="afd82-136">這個新值表示不應該傳送任何 sameSite cookie 。</span><span class="sxs-lookup"><span data-stu-id="afd82-136">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="afd82-137">12月的修補程式列為變更</span><span class="sxs-lookup"><span data-stu-id="afd82-137">December patch behavior changes</span></span>

<span data-ttu-id="afd82-138">.NET Framework 和 .NET Core 2.1 的特定行為變更是 `SameSite` 屬性如何解讀值的方式 `None` 。</span><span class="sxs-lookup"><span data-stu-id="afd82-138">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="afd82-139">在修補程式的值「完全 `None` 不發出屬性」之前，在修補程式之後，它表示「發出具有值的屬性」 `None` 。</span><span class="sxs-lookup"><span data-stu-id="afd82-139">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="afd82-140">在 patch 的值之後，將 `SameSite` `(SameSiteMode)(-1)` 不會發出屬性。</span><span class="sxs-lookup"><span data-stu-id="afd82-140">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="afd82-141">表單驗證和會話狀態 s 的預設 SameSite 值 cookie 已從變更 `None` 為 `Lax` 。</span><span class="sxs-lookup"><span data-stu-id="afd82-141">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="afd82-142">使用 SameSite 的 API 使用方式</span><span class="sxs-lookup"><span data-stu-id="afd82-142">API usage with SameSite</span></span>

<span data-ttu-id="afd82-143">[HttpCoNtext. 回應。 Cookie。 Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) 預設值為 `Unspecified` ，表示未將 SameSite 屬性新增至， cookie 而且用戶端會使用其預設行為 (不嚴格的新瀏覽器，) 不會有舊的瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="afd82-143">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="afd82-144">下列程式碼顯示如何將 cookie SameSite 值變更為 `SameSiteMode.Lax` ：</span><span class="sxs-lookup"><span data-stu-id="afd82-144">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="afd82-145">發出的所有 ASP.NET Core 元件都會 cookie 以適用于其案例的設定覆寫先前的預設值。</span><span class="sxs-lookup"><span data-stu-id="afd82-145">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="afd82-146">覆寫的先前預設值尚未變更。</span><span class="sxs-lookup"><span data-stu-id="afd82-146">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="afd82-147">元件</span><span class="sxs-lookup"><span data-stu-id="afd82-147">Component</span></span> | cookie | <span data-ttu-id="afd82-148">預設</span><span class="sxs-lookup"><span data-stu-id="afd82-148">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | <span data-ttu-id="afd82-149">[SessionOptions.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="afd82-149">[SessionOptions.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie)</span></span> |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | <span data-ttu-id="afd82-150">[CookieTempDataProviderOptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="afd82-150">[CookieTempDataProviderOptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie)</span></span> | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | <span data-ttu-id="afd82-151">[AntiforgeryOptions.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="afd82-151">[AntiforgeryOptions.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)</span></span>| `Strict` |
| <span data-ttu-id="afd82-152">[Cookie 認證](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*)</span><span class="sxs-lookup"><span data-stu-id="afd82-152">[Cookie Authentication](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*)</span></span> | <span data-ttu-id="afd82-153">[CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName)</span><span class="sxs-lookup"><span data-stu-id="afd82-153">[CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName)</span></span> | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | <span data-ttu-id="afd82-154">[TwitterOptions。州 Cookie](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie)</span><span class="sxs-lookup"><span data-stu-id="afd82-154">[TwitterOptions.StateCookie ](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie)</span></span> | `Lax`  |
| <span data-ttu-id="afd82-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions 相互關聯Cookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="afd82-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | <span data-ttu-id="afd82-156">[OpenIdConnectOptionsCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)</span><span class="sxs-lookup"><span data-stu-id="afd82-156">[OpenIdConnectOptions.NonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)</span></span>| `None` |
| <span data-ttu-id="afd82-157">[HttpCoNtext. 回應。 Cookies. 附加](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="afd82-157">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span> | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="afd82-158">ASP.NET Core 3.1 和更新版本提供下列 SameSite 支援：</span><span class="sxs-lookup"><span data-stu-id="afd82-158">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="afd82-159">重新定義 `SameSiteMode.None` 要發出的行為 `SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="afd82-159">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="afd82-160">新增值 `SameSiteMode.Unspecified` 以省略 SameSite 屬性。</span><span class="sxs-lookup"><span data-stu-id="afd82-160">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="afd82-161">所有的 cookie api 都會預設為 `Unspecified` 。</span><span class="sxs-lookup"><span data-stu-id="afd82-161">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="afd82-162">某些使用 cookie 來設定值的元件會更明確地使用其案例。</span><span class="sxs-lookup"><span data-stu-id="afd82-162">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="afd82-163">如需範例，請參閱上表。</span><span class="sxs-lookup"><span data-stu-id="afd82-163">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="afd82-164">在 ASP.NET Core 3.0 和更新版本中，SameSite 預設值已變更，以避免與不一致的用戶端預設值發生衝突。</span><span class="sxs-lookup"><span data-stu-id="afd82-164">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="afd82-165">下列 Api 已將預設值從變更 `SameSiteMode.Lax ` 為 `-1` ，以避免發出下列的 SameSite 屬性 cookie ：</span><span class="sxs-lookup"><span data-stu-id="afd82-165">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="afd82-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> 搭配 [HttpCoNtext. 回應使用。 Cookies. 附加](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="afd82-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="afd82-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  用做為的 factory `CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="afd82-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* <span data-ttu-id="afd82-168">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span><span class="sxs-lookup"><span data-stu-id="afd82-168">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span></span>

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="afd82-169">歷程記錄和變更</span><span class="sxs-lookup"><span data-stu-id="afd82-169">History and changes</span></span>

<span data-ttu-id="afd82-170">SameSite 支援首次使用 [2016 draft 標準](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1)在2.0 的 ASP.NET Core 中執行。</span><span class="sxs-lookup"><span data-stu-id="afd82-170">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="afd82-171">2016標準為加入宣告。</span><span class="sxs-lookup"><span data-stu-id="afd82-171">The 2016 standard was opt-in.</span></span> <span data-ttu-id="afd82-172">依預設，將數個設定 cookie 為，ASP.NET Core 加入宣告 `Lax` 。</span><span class="sxs-lookup"><span data-stu-id="afd82-172">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="afd82-173">在遇到幾個驗證 [問題](https://github.com/aspnet/Announcements/issues/318) 之後，就會 [停用](https://github.com/aspnet/Announcements/issues/348)大部分的 SameSite 使用量。</span><span class="sxs-lookup"><span data-stu-id="afd82-173">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="afd82-174">[修補程式](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) 于2019年11月發行，以從2016標準更新為2019標準。</span><span class="sxs-lookup"><span data-stu-id="afd82-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="afd82-175">[SameSite 規格的2019草稿](https://github.com/aspnet/Announcements/issues/390)：</span><span class="sxs-lookup"><span data-stu-id="afd82-175">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="afd82-176">與 2016 draft **不** 相容。</span><span class="sxs-lookup"><span data-stu-id="afd82-176">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="afd82-177">如需詳細資訊，請參閱本檔中的 [支援舊版瀏覽器](#sob) 。</span><span class="sxs-lookup"><span data-stu-id="afd82-177">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="afd82-178">cookie預設會將指定視為 `SameSite=Lax` 。</span><span class="sxs-lookup"><span data-stu-id="afd82-178">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="afd82-179">指定明確判斷提示的 cookie ，以便 `SameSite=None` 啟用跨網站傳遞應標示為 `Secure` 。</span><span class="sxs-lookup"><span data-stu-id="afd82-179">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="afd82-180">`None` 是要退出的新專案。</span><span class="sxs-lookup"><span data-stu-id="afd82-180">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="afd82-181">針對 ASP.NET Core 2.1、2.2 和3.0 發出的修補程式支援。</span><span class="sxs-lookup"><span data-stu-id="afd82-181">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="afd82-182">ASP.NET Core 3.1 有額外的 SameSite 支援。</span><span class="sxs-lookup"><span data-stu-id="afd82-182">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="afd82-183">預設會在[2020 年2月](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)由[Chrome](https://chromestatus.com/feature/5088147346030592)啟用。</span><span class="sxs-lookup"><span data-stu-id="afd82-183">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="afd82-184">瀏覽器已在2019中開始移至此標準。</span><span class="sxs-lookup"><span data-stu-id="afd82-184">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="afd82-185">受 2016 SameSite 草稿標準變更為 2019 draft 標準的 Api</span><span class="sxs-lookup"><span data-stu-id="afd82-185">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="afd82-186">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="afd82-186">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* <span data-ttu-id="afd82-187">[Cookie選項。 SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)</span><span class="sxs-lookup"><span data-stu-id="afd82-187">[CookieOptions.SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)</span></span>
* <span data-ttu-id="afd82-188">[CookieBuilder. SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)</span><span class="sxs-lookup"><span data-stu-id="afd82-188">[CookieBuilder.SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)</span></span>
* <span data-ttu-id="afd82-189">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span><span class="sxs-lookup"><span data-stu-id="afd82-189">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span></span>
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="afd82-190">支援較舊的瀏覽器</span><span class="sxs-lookup"><span data-stu-id="afd82-190">Supporting older browsers</span></span>

<span data-ttu-id="afd82-191">2016 SameSite 標準規定必須將未知值視為 `SameSite=Strict` 值。</span><span class="sxs-lookup"><span data-stu-id="afd82-191">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="afd82-192">從較舊的瀏覽器存取的應用程式（支援 2016 SameSite 標準）在取得具有值的 SameSite 屬性時可能會中斷 `None` 。</span><span class="sxs-lookup"><span data-stu-id="afd82-192">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="afd82-193">如果 Web 應用程式想要支援較舊的瀏覽器，則必須執行瀏覽器偵測。</span><span class="sxs-lookup"><span data-stu-id="afd82-193">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="afd82-194">ASP.NET Core 不會執行瀏覽器偵測，因為使用者代理程式值會高度變動且經常變更。</span><span class="sxs-lookup"><span data-stu-id="afd82-194">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="afd82-195">中的擴充點 <xref:Microsoft.AspNetCore.CookiePolicy> 可讓您插入使用者代理程式特定邏輯。</span><span class="sxs-lookup"><span data-stu-id="afd82-195">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="afd82-196">在中 `Startup.Configure` ，加入呼叫之前呼叫的程式碼， <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 或 *任何* 寫入 s 的方法 cookie ：</span><span class="sxs-lookup"><span data-stu-id="afd82-196">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="afd82-197">在中 `Startup.ConfigureServices` ，加入類似下列的程式碼：</span><span class="sxs-lookup"><span data-stu-id="afd82-197">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="afd82-198">在上述範例中， `MyUserAgentDetectionLib.DisallowsSameSiteNone` 是使用者提供的程式庫，可偵測使用者代理程式是否不支援 SameSite `None` ：</span><span class="sxs-lookup"><span data-stu-id="afd82-198">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="afd82-199">下列程式碼顯示範例 `DisallowsSameSiteNone` 方法：</span><span class="sxs-lookup"><span data-stu-id="afd82-199">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="afd82-200">下列程式碼僅供示範之用：</span><span class="sxs-lookup"><span data-stu-id="afd82-200">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="afd82-201">不應將其視為已完成。</span><span class="sxs-lookup"><span data-stu-id="afd82-201">It should not be considered complete.</span></span>
> * <span data-ttu-id="afd82-202">不會維護或支援。</span><span class="sxs-lookup"><span data-stu-id="afd82-202">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="afd82-203">測試應用程式的 SameSite 問題</span><span class="sxs-lookup"><span data-stu-id="afd82-203">Test apps for SameSite problems</span></span>

<span data-ttu-id="afd82-204">與遠端網站互動的應用程式（例如透過協力廠商登入）必須：</span><span class="sxs-lookup"><span data-stu-id="afd82-204">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="afd82-205">在多個瀏覽器上測試互動。</span><span class="sxs-lookup"><span data-stu-id="afd82-205">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="afd82-206">套用本檔中討論的[ Cookie 原則瀏覽器偵測和緩和措施](#sob)。</span><span class="sxs-lookup"><span data-stu-id="afd82-206">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="afd82-207">使用可加入新 SameSite 行為的用戶端版本，測試 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="afd82-207">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="afd82-208">Chrome、Firefox 和 Chromium Edge 都有新的加入宣告功能旗標，可用於測試。</span><span class="sxs-lookup"><span data-stu-id="afd82-208">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="afd82-209">當您的應用程式套用 SameSite 修補程式之後，請使用較舊的用戶端版本（特別是 Safari）進行測試。</span><span class="sxs-lookup"><span data-stu-id="afd82-209">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="afd82-210">如需詳細資訊，請參閱本檔中的 [支援舊版瀏覽器](#sob) 。</span><span class="sxs-lookup"><span data-stu-id="afd82-210">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="afd82-211">使用 Chrome 測試</span><span class="sxs-lookup"><span data-stu-id="afd82-211">Test with Chrome</span></span>

<span data-ttu-id="afd82-212">Chrome 78 + 提供誤導的結果，因為它有暫時的緩和措施。</span><span class="sxs-lookup"><span data-stu-id="afd82-212">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="afd82-213">Chrome 78 + 暫時緩和可允許 cookie 不到兩分鐘的舊。</span><span class="sxs-lookup"><span data-stu-id="afd82-213">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="afd82-214">使用已啟用適當測試旗標的 Chrome 76 或77可提供更精確的結果。</span><span class="sxs-lookup"><span data-stu-id="afd82-214">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="afd82-215">測試新的 SameSite 行為切換 `chrome://flags/#same-site-by-default-cookies` 為 **啟用狀態**。</span><span class="sxs-lookup"><span data-stu-id="afd82-215">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="afd82-216">較舊版本的 Chrome (75 和以下的) 會回報為失敗，並出現新的 `None` 設定。</span><span class="sxs-lookup"><span data-stu-id="afd82-216">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="afd82-217">請參閱本檔中的 [支援舊版瀏覽器](#sob) 。</span><span class="sxs-lookup"><span data-stu-id="afd82-217">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="afd82-218">Google 未提供較舊的 chrome 版本。</span><span class="sxs-lookup"><span data-stu-id="afd82-218">Google does not make older chrome versions available.</span></span> <span data-ttu-id="afd82-219">遵循 [下載 Chromium](https://www.chromium.org/getting-involved/download-chromium) 中的指示，測試舊版的 Chrome。</span><span class="sxs-lookup"><span data-stu-id="afd82-219">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="afd82-220">請勿從搜尋較舊版本 chrome 所提供的 **連結下載 Chrome** 。</span><span class="sxs-lookup"><span data-stu-id="afd82-220">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="afd82-221">Chromium 76 Win64</span><span class="sxs-lookup"><span data-stu-id="afd82-221">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="afd82-222">Chromium 74 Win64</span><span class="sxs-lookup"><span data-stu-id="afd82-222">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="afd82-223">從未放大的版本開始 `80.0.3975.0` ，您可以使用新的旗標來停用寬鬆的 + POST 暫時緩和措施， `--enable-features=SameSiteDefaultChecksMethodRigorously` 以在移除緩和措施的功能的最終狀態下測試網站和服務。</span><span class="sxs-lookup"><span data-stu-id="afd82-223">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="afd82-224">如需詳細資訊，請參閱 Chromium Projects [SameSite 更新](https://www.chromium.org/updates/same-site)</span><span class="sxs-lookup"><span data-stu-id="afd82-224">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="afd82-225">使用 Safari 測試</span><span class="sxs-lookup"><span data-stu-id="afd82-225">Test with Safari</span></span>

<span data-ttu-id="afd82-226">Safari 12 會嚴格地實作為先前的草稿，並 `None` 在新值為時失敗 cookie 。</span><span class="sxs-lookup"><span data-stu-id="afd82-226">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="afd82-227">`None` 可透過支援本檔中 [舊版流覽](#sob) 器的瀏覽器偵測程式碼來避免。</span><span class="sxs-lookup"><span data-stu-id="afd82-227">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="afd82-228">使用 MSAL、ADAL 或您所使用的任何程式庫，測試 Safari 12、Safari 13 和以 WebKit 為基礎的 OS 樣式登入。</span><span class="sxs-lookup"><span data-stu-id="afd82-228">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="afd82-229">問題取決於基礎作業系統版本。</span><span class="sxs-lookup"><span data-stu-id="afd82-229">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="afd82-230">已知 OSX Mojave (10.14) 和 iOS 12 具有新 SameSite 行為的相容性問題。</span><span class="sxs-lookup"><span data-stu-id="afd82-230">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="afd82-231">將作業系統升級至 OSX Catalina (10.15) 或 iOS 13 可修正問題。</span><span class="sxs-lookup"><span data-stu-id="afd82-231">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="afd82-232">Safari 目前沒有可用於測試新規格行為的加入宣告旗標。</span><span class="sxs-lookup"><span data-stu-id="afd82-232">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="afd82-233">使用 Firefox 進行測試</span><span class="sxs-lookup"><span data-stu-id="afd82-233">Test with Firefox</span></span>

<span data-ttu-id="afd82-234">您可以在頁面上選擇具有功能旗標的，以在版本 68 + 上測試新標準的 Firefox 支援 `about:config` `network.cookie.sameSite.laxByDefault` 。</span><span class="sxs-lookup"><span data-stu-id="afd82-234">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="afd82-235">舊版 Firefox 沒有相容性問題的報告。</span><span class="sxs-lookup"><span data-stu-id="afd82-235">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="afd82-236">使用 Edge 瀏覽器進行測試</span><span class="sxs-lookup"><span data-stu-id="afd82-236">Test with Edge browser</span></span>

<span data-ttu-id="afd82-237">Edge 支援舊的 SameSite 標準。</span><span class="sxs-lookup"><span data-stu-id="afd82-237">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="afd82-238">Edge 版本44沒有任何已知的新標準相容性問題。</span><span class="sxs-lookup"><span data-stu-id="afd82-238">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="afd82-239">使用 Edge (Chromium) 進行測試</span><span class="sxs-lookup"><span data-stu-id="afd82-239">Test with Edge (Chromium)</span></span>

<span data-ttu-id="afd82-240">SameSite 旗標是在頁面上設定的 `edge://flags/#same-site-by-default-cookies` 。</span><span class="sxs-lookup"><span data-stu-id="afd82-240">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="afd82-241">Edge Chromium 未發現任何相容性問題。</span><span class="sxs-lookup"><span data-stu-id="afd82-241">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-no-locelectron"></a><span data-ttu-id="afd82-242">測試方式 Electron</span><span class="sxs-lookup"><span data-stu-id="afd82-242">Test with Electron</span></span>

<span data-ttu-id="afd82-243">的版本 Electron 包括較舊版本的 Chromium。</span><span class="sxs-lookup"><span data-stu-id="afd82-243">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="afd82-244">例如，小組所使用的版本 Electron 是 Chromium 66，這會展示較舊的行為。</span><span class="sxs-lookup"><span data-stu-id="afd82-244">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="afd82-245">您必須使用您的產品版本來執行您自己的相容性測試 Electron 。</span><span class="sxs-lookup"><span data-stu-id="afd82-245">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="afd82-246">請參閱下一節中的 [支援舊版瀏覽器](#sob) 。</span><span class="sxs-lookup"><span data-stu-id="afd82-246">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="afd82-247">其他資源</span><span class="sxs-lookup"><span data-stu-id="afd82-247">Additional resources</span></span>

* [<span data-ttu-id="afd82-248">Chromium Blog：開發人員：準備開始新的 SameSite = None;安全 Cookie 設定</span><span class="sxs-lookup"><span data-stu-id="afd82-248">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* <span data-ttu-id="afd82-249">[SameSite cookie 說明](https://web.dev/samesite-cookies-explained/)</span><span class="sxs-lookup"><span data-stu-id="afd82-249">[SameSite cookies explained](https://web.dev/samesite-cookies-explained/)</span></span>
* [<span data-ttu-id="afd82-250">2019年11月修補程式</span><span class="sxs-lookup"><span data-stu-id="afd82-250">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="afd82-251">範例</span><span class="sxs-lookup"><span data-stu-id="afd82-251">Sample</span></span>               | <span data-ttu-id="afd82-252">Document</span><span class="sxs-lookup"><span data-stu-id="afd82-252">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="afd82-253">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="afd82-253">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="afd82-254">[.NET Core Razor 頁面](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="afd82-254">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="afd82-255">範例</span><span class="sxs-lookup"><span data-stu-id="afd82-255">Sample</span></span>               | <span data-ttu-id="afd82-256">Document</span><span class="sxs-lookup"><span data-stu-id="afd82-256">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="afd82-257">[.NET Core Razor 頁面](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="afd82-257">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end
