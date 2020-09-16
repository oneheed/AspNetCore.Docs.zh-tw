---
title: Blazor WebAssembly具有 Azure Active Directory 群組和角色的 ASP.NET Core
author: guardrex
description: 瞭解如何設定 Blazor WebAssembly 以使用 Azure Active Directory 群組和角色。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/28/2020
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 81114768a3600544dda46efbc886e2f56932aba7
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90592925"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a>Azure AD 群組、系統管理角色和使用者定義的角色

[Luke Latham](https://github.com/guardrex) And [Javier Calvarro Nelson](https://github.com/javiercn)

Azure Active Directory (AAD) 提供數個可結合的授權方法 ASP.NET Core Identity ：

* 使用者定義群組
  * 安全性
  * Microsoft 365
  * 散發
* 角色
  * 內建的系統管理角色
  * 使用者定義角色

本文中的指導方針適用于 Blazor WebAssembly 下列主題中所述的 AAD 部署案例：

* [獨立的 Microsoft 帳戶](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [獨立的 AAD](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [使用 AAD 託管](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a>Microsoft Graph API 許可權

具有超過五個內建 AAD 系統管理員角色和安全性群組成員資格的任何應用程式使用者都需要 [MICROSOFT GRAPH API](/graph/use-the-api) 呼叫。

若要允許圖形 API 呼叫，請為裝載解決方案的獨立或用戶端應用程式提供 Blazor Azure 入口網站中的下列任何 [圖形 API 許可權](/graph/permissions-reference) ：

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

`Directory.Read.All` 是最低許可權許可權，而且是本文中所述範例所使用的許可權。

## <a name="user-defined-groups-and-built-in-administrative-roles"></a>使用者定義群組和內建系統管理角色

若要在 Azure 入口網站中設定應用程式以提供 `groups` 成員資格宣告，請參閱下列 Azure 文章。 將使用者指派給使用者定義的 AAD 群組和內建的系統管理角色。

* [使用 Azure AD 安全性群組的角色](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [`groupMembershipClaims` 屬性](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

下列範例假設使用者已指派給 AAD 內建的 *計費管理員* 角色。

AAD 所傳送的單一宣告會 `groups` 將使用者的群組和角色顯示為 JSON 陣列中 (guid) 的物件識別碼。 應用程式必須將群組和角色的 JSON 陣列轉換為 `group` 應用程式可針對其建立 [原則](xref:security/authorization/policies) 的個別宣告。

當指派的內建 Azure 系統管理角色和使用者定義群組數目超過五個時，AAD 會傳送 `hasgroups` 具有值的宣告， `true` 而不是傳送宣告 `groups` 。 任何可能有超過五個角色和群組指派給其使用者的應用程式，都必須進行個別的圖形 API 呼叫，以取得使用者的角色和群組。 本文中提供的範例執行會說明這種情況。 如需詳細資訊，請 `groups` 參閱 `hasgroups` [Microsoft 身分識別平臺存取權杖](/azure/active-directory/develop/access-tokens#payload-claims) 中的和宣告資訊：承載宣告文章。

擴充 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 以包含群組和角色的陣列屬性。 將空的陣列指派給每個屬性，以便 `null` 稍後在迴圈中使用這些屬性時，不需要檢查 `foreach` 。

`CustomUserAccount.cs`:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; } = new string[] { };

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = new string[] { };
}
```

在獨立應用程式或託管解決方案的用戶端應用程式中 Blazor ，建立自訂 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 類別。 針對取得角色和群組資訊的圖形 API 呼叫，請使用正確的範圍 (許可權) 。

`GraphAPIAuthorizationMessageHandler.cs`:

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/Directory.Read.All" });
    }
}
```

在 `Program.Main` (`Program.cs`) 中，新增 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 執行服務，並加入名 <xref:System.Net.Http.HttpClient> 為的，以提出圖形 API 要求。 下列範例會命名用戶端 `GraphAPI` ：

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

建立 AAD directory 物件類別，以從圖形 API 呼叫接收開放式資料通訊協定 (OData) 角色和群組。 OData 會以 JSON 格式送達，以及用來 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 填入類別實例的呼叫 `DirectoryObjects` 。

`DirectoryObjects.cs`:

```csharp
using System.Collections.Generic;
using System.Text.Json.Serialization;

public class DirectoryObjects
{
    [JsonPropertyName("@odata.context")]
    public string Context { get; set; }

    [JsonPropertyName("value")]
    public List<Value> Values { get; set; }
}

public class Value
{
    [JsonPropertyName("@odata.type")]
    public string Type { get; set; }

    [JsonPropertyName("id")]
    public string Id { get; set; }
}
```

建立自訂的使用者 factory 來處理角色和群組宣告。 下列範例範例也會處理宣告 `roles` 陣列，這會在 [使用者定義的角色](#user-defined-roles) 一節中討論。 如果宣告 `hasgroups` 存在， <xref:System.Net.Http.HttpClient> 就會使用命名的來提出要求圖形 API 的授權要求，以取得使用者的角色和群組。 此實行使用 Microsoft Identity Platform v1.0 端點 `https://graph.microsoft.com/v1.0/me/memberOf` ([API 檔](/graph/api/user-list-memberof)) 。 Identity針對 v2.0 升級 MSAL 套件時，本主題中的指導方針將會針對 v2.0 進行更新。

`CustomAccountFactory.cs`:

```csharp
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomUserFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
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
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                try
                {
                    var client = clientFactory.CreateClient("GraphAPI");

                    var response = await client.GetAsync("v1.0/me/memberOf");

                    if (response.IsSuccessStatusCode)
                    {
                        var userObjects = await response.Content
                            .ReadFromJsonAsync<DirectoryObjects>();

                        foreach (var obj in userObjects?.Values)
                        {
                            userIdentity.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = userIdentity.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        userIdentity.RemoveClaim(claim);
                    }
                    else
                    {
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {MESSAGE}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

不需要提供程式碼來移除原始宣告 `groups` （如果有的話），因為架構會自動將它移除。

> [!NOTE]
> 此範例中的方法：
>
> * 新增自訂 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 類別以將存取權杖附加至傳出要求。
> * 加入名 <xref:System.Net.Http.HttpClient> 為的，將 WEB api 要求發出至安全的外部 WEB api 端點。
> * 使用已命名的 <xref:System.Net.Http.HttpClient> 來發出授權的要求。
>
> 您可以在本文中找到此方法的一般涵蓋範圍 <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> 。

在 `Program.Main` `Program.cs` 託管解決方案的獨立應用程式或用戶端應用程式 () 中註冊 factory Blazor 。 同意 `Directory.Read.All` 許可權範圍作為應用程式的其他範圍：

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

在中為每個群組或角色建立 [原則](xref:security/authorization/policies) `Program.Main` 。 下列範例會建立 AAD 內建 *計費管理員* 角色的原則：

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

如需 AAD 角色物件識別碼的完整清單，請參閱 [Aad 系統管理角色群組識別碼](#aad-adminstrative-role-group-ids) 一節。

在下列範例中，應用程式會使用上述原則來授權使用者。

此[ `AuthorizeView` 元件](xref:blazor/security/index#authorizeview-component)適用于原則：

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrative Role
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

您也可以 [在程式碼中使用程式邏輯來執行](xref:blazor/security/index#procedural-logic)原則檢查：

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

## <a name="user-defined-roles"></a>使用者定義角色

AAD 註冊的應用程式也可以設定為使用使用者定義的角色。

若要在 Azure 入口網站中設定應用程式以提供 `roles` 成員資格宣告，請參閱 [如何：在應用程式中新增應用程式角色，並在 Azure 檔中的權杖中接收這些角色](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) 。

下列範例假設應用程式已設定兩個角色：

* `admin`
* `developer`

> [!NOTE]
> 雖然您無法將角色指派給沒有 Azure AD Premium 帳戶的安全性群組，但您可以將使用者指派給角色，並接收 `roles` 具有標準 Azure 帳戶的使用者宣告。 本節中的指導方針不需要 Azure AD Premium 帳戶。
>
> 在 Azure 入口網站中指派多個角色，方法是為每個額外的角色指派 **_重新新增使用者_** 。

AAD 所傳送的單一宣告會 `roles` 將使用者定義的角色顯示為 `appRoles` `value` JSON 陣列中的 s。 應用程式必須將角色的 JSON 陣列轉換成個別 `role` 宣告。

`CustomUserFactory`[使用者定義群組和 AAD 內建系統管理角色](#user-defined-groups-and-built-in-administrative-roles)區段中顯示的會設定為 `roles` 使用 JSON 陣列值的宣告。 `CustomUserFactory`在託管解決方案的獨立應用程式或用戶端應用程式中新增並註冊， Blazor 如[使用者定義的群組和 AAD 內建的系統管理角色](#user-defined-groups-and-built-in-administrative-roles)一節所示。 不需要提供程式碼來移除原始宣告， `roles` 因為它會由架構自動移除。

在 `Program.Main` 託管解決方案的獨立應用程式或用戶端應用程式中 Blazor ，將名為 "" 的宣告指定 `role` 為角色宣告：

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

元件授權方法此時可正常運作。 元件中的任何授權機制都可以使用 `admin` 角色來授權使用者：

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

## <a name="aad-adminstrative-role-group-ids"></a>AAD 系統管理角色群組識別碼

下表中提供的物件識別碼是用來建立宣告[policies](xref:security/authorization/policies)的原則 `group` 。 原則可讓應用程式在應用程式中授權使用者使用各種活動。 如需詳細資訊，請參閱 [使用者定義的群組和 AAD 內建的系統管理角色](#user-defined-groups-and-built-in-administrative-roles) 一節。

AAD 系統管理角色 | 物件識別碼
--- | ---
應用程式管理員 | fa11557b-4f15-4ddd-85d5-313c7cd74047
應用程式開發人員 | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
驗證系統管理員 | 02d110a1-96b1-419e-af87-746461b60ed7
Azure DevOps 管理員 | a5311ace-ca41-44cd-b833-8d22caa0b34f
Azure 資訊保護管理員 | 18632dce-f9b5-4f01-abb5-37051f06860e
B2C IEF 金鑰集管理員 | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
B2C IEF 原則管理員 | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
B2C 使用者流程管理員 | baa531b7-8cf0-44ad-8f98-eded88dae827
B2C 使用者流程屬性管理員 | dd0baca0-a535-48c1-b871-8431abe16452
計費管理員 | 69ff516a-b57d-4697-a429-9de4af7b5609
雲端應用程式系統管理員 | 250b5fe3-b553-458d-9a53-b782c13c34bf
雲端裝置管理員 | 26cd4b44-2636-4ddb-bdfa-27feae66f86d
規範管理員 | 9d6e1dd0-c9f8-45f8-b558-b134f700116c
合規性資料管理員 | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
條件式存取系統管理員 | 8f71a611-137d-49af-87ad-e97f1fd5da76
客戶 LockBox 存取核准者 | c18d54a8-b13e-4954-a1a4-7deaf2e4f184
電腦分析系統管理員 | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
目錄讀取器 | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Dynamics 365 管理員 | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
Exchange 系統管理員 | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
外部 Identity 提供者管理員 | febfaeb4-e478-407a-b4b3-f4d9716618a2
全域管理員 | a45ba61b-44db-462c-924b-3b2719152588
全域讀取者 | f6903b21-6aba-4124-b44c-76671796b9d5
群組管理員 | 158b3e5a-d89d-460b-92b5-3b34985f0197
來賓邀請者 | 4c730a1d-cc22-44af-8f9f-4eec635c7502
服務台管理員 | 108678c8-6628-44e1-8d01-caf598a6a5f5
Intune 管理員 | 79950741-23fa-4189-b2cb-46640601c497
Kaizala 管理員 | d6322af2-48e7-42e0-8c68-0bbe31af3412
授權管理員 | 3355458a-e423-44bf-8b98-4ac5e572cea5
訊息中心隱私權讀取者 | 6395db95-9fb8-42b9-b1ed-30a2405eee6f
訊息中心讀取者 | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Office 應用程式管理員 | 5f3870cd-b042-4f93-86d7-c9d77c664dc7
密碼管理員 | 466e48b7-5d66-4ae5-8911-1a118de74941
Power BI 管理員 | 984e83b8-8337-4255-91a1-acb663175ab4
Power Platform 管理員 | 76d6f95e-9a15-4d7d-8d21-00de00faf9fd
特殊權限驗證管理員 | 0829f731-b46d-419f-9742-aeb122367d11
特殊權限角色管理員 | f20a725a-d1c8-4107-83ea-1171c97d00c7
報表讀者 | 54635450-e8ed-4f2d-9632-07db2517b4de
搜尋管理員 | c770a2f1-c9ba-4e60-9176-9f52b1eb1a31
搜尋編輯者 | 6a6858c6-5f0d-44ac-87c7-0190fbedd271
安全性系統管理員 | 20fa50e3-6531-44d8-bd39-b251420568ad
安全性操作員 | 43aae017-8e51-4188-91ab-e6debd572800
安全性讀取者 | 45035cd3-fd97-4250-8197-3a53d3562d9b
服務支援管理員 | 2c92cf45-c914-48f8-9bf9-fc14b28818ab
SharePoint 管理員 | e1c32229-875e-461d-ae24-3cb99116e86c
商務用 Skype 的管理員 | 0a8cee12-e21d-43ef-abd9-f1ea85710e30
Microsoft Teams 通訊系統管理員 | 2393e455-6e13-4743-9f52-63fcec2b6a9c
Microsoft Teams 通訊支援工程師 | 802dd94e-d717-46f6-af98-b9167071e9fc
團隊通訊專家 | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Microsoft Teams 服務管理員 | 8846a0be-197b-443a-b13c-11192691fa24
使用者管理員 | 1f6eed58-7dd3-460b-a298-666f975427a1

## <a name="additional-resources"></a>其他資源

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
