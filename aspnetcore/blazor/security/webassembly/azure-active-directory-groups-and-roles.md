---
title: Blazor WebAssembly具有 Azure Active Directory 群組和角色的 ASP.NET Core
author: guardrex
description: 瞭解如何設定 Blazor WebAssembly 以使用 Azure Active Directory 群組和角色。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 01/24/2021
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 8d18a8f9282e83c3b3ef5b9c7c6651c9b525a21f
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751584"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a>Azure Active Directory (AAD) 群組、系統管理員角色和應用程式角色

[Luke Latham](https://github.com/guardrex) And [Javier Calvarro Nelson](https://github.com/javiercn)

> [!NOTE]
> 本文適用于 Blazor 使用 Microsoft 1.0 的 ASP.NET Core apps 3.1 版 Identity ，並將于不久後更新為 5.0 Identity 2.0。 如需詳細資訊，請參閱下列 GitHub 問題和提取要求。 提取要求的 [檔案 **變更** ] 索引標籤包含發行項更新的草稿文字和範例。 審核和最終更新之後，提取要求將會合並到即時檔集。
>
> * 問題： [ Blazor 使用 AAD 群組和角色進行 WASM， (dotnet/AspNetCore.Docs #17683) ](https://github.com/dotnet/AspNetCore.Docs/issues/17683)
> * 提取要求： [ Blazor AAD 群組和角色主題 5.0 (dotnet/AspNetCore.Docs #20856) ](https://github.com/dotnet/AspNetCore.Docs/pull/20856)

Azure Active Directory (AAD) 提供數個可結合的授權方法 ASP.NET Core Identity ：

* 群組
  * 安全性
  * Microsoft 365
  * 散發
* 角色
  * AAD 系統管理員角色
  * 應用程式角色

本文中的指導方針適用于 Blazor WebAssembly 下列主題中所述的 AAD 部署案例：

* [獨立的 Microsoft 帳戶](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [獨立的 AAD](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [使用 AAD 託管](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

本文的指導方針提供用戶端和伺服器應用程式的指示：

* **用戶端**：獨立 Blazor WebAssembly 應用程式或 *`Client`* 託管解決方案的應用程式 Blazor 。
* **伺服器**：獨立 ASP.NET CORE server api/web api 應用程式或 *`Server`* 託管解決方案的應用程式 Blazor 。

## <a name="scopes"></a>範圍

若要允許使用者設定檔、角色指派和群組成員資格資料的 [MICROSOFT GRAPH API](/graph/use-the-api) 呼叫， **用戶端** 會使用 `https://graph.microsoft.com/User.Read` (中的 () [圖形 API 許可權) 範圍](/graph/permissions-reference) 進行設定。

針對角色和群組成員資格資料呼叫圖形 API 的 **伺服器** 應用程式， `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [圖形 API](/graph/permissions-reference) () 範圍 Azure 入口網站範圍中提供的許可權。

除了本文第一節所列的主題所述的 AAD 部署案例中所需的範圍之外，還需要這些範圍。

> [!NOTE]
> 「許可權」和「範圍」兩字都是在 Azure 入口網站以及各種 Microsoft 和外部檔集中交替使用。 本文使用「範圍」一字，在 Azure 入口網站中指派給應用程式的許可權。

## <a name="group-membership-claims-attribute"></a>群組成員資格宣告屬性

在 **用戶端** 和 **伺服器** 應用程式之 Azure 入口網站的應用程式資訊清單中，將 [ `groupMembershipClaims` 屬性](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)設定為 `All` 。 的值 `All` 會取得已登入使用者所屬的所有安全性群組、通訊群組和角色。

1. 開啟應用程式的 Azure 入口網站註冊。
1. 選取提要欄位中的 [**管理**  >  **資訊清單**]。
1. 尋找 `groupMembershipClaims` 屬性。
1. 將值設定為 `All`。
1. 選取 [儲存] 按鈕。

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a>自訂使用者帳戶

在 Azure 入口網站中，將使用者指派給 AAD 安全性群組和 AAD 系統管理員角色。

本文中的範例：

* 假設使用者已指派給 Azure 入口網站 AAD 租使用者中的 AAD *計費管理員* 角色，以取得存取伺服器 API 資料的授權。
* 使用 [授權原則](xref:security/authorization/policies) 來控制 **用戶端** 和 **伺服器** 應用程式中的存取。

在 **用戶端** 應用程式中，擴充 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 以包含下列內容的屬性：

* `Roles`： [應用程式角色](#app-roles) 一節中涵蓋的 AAD 應用程式角色陣列 () 
* `Wids`：知名識別碼宣告中的 AAD 系統管理員角色 [ (`wids`) ](/azure/active-directory/develop/access-tokens#payload-claims)
* `Oid`：不可變的 [物件識別碼宣告 (`oid`) ](/azure/active-directory/develop/id-tokens#payload-claims) (可唯一識別租使用者內和跨租使用者的使用者) 

將空的陣列指派給每一個陣列屬性，以便 `null` 在迴圈中使用這些屬性時，不需要檢查 `foreach` 。

`CustomUserAccount.cs`:

```csharp
using System;
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = Array.Empty<string>();

    [JsonPropertyName("wids")]
    public string[] Wids { get; set; } = Array.Empty<string>();

    [JsonPropertyName("oid")]
    public string Oid { get; set; }
}
```

將套件參考新增至 **用戶端** 應用程式的專案檔 [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) 。

在文章的 *GRAPH sdk* 區段中，新增 graph sdk 公用程式類別和設定 <xref:blazor/security/webassembly/graph-api#graph-sdk> 。 在 `GraphClientExtensions` 類別中，于 `User.Read` 方法中指定存取權杖的範圍 `AuthenticateRequestAsync` ：

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

將下列自訂使用者帳戶 factory 新增至 **用戶端** 應用程式。 自訂使用者 factory 是用來建立：

* 應用程式角色宣告 (`appRole`)  ([應用程式角色](#app-roles) 一節中所涵蓋的) 
* AAD 系統管理員角色宣告 (`directoryRole`) 
* 使用者的行動電話號碼範例使用者設定檔資料宣告 (`mobilePhone`) 
* AAD 群組宣告 (`directoryGroup`) 

`CustomAccountFactory.cs`:

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor,
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
        this.logger = logger;
    }
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("appRole", role));
            }

            foreach (var wid in account.Wids)
            {
                userIdentity.AddClaim(new Claim("directoryRole", wid));
            }

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);

                var requestMe = graphClient.Me.Request();
                var user = await requestMe.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilePhone",
                        user.MobilePhone));
                }

                var requestMemberOf = graphClient.Users[account.Oid].MemberOf;
                var memberships = await requestMemberOf.Request().GetAsync();

                if (memberships != null)
                {
                    foreach (var entry in memberships)
                    {
                        if (entry.ODataType == "#microsoft.graph.group")
                        {
                            userIdentity.AddClaim(
                                new Claim("directoryGroup", entry.Id));
                        }
                    }
                }
            }
            catch (ServiceException exception)
            {
                logger.LogError("Graph API service failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

上述程式碼不包含可轉移的成員資格。 如果應用程式需要直接和可轉移的群組成員資格宣告，請 `MemberOf` `IUserMemberOfCollectionWithReferencesRequestBuilder` 使用 () 取代) 的屬性 (`TransitiveMemberOf` `IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder` 。

上述程式碼會忽略 () 的群組成員資格宣告 `groups` ，這些是 Aad 系統管理員角色 (`#microsoft.graph.directoryRole` 類型) ，因為 Microsoft 平臺2.0 所傳回的 GUID 值 Identity 是 Aad 系統管理員角色 **實體識別碼** ，而不是 [**角色範本識別碼**](/azure/active-directory/roles/permissions-reference#role-template-ids)。 實體識別碼在 Microsoft 平臺2.0 中的租使用者之間並不穩定 Identity ，而且不應該用來為應用程式中的使用者建立授權原則。 一律將 **角色範本識別碼** 用於 **`wids` 宣告所提供** 的 AAD 系統管理員角色。

在 `Program.Main` **用戶端** 應用程式中，將 MSAL authentication 設定為使用自訂使用者帳戶 factory。

`Program.cs`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState,
    CustomUserAccount>(options =>
{
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount,
    CustomAccountFactory>();

...

builder.Services.AddGraphClient();
```

## <a name="authorization-configuration"></a>授權設定

在 **用戶端** 應用程式中，為每個 [應用程式角色](#app-roles)、AAD 系統管理員角色或中的安全性群組建立 [原則](xref:security/authorization/policies) `Program.Main` 。 下列範例會建立 AAD *計費管理員* 角色的原則：

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

如需 AAD 系統管理員角色的完整識別碼清單，請參閱 Azure 檔中的 [角色範本識別碼](/azure/active-directory/roles/permissions-reference#role-template-ids) 。 如需授權原則的詳細資訊，請參閱 <xref:security/authorization/policies> 。

在下列範例中， **用戶端** 應用程式會使用上述原則來授權使用者。

此[ `AuthorizeView` 元件](xref:blazor/security/index#authorizeview-component)適用于原則：

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrator Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

您可以使用[ `[Authorize]` 屬性](xref:blazor/security/index#authorize-attribute)指示詞 () ，根據原則來存取整個元件 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ：

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

如果使用者未登入，系統會將他們重新導向至 AAD 登入頁面，然後在登入之後返回該元件。

原則檢查也可以在程式 [代碼中使用程式邏輯來執行](xref:blazor/security/index#procedural-logic)。

`Pages/CheckPolicy.razor`:

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

## <a name="authorize-server-apiweb-api-access"></a>授權伺服器 API/web API 存取

當存取權杖包含、和宣告時，伺服器 API 應用程式可授權使用者使用安全性群組、AAD 系統管理員角色和應用程式角色的[授權原則](xref:security/authorization/policies)來存取安全的 API 端點 `groups` `wids` `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` 。 下列範例會在中 `Startup.ConfigureServices` 使用 `wids` (知名的識別碼/角色範本識別碼) 宣告來建立 AAD 計費管理員角色的原則：

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

如需 AAD 系統管理員角色的完整識別碼清單，請參閱 Azure 檔中的 [角色範本識別碼](/azure/active-directory/roles/permissions-reference#role-template-ids) 。 如需授權原則的詳細資訊，請參閱 <xref:security/authorization/policies> 。

若要存取 **伺服器** 應用程式中的控制器，您可以根據 (API 檔：) 的原則名稱來使用 [ `[Authorize]` 屬性](xref:security/authorization/simple) <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> 。

下列範例會限制對 Azure 計費管理員的帳單資料存取， `BillingDataController` 原則名稱為 `BillingAdministrator` ：

```csharp
...
using Microsoft.AspNetCore.Authorization;

[Authorize(Policy = "BillingAdministrator")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

如需詳細資訊，請參閱<xref:security/authorization/policies>。

## <a name="app-roles"></a>應用程式角色

若要在 Azure 入口網站中設定應用程式以提供應用程式角色成員資格宣告，請參閱 [如何：在應用程式中新增應用程式角色，並在 Azure 檔中的權杖中接收這些角色](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) 。

下列範例假設 **用戶端** 和 **伺服器** 應用程式都是以兩個角色設定，而角色則指派給測試使用者：

* `admin`
* `developer`

> [!NOTE]
> 開發託管 Blazor WebAssembly 應用程式或獨立應用程式的用戶端-伺服器組 (獨立 Blazor WebAssembly 應用程式和獨立 ASP.NET CORE server api/web api 應用程式) 時， `appRoles` 用戶端和伺服器 Azure 入口網站應用程式註冊的資訊清單屬性都必須包含相同設定的角色。 在用戶端應用程式的資訊清單中建立角色之後，請將它們完整複製到伺服器應用程式的資訊清單。 如果您未在 `appRoles` 用戶端與伺服器應用程式註冊之間建立資訊清單的鏡像，則不會針對伺服器 api/WEB API 的已驗證使用者建立角色宣告，即使其存取權杖具有正確的角色宣告也一樣。

> [!NOTE]
> 雖然您無法將角色指派給沒有 Azure AD Premium 帳戶的群組，但您可以將角色指派給使用者，並為具有標準 Azure 帳戶的使用者接收角色宣告。 本節中的指導方針不需要 AAD Premium 帳戶。
>
> 在 Azure 入口網站中指派多個角色，方法是為每個額外的角色指派 **_重新新增使用者_** 。

`CustomAccountFactory`[[自訂使用者帳戶](#custom-user-account)] 區段中顯示的會設定為 `roles` 使用 JSON 陣列值的宣告。 `CustomAccountFactory`在 **用戶端** 應用程式中新增並註冊，如 [自訂使用者帳戶](#custom-user-account)一節所示。 不需要提供程式碼來移除原始宣告， `roles` 因為它會由架構自動移除。

在 `Program.Main` **用戶端** 應用程式的中，將名為 "" 的宣告指定為 `appRole` 檢查的角色宣告 <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> ：

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> 如果您想要使用宣告 `directoryRoles` () 新增系統管理員角色，請將 " `directoryRoles` " 指派給 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType> 。

在 `Startup.ConfigureServices` **伺服器** 應用程式的中，將名為 "" 的宣告指定為 `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` 檢查的角色宣告 <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> ：

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
    {
        Configuration.Bind("AzureAd", options);
        options.TokenValidationParameters.RoleClaimType = 
            "http://schemas.microsoft.com/ws/2008/06/identity/claims/role";
    },
    options => { Configuration.Bind("AzureAd", options); });
```

> [!NOTE]
> 如果您想要使用宣告 `wids` () 新增系統管理員角色，請將 " `wids` " 指派給 <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType> 。

元件授權方法此時可正常運作。 **用戶端** 應用程式元件中的任何授權機制都可以使用 `admin` 角色來授權使用者：

* [`AuthorizeView` 元件](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* [ `[Authorize]` 屬性](xref:blazor/security/index#authorize-attribute)指示詞 (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) 

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [程式邏輯](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

支援多個角色測試：

* 要求使用者必須在 `admin` 具有元件的 **或** `developer` 角色中 `AuthorizeView` ：

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* 要求使用者必須 **同時** 位於 `admin` **和** `developer` 角色的 `AuthorizeView` 元件中：

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* 要求使用者必須在 `admin` 具有屬性的 **或** `developer` 角色中 `[Authorize]` ：

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* 要求使用者必須 **同時** 位於 `admin` **和** `developer` 角色的 `[Authorize]` 屬性中：

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* 需要 **使用者在** `admin` **或** `developer` 角色中具有程式碼：

  ```razor
  @code {
      private async Task DoSomething()
      {
          var authState = await AuthenticationStateProvider
              .GetAuthenticationStateAsync();
          var user = authState.User;

          if (user.IsInRole("admin") || user.IsInRole("developer"))
          {
              ...
          }
          else
          {
              ...
          }
      }
  }
  ```

* 藉由 **將** 條件式 `admin`  `developer` [或 (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators)變更為上述範例中的 [條件式和 (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) ，要求使用者在和角色中具有程式性程式碼：

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

**伺服器** 應用程式控制器中的任何授權機制都可以使用該 `admin` 角色來授權使用者：

* [ `[Authorize]` 屬性](xref:blazor/security/index#authorize-attribute)指示詞 (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) 

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [程式邏輯](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

支援多個角色測試：

* 要求使用者必須在 `admin` 具有屬性的 **或** `developer` 角色中 `[Authorize]` ：

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* 要求使用者必須 **同時** 位於 `admin` **和** `developer` 角色的 `[Authorize]` 屬性中：

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* 需要 **使用者在** `admin` **或** `developer` 角色中具有程式碼：

  ```csharp
  static readonly string[] scopeRequiredByApi = new string[] { "API.Access" };

  ...

  [HttpGet]
  public IEnumerable<ReturnType> Get()
  {
      HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

      if (User.IsInRole("admin") || User.IsInRole("developer"))
      {
          ...
      }
      else
      {
          ...
      }

      return ...
  }
  ```

* 藉由 **將** 條件式 `admin`  `developer` [或 (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators)變更為上述範例中的 [條件式和 (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) ，要求使用者在和角色中具有程式性程式碼：

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a>其他資源

* [Azure 檔)  (角色範本識別碼 ](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [`groupMembershipClaims` (Azure 檔的屬性) ](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [如何：在應用程式中新增應用程式角色，並在 Azure 檔 (的權杖中接收這些角色) ](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [ (Azure 檔的應用程式角色) ](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
