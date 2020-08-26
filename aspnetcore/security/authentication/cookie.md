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
ms.openlocfilehash: 48b9c41b468f04134164a9c499e7fadca107cab2
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865112"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a>使用 cookie 驗證但不使用 ASP.NET Core Identity

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core Identity 是完整的完整功能驗證提供者，可用於建立和維護登入。 但是， cookie 不能使用不使用的驗證提供者 ASP.NET Core Identity 。 如需詳細資訊，請參閱<xref:security/authentication/identity>。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

為了示範範例應用程式中的目的，假設使用者的使用者帳戶（Maria Rodriguez）會硬式編碼到應用程式中。 使用 **電子郵件** 位址 `maria.rodriguez@contoso.com` 和任何密碼來登入使用者。 使用者會在 `AuthenticateUser` *頁面/帳戶/登入 .cs* 檔案的方法中進行驗證。 在真實世界的範例中，使用者會針對資料庫進行驗證。

## <a name="configuration"></a>組態

如果應用程式不使用 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app)，請在專案檔中建立 AspNetCore 的封裝參考 [。 Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) 套件。

在 `Startup.ConfigureServices` 方法中，使用和方法建立驗證中介軟體 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 服務 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ：

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> 傳遞以 `AddAuthentication` 設定應用程式的預設驗證配置。 `AuthenticationScheme` 當有多個 cookie 驗證實例，而且您想要 [使用特定配置來授權](xref:security/authorization/limitingidentitybyscheme)時，這會很有用。 將設定 `AuthenticationScheme` 為[ Cookie AuthenticationDefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)時，AuthenticationScheme 會為配置提供值 " Cookie s"。 您可以提供任何區分配置的字串值。

應用程式的驗證配置不同于應用程式的 cookie 驗證配置。 當 cookie 未提供驗證配置時 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ，它會使用 `CookieAuthenticationDefaults.AuthenticationScheme` ( " Cookie s" ) 。

驗證 cookie 的 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 屬性預設會設定為 `true` 。 cookie當網站訪客未同意資料收集時，允許驗證。 如需詳細資訊，請參閱<xref:security/gdpr#essential-cookies>。

在中 `Startup.Configure` ，呼叫 `UseAuthentication` 和 `UseAuthorization` 來設定 `HttpContext.User` 屬性，並針對要求執行授權中介軟體。 呼叫 `UseAuthentication` 和方法之前，請先呼叫和 `UseAuthorization` 方法 `UseEndpoints` ：

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>類別是用來設定驗證提供者選項。

在 `CookieAuthenticationOptions` 服務設定中，于方法中設定驗證 `Startup.ConfigureServices` ：

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a>Cookie 原則中介軟體

[ Cookie 原則中介軟體](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)可啟用 cookie 原則功能。 將中介軟體新增至應用程式處理管線的順序是機密的， &mdash; 它只會影響在管線中註冊的下游元件。

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> Cookie cookie cookie 當附加或刪除時，請使用提供給原則中介軟體來控制處理與處理處理常式的全域特性 cookie 。

預設 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 值是 `SameSiteMode.Lax` 允許 OAuth2 authentication。 若要嚴格地強制執行相同的網站原則 `SameSiteMode.Strict` ，請設定 `MinimumSameSitePolicy` 。 雖然這項設定會中斷 OAuth2 和其他的跨原始來源驗證配置，但它會提高 cookie 不依賴跨原始來源要求處理的其他類型應用程式的安全性層級。

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

的 Cookie 原則中介軟體設定， `MinimumSameSitePolicy` 可能會影響 `Cookie.SameSite` 下列矩陣中的設定 `CookieAuthenticationOptions` 。

| MinimumSameSitePolicy | Cookie.SameSite | 結果 Cookie 。SameSite 設定 |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode。無     | SameSiteMode。無<br>SameSiteMode 不嚴格<br>SameSiteMode 嚴謹 | SameSiteMode。無<br>SameSiteMode 不嚴格<br>SameSiteMode 嚴謹 |
| SameSiteMode 不嚴格      | SameSiteMode。無<br>SameSiteMode 不嚴格<br>SameSiteMode 嚴謹 | SameSiteMode 不嚴格<br>SameSiteMode 不嚴格<br>SameSiteMode 嚴謹 |
| SameSiteMode 嚴謹   | SameSiteMode。無<br>SameSiteMode 不嚴格<br>SameSiteMode 嚴謹 | SameSiteMode 嚴謹<br>SameSiteMode 嚴謹<br>SameSiteMode 嚴謹 |

## <a name="create-an-authentication-no-loccookie"></a>建立驗證 cookie

若要建立 cookie 保存使用者資訊，請建立 <xref:System.Security.Claims.ClaimsPrincipal> 。 使用者資訊會序列化並儲存在中 cookie 。 

<xref:System.Security.Claims.ClaimsIdentity>使用任何必要的 <xref:System.Security.Claims.Claim> 來建立，並呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 以登入使用者：

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

`SignInAsync` 建立加密的 cookie ，並將它新增至目前的回應。 如果 `AuthenticationScheme` 未指定，則會使用預設配置。

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> 預設只會用於一些特定的路徑，例如登入路徑和登出路徑。 如需詳細資訊，請參閱[ Cookie AuthenticationHandler 來源](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334)。

ASP.NET Core 的 [資料保護](xref:security/data-protection/using-data-protection) 系統用於加密。 若為裝載于多部電腦上的應用程式、跨應用程式的負載平衡，或使用 web 伺服陣列，請 [將資料保護設定](xref:security/data-protection/configuration/overview) 為使用相同的金鑰通道和應用程式識別碼。

## <a name="sign-out"></a>登出

若要登出目前的使用者並刪除其 cookie ，請呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ：

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

如果 `CookieAuthenticationDefaults.AuthenticationScheme` 未使用 (或 " Cookie s" ) 作為配置 (例如 "Contoso Cookie " ) ，請提供設定驗證提供者時所使用的配置。 否則，會使用預設配置。

## <a name="react-to-back-end-changes"></a>回應後端變更

一旦 cookie 建立之後， cookie 就會是身分識別的單一來源。 如果在後端系統中停用使用者帳戶：

* 應用程式的 cookie 驗證系統會根據驗證繼續處理要求 cookie 。
* 只要驗證有效，使用者就會一直登入應用程式 cookie 。

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>事件可以用來攔截和覆寫身分識別的驗證 cookie 。 cookie在每個要求上進行驗證，可降低撤銷存取應用程式之使用者的風險。

驗證的其中一個方法 cookie 是以追蹤使用者資料庫變更的時間為基礎。 如果自使用者發出後資料庫尚未變更，則 cookie 不需要重新驗證使用者（如果其 cookie 仍然有效）。 在範例應用程式中，資料庫會在中執行， `IUserRepository` 並儲存 `LastChanged` 值。 當使用者在資料庫中更新時， `LastChanged` 值會設定為目前的時間。

若要使 cookie 資料庫根據值變更時失效 `LastChanged` ，請 cookie 使用 `LastChanged` 包含 `LastChanged` 資料庫目前值的宣告來建立。

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

若要執行事件的覆寫 `ValidatePrincipal` ，請在衍生自的類別中撰寫具有下列簽章的方法 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> ：

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

以下是的範例執行 `CookieAuthenticationEvents` ：

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

cookie在方法的服務註冊期間註冊事件實例 `Startup.ConfigureServices` 。 為您的類別提供 [範圍服務註冊](xref:fundamentals/dependency-injection#service-lifetimes) `CustomCookieAuthenticationEvents` ：

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

假設有一種情況，使用者的名稱會以 &mdash; 任何方式來更新不會影響安全性的決策。 如果您想要以非破壞性的更新使用者主體，請呼叫 `context.ReplacePrincipal` 並將 `context.ShouldRenew` 屬性設為 `true` 。

> [!WARNING]
> 此處所述的方法會在每個要求上觸發。 針對 cookie 每個要求的所有使用者驗證驗證，可能會導致應用程式的效能大幅下降。

## <a name="persistent-no-loccookies"></a>持續性 cookie s

您可能想要在 cookie 瀏覽器會話之間保存。 只有在登入或類似的機制中，以明確的使用者同意來啟用此持續性，才能使用 [記住我] 核取方塊。 

下列程式碼片段會建立身分識別，並 cookie 透過瀏覽器關閉來對應繼續生存。 系統會接受先前設定的任何滑動到期設定。 如果在 cookie 瀏覽器關閉時過期，則瀏覽器會在 cookie 重新開機後清除。

設定 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> 為 `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ：

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

## <a name="absolute-no-loccookie-expiration"></a>絕對 cookie 到期

您可以使用設定絕對到期時間 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> 。 若要建立持續性 cookie ， `IsPersistent` 也必須設定。 否則， cookie 會以會話為基礎的存留期建立，而且可能會在其保留的驗證票證之前或之後過期。 當 `ExpiresUtc` 設定時，它會覆寫之 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> 選項的值 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> （如果有設定的話）。

下列程式碼片段會建立一個 cookie 會持續20分鐘的身分識別。 這會忽略先前設定的任何滑動到期設定。

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

ASP.NET Core Identity 是完整的完整功能驗證提供者，可用於建立和維護登入。 但是， cookie 不能使用不使用的驗證提供者 ASP.NET Core Identity 。 如需詳細資訊，請參閱<xref:security/authentication/identity>。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

為了示範範例應用程式中的目的，假設使用者的使用者帳戶（Maria Rodriguez）會硬式編碼到應用程式中。 使用 **電子郵件** 位址 `maria.rodriguez@contoso.com` 和任何密碼來登入使用者。 使用者會在 `AuthenticateUser` *頁面/帳戶/登入 .cs* 檔案的方法中進行驗證。 在真實世界的範例中，使用者會針對資料庫進行驗證。

## <a name="configuration"></a>組態

如果應用程式不使用 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app)，請在專案檔中建立 AspNetCore 的封裝參考 [。 Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) 套件。

在 `Startup.ConfigureServices` 方法中，使用和方法建立驗證中介軟體 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 服務 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ：

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> 傳遞以 `AddAuthentication` 設定應用程式的預設驗證配置。 `AuthenticationScheme` 當有多個 cookie 驗證實例，而且您想要 [使用特定配置來授權](xref:security/authorization/limitingidentitybyscheme)時，這會很有用。 將設定 `AuthenticationScheme` 為[ Cookie AuthenticationDefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)時，AuthenticationScheme 會為配置提供值 " Cookie s"。 您可以提供任何區分配置的字串值。

應用程式的驗證配置不同于應用程式的 cookie 驗證配置。 當 cookie 未提供驗證配置時 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ，它會使用 `CookieAuthenticationDefaults.AuthenticationScheme` ( " Cookie s" ) 。

驗證 cookie 的 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 屬性預設會設定為 `true` 。 cookie當網站訪客未同意資料收集時，允許驗證。 如需詳細資訊，請參閱<xref:security/gdpr#essential-cookies>。

在 `Startup.Configure` 方法中，呼叫 `UseAuthentication` 方法以叫用設定屬性的驗證中介軟體 `HttpContext.User` 。 呼叫 `UseAuthentication` 或之前，請先呼叫方法 `UseMvcWithDefaultRoute` `UseMvc` ：

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>類別是用來設定驗證提供者選項。

在 `CookieAuthenticationOptions` 服務設定中，于方法中設定驗證 `Startup.ConfigureServices` ：

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a>Cookie 原則中介軟體

[ Cookie 原則中介軟體](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)可啟用 cookie 原則功能。 將中介軟體新增至應用程式處理管線的順序是機密的， &mdash; 它只會影響在管線中註冊的下游元件。

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> Cookie cookie cookie 當附加或刪除時，請使用提供給原則中介軟體來控制處理與處理處理常式的全域特性 cookie 。

預設 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 值是 `SameSiteMode.Lax` 允許 OAuth2 authentication。 若要嚴格地強制執行相同的網站原則 `SameSiteMode.Strict` ，請設定 `MinimumSameSitePolicy` 。 雖然這項設定會中斷 OAuth2 和其他的跨原始來源驗證配置，但它會提高 cookie 不依賴跨原始來源要求處理的其他類型應用程式的安全性層級。

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

的 Cookie 原則中介軟體設定， `MinimumSameSitePolicy` 可能會影響 `Cookie.SameSite` 下列矩陣中的設定 `CookieAuthenticationOptions` 。

| MinimumSameSitePolicy | Cookie.SameSite | 結果 Cookie 。SameSite 設定 |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode。無     | SameSiteMode。無<br>SameSiteMode 不嚴格<br>SameSiteMode 嚴謹 | SameSiteMode。無<br>SameSiteMode 不嚴格<br>SameSiteMode 嚴謹 |
| SameSiteMode 不嚴格      | SameSiteMode。無<br>SameSiteMode 不嚴格<br>SameSiteMode 嚴謹 | SameSiteMode 不嚴格<br>SameSiteMode 不嚴格<br>SameSiteMode 嚴謹 |
| SameSiteMode 嚴謹   | SameSiteMode。無<br>SameSiteMode 不嚴格<br>SameSiteMode 嚴謹 | SameSiteMode 嚴謹<br>SameSiteMode 嚴謹<br>SameSiteMode 嚴謹 |

## <a name="create-an-authentication-no-loccookie"></a>建立驗證 cookie

若要建立 cookie 保存使用者資訊，請建立 <xref:System.Security.Claims.ClaimsPrincipal> 。 使用者資訊會序列化並儲存在中 cookie 。 

<xref:System.Security.Claims.ClaimsIdentity>使用任何必要的 <xref:System.Security.Claims.Claim> 來建立，並呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 以登入使用者：

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync` 建立加密的 cookie ，並將它新增至目前的回應。 如果 `AuthenticationScheme` 未指定，則會使用預設配置。

ASP.NET Core 的 [資料保護](xref:security/data-protection/using-data-protection) 系統用於加密。 若為裝載于多部電腦上的應用程式、跨應用程式的負載平衡，或使用 web 伺服陣列，請 [將資料保護設定](xref:security/data-protection/configuration/overview) 為使用相同的金鑰通道和應用程式識別碼。

## <a name="sign-out"></a>登出

若要登出目前的使用者並刪除其 cookie ，請呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ：

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

如果 `CookieAuthenticationDefaults.AuthenticationScheme` 未使用 (或 " Cookie s" ) 作為配置 (例如 "Contoso Cookie " ) ，請提供設定驗證提供者時所使用的配置。 否則，會使用預設配置。

## <a name="react-to-back-end-changes"></a>回應後端變更

一旦 cookie 建立之後， cookie 就會是身分識別的單一來源。 如果在後端系統中停用使用者帳戶：

* 應用程式的 cookie 驗證系統會根據驗證繼續處理要求 cookie 。
* 只要驗證有效，使用者就會一直登入應用程式 cookie 。

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>事件可以用來攔截和覆寫身分識別的驗證 cookie 。 cookie在每個要求上進行驗證，可降低撤銷存取應用程式之使用者的風險。

驗證的其中一個方法 cookie 是以追蹤使用者資料庫變更的時間為基礎。 如果自使用者發出後資料庫尚未變更，則 cookie 不需要重新驗證使用者（如果其 cookie 仍然有效）。 在範例應用程式中，資料庫會在中執行， `IUserRepository` 並儲存 `LastChanged` 值。 當使用者在資料庫中更新時， `LastChanged` 值會設定為目前的時間。

若要使 cookie 資料庫根據值變更時失效 `LastChanged` ，請 cookie 使用 `LastChanged` 包含 `LastChanged` 資料庫目前值的宣告來建立。

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

若要執行事件的覆寫 `ValidatePrincipal` ，請在衍生自的類別中撰寫具有下列簽章的方法 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> ：

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

以下是的範例執行 `CookieAuthenticationEvents` ：

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

cookie在方法的服務註冊期間註冊事件實例 `Startup.ConfigureServices` 。 為您的類別提供 [範圍服務註冊](xref:fundamentals/dependency-injection#service-lifetimes) `CustomCookieAuthenticationEvents` ：

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

假設有一種情況，使用者的名稱會以 &mdash; 任何方式來更新不會影響安全性的決策。 如果您想要以非破壞性的更新使用者主體，請呼叫 `context.ReplacePrincipal` 並將 `context.ShouldRenew` 屬性設為 `true` 。

> [!WARNING]
> 此處所述的方法會在每個要求上觸發。 針對 cookie 每個要求的所有使用者驗證驗證，可能會導致應用程式的效能大幅下降。

## <a name="persistent-no-loccookies"></a>持續性 cookie s

您可能想要在 cookie 瀏覽器會話之間保存。 只有在登入或類似的機制中，以明確的使用者同意來啟用此持續性，才能使用 [記住我] 核取方塊。 

下列程式碼片段會建立身分識別，並 cookie 透過瀏覽器關閉來對應繼續生存。 系統會接受先前設定的任何滑動到期設定。 如果在 cookie 瀏覽器關閉時過期，則瀏覽器會在 cookie 重新開機後清除。

設定 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> 為 `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ：

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

## <a name="absolute-no-loccookie-expiration"></a>絕對 cookie 到期

您可以使用設定絕對到期時間 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> 。 若要建立持續性 cookie ， `IsPersistent` 也必須設定。 否則， cookie 會以會話為基礎的存留期建立，而且可能會在其保留的驗證票證之前或之後過期。 當 `ExpiresUtc` 設定時，它會覆寫之 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> 選項的值 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> （如果有設定的話）。

下列程式碼片段會建立一個 cookie 會持續20分鐘的身分識別。 這會忽略先前設定的任何滑動到期設定。

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

## <a name="additional-resources"></a>其他資源

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [以原則為基礎的角色檢查](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
