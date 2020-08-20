---
title: IdentityASP.NET Core 專案中的 Scaffold
author: rick-anderson
description: 瞭解如何 Identity 在 ASP.NET Core 專案中 scaffold。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
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
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 09535f41d15b90fa5e50eb1f22f6aecef0530f0c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629557"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a>IdentityASP.NET Core 專案中的 Scaffold

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core [ASP.NET Core Identity](xref:security/authentication/identity) 以[ Razor 類別庫](xref:razor-pages/ui-class)的形式提供。 包含的應用程式 Identity 可以套用 scaffolder，以選擇性地加入類別庫中包含的原始程式碼 Identity Razor (RCL) 。 建議您產生原始程式碼，以便能夠修改程式碼並變更行為。 例如，您可以指示 Scaffolder 產生註冊使用的程式碼。 產生的程式碼優先于 RCL 中的相同程式碼 Identity 。 若要完全掌控 UI，而不使用預設 RCL，請參閱 [建立完整 Identity UI 來源](#full)一節。

**未**包含驗證的應用程式可以套用 scaffolder 來新增 RCL Identity 套件。 您可以選擇 Identity 要產生的程式碼。

雖然 scaffolder 會產生大部分必要的程式碼，但您必須更新您的專案，才能完成此程式。 本檔說明完成程式更新所需的步驟 Identity 。

我們建議使用可顯示檔案差異的原始檔控制系統，並可讓您將變更移出。 執行 scaffolder 後檢查變更 Identity 。

使用 [雙因素驗證](xref:security/authentication/identity-enable-qrcodes)、 [帳戶確認和密碼](xref:security/authentication/accconfirm)復原，以及其他安全性功能時，都需要服務 Identity 。 當樣板時，不會產生服務或服務存根 Identity 。 啟用這些功能的服務必須以手動方式新增。 例如，請參閱 [要求電子郵件確認](xref:security/authentication/accconfirm#require-email-confirmation)。

當 Identity 具有現有個別帳戶的專案具有新的資料內容時：

* 在中 `Startup.ConfigureServices` ，移除對下列各項的呼叫：
  * `AddDbContext`
  * `AddDefaultIdentity`

例如， `AddDbContext` `AddDefaultIdentity` 在下列程式碼中，和會批註化：

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

先前的程式碼會將在*區域/ Identity / Identity HostingStartup.cs*中複製的程式碼批註

一般而言，使用個別帳戶所建立的應用程式 ***不*** 應該建立新的資料內容。

## <a name="scaffold-no-locidentity-into-an-empty-project"></a>Scaffold Identity 至空白專案

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

使用與 `Startup` 下列類似的程式碼來更新類別：

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a>Scaffold Identity 至 Razor 沒有現有授權的專案

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity是在*區域/ Identity / Identity HostingStartup.cs*中設定。 如需詳細資訊，請參閱 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>遷移、UseAuthentication 和版面配置

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>啟用驗證

使用與 `Startup` 下列類似的程式碼來更新類別：

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>版面配置變更

選擇性：將登入部分 (`_LoginPartial`) 新增至版面配置檔案：

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a>Scaffold Identity 至 Razor 具有授權的專案

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

某些 Identity 選項是在*區域/ Identity / Identity HostingStartup.cs*中設定。 如需詳細資訊，請參閱 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a>Scaffold Identity 至沒有現有授權的 MVC 專案

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

選擇性：將登入部分 (`_LoginPartial`) 新增至 *Views/Shared/_Layout cshtml* 檔案：

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* 將 *Pages/shared/_LoginPartial 的 cshtml* 檔案移至 *Views/shared/_LoginPartial. cshtml*

Identity是在*區域/ Identity / Identity HostingStartup.cs*中設定。 如需詳細資訊，請參閱 IHostingStartup。

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

使用與 `Startup` 下列類似的程式碼來更新類別：

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a>Scaffold Identity 至具有授權的 MVC 專案

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a>Scaffold Identity 至 Blazor Server 沒有現有授權的專案

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity是在*區域/ Identity / Identity HostingStartup.cs*中設定。 如需詳細資訊，請參閱 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。

### <a name="migrations"></a>移轉

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a>將 XSRF token 傳遞至應用程式

權杖可以傳遞給元件：

* 當驗證權杖已布建並儲存至驗證時 cookie ，可傳遞至元件。
* Razor 元件無法 `HttpContext` 直接使用，因此沒有任何方法可 [ (XSRF) 權杖](xref:security/anti-request-forgery) ，以張貼至 Identity 的登出端點 `/Identity/Account/Logout` 。 XSRF token 可以傳遞至元件。

如需詳細資訊，請參閱<xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>。

在 *Pages/_Host cshtml* 檔案中，將標記加入至和類別之後建立權杖 `InitialApplicationState` `TokenProvider` ：

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

更新 `App` (*應用程式的元件。 Razor*) 以指派 `InitialState.XsrfToken` ：

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

主題中所 `TokenProvider` 示範的服務用於 `LoginDisplay` 下列版面配置 [和驗證流程變更](#layout-and-authentication-flow-changes) 區段中的元件。

### <a name="enable-authentication"></a>啟用驗證

在 `Startup` 類別中：

* 確認已 Razor 在中加入頁面服務 `Startup.ConfigureServices` 。
* 如果使用 [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)，請註冊服務。
* `UseDatabaseErrorPage`針對開發環境，在的應用程式產生器上呼叫 `Startup.Configure` 。
* Call `UseAuthentication` 和 `UseAuthorization` after `UseRouting` 。
* 新增頁面的端點 Razor 。

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a>版面配置和驗證流程變更

將 `RedirectToLogin` (*RedirectToLogin* 的元件) 新增至專案根目錄中的應用程式 *共用* 資料夾：

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo("Identity/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

將 `LoginDisplay` (*LoginDisplay* 的元件) 加入應用程式的 *共用* 資料夾。 [TokenProvider 服務](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)會針對張貼至登出端點的 HTML 表單，提供 XSRF token Identity ：

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage/Index">
            Hello, @context.User.Identity.Name!
        </a>
        <form action="/Identity/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

在 `MainLayout` 元件 (*共用/MainLayout* ]) 中，將元件新增 `LoginDisplay` 至最上層資料列 `<div>` 元素的內容：

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a>樣式驗證端點

由於 Blazor Server 使用 Razor 頁面 Identity 頁面，當訪客在頁面和元件之間流覽時，UI 的樣式會變更 Identity 。 您有兩個選項可處理 incongruous 樣式：

#### <a name="build-no-locidentity-components"></a>組建 Identity 元件

使用元件 Identity 而非頁面的方法是建立 Identity 元件。 因為 `SignInManager` `UserManager` 元件中不支援和 Razor ，所以請使用應用程式中的 API 端點 Blazor Server 來處理使用者帳戶動作。

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a>搭配應用程式樣式使用自訂版面配置 Blazor

您 Identity 可以修改頁面版面配置和樣式，以產生使用預設主題的頁面 Blazor 。

> [!NOTE]
> 本節中的範例只是自訂的起點。 可能需要額外的工作才能獲得最佳使用者體驗。

建立新的 `NavMenu_IdentityLayout` 元件， (*共用/NavMenu_ Identity 版面配置。 razor*) 。 針對元件的標記和程式碼，請使用應用程式元件的相同內容 `NavMenu` (*Shared/NavMenu razor*) 。 `NavLink`針對需要驗證或授權的元件，將任何無法匿名連接的元件帶出至元件，因為元件中的自動重新導向 `RedirectToLogin` 失敗。

在 *Pages/Shared/Layout. cshtml* 檔案中，進行下列變更：

* 將指示詞新增至檔案頂端，以使用標籤協助 Razor 程式和 *共用* 資料夾中的應用程式元件：

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  取代 `{APPLICATION ASSEMBLY}` 為應用程式的元件名稱。

* 將 `<base>` 標記和樣式表單新增 Blazor `<link>` 至 `<head>` 內容：

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* 將標記的內容變更 `<body>` 為下列內容：

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_IdentityLayout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default Identity UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/Identity/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/Identity/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/Identity/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a>Scaffold Identity 至 Blazor Server 具有授權的專案

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

某些 Identity 選項是在*區域/ Identity / Identity HostingStartup.cs*中設定。 如需詳細資訊，請參閱 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a>建立完整的 Identity UI 來源

若要維持 UI 的完整控制權 Identity ，請執行 Identity scaffolder，然後選取 [覆 **寫所有**檔案]。

下列醒目提示的程式碼顯示 Identity Identity 在 ASP.NET Core 2.1 web 應用程式中，用來取代預設 UI 的變更。 您可能會想要這樣做，才能完全控制 Identity UI。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Identity下列程式碼將取代預設值：

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

下列程式碼會設定 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)和 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)：

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

註冊 `IEmailSender` 執行，例如：

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a>密碼設定

如果 <xref:Microsoft.AspNetCore.Identity.PasswordOptions> 在中設定 `Startup.ConfigureServices` ，scaffold 頁面中的屬性可能需要[ `[StringLength]` 屬性](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute)設定 `Password` Identity 。 `InputModel`您 `Password` 可以在下列檔案中找到屬性：

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a>停用頁面

這幾節會說明如何停用 [註冊] 頁面，但方法可以用來停用任何頁面。

若要停用使用者註冊：

* Scaffold Identity 。 包含 Account。 Register、Account、Login 和 Account. RegisterConfirmation。 例如：

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* 更新 *區域/ Identity /Pages/Account/Register.cshtml.cs* ，讓使用者無法從此端點註冊：

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* 更新 *區域/ Identity /Pages/Account/Register.cshtml* ，以便與先前的變更一致：

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* 批註或移除*區域/ Identity /Pages/Account/Login.cshtml*的註冊連結

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* 更新 *區域/ Identity /Pages/Account/RegisterConfirmation* 頁面。

  * 移除 cshtml 檔案中的程式碼和連結。
  * 移除下列內容中的確認碼 `PageModel` ：

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>使用其他應用程式來新增使用者

提供在 web 應用程式外部新增使用者的機制。 新增使用者的選項包括：

* 專用的管理 web 應用程式。
* 主控台應用程式。

下列程式碼概述新增使用者的一種方法：

* 使用者清單會讀入記憶體中。
* 針對每個使用者產生強式唯一密碼。
* 使用者會新增至 Identity 資料庫。
* 系統會通知使用者，並告知使用者變更密碼。

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

下列程式碼概述如何新增使用者：

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

在生產案例中，可以遵循類似的方法。

## <a name="prevent-publish-of-static-no-locidentity-assets"></a>防止發佈靜態 Identity 資產

若要防止將靜態 Identity 資產發行至 web 根目錄，請參閱 <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> 。

## <a name="additional-resources"></a>其他資源

* [驗證碼變更為 ASP.NET Core 2.1 和更新版本](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2.1 和更新版本會 [ASP.NET Core Identity](xref:security/authentication/identity) 以[ Razor 類別庫](xref:razor-pages/ui-class)的形式提供。 包含的應用程式 Identity 可以套用 scaffolder，以選擇性地加入類別庫中包含的原始程式碼 Identity Razor (RCL) 。 建議您產生原始程式碼，以便能夠修改程式碼並變更行為。 例如，您可以指示 Scaffolder 產生註冊使用的程式碼。 產生的程式碼優先于 RCL 中的相同程式碼 Identity 。 若要完全掌控 UI，而不使用預設 RCL，請參閱 [建立完整身分識別 UI 來源](#full)一節。

**未**包含驗證的應用程式可以套用 scaffolder 來新增 RCL Identity 套件。 您可以選擇 Identity 要產生的程式碼。

雖然 scaffolder 會產生大部分必要的程式碼，但您必須更新您的專案，才能完成此程式。 本檔說明完成程式更新所需的步驟 Identity 。

當 Identity scaffolder 執行時，會在專案目錄中建立 *ScaffoldingReadme.txt* 檔案。 *ScaffoldingReadme.txt*檔案包含完成此樣板更新所需功能的一般指示 Identity 。 本檔包含比 *ScaffoldingReadme.txt* 檔案更完整的指示。

我們建議使用可顯示檔案差異的原始檔控制系統，並可讓您將變更移出。 執行 scaffolder 後檢查變更 Identity 。

> [!NOTE]
> 使用 [雙因素驗證](xref:security/authentication/identity-enable-qrcodes)、 [帳戶確認和密碼](xref:security/authentication/accconfirm)復原，以及其他安全性功能時，都需要服務 Identity 。 當樣板時，不會產生服務或服務存根 Identity 。 啟用這些功能的服務必須以手動方式新增。 例如，請參閱 [要求電子郵件確認](xref:security/authentication/accconfirm#require-email-confirmation)。

## <a name="scaffold-no-locidentity-into-an-empty-project"></a>Scaffold Identity 至空白專案

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

將下列反白顯示的呼叫新增至 `Startup` 類別：

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a>Scaffold Identity 至 Razor 沒有現有授權的專案

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity是在*區域/ Identity / Identity HostingStartup.cs*中設定。 如需詳細資訊，請參閱 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>遷移、UseAuthentication 和版面配置

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>啟用驗證

在 `Configure` 類別的方法中 `Startup` ，于之後呼叫 [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) `UseStaticFiles` ：

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>版面配置變更

選擇性：將登入部分 (`_LoginPartial`) 新增至版面配置檔案：

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a>Scaffold Identity 至 Razor 具有授權的專案

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

某些 Identity 選項是在*區域/ Identity / Identity HostingStartup.cs*中設定。 如需詳細資訊，請參閱 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a>Scaffold Identity 至沒有現有授權的 MVC 專案

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

選擇性：將登入部分 (`_LoginPartial`) 新增至 *Views/Shared/_Layout cshtml* 檔案：

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* 將 *Pages/shared/_LoginPartial 的 cshtml* 檔案移至 *Views/shared/_LoginPartial. cshtml*

Identity是在*區域/ Identity / Identity HostingStartup.cs*中設定。 如需詳細資訊，請參閱 IHostingStartup。

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

呼叫 [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 之後 `UseStaticFiles` ：

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a>Scaffold Identity 至具有授權的 MVC 專案

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

刪除 *頁面/共用* 資料夾，以及該資料夾中的檔案。

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a>建立完整的 Identity UI 來源

若要維持 UI 的完整控制權 Identity ，請執行 Identity scaffolder，然後選取 [覆 **寫所有**檔案]。

下列醒目提示的程式碼顯示 Identity Identity 在 ASP.NET Core 2.1 web 應用程式中，用來取代預設 UI 的變更。 您可能會想要這樣做，才能完全控制 Identity UI。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Identity下列程式碼將取代預設值：

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

下列程式碼會設定 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)和 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)：

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

註冊 `IEmailSender` 執行，例如：

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a>密碼設定

如果 <xref:Microsoft.AspNetCore.Identity.PasswordOptions> 在中設定 `Startup.ConfigureServices` ，scaffold 頁面中的屬性可能需要[ `[StringLength]` 屬性](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute)設定 `Password` Identity 。 `InputModel`您 `Password` 可以在下列檔案中找到屬性：

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a>停用註冊頁面

若要停用使用者註冊：

* Scaffold Identity 。 包含 Account。 Register、Account、Login 和 Account. RegisterConfirmation。 例如：

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* 更新 *區域/ Identity /Pages/Account/Register.cshtml.cs* ，讓使用者無法從此端點註冊：

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* 更新 *區域/ Identity /Pages/Account/Register.cshtml* ，以便與先前的變更一致：

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* 批註或移除*區域/ Identity /Pages/Account/Login.cshtml*的註冊連結

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* 更新 *區域/ Identity /Pages/Account/RegisterConfirmation* 頁面。

  * 移除 cshtml 檔案中的程式碼和連結。
  * 移除下列內容中的確認碼 `PageModel` ：

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>使用其他應用程式來新增使用者

提供在 web 應用程式外部新增使用者的機制。 新增使用者的選項包括：

* 專用的管理 web 應用程式。
* 主控台應用程式。

下列程式碼概述新增使用者的一種方法：

* 使用者清單會讀入記憶體中。
* 針對每個使用者產生強式唯一密碼。
* 使用者會新增至 Identity 資料庫。
* 系統會通知使用者，並告知使用者變更密碼。

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

下列程式碼概述如何新增使用者：

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

在生產案例中，可以遵循類似的方法。

## <a name="additional-resources"></a>其他資源

* [驗證碼變更為 ASP.NET Core 2.1 和更新版本](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
