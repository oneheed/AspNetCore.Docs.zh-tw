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
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>防止跨網站偽造要求 (XSRF/CSRF) 攻擊 ASP.NET Core

依 [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)和 [Steve Smith](https://ardalis.com/)

跨網站要求偽造 (也稱為 XSRF 或 CSRF) 是針對 web 裝載的應用程式進行攻擊，而惡意的 web 應用程式可能會影響用戶端瀏覽器與信任該瀏覽器的 web 應用程式之間的互動。 這些攻擊是可能的，因為 web 瀏覽器會在每個網站要求時自動傳送某些類型的驗證權杖。 這種形式的惡意探索也稱為單鍵 *攻擊* 或 *會話騎* ，因為攻擊會利用使用者先前經過驗證的會話。

CSRF 攻擊的範例：

1. 使用者 `www.good-banking-site.com` 使用表單驗證登入。 伺服器會驗證使用者，併發出包含驗證的回應 cookie 。 網站很容易受到攻擊，因為它會信任透過有效驗證所收到的任何要求 cookie 。
1. 使用者造訪惡意網站 `www.bad-crook-site.com` 。

   惡意網站 `www.bad-crook-site.com` 包含類似下面的 HTML 表單：

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   請注意，表單的 `action` 文章會張貼到易受攻擊的網站，而不是惡意網站。 這是 CSRF 的「跨網站」部分。

1. 使用者選取 [提交] 按鈕。 瀏覽器會提出要求，並自動包含要求之網域的驗證 cookie `www.good-banking-site.com` 。
1. 要求會在伺服器上 `www.good-banking-site.com` 以使用者的驗證內容執行，而且可以執行已驗證的使用者可以執行的任何動作。

除了使用者選取按鈕來提交表單的情況之外，惡意網站也可能：

* 執行自動提交表單的腳本。
* 以 AJAX 要求形式傳送表單提交。
* 使用 CSS 隱藏表單。

除了一開始造訪惡意網站之外，這些替代案例不需要使用者採取任何動作或輸入。

使用 HTTPS 不會防止 CSRF 攻擊。 惡意網站可以傳送要求， `https://www.good-banking-site.com/` 就像傳送不安全的要求一樣簡單。

某些攻擊會以回應 GET 要求的端點為目標，在這種情況下，可以使用影像標記來執行動作。 這種形式的攻擊常見於允許影像但封鎖 JavaScript 的論壇網站。 變更 GET 要求狀態（變更變數或資源）的應用程式很容易受到惡意攻擊。 **取得變更狀態的要求不安全。最佳做法是永遠不要變更 GET 要求的狀態。**

使用進行驗證的 web 應用程式可能會 CSRF 攻擊， cookie 因為：

* cookieWeb 應用程式所發行的瀏覽器存放區。
* 儲存 cookie cookie 的包含已驗證使用者的會話 s。
* 瀏覽器會 cookie 在每次要求時，將所有與網域相關聯的所有傳送至 web 應用程式，不論在瀏覽器中產生應用程式要求的方式為何。

不過，CSRF 攻擊並不限於利用 cookie 。 例如，基本和摘要式驗證也很容易受到攻擊。 使用者使用基本或摘要式驗證登入之後，瀏覽器會自動傳送認證，直到會話 &dagger; 結束為止。

&dagger;在此內容中， *會話* 是指驗證使用者的用戶端會話。 它與伺服器端會話或 [ASP.NET Core 會話中介軟體](xref:fundamentals/app-state)無關。

使用者可以採取預防措施來防止 CSRF 弱點：

* 使用完畢後，請登出 web 應用程式。
* 定期清除瀏覽器 cookie 。

不過，CSRF 弱點基本上是 web 應用程式的問題，而不是終端使用者。

## <a name="authentication-fundamentals"></a>驗證基本概念

Cookie以驗證為基礎的驗證是一種常見的驗證形式。 以權杖為基礎的驗證系統越來越普及，特別是針對單一頁面應用程式 (Spa) 。

### <a name="no-loccookie-based-authentication"></a>Cookie以驗證為基礎

當使用者使用其使用者名稱和密碼進行驗證時，就會發出權杖，其中包含可用於驗證和授權的驗證票證。 權杖會儲存為 cookie 每個用戶端所發出的要求。 產生和驗證此程式 cookie 是由 Cookie 驗證中介軟體所執行。 [中介軟體](xref:fundamentals/middleware/index)會將使用者主體序列化為加密 cookie 。 在後續的要求中，中介軟體會驗證、重新建立 cookie 主體，並將主體指派給[HttpCoNtext](/dotnet/api/microsoft.aspnetcore.http.httpcontext)的[使用者](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)屬性。

### <a name="token-based-authentication"></a>權杖式驗證

當使用者經過驗證時，就會發出權杖， (不是 antiforgery 權杖) 。 權杖包含 [宣告](/dotnet/framework/security/claims-based-identity-model) 形式的使用者資訊，或將應用程式指向應用程式中維護之使用者狀態的參考權杖。 當使用者嘗試存取需要驗證的資源時，權杖會以持有人權杖形式以其他授權標頭的形式傳送至應用程式。 這會讓應用程式成為無狀態。 在每個後續要求中，權杖會在伺服器端驗證的要求中傳遞。 此權杖未 *加密*;它經過 *編碼*。 在伺服器上，權杖會解碼以存取其資訊。 若要在後續要求時傳送權杖，請將權杖儲存在瀏覽器的本機儲存體中。 如果權杖儲存在瀏覽器的本機儲存體中，請不要擔心 CSRF 弱點。 當令牌儲存在中時，CSRF 是一項考慮 cookie 。 如需詳細資訊，請參閱 GitHub 問題 [SPA 程式碼範例 cookie 會新增兩個](https://github.com/dotnet/AspNetCore.Docs/issues/13369)。

### <a name="multiple-apps-hosted-at-one-domain"></a>裝載于一個網域的多個應用程式

共用裝載環境容易遭受會話劫持、登入 CSRF 和其他攻擊。

雖然 `example1.contoso.net` 和 `example2.contoso.net` 是不同的主機，但是網域下的主機之間會有隱含的信任關係 `*.contoso.net` 。 此隱含信任關聯性可讓可能不受信任的主機影響彼此， cookie (管理 AJAX 要求的相同原始原則不一定適用于 HTTP cookie s) 。

在相同網域上裝載的應用程式之間惡意探索受信任的攻擊，並 cookie 不會共用網域來防止攻擊。 當每個應用程式裝載于自己的網域時，不會有隱含的 cookie 信任關係可進行攻擊。

## <a name="aspnet-core-antiforgery-configuration"></a>ASP.NET Core antiforgery 設定

> [!WARNING]
> ASP.NET Core 使用 [ASP.NET Core 資料保護](xref:security/data-protection/introduction)來實行 antiforgery。 資料保護堆疊必須設定為可在伺服器陣列中運作。 如需詳細資訊，請參閱設定 [資料保護](xref:security/data-protection/configuration/overview) 。

::: moniker range=">= aspnetcore-3.0"

在中呼叫下列其中一個 Api 時，會將 Antiforgery 中介軟體新增至相依性 [插入](xref:fundamentals/dependency-injection) 容器中 `Startup.ConfigureServices` ：

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

當呼叫時，Antiforgery 中介軟體會新增至相依性[插入](xref:fundamentals/dependency-injection)容器 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>`Startup.ConfigureServices`

::: moniker-end

在 ASP.NET Core 2.0 或更新版本中， [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) 會將 antiforgery token 插入至 HTML 表單元素。 檔案中的下列標記 Razor 會自動產生 antiforgery 權杖：

```cshtml
<form method="post">
    ...
</form>
```

同樣地，如果表單的方法未取得，則 [IHtmlHelper](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) 預設會產生 antiforgery 權杖。

當 `<form>` 標籤包含 `method="post"` 屬性，且下列其中一個條件成立時，就會自動產生 HTML form 元素的 antiforgery token：

* Action 屬性是空的 (`action=""`) 。
*  () 未提供 action 屬性 `<form method="post">` 。

您可以停用 HTML 表單元素的自動產生 antiforgery token：

* 使用屬性明確停用 antiforgery 權杖 `asp-antiforgery` ：

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* 您可以使用標籤協助程式 [！退出符號](xref:mvc/views/tag-helpers/intro#opt-out)，選擇不使用標記協助程式的表單元素：

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* `FormTagHelper`從 view 中移除。 您 `FormTagHelper` 可以藉由將下列指示詞新增至視圖，從視圖中移除 Razor ：

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> 系統會自動保護[ Razor 頁面](xref:razor-pages/index)的 XSRF/CSRF。 如需詳細資訊，請參閱 [XSRF/CSRF 和 Razor Pages](xref:razor-pages/index#xsrf)。

防禦 CSRF 攻擊最常見的方法是使用 (STP) 的 *同步器權杖模式* 。 當使用者要求具有表單資料的頁面時，會使用 STP：

1. 伺服器會將與目前使用者的身分識別相關聯的權杖傳送給用戶端。
1. 用戶端會將權杖傳送回伺服器進行驗證。
1. 如果伺服器收到的權杖不符合已驗證使用者的身分識別，則會拒絕該要求。

權杖是唯一且無法預期的。 此權杖也可以用來確保一連串要求的正確排序 (例如，確定要求順序：頁面 1 > 第2頁 > 第3頁) 。 ASP.NET Core MVC 和 Pages 範本中的所有表單都會 Razor 產生 antiforgery 權杖。 下列對等的 view 範例會產生 antiforgery 權杖：

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

明確地將 antiforgery token 新增至專案，而不使用標籤協助程式搭配 `<form>` HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) ：

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

在上述每個案例中，ASP.NET Core 加入隱藏的表單欄位，如下所示：

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

ASP.NET Core 包含使用 antiforgery 權杖的三個 [篩選](xref:mvc/controllers/filters) 條件：

* [ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a>Antiforgery 選項

自訂中的 [antiforgery 選項](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) `Startup.ConfigureServices` ：

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

&dagger;`Cookie`使用[ Cookie Builder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder)類別的屬性來設定 antiforgery 屬性。

| 選項 | 描述 |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | 決定用來建立 antiforgery 的設定 cookie 。 |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Antiforgery 系統用來在 views 中轉譯 antiforgery token 之隱藏表單欄位的名稱。 |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Antiforgery 系統所使用的標頭名稱。 如果 `null` 為，則系統只會考慮表單資料。 |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | 指定是否隱藏標頭的產生 `X-Frame-Options` 。 依預設，會產生值為 "SAMEORIGIN" 的標頭。 預設為 `false`。 |

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

| 選項 | 描述 |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | 決定用來建立 antiforgery 的設定 cookie 。 |
| [Cookie網域](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | 的網域 cookie 。 預設為 `null`。 這個屬性已過時，將在未來的版本中移除。 建議的替代方式是 Cookie 。域。 |
| [Cookie名字](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | cookie 的名稱。 如果未設定，系統會產生以 [預設 Cookie 前置](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) 詞 ( "開頭的唯一名稱。AspNetCore. Antiforgery. ") 。 這個屬性已過時，將在未來的版本中移除。 建議的替代方式是 Cookie 。名字。 |
| [Cookie路徑](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | 在上設定的路徑 cookie 。 這個屬性已過時，將在未來的版本中移除。 建議的替代方式是 Cookie 。路徑。 |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Antiforgery 系統用來在 views 中轉譯 antiforgery token 之隱藏表單欄位的名稱。 |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Antiforgery 系統所使用的標頭名稱。 如果 `null` 為，則系統只會考慮表單資料。 |
| [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | 指定 antiforgery 系統是否需要 HTTPS。 如果 `true` 為，則非 HTTPS 要求會失敗。 預設為 `false`。 這個屬性已過時，將在未來的版本中移除。 建議的替代方式是設定 Cookie 。SecurePolicy. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | 指定是否隱藏標頭的產生 `X-Frame-Options` 。 依預設，會產生值為 "SAMEORIGIN" 的標頭。 預設為 `false`。 |

::: moniker-end

如需詳細資訊，請參閱[ Cookie AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions)。

## <a name="configure-antiforgery-features-with-iantiforgery"></a>使用 IAntiforgery 設定 antiforgery 功能

[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) 會提供 API 來設定 antiforgery 功能。 `IAntiforgery` 可以在類別的方法中要求 `Configure` `Startup` 。 下列範例會從應用程式的首頁使用中介軟體來產生 antiforgery 權杖，並 cookie 使用本主題稍後所述的預設角度命名慣例，以 (的方式將它傳送至回應中) ：

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

### <a name="require-antiforgery-validation"></a>需要 antiforgery 驗證

[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) 是可套用至個別動作、控制器或全域的動作篩選準則。 除非要求包含有效的 antiforgery token，否則會封鎖對套用此篩選的動作提出的要求。

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

`ValidateAntiForgeryToken`屬性需要權杖來要求它所標記的動作方法，包括 HTTP GET 要求。 如果屬性套用至 `ValidateAntiForgeryToken` 應用程式的控制器，則可以使用屬性來覆寫它 `IgnoreAntiforgeryToken` 。

> [!NOTE]
> ASP.NET Core 不支援新增 antiforgery 權杖來自動取得要求。

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a>只自動驗證不安全 HTTP 方法的 antiforgery 權杖

ASP.NET Core 的應用程式不會產生安全 HTTP 方法的 antiforgery 權杖， (GET、HEAD、OPTIONS 和 TRACE) 。 您 `ValidateAntiForgeryToken` `IgnoreAntiforgeryToken` 可以使用 [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) 屬性，而不是廣泛地套用屬性，然後以屬性覆寫它。 這個屬性與屬性的運作方式完全相同 `ValidateAntiForgeryToken` ，不同之處在于，它不需要權杖來處理使用下列 HTTP 方法所提出的要求：

* GET
* HEAD
* OPTIONS
* TRACE

我們建議您 `AutoValidateAntiforgeryToken` 廣泛使用非 API 案例。 這可確保預設會保護張貼動作。 替代方式是除非套用至個別的動作方法，否則預設會忽略 antiforgery 權杖 `ValidateAntiForgeryToken` 。 在此案例中，更有可能會將 POST 動作方法保持不受保護，讓應用程式容易遭受 CSRF 攻擊。 所有文章都應該傳送 antiforgery token。

Api 沒有自動傳送權杖的非部分的機制 cookie 。 執行可能取決於用戶端程式代碼的執行。 以下顯示一些範例：

類別層級範例：

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

全域範例：

::: moniker range="< aspnetcore-3.0"

服務。>addmvc (選項 => 選項。篩選。加入 (new AutoValidateAntiforgeryTokenAttribute ( # A3 # A4 # A5;

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a>覆寫全域或控制器 antiforgery 屬性

[IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)篩選準則可用來消除指定動作 (或控制器) 的 antiforgery token 需求。 套用時，此篩選準則會覆寫 `ValidateAntiForgeryToken` `AutoValidateAntiforgeryToken` 在較高層級指定， (全域或控制器) 上指定。

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

## <a name="refresh-tokens-after-authentication"></a>驗證後重新整理權杖

藉由將使用者重新導向至 [view] 或 [Pages] 頁面，即可在使用者通過驗證之後重新整理權杖 Razor 。

## <a name="javascript-ajax-and-spas"></a>JavaScript、AJAX 和 Spa

在傳統的 HTML 應用程式中，antiforgery 權杖會使用隱藏的表單欄位傳遞到伺服器。 在新式以 JavaScript 為基礎的應用程式和 Spa 中，許多要求都會以程式設計的方式進行。 這些 AJAX 要求可能會使用其他技術 (例如要求標頭或 cookie s) 傳送權杖。

如果 cookie 使用來儲存驗證權杖，以及驗證服務器上的 API 要求，CSRF 就是潛在的問題。 如果使用本機儲存體來儲存權杖，可能會緩和 CSRF 弱點，因為來自本機儲存體的值不會在每次要求時自動傳送至伺服器。 因此，使用本機儲存體將 antiforgery token 儲存在用戶端上，並以要求標頭的形式傳送權杖是建議的方法。

### <a name="javascript"></a>JavaScript

使用 JavaScript 搭配 views，即可使用視圖內的服務來建立權杖。 將 [AspNetCore. Antiforgery. IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) 服務插入至 view 並呼叫 [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens)：

[!code-cshtml[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

這種方法不需要直接處理 cookie 來自伺服器的設定，或從用戶端讀取。

上述範例使用 JavaScript 來讀取 AJAX POST 標頭的隱藏欄位值。

JavaScript 也可以存取中的權杖 cookie ，並使用的 cookie 內容來建立具有權杖值的標頭。

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

假設腳本要求在名為的標頭中傳送權杖 `X-CSRF-TOKEN` ，請設定 antiforgery 服務以尋找 `X-CSRF-TOKEN` 標頭：

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

下列範例使用 JavaScript，以適當的標頭提出 AJAX 要求：

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

### <a name="angularjs"></a>AngularJS

AngularJS 使用慣例來處理 CSRF。 如果伺服器以名稱傳送 cookie `XSRF-TOKEN` ，AngularJS `$http` 服務 cookie 會在將要求傳送至伺服器時，將值加入至標頭。 此程式為自動。 不需要在用戶端明確設定標頭。 標頭名稱為 `X-XSRF-TOKEN` 。 伺服器應該偵測到此標頭，並驗證其內容。

ASP.NET Core API 在應用程式啟動時使用此慣例：

* 設定您的應用程式，以在呼叫的中提供權杖 cookie `XSRF-TOKEN` 。
* 設定 antiforgery 服務以尋找名為的標頭 `X-XSRF-TOKEN` 。

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

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="extend-antiforgery"></a>擴充 antiforgery

[IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider)型別可讓開發人員藉由在每個權杖中來回往返額外資料，來擴充反 CSRF 系統的行為。 每次產生欄位 token 時都會呼叫 [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) 方法，並將傳回值內嵌在產生的權杖內。 實施者可以傳回時間戳記、nonce 或任何其他值，然後在驗證權杖時呼叫 [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) 來驗證此資料。 用戶端的使用者名稱已內嵌在產生的權杖中，因此不需要包含這項資訊。 如果權杖包含補充資料但未 `IAntiForgeryAdditionalDataProvider` 設定，則不會驗證補充資料。

## <a name="additional-resources"></a>其他資源

* [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) 開啟的 [Web 應用程式安全性專案](https://www.owasp.org/index.php/Main_Page) (OWASP) 。
* <xref:host-and-deploy/web-farm>
