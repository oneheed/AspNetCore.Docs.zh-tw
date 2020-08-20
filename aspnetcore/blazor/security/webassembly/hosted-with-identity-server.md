---
title: 使用伺服器保護託管的 ASP.NET Core Blazor WebAssembly 應用程式 Identity
author: guardrex
description: 若要 Blazor 在使用[ Identity 伺服器](https://identityserver.io/)後端的 Visual Studio 內建立具有驗證的新託管方案
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: ef5e9e1becb511ef383b22fc96441b0f61537354
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626216"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a>Blazor WebAssembly使用伺服器保護 ASP.NET Core 託管應用 Identity 程式

由 [Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)

本文說明如何建立使用[ Identity 伺服器](https://identityserver.io/)來驗證使用者和 API 呼叫的[託管 Blazor WebAssembly 應用程式](xref:blazor/hosting-models#blazor-webassembly)。

> [!NOTE]
> 若要將獨立或託管的 Blazor WebAssembly 應用程式設定為使用現有的外部 Identity 伺服器實例，請遵循中的指導方針 <xref:blazor/security/webassembly/standalone-with-authentication-library> 。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

若要建立 Blazor WebAssembly 具有驗證機制的新專案：

1. 在 [**建立新的 ASP.NET Core Web 應用程式**] 對話方塊中選擇** Blazor WebAssembly 應用程式**範本之後，請選取 [**驗證**] 下的 [**變更**]。

1. 使用「**儲存使用者帳戶應用程式內**」選項選取**個別使用者帳戶**，以使用 ASP.NET Core 的系統將使用者儲存在應用程式內 [Identity](xref:security/authentication/identity) 。

1. 在 [ **Advanced** ] 區段中，選取 [裝載**ASP.NET Core** ] 核取方塊。

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code / .NET Core CLI](#tab/visual-studio-code+netcore-cli)

若要 Blazor WebAssembly 在空的資料夾中建立具有驗證機制的新專案，請指定 `Individual` 驗證機制，並 `-au|--auth` 使用 ASP.NET Core 的系統將使用者儲存在應用程式中的選項 [Identity](xref:security/authentication/identity) ：

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| 預留位置  | 範例        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

使用選項指定的輸出位置會 `-o|--output` 建立專案資料夾（如果不存在），而且會成為應用程式名稱的一部分。

如需詳細資訊，請參閱 [`dotnet new`](/dotnet/core/tools/dotnet-new) .Net Core 指南中的命令。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

若要建立 Blazor WebAssembly 具有驗證機制的新專案：

1. 在 [**設定新的 Blazor WebAssembly 應用程式**] 步驟中，從 [**驗證**] 下拉式清單中選取 [**應用程式內) 的個別驗證 (** 。

1. 應用程式會針對使用 ASP.NET Core 儲存在應用程式中的個別使用者建立 [Identity](xref:security/authentication/identity) 。

1. 選取 [ **主控 ASP.NET Core** ] 核取方塊。

---

## <a name="server-app-configuration"></a>伺服器應用程式設定

下列各節說明在包含驗證支援時，專案的新增專案。

### <a name="startup-class"></a>啟始類別

`Startup`類別具有下列新增專案。

* 在 `Startup.ConfigureServices` 中：

  * ASP.NET Core Identity:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * Identity具有其他 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper 方法的伺服器，可在伺服器上設定預設的 ASP.NET Core 慣例 Identity ：

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * 使用額外的 helper 方法進行驗證，以設定 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 應用程式驗證服務器所產生的 JWT 權杖 Identity ：

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* 在 `Startup.Configure` 中：

  * Identity伺服器中介軟體會公開 OpenID Connect (OIDC) 端點：

    ```csharp
    app.UseIdentityServer();
    ```

  * 驗證中介軟體負責驗證要求的認證，並在要求內容上設定使用者：

    ```csharp
    app.UseAuthentication();
    ```

  * 授權中介軟體可啟用授權功能：

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Helper 方法會設定 ASP.NET Core 案例的[ Identity 伺服器](https://identityserver.io/)。 Identity伺服器是一種功能強大且可擴充的架構，可處理應用程式安全性的考慮。 Identity針對最常見的情況，伺服器會公開不必要的複雜性。 因此，我們假設有一組慣例和設定選項，我們考慮的是很好的起點。 一旦您的驗證需要變更， Identity 就可以使用伺服器的完整功能來自訂驗證，以符合應用程式的需求。

### <a name="addno-locidentityserverjwt"></a>新增 Identity ServerJwt

<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Helper 方法會設定應用程式的原則配置作為預設驗證處理常式。 原則設定為允許 Identity 處理所有路由至 URL 空間中之子路徑的要求 Identity `/Identity` 。 會 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 處理所有其他要求。 此外，這個方法：

* 註冊 `{APPLICATION NAME}API` 具有 Identity 預設範圍之伺服器的 API 資源 `{APPLICATION NAME}API` 。
* 設定 JWT 持有人權杖中介軟體，以驗證 Identity 伺服器針對應用程式所發出的權杖。

### <a name="weatherforecastcontroller"></a>WeatherForecastController

在 `WeatherForecastController` (`Controllers/WeatherForecastController.cs`) 中，會將屬性套用 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 至類別。 屬性（attribute）會指出使用者必須根據預設原則來取得存取資源的授權。 預設授權原則會設定為使用預設的驗證配置，此配置是由設定 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 。 Helper 方法會將 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 要求設定為應用程式要求的預設處理常式。

### <a name="applicationdbcontext"></a>[ApplicationdbcoNtext

在 `ApplicationDbContext` (`Data/ApplicationDbContext.cs`) 中，會 <xref:Microsoft.EntityFrameworkCore.DbContext> 擴充 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> 以包含伺服器的架構 Identity 。 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> 衍生自 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>。

若要取得資料庫架構的完整控制權，請從其中一個可用類別繼承， Identity <xref:Microsoft.EntityFrameworkCore.DbContext> 並設定內容以包含 Identity 架構，方法是 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` 在方法中呼叫 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> 。

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

在 `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`) 中，會布建用戶端端點以提供 OIDC 參數。

### <a name="app-settings"></a>應用程式設定

在應用程式佈建檔 (`appsettings.json`) 的專案根目錄中， `IdentityServer` 區段會描述已設定的用戶端清單。 在下列範例中，有一個用戶端。 用戶端名稱會對應到應用程式名稱，並依照慣例對應至 OAuth `ClientId` 參數。 設定檔會指出正在設定的應用程式類型。 設定檔會在內部使用，以促進可簡化伺服器設定程式的慣例。 <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如 `BlazorSample.Client`) 。

## <a name="client-app-configuration"></a>用戶端應用程式設定

### <a name="authentication-package"></a>驗證套件

建立應用程式以使用 () 的個別使用者帳戶時 `Individual` ，應用程式會 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 在應用程式的專案檔中自動收到套件的套件參考。 此套件提供一組基本類型，可協助應用程式驗證使用者，並取得權杖以呼叫受保護的 Api。

如果將驗證新增至應用程式，請手動將套件新增到應用程式的專案檔：

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （ [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)）中找到符合應用程式共用架構版本的最新穩定版本套件。

### <a name="httpclient-configuration"></a>`HttpClient` 配置

在 `Program.Main` (`Program.cs`) 中，會將名為 <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) 設定為在對 <xref:System.Net.Http.HttpClient> 伺服器 API 提出要求時，提供包含存取權杖的實例：

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> 如果您要將 Blazor WebAssembly 應用程式設定為使用 Identity 不屬於託管解決方案的現有伺服器實例 Blazor ，請將 <xref:System.Net.Http.HttpClient> 基底位址註冊從 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> () 變更 `builder.HostEnvironment.BaseAddress` 為伺服器應用程式的 API 授權端點 URL。

### <a name="api-authorization-support"></a>API 授權支援

驗證使用者的支援是由套件內提供的擴充方法插入服務容器 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 。 這個方法會設定應用程式所需的服務，以與現有的授權系統互動。

```csharp
builder.Services.AddApiAuthorization();
```

根據預設，應用程式的設定會依照慣例載入 `_configuration/{client-id}` 。 依照慣例，用戶端識別碼會設定為應用程式的元件名稱。 您可以使用選項來呼叫多載，以將此 URL 變更為指向不同的端點。

### <a name="imports-file"></a>匯入檔案

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>索引頁面

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>應用程式元件

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin 元件

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay 元件

`LoginDisplay`元件 (`Shared/LoginDisplay.razor`) 會轉譯在 `MainLayout` 元件 (`Shared/MainLayout.razor`) 並管理下列行為：

* 針對已驗證的使用者：
  * 顯示目前的使用者名稱。
  * 提供中 [使用者設定檔] 頁面的連結 ASP.NET Core Identity 。
  * 提供用來登出應用程式的按鈕。
* 匿名使用者：
  * 提供註冊的選項。
  * 提供登入的選項。

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a>驗證元件

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>FetchData 元件

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>執行應用程式

從伺服器專案執行應用程式。 使用 Visual Studio 時，您可以：

* 將工具列中的 [ **啟始專案** ] 下拉式清單設定為 *伺服器 API 應用程式* ，然後選取 [ **執行** ] 按鈕。
* 在 **方案總管** 中選取伺服器專案，然後選取工具列中的 [ **執行** ] 按鈕，或從 [ **調試** 程式] 功能表啟動應用程式。

## <a name="name-and-role-claim-with-api-authorization"></a>使用 API 授權的名稱和角色宣告

### <a name="custom-user-factory"></a>自訂使用者 factory

在用戶端應用程式中，建立自訂的使用者 factory。 Identity 伺服器會在單一宣告中以 JSON 陣列的形式傳送多個角色 `role` 。 單一角色會以字串值的形式傳送到宣告中。 Factory 會 `role` 為每個使用者的角色建立個別宣告。

`CustomUserFactory.cs`:

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

在用戶端應用程式中，在 () 中註冊 factory `Program.Main` `Program.cs` ：

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

在伺服器應用程式中，在產生器 <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> 上呼叫 Identity ，這會新增角色相關服務：

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a>設定 Identity 伺服器

使用下列 **其中一** 種方法：

* [API 授權選項](#api-authorization-options)
* [設定檔服務](#profile-service)

#### <a name="api-authorization-options"></a>API 授權選項

在伺服器應用程式中：

* 設定 Identity 伺服器將 `name` 和宣告放 `role` 入識別碼權杖和存取權杖中。
* 防止 JWT 權杖處理常式中的角色預設對應。

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a>設定檔服務

在伺服器應用程式中，建立一個 `ProfileService` 執行。

`ProfileService.cs`:

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

在伺服器應用程式中，在中註冊設定檔服務 `Startup.ConfigureServices` ：

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>使用授權機制

在用戶端應用程式中，元件授權方法此時可正常運作。 元件中的任何授權機制都可以使用角色來授權使用者：

* [ `AuthorizeView` 元件](xref:blazor/security/index#authorizeview-component) (範例： `<AuthorizeView Roles="admin">`) 
* [ `[Authorize]` 屬性](xref:blazor/security/index#authorize-attribute)指示詞 (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)  (範例： `@attribute [Authorize(Roles = "admin")]`) 
* 程式[邏輯](xref:blazor/security/index#procedural-logic) (範例： `if (user.IsInRole("admin")) { ... }`) 

  支援多個角色測試：

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

`User.Identity.Name` 會在用戶端應用程式中填入使用者的使用者名稱，通常是他們的登入電子郵件地址。

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>其他資源

* [部署至 Azure App Service](xref:security/authentication/identity/spa#deploy-to-production)
* [從 Key Vault (Azure 檔匯入憑證) ](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [具有安全預設用戶端之應用程式中未經驗證或未經授權的 web API 要求](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
