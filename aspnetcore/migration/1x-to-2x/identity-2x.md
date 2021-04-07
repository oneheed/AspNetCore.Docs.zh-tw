---
title: 將驗證遷移 Identity 至 ASP.NET Core 2。0
author: rick-anderson
description: 本文概述遷移 ASP.NET Core 1.x 驗證和 ASP.NET Core 2.0 的最常見步驟 Identity 。
ms.author: scaddie
ms.date: 06/21/2019
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
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: 8fcd2f176082f8b8a418af8aaab80ba33e9c3c2e
ms.sourcegitcommit: 0abfe496fed8e9470037c8128efa8a50069ccd52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2021
ms.locfileid: "106564200"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a>將驗證遷移 Identity 至 ASP.NET Core 2。0

由 [Scott Addie](https://github.com/scottaddie) 和 [Hao Kung](https://github.com/HaoK)

ASP.NET Core 2.0 有新的驗證模型， [Identity](xref:security/authentication/identity) 可使用服務簡化設定。 使用驗證的 ASP.NET Core 1.x 應用程式，或 Identity 可以更新為使用新的模型，如下所述。

## <a name="update-namespaces"></a>更新命名空間

在1.x 中，在 `IdentityRole` `IdentityUser` 命名空間中找到類別，例如和 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 。

在2.0 中， <xref:Microsoft.AspNetCore.Identity> 命名空間變成了許多這類類別的新家庭。 使用預設程式 Identity 代碼時，受影響的類別包括 `ApplicationUser` 和 `Startup` 。 調整您 `using` 的語句，以解決受影響的參考。

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>驗證中介軟體和服務

在1.x 專案中，驗證是透過中介軟體來設定。 系統會針對您想要支援的每個驗證配置叫用中介軟體方法。

下列1.x 範例會在啟動時設定 Facebook 驗證 Identity *。 .cs*：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

在2.0 專案中，驗證是透過服務來設定。 每個驗證配置都是在 `ConfigureServices` *啟動* 的方法中註冊。 `UseIdentity`方法會被取代為 `UseAuthentication` 。

下列2.0 範例會在啟動時設定 Facebook 驗證 Identity *。 .cs*：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

`UseAuthentication`方法會新增單一驗證中介軟體元件，負責自動驗證和處理遠端驗證要求。 它會將所有個別中介軟體元件取代為單一的一般中介軟體元件。

以下是每個主要驗證配置的2.0 遷移指示。

### <a name="cookie-based-authentication"></a>Cookie以驗證為基礎

選取下列兩個選項的其中一個，並在啟動中進行必要的變更 *。 .cs*：

1. 使用 cookieIdentity
    - 以 `UseIdentity` `UseAuthentication` 方法中的取代 `Configure` ：

        ```csharp
        app.UseAuthentication();
        ```

    - `AddIdentity`在方法中叫用方法 `ConfigureServices` ，以加入 cookie 驗證服務。
    - （選擇性） `ConfigureApplicationCookie` `ConfigureExternalCookie` 在方法中叫用或方法 `ConfigureServices` 來調整 Identity cookie 設定。

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. 使用 cookie s （不含） Identity
    - `UseCookieAuthentication` `Configure` 以下列內容取代方法中的方法呼叫 `UseAuthentication` ：

        ```csharp
        app.UseAuthentication();
        ```

    - 叫 `AddAuthentication` `AddCookie` 用方法中的和方法 `ConfigureServices` ：

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User,
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a>JWT 持有人驗證

在 *Startup* 中進行下列變更：
- `UseJwtBearerAuthentication` `Configure` 以下列內容取代方法中的方法呼叫 `UseAuthentication` ：

    ```csharp
    app.UseAuthentication();
    ```

- `AddJwtBearer`在方法中叫用方法 `ConfigureServices` ：

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    此程式碼片段不會使用 Identity ，因此應該透過傳遞 `JwtBearerDefaults.AuthenticationScheme` 給方法來設定預設配置 `AddAuthentication` 。

### <a name="openid-connect-oidc-authentication"></a>OpenID Connect (OIDC) 驗證

在 *Startup* 中進行下列變更：

- `UseOpenIdConnectAuthentication` `Configure` 以下列內容取代方法中的方法呼叫 `UseAuthentication` ：

    ```csharp
    app.UseAuthentication();
    ```

- `AddOpenIdConnect`在方法中叫用方法 `ConfigureServices` ：

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- `PostLogoutRedirectUri` `OpenIdConnectOptions` 以下列內容取代動作中的屬性 `SignedOutRedirectUri` ：

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a>Facebook 驗證

在 *Startup* 中進行下列變更：
- `UseFacebookAuthentication` `Configure` 以下列內容取代方法中的方法呼叫 `UseAuthentication` ：

    ```csharp
    app.UseAuthentication();
    ```

- `AddFacebook`在方法中叫用方法 `ConfigureServices` ：

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Google 驗證

在 *Startup* 中進行下列變更：
- `UseGoogleAuthentication` `Configure` 以下列內容取代方法中的方法呼叫 `UseAuthentication` ：

    ```csharp
    app.UseAuthentication();
    ```

- `AddGoogle`在方法中叫用方法 `ConfigureServices` ：

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a>Microsoft 帳戶驗證

如需 Microsoft 帳戶驗證的詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/14455)。

在 *Startup* 中進行下列變更：
- `UseMicrosoftAccountAuthentication` `Configure` 以下列內容取代方法中的方法呼叫 `UseAuthentication` ：

    ```csharp
    app.UseAuthentication();
    ```

- `AddMicrosoftAccount`在方法中叫用方法 `ConfigureServices` ：

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a>Twitter 驗證

在 *Startup* 中進行下列變更：
- `UseTwitterAuthentication` `Configure` 以下列內容取代方法中的方法呼叫 `UseAuthentication` ：

    ```csharp
    app.UseAuthentication();
    ```

- `AddTwitter`在方法中叫用方法 `ConfigureServices` ：

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>設定預設驗證配置

在1.x 中， `AutomaticAuthenticate` `AutomaticChallenge` [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) 基類的和屬性必須在單一驗證配置上進行設定。 沒有足夠的方法可以強制執行此作業。

在2.0 中，已將這兩個屬性移除為個別實例上的屬性 `AuthenticationOptions` 。 您可以在啟動的方法中，于方法呼叫中設定這些設定 `AddAuthentication` `ConfigureServices` *。 .cs*：

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

在上述程式碼片段中，預設配置是設定為 `CookieAuthenticationDefaults.AuthenticationScheme` ( " Cookie s" ) 。

或者，您也可以使用方法的 `AddAuthentication` 多載版本來設定一個以上的屬性。 在下列多載的方法範例中，預設配置是設定為 `CookieAuthenticationDefaults.AuthenticationScheme` 。 您也可以在個別的 `[Authorize]` 屬性或授權原則中指定驗證配置。

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

如果下列其中一個條件成立，請在2.0 中定義預設配置：
- 您希望使用者自動登入
- 您可以使用 `[Authorize]` 屬性或授權原則，而不需要指定配置

這項規則的例外狀況是 `AddIdentity` 方法。 這個方法 cookie 會為您新增，並將預設的驗證和挑戰配置設定為應用程式 cookie `IdentityConstants.ApplicationScheme` 。 此外，它也會將預設的登入配置設定為外部 cookie `IdentityConstants.ExternalScheme` 。

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>使用 HttpCoNtext 驗證延伸模組

`IAuthenticationManager`介面是 1. x 驗證系統的主要進入點。 它已取代為命名空間中的一組新 `HttpContext` 擴充方法 `Microsoft.AspNetCore.Authentication` 。

例如，1.x 專案會參考 `Authentication` 屬性：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

在2.0 專案中，匯入 `Microsoft.AspNetCore.Authentication` 命名空間，並刪除 `Authentication` 屬性參考：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>Windows 驗證 (HTTP.sys/IISIntegration) 

Windows 驗證的變化有兩種：

* 主機只允許已驗證的使用者。 此差異不受2.0 變更影響。
* 主機允許匿名和已驗證的使用者。 這種變化會受到2.0 變更所影響。 例如，應用程式應該允許 [IIS](xref:host-and-deploy/iis/index) 或 [HTTP.sys](xref:fundamentals/servers/httpsys) 層的匿名使用者，但在控制器層級授權使用者。 在此案例中，請設定方法中的預設配置 `Startup.ConfigureServices` 。

  若為 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)，請將預設配置設定為 `IISDefaults.AuthenticationScheme` ：

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  若為 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)，請將預設配置設定為 `HttpSysDefaults.AuthenticationScheme` ：

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  無法設定預設配置，可防止授權 (挑戰) 要求使用下列例外狀況：

  > `System.InvalidOperationException`：未指定 authenticationScheme，而且找不到 DefaultChallengeScheme。

如需詳細資訊，請參閱<xref:security/authentication/windowsauth>。

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a>IdentityCookie選項實例

2.0 變更的副作用是切換為使用命名選項，而不是 cookie 選項實例。 已移除自訂 Identity cookie 配置名稱的功能。

例如，1.x 專案使用「函式 [插入](xref:mvc/controllers/dependency-injection#constructor-injection) 」將參數傳遞至 `IdentityCookieOptions` *AccountController .cs* 和 *ManageController .cs*。 您 cookie 可以從提供的實例存取外部驗證配置：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

上述的函式插入在2.0 專案中變成不必要，而且 `_externalCookieScheme` 可以刪除欄位：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

1.x 專案使用欄位，如下所示 `_externalCookieScheme` ：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

在2.0 專案中，以下列程式碼取代上述程式碼。 您 `IdentityConstants.ExternalScheme` 可以直接使用常數。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

藉 `SignOutAsync` 由匯入下列命名空間來解析新加入的呼叫：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a>新增 Identity 使用者 POCO 導覽屬性

Entity Framework (EF) 基底 POCO 的核心導覽屬性， `IdentityUser` (一般舊的 CLR 物件) 已經被移除。 如果您的1.x 專案使用這些屬性，請手動將其新增回2.0 專案：

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

若要在執行 EF Core 遷移時避免重複的外鍵，請在 `IdentityDbContext` 呼叫) 之後，將下列內容新增至類別的 `OnModelCreating` 方法 (`base.OnModelCreating();` ：

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Core Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Core Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a>取代 GetExternalAuthenticationSchemes

同步方法 `GetExternalAuthenticationSchemes` 已移除，以取代非同步版本。 1.x 專案在 *控制器/ManageController* 中有下列程式碼：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

這個方法會出現在 *Views/Account/Login 中。 cshtml* 也會出現：

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

在2.0 專案中，請使用 <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> 方法。 *ManageController* 中的變更類似于下列程式碼：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

在 *Login* 中，在 `AuthenticationScheme` 迴圈中存取的屬性會 `foreach` 變更為 `Name` ：

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>ManageLoginsViewModel 屬性變更

`ManageLoginsViewModel`ManageController 的動作中會使用物件。 `ManageLogins`  在1.x 專案中，物件的屬性傳回 `OtherLogins` 型別為 `IList<AuthenticationDescription>` 。 此傳回類型需要匯入 `Microsoft.AspNetCore.Http.Authentication` ：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

在2.0 專案中，傳回型別會變更為 `IList<AuthenticationScheme>` 。 這個新的傳回型別需要以匯入來取代匯 `Microsoft.AspNetCore.Http.Authentication` 入 `Microsoft.AspNetCore.Authentication` 。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>其他資源

如需詳細資訊，請參閱 GitHub 上的 [驗證2.0 問題討論](https://github.com/aspnet/Security/issues/1338) 。
