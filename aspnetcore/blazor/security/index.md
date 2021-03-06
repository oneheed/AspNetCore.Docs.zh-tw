---
title: ASP.NET 核心 Blazor 驗證與授權
author: guardrex
description: 瞭解 Blazor 驗證與授權案例。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/index
ms.openlocfilehash: a840e5d54d48441e10c2a58af591a530c8fd7de9
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394885"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a>ASP.NET 核心 Blazor 驗證與授權

ASP.NET Core 支援在應用程式中設定和管理安全性 Blazor 。

Blazor Server和應用程式之間的安全性案例不同 Blazor WebAssembly 。 由於 Blazor Server 應用程式是在伺服器上執行，因此授權檢查能夠判斷：

* 要提供給使用者的 UI 選項 (例如，使用者可以使用哪些功能表項目)。
* 適用於應用程式和元件之特定區域的存取規則。

Blazor WebAssembly 應用程式會在用戶端上執行。 授權「僅」會被用來決定要顯示的 UI 選項。 由於用戶端檢查可以由使用者修改或略過，因此 Blazor WebAssembly 應用程式無法強制執行授權存取規則。

[ Razor 頁面授權慣例](xref:security/authorization/razor-pages-authorization)不適用於可路由傳送的 Razor 元件。 如果無法路由傳送的 Razor 元件 [內嵌在頁面中](xref:blazor/components/prerendering-and-integration)，頁面的授權慣例會間接影響 Razor 元件以及頁面內容的其餘部分。

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601><xref:Microsoft.AspNetCore.Identity.UserManager%601>元件中不支援和 Razor 。

## <a name="authentication"></a>驗證

Blazor 使用現有的 ASP.NET 核心驗證機制來建立使用者的身分識別。 確切的機制取決於應用程式的裝載方式 Blazor Blazor WebAssembly 或 Blazor Server 。

### <a name="blazor-webassembly-authentication"></a>Blazor WebAssembly 驗證

在 Blazor WebAssembly 應用程式中，可以略過驗證檢查，因為使用者可以修改所有用戶端程式代碼。 這同樣也適用於所有的用戶端應用程式技術，包括 JavaScript SPA 架構或任何作業系統的原生應用程式。

新增下列內容：

* 應用程式專案檔的套件參考 [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) 。
* `Microsoft.AspNetCore.Components.Authorization`應用程式檔案的命名空間 `_Imports.razor` 。

若要處理驗證，請使用內建或自訂 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 服務，如下節所述。

如需建立應用程式和設定的詳細資訊，請參閱 <xref:blazor/security/webassembly/index> 。

### <a name="blazor-server-authentication"></a>Blazor Server 驗證

Blazor Server 應用程式會透過使用建立的即時連接來運作 SignalR 。 建立連線時，會處理[以 SignalR 應用程式為基礎的驗證](xref:signalr/authn-and-authz)。 驗證可以根據 cookie 或其他某些持有人權杖。

<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>適用于應用程式的內建服務會 Blazor Server 從 ASP.NET Core 取得驗證狀態資料 `HttpContext.User` 。 這是驗證狀態與現有 ASP.NET Core 驗證機制整合的方式。

如需建立應用程式和設定的詳細資訊，請參閱 <xref:blazor/security/server/index> 。

## <a name="authenticationstateprovider-service"></a>AuthenticationStateProvider 服務

<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 是 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 元件與 <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> 元件用來取得驗證狀態的基礎服務。

您通常不會直接使用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>。 使用本文稍後所述的[ `AuthorizeView` 元件](#authorizeview-component)或 [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) 方法。 使用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 的主要缺點，在於系統不會在基礎驗證狀態資料變更時自動通知該元件。

<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 服務可以提供目前使用者的 <xref:System.Security.Claims.ClaimsPrincipal> 資料，如下列範例所示：

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type: @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

如果 `user.Identity.IsAuthenticated` 為 `true`，且由於使用者為 <xref:System.Security.Claims.ClaimsPrincipal>，系統便可以列舉宣告，並評估角色中的成員資格。

如需有關相依性插入 (DI) 與服務的詳細資訊，請參閱 <xref:blazor/fundamentals/dependency-injection> 與 <xref:fundamentals/dependency-injection>。

## <a name="implement-a-custom-authenticationstateprovider"></a>實作自訂 AuthenticationStateProvider

如果應用程式需要自訂提供者，請執行 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 和覆寫 `GetAuthenticationStateAsync` ：

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

在 Blazor WebAssembly 應用程式中， `CustomAuthStateProvider` 服務是在中 `Main` 註冊 `Program.cs` ：

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

在 Blazor Server 應用程式中， `CustomAuthStateProvider` 服務會在中註冊 `Startup.ConfigureServices` ：

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

使用 `CustomAuthStateProvider` 上述範例中的，所有使用者都會使用使用者名稱進行驗證 `mrfibuli` 。

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>將驗證狀態公開為階層式參數

如果程式邏輯需要驗證狀態資料（例如，在執行使用者所觸發的動作時），請藉由定義型別的串聯參數來取得驗證狀態資料 `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` ：

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

如果 `user.Identity.IsAuthenticated` 為 `true`，系統便可以列舉宣告，並評估角色中的成員資格。

`Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` 使用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> `App` 元件 () 中的和元件來設定串聯參數 `App.razor` ：

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)" />
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

在 Blazor WebAssembly 應用程式中，將選項和授權的服務加入至 `Program.Main` ：

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

在 Blazor Server 應用程式中，選項和授權的服務已經存在，因此不需要採取進一步的動作。

## <a name="authorization"></a>授權

在使用者被驗證之後，系統便會套用「授權」規則以控制使用者可以執行的動作。

存取權通常會依據下列情況來授與或拒絕：

* 使用者已通過驗證 (已登入)。
* 使用者屬於某個「角色」。
* 使用者具有「宣告」。
* 已滿足某個「原則」。

這些概念與 ASP.NET Core MVC 或 Pages 應用程式中的每個概念相同 Razor 。 如需有關 ASP.NET Core 安全性的詳細資訊，請參閱[ASP.NET Core 安全性和 Identity ](xref:security/index)的文章。

## <a name="authorizeview-component"></a>AuthorizeView 元件

<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>元件會視使用者是否已獲授權，選擇性地顯示 UI 內容。 此方法可讓您只需要向使用者「顯示」資料，而不需要在程序性邏輯中使用該使用者的身分識別。

該元件會公開 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> 類型的 `context` 變數，您可以使用它來存取已登入使用者的相關資訊：

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

如果使用者未獲得授權，您也可以提供不同的內容來顯示：

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authorized.</p>
        <button @onclick="SecureMethod">Authorized Only Button</button>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>

@code {
    private void SecureMethod() { ... }
}
```

`<Authorized>`和標記的內容 `<NotAuthorized>` 可以包含任意專案，例如其他互動式元件。

授權專案的預設事件處理常式（例如 `SecureMethod` `<button>` 上述範例中的元素方法）只能由授權的使用者叫用。

授權情況 (例如控制 UI 選項或存取的角色或原則) 已涵蓋於[授權](#authorization)一節。

如果未指定授權條件，<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 會使用預設的原則，並將：

* 已驗證 (已登入) 的使用者視為已授權。
* 未驗證 (已登出) 的使用者視為未經授權。

元件 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 可以用在 `NavMenu` 元件 (`Shared/NavMenu.razor`) ，以 (`<li>...</li>`) 為[ `NavLink` 元件](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) () 顯示清單專案 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ，但請注意，此方法只會從轉譯的輸出中移除清單專案。 它不會防止使用者流覽至元件。

從包含驗證的[ Blazor 專案範本](xref:blazor/project-structure)建立的應用程式會使用 `LoginDisplay` 相依于元件的元件 `AuthorizeView` 。 `AuthorizeView`元件會選擇性地向使用者顯示 Identity 相關工作的內容。 下列範例是來自[ Blazor WebAssembly 專案範本](xref:blazor/project-structure)。

`Shared/LoginDisplay.razor`:

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginLogout">Log out</button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code{
    private async Task BeginLogout(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

下列範例是來自[ Blazor Server 專案範本](xref:blazor/project-structure)，並使用 ASP.NET Core Identity `Identity` 應用程式區域中的端點來處理 Identity 相關的工作。

`Shared/LoginDisplay.razor`:

```razor
<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage">Hello, @context.User.Identity.Name!</a>
        <form method="post" action="Identity/Account/LogOut">
            <button type="submit" class="nav-link btn btn-link">Log out</button>
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Log in</a>
    </NotAuthorized>
</AuthorizeView>
```

### <a name="role-based-and-policy-based-authorization"></a>角色型和原則型授權

<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 元件支援「角色型」或「原則型」授權。

針對角色型授權，請使用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> 參數：

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

如需詳細資訊，請參閱<xref:security/authorization/roles>。

針對原則型授權，請使用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> 參數：

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

宣告型授權是特殊案例的原則型授權。 例如，您可以定義要求使用者具備特定宣告的原則。 如需詳細資訊，請參閱<xref:security/authorization/policies>。

這些 Api 可以在 Blazor Server 或 Blazor WebAssembly 應用程式中使用。

如果未指定 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> 和 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy>，<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 便會使用預設原則。

### <a name="content-displayed-during-asynchronous-authentication"></a>在非同步驗證期間所顯示的內容

Blazor 允許以 *非同步方式* 判斷驗證狀態。 這種方法的主要案例是對 Blazor WebAssembly 外部端點提出要求以進行驗證的應用程式。

在驗證期間，<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 預設不會顯示任何內容。 若要在進行驗證時顯示內容，請使用 `<Authorizing>` 標記：

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

這種方法通常不適用於 Blazor Server 應用程式。 Blazor Server 應用程式會在狀態建立後立即得知驗證狀態。 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> 您可以在 Blazor Server 應用程式的元件中提供內容 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> ，但永遠不會顯示內容。

## <a name="authorize-attribute"></a>[Authorize] 屬性

[ `[Authorize]` 屬性](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)可用於 Razor 元件中：

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> 僅 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 在透過 `@page` 路由器達成的元件上使用 Blazor 。 授權僅會以路由的層面執行，且「不」適用於在頁面內轉譯的子元件。 若要授權在頁面內顯示特定組件，請改為使用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>。

[ `[Authorize]` 屬性](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)也支援以角色為基礎或以原則為基礎的授權。 針對角色型授權，請使用 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> 參數：

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

針對原則型授權，請使用 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> 參數：

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

如果 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> 未指定或，則 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 會使用預設原則，其預設為處理：

* 已驗證 (已登入) 的使用者視為已授權。
* 未驗證 (已登出) 的使用者視為未經授權。

## <a name="customize-unauthorized-content-with-the-router-component"></a>搭配 Router 元件自訂未經授權的內容

<xref:Microsoft.AspNetCore.Components.Routing.Router>元件與元件搭配使用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> ，可讓應用程式在下列情況下指定自訂內容：

* 使用者無法通過套用 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 至元件的條件。 元素的標記隨即 [`<NotAuthorized>`](xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView.NotAuthorized?displayProperty=nameWithType) 顯示。 屬性 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 區段中包含屬性。 [ `[Authorize]` ](#authorize-attribute)
* 非同步授權正在進行中，這通常表示正在驗證使用者的程式正在進行中。 元素的標記隨即 [`<Authorizing>`](xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView.Authorizing?displayProperty=nameWithType) 顯示。
* 找不到內容。 元素的標記隨即 [`<NotFound>`](xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound?displayProperty=nameWithType) 顯示。

在預設[ Blazor Server 專案範本](xref:blazor/project-structure)中， `App`) 的元件 (會 `App.razor` 示範如何設定自訂內容：

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <h1>Sorry</h1>
                    <p>You're not authorized to reach this page.</p>
                    <p>You may need to log in as a different user.</p>
                </NotAuthorized>
                <Authorizing>
                    <h1>Authorization in progress</h1>
                    <p>Only visible while authorization is in progress.</p>
                </Authorizing>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

`<NotFound>`、 `<NotAuthorized>` 和標記的內容 `<Authorizing>` 可以包含任意專案，例如其他互動式元件。

如果 `<NotAuthorized>` 未指定標籤，則會 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> 使用下列回復訊息：

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>關於驗證狀態變更的通知

如果應用程式判斷基礎驗證狀態資料已變更 (例如，因為使用者已登出或其他使用者已) 變更其角色，則[自訂 `AuthenticationStateProvider` ](#implement-a-custom-authenticationstateprovider)可以選擇性地 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> 在基類上叫用方法 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 。 這會通知驗證狀態資料的取用者 (例如 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) 使用新資料來重新轉譯。

## <a name="procedural-logic"></a>程序性邏輯

如果應用程式需要在程式邏輯中檢查授權規則，請使用型別的串聯參數 `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` 來取得使用者的 <xref:System.Security.Claims.ClaimsPrincipal> 。 `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` 可以與其他服務結合，例如 `IAuthorizationService` ，以評估原則。

```razor
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> 在 Blazor WebAssembly 應用程式元件中，新增 <xref:Microsoft.AspNetCore.Authorization> 和 <xref:Microsoft.AspNetCore.Components.Authorization> 命名空間：
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> 您可以藉由將這些命名空間新增至應用程式的檔案，以全域方式提供這些命名空間 `_Imports.razor` 。

## <a name="troubleshoot-errors"></a>針對錯誤進行疑難排解

常見錯誤：

* **授權需要類型的級聯參數 `Task\<AuthenticationState>` 。請考慮使用 `CascadingAuthenticationState` 以提供此。**

* **`null` 收到的值為 `authenticationStateTask`**

專案可能不是使用 Blazor Server 已啟用驗證的範本所建立。 `<CascadingAuthenticationState>`在 UI 樹狀結構的某個部分周圍包裝，例如在 `App` 元件 () ， `App.razor` 如下所示：

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` 會提供串聯參數，進而從基礎 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> DI 服務接收。

## <a name="additional-resources"></a>其他資源

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* [建立驗證的自訂版本 MSAL JavaScript 程式庫](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [絕佳 Blazor ：驗證](https://github.com/AdrienTorris/awesome-blazor#authentication) 社區範例連結
