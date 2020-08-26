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
ms.openlocfilehash: 3ba033b4165b19131d11311e5ae9d64e6afe48ca
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865426"
---
# <a name="work-with-samesite-no-loccookies-in-aspnet-core"></a>cookie在 ASP.NET Core 中使用 SameSite

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

SameSite 是一項 [IETF](https://ietf.org/about/) 草稿標準，其設計目的是為了提供某些保護，以防止跨網站偽造要求 (CSRF) 攻擊。 最初是在 [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07)中繪製草稿標準，已于 [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00)更新。 更新的標準與先前的標準沒有回溯相容性，但以下是最顯著的差異：

* Cookie預設會將不含 SameSite 標頭的 s 視為 `SameSite=Lax` 。
* `SameSite=None` 必須用來允許跨網站 cookie 使用。
* Cookie判斷提示的 `SameSite=None` 也必須標示為 `Secure` 。
* 使用的應用程式 [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) 可能會遇到或的問題， `sameSite=Lax` `sameSite=Strict` cookie 因為 `<iframe>` 會被視為跨網站案例。
* `SameSite=None` [2016 標準](https://tools.ietf.org/html/draft-west-first-party-cookies-07)不允許此值，而且會導致某些執行將這類視為 cookie `SameSite=Strict` 。 請參閱本檔中的 [支援舊版瀏覽器](#sob) 。

此 `SameSite=Lax` 設定適用于大部分的應用程式 cookie 。 某些形式的驗證，例如 [OpenID Connect](https://openid.net/connect/) (OIDC) 和 [WS-同盟](https://auth0.com/docs/protocols/ws-fed) 預設為以 POST 為基礎的重新導向。 以 POST 為基礎的重新導向會觸發 SameSite 瀏覽器保護，因此這些元件會停用 SameSite。 由於要求流程的差異，大部分的 [OAuth](https://oauth.net/) 登入不會受到影響。

每個發出的 ASP.NET Core 元件都 cookie 需要決定 SameSite 是否適當。

## <a name="samesite-and-no-locidentity"></a>SameSite 和 Identity

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="samesite-test-sample-code"></a>SameSite 測試範例程式碼

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

您可以下載並測試下列範例：

| 範例               | Document |
| ----------------- | ------------ |
| [.NET Core MVC](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [.NET Core Razor 頁面](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

您可以下載並測試下列範例：


| 範例               | Document |
| ----------------- | ------------ |
| [.NET Core Razor 頁面](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a>SameSite 屬性的 .NET Core 支援

.NET Core 2.2 和更新版本支援 SameSite 自12月2019版更新後的 2019 draft 標準。 開發人員可以使用屬性，以程式設計方式控制 sameSite 屬性的值 `HttpCookie.SameSite` 。 將屬性設為 [嚴格]、[不嚴格] 或 [無]，會 `SameSite` 導致這些值使用來寫入至網路 cookie 。 將它設定為等於 `(SameSiteMode)(-1)` ，表示網路上沒有任何 sameSite 屬性應該包含在 cookie

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

.NET Core 3.0 和更新版本支援更新的 SameSite 值，並將額外的列舉值新增 `SameSiteMode.Unspecified` 至 `SameSiteMode` 列舉。
這個新值表示不應該傳送任何 sameSite cookie 。

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a>12月的修補程式列為變更

.NET Framework 和 .NET Core 2.1 的特定行為變更是 `SameSite` 屬性如何解讀值的方式 `None` 。 在修補程式的值「完全 `None` 不發出屬性」之前，在修補程式之後，它表示「發出具有值的屬性」 `None` 。 在 patch 的值之後，將 `SameSite` `(SameSiteMode)(-1)` 不會發出屬性。

表單驗證和會話狀態 s 的預設 SameSite 值 cookie 已從變更 `None` 為 `Lax` 。

::: moniker-end

## <a name="api-usage-with-samesite"></a>使用 SameSite 的 API 使用方式

[HttpCoNtext. 回應。 Cookie。 Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) 預設值為 `Unspecified` ，表示未將 SameSite 屬性新增至， cookie 而且用戶端會使用其預設行為 (不嚴格的新瀏覽器，) 不會有舊的瀏覽器。 下列程式碼顯示如何將 cookie SameSite 值變更為 `SameSiteMode.Lax` ：

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

發出的所有 ASP.NET Core 元件都會 cookie 以適用于其案例的設定覆寫先前的預設值。 覆寫的先前預設值尚未變更。

| 元件 | cookie | 預設 |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [SessionOptions.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [CookieTempDataProviderOptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [AntiforgeryOptions.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [Cookie 認證](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [TwitterOptions。州 Cookie](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions 相互關聯Cookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None` |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [OpenIdConnectOptionsCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [HttpCoNtext. 回應。 Cookies. 附加](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

ASP.NET Core 3.1 和更新版本提供下列 SameSite 支援：

* 重新定義 `SameSiteMode.None` 要發出的行為 `SameSite=None`
* 新增值 `SameSiteMode.Unspecified` 以省略 SameSite 屬性。
* 所有的 cookie api 都會預設為 `Unspecified` 。 某些使用 cookie 來設定值的元件會更明確地使用其案例。 如需範例，請參閱上表。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

在 ASP.NET Core 3.0 和更新版本中，SameSite 預設值已變更，以避免與不一致的用戶端預設值發生衝突。 下列 Api 已將預設值從變更 `SameSiteMode.Lax ` 為 `-1` ，以避免發出下列的 SameSite 屬性 cookie ：

* <xref:Microsoft.AspNetCore.Http.CookieOptions> 搭配 [HttpCoNtext. 回應使用。 Cookies. 附加](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)
* <xref:Microsoft.AspNetCore.Http.CookieBuilder>  用做為的 factory `CookieOptions`
* [CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a>歷程記錄和變更

SameSite 支援首次使用 [2016 draft 標準](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1)在2.0 的 ASP.NET Core 中執行。 2016標準為加入宣告。 依預設，將數個設定 cookie 為，ASP.NET Core 加入宣告 `Lax` 。 在遇到幾個驗證 [問題](https://github.com/aspnet/Announcements/issues/318) 之後，就會 [停用](https://github.com/aspnet/Announcements/issues/348)大部分的 SameSite 使用量。

[修補程式](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) 于2019年11月發行，以從2016標準更新為2019標準。 [SameSite 規格的2019草稿](https://github.com/aspnet/Announcements/issues/390)：

* 與 2016 draft **不** 相容。 如需詳細資訊，請參閱本檔中的 [支援舊版瀏覽器](#sob) 。
* cookie預設會將指定視為 `SameSite=Lax` 。
* 指定明確判斷提示的 cookie ，以便 `SameSite=None` 啟用跨網站傳遞應標示為 `Secure` 。 `None` 是要退出的新專案。
* 針對 ASP.NET Core 2.1、2.2 和3.0 發出的修補程式支援。 ASP.NET Core 3.1 有額外的 SameSite 支援。
* 預設會在[2020 年2月](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)由[Chrome](https://chromestatus.com/feature/5088147346030592)啟用。 瀏覽器已在2019中開始移至此標準。

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a>受 2016 SameSite 草稿標準變更為 2019 draft 標準的 Api

* [SameSiteMode](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [Cookie選項。 SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [CookieBuilder. SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a>支援較舊的瀏覽器

2016 SameSite 標準規定必須將未知值視為 `SameSite=Strict` 值。 從較舊的瀏覽器存取的應用程式（支援 2016 SameSite 標準）在取得具有值的 SameSite 屬性時可能會中斷 `None` 。 如果 Web 應用程式想要支援較舊的瀏覽器，則必須執行瀏覽器偵測。 ASP.NET Core 不會執行瀏覽器偵測，因為使用者代理程式值會高度變動且經常變更。 中的擴充點 <xref:Microsoft.AspNetCore.CookiePolicy> 可讓您插入使用者代理程式特定邏輯。

在中 `Startup.Configure` ，加入呼叫之前呼叫的程式碼， <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 或 *任何* 寫入 s 的方法 cookie ：

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

在中 `Startup.ConfigureServices` ，加入類似下列的程式碼：

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

在上述範例中， `MyUserAgentDetectionLib.DisallowsSameSiteNone` 是使用者提供的程式庫，可偵測使用者代理程式是否不支援 SameSite `None` ：

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

下列程式碼顯示範例 `DisallowsSameSiteNone` 方法：

> [!WARNING]
> 下列程式碼僅供示範之用：
> * 不應將其視為已完成。
> * 不會維護或支援。

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a>測試應用程式的 SameSite 問題

與遠端網站互動的應用程式（例如透過協力廠商登入）必須：

* 在多個瀏覽器上測試互動。
* 套用本檔中討論的[ Cookie 原則瀏覽器偵測和緩和措施](#sob)。

使用可加入新 SameSite 行為的用戶端版本，測試 web 應用程式。 Chrome、Firefox 和 Chromium Edge 都有新的加入宣告功能旗標，可用於測試。 當您的應用程式套用 SameSite 修補程式之後，請使用較舊的用戶端版本（特別是 Safari）進行測試。 如需詳細資訊，請參閱本檔中的 [支援舊版瀏覽器](#sob) 。

### <a name="test-with-chrome"></a>使用 Chrome 測試

Chrome 78 + 提供誤導的結果，因為它有暫時的緩和措施。 Chrome 78 + 暫時緩和可允許 cookie 不到兩分鐘的舊。 使用已啟用適當測試旗標的 Chrome 76 或77可提供更精確的結果。 測試新的 SameSite 行為切換 `chrome://flags/#same-site-by-default-cookies` 為 **啟用狀態**。 較舊版本的 Chrome (75 和以下的) 會回報為失敗，並出現新的 `None` 設定。 請參閱本檔中的 [支援舊版瀏覽器](#sob) 。

Google 未提供較舊的 chrome 版本。 遵循 [下載 Chromium](https://www.chromium.org/getting-involved/download-chromium) 中的指示，測試舊版的 Chrome。 請勿從搜尋較舊版本 chrome 所提供的 **連結下載 Chrome** 。

* [Chromium 76 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [Chromium 74 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

從未放大的版本開始 `80.0.3975.0` ，您可以使用新的旗標來停用寬鬆的 + POST 暫時緩和措施， `--enable-features=SameSiteDefaultChecksMethodRigorously` 以在移除緩和措施的功能的最終狀態下測試網站和服務。 如需詳細資訊，請參閱 Chromium Projects [SameSite 更新](https://www.chromium.org/updates/same-site)

### <a name="test-with-safari"></a>使用 Safari 測試

Safari 12 會嚴格地實作為先前的草稿，並 `None` 在新值為時失敗 cookie 。 `None` 可透過支援本檔中 [舊版流覽](#sob) 器的瀏覽器偵測程式碼來避免。 使用 MSAL、ADAL 或您所使用的任何程式庫，測試 Safari 12、Safari 13 和以 WebKit 為基礎的 OS 樣式登入。 問題取決於基礎作業系統版本。 已知 OSX Mojave (10.14) 和 iOS 12 具有新 SameSite 行為的相容性問題。 將作業系統升級至 OSX Catalina (10.15) 或 iOS 13 可修正問題。 Safari 目前沒有可用於測試新規格行為的加入宣告旗標。

### <a name="test-with-firefox"></a>使用 Firefox 進行測試

您可以在頁面上選擇具有功能旗標的，以在版本 68 + 上測試新標準的 Firefox 支援 `about:config` `network.cookie.sameSite.laxByDefault` 。 舊版 Firefox 沒有相容性問題的報告。

### <a name="test-with-edge-browser"></a>使用 Edge 瀏覽器進行測試

Edge 支援舊的 SameSite 標準。 Edge 版本44沒有任何已知的新標準相容性問題。

### <a name="test-with-edge-chromium"></a>使用 Edge (Chromium) 進行測試

SameSite 旗標是在頁面上設定的 `edge://flags/#same-site-by-default-cookies` 。 Edge Chromium 未發現任何相容性問題。

### <a name="test-with-no-locelectron"></a>測試方式 Electron

的版本 Electron 包括較舊版本的 Chromium。 例如，小組所使用的版本 Electron 是 Chromium 66，這會展示較舊的行為。 您必須使用您的產品版本來執行您自己的相容性測試 Electron 。 請參閱下一節中的 [支援舊版瀏覽器](#sob) 。

## <a name="additional-resources"></a>其他資源

* [Chromium Blog：開發人員：準備開始新的 SameSite = None;安全 Cookie 設定](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [SameSite cookie 說明](https://web.dev/samesite-cookies-explained/)
* [2019年11月修補程式](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| 範例               | Document |
| ----------------- | ------------ |
| [.NET Core MVC](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [.NET Core Razor 頁面](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| 範例               | Document |
| ----------------- | ------------ |
| [.NET Core Razor 頁面](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end
