---
title: 在 cookie 不 ASP.NET Core 的情況下使用驗證Identity
author: rick-anderson
description: 瞭解如何在 cookie 不 ASP.NET Core 的情況下使用驗證 Identity 。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
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
uid: security/authentication/cookie
ms.openlocfilehash: 325bc3dcd48517d93d5f6f2d56e92651c780d759
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021883"
---
# <a name="use-no-loccookie-authentication-without-aspnet-core-no-locidentity"></a>在 cookie 不 ASP.NET Core 的情況下使用驗證Identity

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core Identity 是完整的完整功能驗證提供者，可用於建立和維護登入。 不過，您 cookie 可以使用不含 ASP.NET Core 的驗證提供者 Identity 。 如需詳細資訊，請參閱<xref:security/authentication/identity>。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

針對範例應用程式中的示範用途，假設使用者的使用者帳戶（Maria Rodriguez）已硬式編碼到應用程式中。 使用 [**電子郵件**位址] `maria.rodriguez@contoso.com` 和 [任何密碼] 來登入使用者。 使用者會在 `AuthenticateUser` *頁面/帳戶/登入. cshtml .cs*檔案的方法中進行驗證。 在真實世界的範例中，使用者會針對資料庫進行驗證。

## <a name="configuration"></a>組態

如果應用程式不使用[AspNetCore 中繼套件](xref:fundamentals/metapackage-app)，請在 AspNetCore 的專案檔中建立套件參考[。 Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)套件。

在 `Startup.ConfigureServices` 方法中，使用和方法建立驗證中介軟體 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 服務 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ：

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>傳遞以 `AddAuthentication` 設定應用程式的預設驗證配置。 `AuthenticationScheme`當有多個 cookie 驗證實例，而您想要以[特定的配置進行授權](xref:security/authorization/limitingidentitybyscheme)時，會很有用。 將設定 `AuthenticationScheme` 為[ Cookie AuthenticationDefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)時，AuthenticationScheme 會 Cookie 為配置提供 "s" 的值。 您可以提供可區分配置的任何字串值。

應用程式的驗證配置與應用程式的 cookie 驗證配置不同。 cookie未提供驗證配置給時 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ，它會使用 `CookieAuthenticationDefaults.AuthenticationScheme` ( " Cookie s" ) 。

cookie根據預設，驗證的 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 屬性會設定為 `true` 。 cookie當網站訪客未同意資料收集時，允許驗證。 如需詳細資訊，請參閱<xref:security/gdpr#essential-cookies>。

在中 `Startup.Configure` ，呼叫 `UseAuthentication` 和， `UseAuthorization` 以設定 `HttpContext.User` 要求的屬性和執行授權中介軟體。 呼叫 `UseAuthentication` 和 `UseAuthorization` 方法，再呼叫 `UseEndpoints` ：

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>類別是用來設定驗證提供者選項。

在 `CookieAuthenticationOptions` 服務設定中，于方法中進行驗證 `Startup.ConfigureServices` ：

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a>Cookie原則中介軟體

[ Cookie 原則中介軟體](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)可啟用 cookie 原則功能。 將中介軟體新增至應用程式處理管線會區分順序， &mdash; 它只會影響在管線中註冊的下游元件。

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

使用 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> 提供給 Cookie 原則中介軟體，以控制 cookie cookie 當附加或刪除時，處理和連結處理處理常式的全域特性 cookie 。

預設 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 值是 `SameSiteMode.Lax` 允許 OAuth2 authentication。 若要嚴格地強制執行的相同網站原則 `SameSiteMode.Strict` ，請設定 `MinimumSameSitePolicy` 。 雖然這項設定會中斷 OAuth2 和其他跨原始來源驗證配置，但它會提升 cookie 不依賴跨原始來源要求處理之其他類型應用程式的安全性層級。

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

的 Cookie 原則中介軟體設定 `MinimumSameSitePolicy` 可以 `Cookie.SameSite` 根據下列矩陣，影響 [設定] 中的設定 `CookieAuthenticationOptions` 。

| MinimumSameSitePolicy | Cookie.SameSite | 結果 Cookie 。SameSite 設定 |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode。無     | SameSiteMode。無<br>SameSiteMode. 寬鬆<br>SameSiteMode。 Strict | SameSiteMode。無<br>SameSiteMode. 寬鬆<br>SameSiteMode。 Strict |
| SameSiteMode. 寬鬆      | SameSiteMode。無<br>SameSiteMode. 寬鬆<br>SameSiteMode。 Strict | SameSiteMode. 寬鬆<br>SameSiteMode. 寬鬆<br>SameSiteMode。 Strict |
| SameSiteMode。 Strict   | SameSiteMode。無<br>SameSiteMode. 寬鬆<br>SameSiteMode。 Strict | SameSiteMode。 Strict<br>SameSiteMode。 Strict<br>SameSiteMode。 Strict |

## <a name="create-an-authentication-no-loccookie"></a>建立驗證cookie

若要建立 cookie 保存使用者資訊，請建立 <xref:System.Security.Claims.ClaimsPrincipal> 。 使用者資訊會序列化並儲存在中 cookie 。 

<xref:System.Security.Claims.ClaimsIdentity>使用任何必要的 <xref:System.Security.Claims.Claim> 來建立，並呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 以登入使用者：

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

`SignInAsync`建立已加密的 cookie ，並將它新增至目前的回應。 如果 `AuthenticationScheme` 未指定，則會使用預設配置。

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri>根據預設，只會用於一些特定路徑，例如登入路徑和登出路徑。 如需詳細資訊，請參閱[ Cookie AuthenticationHandler 來源](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334)。

ASP.NET Core 的[資料保護](xref:security/data-protection/using-data-protection)系統用於加密。 若為裝載于多部電腦上的應用程式、跨應用程式的負載平衡，或使用 web 伺服陣列，請[將資料保護設定](xref:security/data-protection/configuration/overview)為使用相同的金鑰環形和應用程式識別碼。

## <a name="sign-out"></a>登出

若要登出目前的使用者並刪除其 cookie ，請呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ：

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

如果 `CookieAuthenticationDefaults.AuthenticationScheme` (或 " Cookie s" ) 不是用來做為配置 (例如 "Contoso Cookie " ) ，請提供設定驗證提供者時所使用的架構。 否則，會使用預設配置。

## <a name="react-to-back-end-changes"></a>對後端變更做出回應

一旦 cookie 建立之後， cookie 就是身分識別的單一來源。 如果在後端系統中停用使用者帳戶：

* 應用程式的 cookie 驗證系統會繼續根據驗證來處理要求 cookie 。
* 只要驗證有效，使用者就會繼續登入應用程式 cookie 。

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>事件可以用來攔截和覆寫身分識別的驗證 cookie 。 cookie在每個要求上驗證，可降低撤銷存取應用程式之使用者的風險。

驗證的其中一個方法 cookie 是以追蹤使用者資料庫變更的時間為基礎。 如果自從使用者發出之後，資料庫尚未變更 cookie ，則不需要重新驗證使用者（如果 cookie 仍然有效）。 在範例應用程式中，資料庫會在中實作為， `IUserRepository` 並儲存 `LastChanged` 值。 當資料庫中的使用者更新時，此 `LastChanged` 值會設為目前的時間。

若要在 cookie 資料庫根據值而變更時使失效 `LastChanged` ，請 cookie 使用 `LastChanged` 包含 `LastChanged` 資料庫目前值的宣告來建立：

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

cookie在方法的服務註冊期間註冊事件實例 `Startup.ConfigureServices` 。 為您的類別提供[限定範圍的服務註冊](xref:fundamentals/dependency-injection#service-lifetimes) `CustomCookieAuthenticationEvents` ：

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

假設使用者的名稱已更新 &mdash; 為不會以任何方式影響安全性的決策。 如果您想要以非破壞性的更新使用者主體，請呼叫， `context.ReplacePrincipal` 並將 `context.ShouldRenew` 屬性設定為 `true` 。

> [!WARNING]
> 這裡所述的方法會在每個要求上觸發。 cookie針對每個要求的所有使用者驗證驗證，可能會對應用程式造成很大的效能影響。

## <a name="persistent-no-loccookies"></a>持續性 cookie s

您可能想要在 cookie 瀏覽器會話之間保存。 只有在登入或類似的機制上有 [記住我] 核取方塊的明確使用者同意，才能啟用此持續性。 

下列程式碼片段會建立身分識別，並 cookie 透過瀏覽器結束來對應該不受。 系統會接受先前設定的任何滑動到期設定。 如果在 cookie 瀏覽器關閉時過期，瀏覽器會在 cookie 重新開機後清除。

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>在中，將設為 `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ：

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

絕對到期時間可以使用來設定 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> 。 若要建立持續性 cookie ， `IsPersistent` 也必須設定。 否則， cookie 會使用以會話為基礎的存留期來建立，而且可能會在它所保留的驗證票證之前或之後過期。 當 `ExpiresUtc` 設定時，它會覆寫 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> 選項的值 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> （如果已設定的話）。

下列程式碼片段會建立持續20分鐘的身分識別和對應 cookie 的。 這會忽略先前設定的任何滑動到期設定。

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

ASP.NET Core Identity 是完整的完整功能驗證提供者，可用於建立和維護登入。 不過，您 cookie 可以使用不含 ASP.NET Core 的驗證驗證提供者 Identity 。 如需詳細資訊，請參閱<xref:security/authentication/identity>。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

針對範例應用程式中的示範用途，假設使用者的使用者帳戶（Maria Rodriguez）已硬式編碼到應用程式中。 使用 [**電子郵件**位址] `maria.rodriguez@contoso.com` 和 [任何密碼] 來登入使用者。 使用者會在 `AuthenticateUser` *頁面/帳戶/登入. cshtml .cs*檔案的方法中進行驗證。 在真實世界的範例中，使用者會針對資料庫進行驗證。

## <a name="configuration"></a>組態

如果應用程式不使用[AspNetCore 中繼套件](xref:fundamentals/metapackage-app)，請在 AspNetCore 的專案檔中建立套件參考[。 Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)套件。

在 `Startup.ConfigureServices` 方法中，使用和方法建立驗證中介軟體 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 服務 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ：

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>傳遞以 `AddAuthentication` 設定應用程式的預設驗證配置。 `AuthenticationScheme`當有多個 cookie 驗證實例，而您想要以[特定的配置進行授權](xref:security/authorization/limitingidentitybyscheme)時，會很有用。 將設定 `AuthenticationScheme` 為[ Cookie AuthenticationDefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)時，AuthenticationScheme 會 Cookie 為配置提供 "s" 的值。 您可以提供可區分配置的任何字串值。

應用程式的驗證配置與應用程式的 cookie 驗證配置不同。 cookie未提供驗證配置給時 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ，它會使用 `CookieAuthenticationDefaults.AuthenticationScheme` ( " Cookie s" ) 。

cookie根據預設，驗證的 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 屬性會設定為 `true` 。 cookie當網站訪客未同意資料收集時，允許驗證。 如需詳細資訊，請參閱<xref:security/gdpr#essential-cookies>。

在 `Startup.Configure` 方法中，呼叫 `UseAuthentication` 方法來叫用設定屬性的驗證中介軟體 `HttpContext.User` 。 呼叫 `UseAuthentication` 或之前，請先呼叫方法 `UseMvcWithDefaultRoute` `UseMvc` ：

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>類別是用來設定驗證提供者選項。

在 `CookieAuthenticationOptions` 服務設定中，于方法中進行驗證 `Startup.ConfigureServices` ：

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a>Cookie原則中介軟體

[ Cookie 原則中介軟體](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)可啟用 cookie 原則功能。 將中介軟體新增至應用程式處理管線會區分順序， &mdash; 它只會影響在管線中註冊的下游元件。

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

使用 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> 提供給 Cookie 原則中介軟體，以控制 cookie cookie 當附加或刪除時，處理和連結處理處理常式的全域特性 cookie 。

預設 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 值是 `SameSiteMode.Lax` 允許 OAuth2 authentication。 若要嚴格地強制執行的相同網站原則 `SameSiteMode.Strict` ，請設定 `MinimumSameSitePolicy` 。 雖然這項設定會中斷 OAuth2 和其他跨原始來源驗證配置，但它會提升 cookie 不依賴跨原始來源要求處理之其他類型應用程式的安全性層級。

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

的 Cookie 原則中介軟體設定 `MinimumSameSitePolicy` 可以 `Cookie.SameSite` 根據下列矩陣，影響 [設定] 中的設定 `CookieAuthenticationOptions` 。

| MinimumSameSitePolicy | Cookie.SameSite | 結果 Cookie 。SameSite 設定 |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode。無     | SameSiteMode。無<br>SameSiteMode. 寬鬆<br>SameSiteMode。 Strict | SameSiteMode。無<br>SameSiteMode. 寬鬆<br>SameSiteMode。 Strict |
| SameSiteMode. 寬鬆      | SameSiteMode。無<br>SameSiteMode. 寬鬆<br>SameSiteMode。 Strict | SameSiteMode. 寬鬆<br>SameSiteMode. 寬鬆<br>SameSiteMode。 Strict |
| SameSiteMode。 Strict   | SameSiteMode。無<br>SameSiteMode. 寬鬆<br>SameSiteMode。 Strict | SameSiteMode。 Strict<br>SameSiteMode。 Strict<br>SameSiteMode。 Strict |

## <a name="create-an-authentication-no-loccookie"></a>建立驗證cookie

若要建立 cookie 保存使用者資訊，請建立 <xref:System.Security.Claims.ClaimsPrincipal> 。 使用者資訊會序列化並儲存在中 cookie 。 

<xref:System.Security.Claims.ClaimsIdentity>使用任何必要的 <xref:System.Security.Claims.Claim> 來建立，並呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 以登入使用者：

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync`建立已加密的 cookie ，並將它新增至目前的回應。 如果 `AuthenticationScheme` 未指定，則會使用預設配置。

ASP.NET Core 的[資料保護](xref:security/data-protection/using-data-protection)系統用於加密。 若為裝載于多部電腦上的應用程式、跨應用程式的負載平衡，或使用 web 伺服陣列，請[將資料保護設定](xref:security/data-protection/configuration/overview)為使用相同的金鑰環形和應用程式識別碼。

## <a name="sign-out"></a>登出

若要登出目前的使用者並刪除其 cookie ，請呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ：

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

如果 `CookieAuthenticationDefaults.AuthenticationScheme` (或 " Cookie s" ) 不是用來做為配置 (例如 "Contoso Cookie " ) ，請提供設定驗證提供者時所使用的架構。 否則，會使用預設配置。

## <a name="react-to-back-end-changes"></a>對後端變更做出回應

一旦 cookie 建立之後， cookie 就是身分識別的單一來源。 如果在後端系統中停用使用者帳戶：

* 應用程式的 cookie 驗證系統會繼續根據驗證來處理要求 cookie 。
* 只要驗證有效，使用者就會繼續登入應用程式 cookie 。

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>事件可以用來攔截和覆寫身分識別的驗證 cookie 。 cookie在每個要求上驗證，可降低撤銷存取應用程式之使用者的風險。

驗證的其中一個方法 cookie 是以追蹤使用者資料庫變更的時間為基礎。 如果自從使用者發出之後，資料庫尚未變更 cookie ，則不需要重新驗證使用者（如果 cookie 仍然有效）。 在範例應用程式中，資料庫會在中實作為， `IUserRepository` 並儲存 `LastChanged` 值。 當資料庫中的使用者更新時，此 `LastChanged` 值會設為目前的時間。

若要在 cookie 資料庫根據值而變更時使失效 `LastChanged` ，請 cookie 使用 `LastChanged` 包含 `LastChanged` 資料庫目前值的宣告來建立：

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

cookie在方法的服務註冊期間註冊事件實例 `Startup.ConfigureServices` 。 為您的類別提供[限定範圍的服務註冊](xref:fundamentals/dependency-injection#service-lifetimes) `CustomCookieAuthenticationEvents` ：

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

假設使用者的名稱已更新 &mdash; 為不會以任何方式影響安全性的決策。 如果您想要以非破壞性的更新使用者主體，請呼叫， `context.ReplacePrincipal` 並將 `context.ShouldRenew` 屬性設定為 `true` 。

> [!WARNING]
> 這裡所述的方法會在每個要求上觸發。 cookie針對每個要求的所有使用者驗證驗證，可能會對應用程式造成很大的效能影響。

## <a name="persistent-no-loccookies"></a>持續性 cookie s

您可能想要在 cookie 瀏覽器會話之間保存。 只有在登入或類似的機制上有 [記住我] 核取方塊的明確使用者同意，才能啟用此持續性。 

下列程式碼片段會建立身分識別，並 cookie 透過瀏覽器結束來對應該不受。 系統會接受先前設定的任何滑動到期設定。 如果在 cookie 瀏覽器關閉時過期，瀏覽器會在 cookie 重新開機後清除。

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>在中，將設為 `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ：

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

絕對到期時間可以使用來設定 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> 。 若要建立持續性 cookie ， `IsPersistent` 也必須設定。 否則， cookie 會使用以會話為基礎的存留期來建立，而且可能會在它所保留的驗證票證之前或之後過期。 當 `ExpiresUtc` 設定時，它會覆寫 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> 選項的值 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> （如果已設定的話）。

下列程式碼片段會建立持續20分鐘的身分識別和對應 cookie 的。 這會忽略先前設定的任何滑動到期設定。

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
