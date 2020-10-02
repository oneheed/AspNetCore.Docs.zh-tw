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
ms.openlocfilehash: 7a0c606d82dd625c179ec89e22b9313dfa5d18b4
ms.sourcegitcommit: c026bf76a0e14a5ee68983519a63574c674e9ff7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91636773"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="5c091-103">Azure Active Directory (AAD) 群組、系統管理員角色和使用者定義的角色</span><span class="sxs-lookup"><span data-stu-id="5c091-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="5c091-104">[Luke Latham](https://github.com/guardrex) And [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="5c091-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="5c091-105">Azure Active Directory (AAD) 提供數個可結合的授權方法 ASP.NET Core Identity ：</span><span class="sxs-lookup"><span data-stu-id="5c091-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="5c091-106">使用者定義群組</span><span class="sxs-lookup"><span data-stu-id="5c091-106">User-defined groups</span></span>
  * <span data-ttu-id="5c091-107">安全性</span><span class="sxs-lookup"><span data-stu-id="5c091-107">Security</span></span>
  * <span data-ttu-id="5c091-108">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="5c091-108">Microsoft 365</span></span>
  * <span data-ttu-id="5c091-109">散發</span><span class="sxs-lookup"><span data-stu-id="5c091-109">Distribution</span></span>
* <span data-ttu-id="5c091-110">角色</span><span class="sxs-lookup"><span data-stu-id="5c091-110">Roles</span></span>
  * <span data-ttu-id="5c091-111">AAD 系統管理員角色</span><span class="sxs-lookup"><span data-stu-id="5c091-111">AAD Administrator Roles</span></span>
  * <span data-ttu-id="5c091-112">使用者定義角色</span><span class="sxs-lookup"><span data-stu-id="5c091-112">User-defined roles</span></span>

<span data-ttu-id="5c091-113">本文中的指導方針適用于 Blazor WebAssembly 下列主題中所述的 AAD 部署案例：</span><span class="sxs-lookup"><span data-stu-id="5c091-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="5c091-114">獨立的 Microsoft 帳戶</span><span class="sxs-lookup"><span data-stu-id="5c091-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="5c091-115">獨立的 AAD</span><span class="sxs-lookup"><span data-stu-id="5c091-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="5c091-116">使用 AAD 託管</span><span class="sxs-lookup"><span data-stu-id="5c091-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a><span data-ttu-id="5c091-117">Microsoft Graph API 許可權</span><span class="sxs-lookup"><span data-stu-id="5c091-117">Microsoft Graph API permission</span></span>

<span data-ttu-id="5c091-118">具有超過五個 AAD 系統管理員角色和安全性群組成員資格的任何應用程式使用者都需要 [MICROSOFT GRAPH API](/graph/use-the-api) 呼叫。</span><span class="sxs-lookup"><span data-stu-id="5c091-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="5c091-119">若要允許圖形 API 呼叫，請為裝載解決方案的獨立或用戶端應用程式提供 Blazor Azure 入口網站中的下列任何 [圖形 API 許可權](/graph/permissions-reference) ：</span><span class="sxs-lookup"><span data-stu-id="5c091-119">To permit Graph API calls, give the standalone or client app of a hosted Blazor solution any of the following [Graph API permissions](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="5c091-120">`Directory.Read.All` 是最低許可權許可權，而且是本文中所述範例所使用的許可權。</span><span class="sxs-lookup"><span data-stu-id="5c091-120">`Directory.Read.All` is the least-privileged permission and is the permission used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="5c091-121">使用者定義群組和系統管理員角色</span><span class="sxs-lookup"><span data-stu-id="5c091-121">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="5c091-122">若要在 Azure 入口網站中設定應用程式以提供 `groups` 成員資格宣告，請參閱下列 Azure 文章。</span><span class="sxs-lookup"><span data-stu-id="5c091-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="5c091-123">將使用者指派給使用者定義的 AAD 群組和 AAD 系統管理員角色。</span><span class="sxs-lookup"><span data-stu-id="5c091-123">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="5c091-124">使用 Azure AD 安全性群組的角色</span><span class="sxs-lookup"><span data-stu-id="5c091-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="5c091-125">`groupMembershipClaims` 屬性</span><span class="sxs-lookup"><span data-stu-id="5c091-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="5c091-126">下列範例假設使用者已指派給 AAD *計費管理員* 角色。</span><span class="sxs-lookup"><span data-stu-id="5c091-126">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="5c091-127">AAD 所傳送的單一宣告會 `groups` 將使用者的群組和角色顯示為 JSON 陣列中 (guid) 的物件識別碼。</span><span class="sxs-lookup"><span data-stu-id="5c091-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="5c091-128">應用程式必須將群組和角色的 JSON 陣列轉換為 `group` 應用程式可針對其建立 [原則](xref:security/authorization/policies) 的個別宣告。</span><span class="sxs-lookup"><span data-stu-id="5c091-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="5c091-129">當指派的 AAD 系統管理員角色和使用者定義群組數目超過五個時，AAD 會傳送具有值的宣告， `hasgroups` `true` 而不是傳送宣告 `groups` 。</span><span class="sxs-lookup"><span data-stu-id="5c091-129">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="5c091-130">任何可能有超過五個角色和群組指派給其使用者的應用程式，都必須進行個別的圖形 API 呼叫，以取得使用者的角色和群組。</span><span class="sxs-lookup"><span data-stu-id="5c091-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="5c091-131">本文中提供的範例執行會說明這種情況。</span><span class="sxs-lookup"><span data-stu-id="5c091-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="5c091-132">如需詳細資訊，請 `groups` 參閱 `hasgroups` [Microsoft 身分識別平臺存取權杖](/azure/active-directory/develop/access-tokens#payload-claims) 中的和宣告資訊：承載宣告文章。</span><span class="sxs-lookup"><span data-stu-id="5c091-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="5c091-133">擴充 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 以包含群組和角色的陣列屬性。</span><span class="sxs-lookup"><span data-stu-id="5c091-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="5c091-134">將空的陣列指派給每個屬性，以便 `null` 稍後在迴圈中使用這些屬性時，不需要檢查 `foreach` 。</span><span class="sxs-lookup"><span data-stu-id="5c091-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="5c091-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="5c091-135">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="5c091-136">在獨立應用程式或託管解決方案的用戶端應用程式中 Blazor ，建立自訂 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 類別。</span><span class="sxs-lookup"><span data-stu-id="5c091-136">In the standalone app or the client app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="5c091-137">針對取得角色和群組資訊的圖形 API 呼叫，請使用正確的範圍 (許可權) 。</span><span class="sxs-lookup"><span data-stu-id="5c091-137">Use the correct scope (permission) for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="5c091-138">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="5c091-138">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="5c091-139">在 `Program.Main` (`Program.cs`) 中，新增 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 執行服務，並加入名 <xref:System.Net.Http.HttpClient> 為的，以提出圖形 API 要求。</span><span class="sxs-lookup"><span data-stu-id="5c091-139">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="5c091-140">下列範例會命名用戶端 `GraphAPI` ：</span><span class="sxs-lookup"><span data-stu-id="5c091-140">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="5c091-141">建立 AAD directory 物件類別，以從圖形 API 呼叫接收開放式資料通訊協定 (OData) 角色和群組。</span><span class="sxs-lookup"><span data-stu-id="5c091-141">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="5c091-142">OData 會以 JSON 格式送達，以及用來 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 填入類別實例的呼叫 `DirectoryObjects` 。</span><span class="sxs-lookup"><span data-stu-id="5c091-142">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="5c091-143">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="5c091-143">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="5c091-144">建立自訂的使用者 factory 來處理角色和群組宣告。</span><span class="sxs-lookup"><span data-stu-id="5c091-144">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="5c091-145">下列範例範例也會處理宣告 `roles` 陣列，這會在 [使用者定義的角色](#user-defined-roles) 一節中討論。</span><span class="sxs-lookup"><span data-stu-id="5c091-145">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="5c091-146">如果宣告 `hasgroups` 存在， <xref:System.Net.Http.HttpClient> 就會使用命名的來提出要求圖形 API 的授權要求，以取得使用者的角色和群組。</span><span class="sxs-lookup"><span data-stu-id="5c091-146">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="5c091-147">此實行使用 Microsoft Identity Platform v1.0 端點 `https://graph.microsoft.com/v1.0/me/memberOf` ([API 檔](/graph/api/user-list-memberof)) 。</span><span class="sxs-lookup"><span data-stu-id="5c091-147">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span> <span data-ttu-id="5c091-148">Identity針對 v2.0 升級 MSAL 套件時，本主題中的指導方針將會針對 v2.0 進行更新。</span><span class="sxs-lookup"><span data-stu-id="5c091-148">The guidance in this topic will be updated for Identity v2.0 when the MSAL packages are upgraded for v2.0.</span></span>

<span data-ttu-id="5c091-149">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="5c091-149">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="5c091-150">不需要提供程式碼來移除原始宣告 `groups` （如果有的話），因為架構會自動將它移除。</span><span class="sxs-lookup"><span data-stu-id="5c091-150">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="5c091-151">此範例中的方法：</span><span class="sxs-lookup"><span data-stu-id="5c091-151">The approach in this example:</span></span>
>
> * <span data-ttu-id="5c091-152">新增自訂 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 類別以將存取權杖附加至傳出要求。</span><span class="sxs-lookup"><span data-stu-id="5c091-152">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="5c091-153">加入名 <xref:System.Net.Http.HttpClient> 為的，將 WEB api 要求發出至安全的外部 WEB api 端點。</span><span class="sxs-lookup"><span data-stu-id="5c091-153">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="5c091-154">使用已命名的 <xref:System.Net.Http.HttpClient> 來發出授權的要求。</span><span class="sxs-lookup"><span data-stu-id="5c091-154">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="5c091-155">您可以在本文中找到此方法的一般涵蓋範圍 <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> 。</span><span class="sxs-lookup"><span data-stu-id="5c091-155">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="5c091-156">在 `Program.Main` `Program.cs` 託管解決方案的獨立應用程式或用戶端應用程式 () 中註冊 factory Blazor 。</span><span class="sxs-lookup"><span data-stu-id="5c091-156">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or client app of a hosted Blazor solution.</span></span> <span data-ttu-id="5c091-157">同意 `Directory.Read.All` 許可權範圍作為應用程式的其他範圍：</span><span class="sxs-lookup"><span data-stu-id="5c091-157">Consent to the `Directory.Read.All` permission scope as an additional scope for the app:</span></span>

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

<span data-ttu-id="5c091-158">在中為每個群組或角色建立 [原則](xref:security/authorization/policies) `Program.Main` 。</span><span class="sxs-lookup"><span data-stu-id="5c091-158">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="5c091-159">下列範例會建立 AAD *計費管理員* 角色的原則：</span><span class="sxs-lookup"><span data-stu-id="5c091-159">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="5c091-160">如需 AAD 角色物件識別碼的完整清單，請參閱 [Aad 系統管理員角色物件識別碼](#aad-administrator-role-object-ids) 一節。</span><span class="sxs-lookup"><span data-stu-id="5c091-160">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="5c091-161">在下列範例中，應用程式會使用上述原則來授權使用者。</span><span class="sxs-lookup"><span data-stu-id="5c091-161">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="5c091-162">此[ `AuthorizeView` 元件](xref:blazor/security/index#authorizeview-component)適用于原則：</span><span class="sxs-lookup"><span data-stu-id="5c091-162">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="5c091-163">您可以使用[ `[Authorize]` 屬性](xref:blazor/security/index#authorize-attribute)指示詞 () ，根據原則來存取整個元件 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ：</span><span class="sxs-lookup"><span data-stu-id="5c091-163">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="5c091-164">如果使用者未登入，系統會將他們重新導向至 AAD 登入頁面，然後在登入之後返回該元件。</span><span class="sxs-lookup"><span data-stu-id="5c091-164">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="5c091-165">您也可以 [在程式碼中使用程式邏輯來執行](xref:blazor/security/index#procedural-logic)原則檢查：</span><span class="sxs-lookup"><span data-stu-id="5c091-165">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="5c091-166">授權使用者定義群組和系統管理員角色的伺服器 API 存取權</span><span class="sxs-lookup"><span data-stu-id="5c091-166">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="5c091-167">除了授權用戶端 WebAssembly 應用程式中的使用者存取頁面和資源，伺服器 API 還可以授權使用者存取安全的 API 端點。</span><span class="sxs-lookup"><span data-stu-id="5c091-167">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="5c091-168">在 *伺服器* 應用程式驗證使用者的存取權杖之後：</span><span class="sxs-lookup"><span data-stu-id="5c091-168">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="5c091-169">應用程式會使用使用者的不可變 [物件識別碼宣告 (`oid`) ](/azure/active-directory/develop/id-tokens#payload-claims) 從 JWT (`id_token`) 取得圖形 API 的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="5c091-169">The app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from the JWT (`id_token`) to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="5c091-170">圖形 API 呼叫會取得使用者的 Azure 使用者定義安全性群組和系統管理員角色成員資格。</span><span class="sxs-lookup"><span data-stu-id="5c091-170">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships.</span></span>
* <span data-ttu-id="5c091-171">成員資格是用來建立 `group` 宣告。</span><span class="sxs-lookup"><span data-stu-id="5c091-171">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="5c091-172">[授權原則](xref:security/authorization/policies) 可以用來限制使用者對伺服器 API 端點的存取。</span><span class="sxs-lookup"><span data-stu-id="5c091-172">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints.</span></span>

> [!NOTE]
> <span data-ttu-id="5c091-173">本指南目前不包含授權使用者的 [AAD 使用者定義角色](#user-defined-roles)基礎。</span><span class="sxs-lookup"><span data-stu-id="5c091-173">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

### <a name="packages"></a><span data-ttu-id="5c091-174">套件</span><span class="sxs-lookup"><span data-stu-id="5c091-174">Packages</span></span>

<span data-ttu-id="5c091-175">針對下列套件，將套件參考新增至 *伺服器* 應用程式：</span><span class="sxs-lookup"><span data-stu-id="5c091-175">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="5c091-176">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="5c091-176">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="5c091-177">[Microsoft。 IdentityModel. ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="5c091-177">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="5c091-178">Azure 設定</span><span class="sxs-lookup"><span data-stu-id="5c091-178">Azure configuration</span></span>

* <span data-ttu-id="5c091-179">確認已對 *伺服器* 應用程式註冊授與圖形 API 許可權的 API 存取權 `Directory.Read.All` ，這是安全性群組的最低許可權存取層級。</span><span class="sxs-lookup"><span data-stu-id="5c091-179">Confirm that the *Server* app registration is given API access to the Graph API permission for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="5c091-180">確認在進行許可權指派之後，會將系統管理員同意套用至許可權。</span><span class="sxs-lookup"><span data-stu-id="5c091-180">Confirm that admin consent is applied to the permission after making the permission assignment.</span></span>
* <span data-ttu-id="5c091-181">將新的用戶端密碼指派給 *伺服器* 應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c091-181">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="5c091-182">在 [ [應用程式設定](#app-settings) ] 區段中，記下應用程式設定的秘密。</span><span class="sxs-lookup"><span data-stu-id="5c091-182">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="5c091-183">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="5c091-183">App settings</span></span>

<span data-ttu-id="5c091-184">在應用程式佈建檔 (`appsettings.json` 或 `appsettings.Production.json`) 中， `ClientSecret` 使用來自 Azure 入口網站的 *伺服器* 應用程式用戶端密碼建立專案：</span><span class="sxs-lookup"><span data-stu-id="5c091-184">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="5c091-185">例如：</span><span class="sxs-lookup"><span data-stu-id="5c091-185">For example:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

### <a name="authorization-policies"></a><span data-ttu-id="5c091-186">在命名空間層級設定的授權原則</span><span class="sxs-lookup"><span data-stu-id="5c091-186">Authorization policies</span></span>

<span data-ttu-id="5c091-187">*Server* [authorization policies](xref:security/authorization/policies) `Startup.ConfigureServices` `Startup.cs` 根據群組物件識別碼和[aad 系統管理員角色物件識別碼](#aad-administrator-role-object-ids)，在伺服器應用程式的 () 中建立 aad 安全性群組和 aad 系統管理員角色的授權原則。</span><span class="sxs-lookup"><span data-stu-id="5c091-187">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="5c091-188">例如，Azure 計費管理員角色原則具有下列設定：</span><span class="sxs-lookup"><span data-stu-id="5c091-188">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="5c091-189">如需詳細資訊，請參閱<xref:security/authorization/policies>。</span><span class="sxs-lookup"><span data-stu-id="5c091-189">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="5c091-190">控制器存取</span><span class="sxs-lookup"><span data-stu-id="5c091-190">Controller access</span></span>

<span data-ttu-id="5c091-191">需要 *伺服器* 應用程式控制器上的原則。</span><span class="sxs-lookup"><span data-stu-id="5c091-191">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="5c091-192">下列範例會 `BillingDataController` `BillingAdmin` 根據 [ [授權原則](#authorization-policies) ] 區段中所設定的原則名稱，限制對 Azure 計費管理員的帳單資料存取權：</span><span class="sxs-lookup"><span data-stu-id="5c091-192">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

### <a name="service-configuration"></a><span data-ttu-id="5c091-193">服務設定</span><span class="sxs-lookup"><span data-stu-id="5c091-193">Service configuration</span></span>

<span data-ttu-id="5c091-194">在 *伺服器* 應用程式的 `Startup.ConfigureServices` 方法中，新增邏輯以進行圖形 API 呼叫，並建立 `group` 使用者安全性群組和角色的使用者宣告。</span><span class="sxs-lookup"><span data-stu-id="5c091-194">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="5c091-195">本節中的範例程式碼會使用 Active Directory 驗證程式庫 (ADAL) （以 Microsoft Platform v1.0 為基礎） Identity 。</span><span class="sxs-lookup"><span data-stu-id="5c091-195">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span> <span data-ttu-id="5c091-196">本主題將針對適用于 .NET 5 的 v2.0 進行更新 Identity 。</span><span class="sxs-lookup"><span data-stu-id="5c091-196">This topic will be updated for Identity v2.0 for .NET 5.</span></span> <span data-ttu-id="5c091-197">監視 [[RC1] Microsoft Identity Platform 2.0 for Blazor (dotnet/AspNetCore.Docs #19503) ，以 ](https://github.com/dotnet/AspNetCore.Docs/issues/19503)追蹤此工作的進度。</span><span class="sxs-lookup"><span data-stu-id="5c091-197">Track progress on this work by monitoring [[RC1] Microsoft Identity Platform 2.0 for Blazor (dotnet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503).</span></span>

<span data-ttu-id="5c091-198">`Startup`*伺服器*應用程式類別中的程式碼需要其他命名空間。</span><span class="sxs-lookup"><span data-stu-id="5c091-198">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="5c091-199">下列一組 `using` 語句包括本節後面的程式碼所需的命名空間：</span><span class="sxs-lookup"><span data-stu-id="5c091-199">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Linq;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.AzureAD.UI;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Graph;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.IdentityModel.Logging;
```

<span data-ttu-id="5c091-200">設定時 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> ：</span><span class="sxs-lookup"><span data-stu-id="5c091-200">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="5c091-201">（選擇性）包含的處理 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="5c091-201">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="5c091-202">例如，應用程式可以記錄失敗的驗證。</span><span class="sxs-lookup"><span data-stu-id="5c091-202">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="5c091-203">在中 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> ，提出圖形 API 呼叫，以取得使用者的群組和角色。</span><span class="sxs-lookup"><span data-stu-id="5c091-203">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="5c091-204"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> 提供個人識別資訊 (PII) 記錄訊息中。</span><span class="sxs-lookup"><span data-stu-id="5c091-204"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="5c091-205">只啟用 PII 以測試使用者帳戶進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="5c091-205">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="5c091-206">在 `Startup.ConfigureServices` 中：</span><span class="sxs-lookup"><span data-stu-id="5c091-206">In `Startup.ConfigureServices`:</span></span>

```csharp
#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
        OnAuthenticationFailed = context =>
        {
            // Optional: Log the exception

#if DEBUG
            Console.WriteLine($"OnAuthenticationFailed: {context.Exception}");
#endif

            return Task.FromResult(0);
        },
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;
            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var authContext = new AuthenticationContext(
                    Configuration["AzureAd:Instance"] +
                    Configuration["AzureAd:TenantId"]);
                AuthenticationResult authResult = null;

                try
                {
                    authResult = await authContext.AcquireTokenSilentAsync(
                        "https://graph.microsoft.com", 
                        Configuration["AzureAd:ClientId"]);
                }
                catch (AdalException adalException)
                {
                    if (adalException.ErrorCode == 
                        AdalError.FailedToAcquireTokenSilently || 
                        adalException.ErrorCode == 
                        AdalError.UserInteractionRequired)
                    {
                        var userAssertion = new UserAssertion(accessToken.RawData,
                            "urn:ietf:params:oauth:grant-type:jwt-bearer", oid);
                        var clientCredential = new ClientCredential(
                            Configuration["AzureAd:ClientId"],
                            Configuration["AzureAd:ClientSecret"]);
                        authResult = await authContext.AcquireTokenAsync(
                            "https://graph.microsoft.com", clientCredential, 
                            userAssertion);
                    }
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", 
                                authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                        .Request().GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error

#if DEBUG
                    Console.WriteLine(
                        "OnTokenValidated: Service Exception: " +
                        $"{serviceException.Message}");
#endif
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }
            else
            {
                // Optional: Log missing OID claim

#if DEBUG
                Console.WriteLine($"OnTokenValidated: OID missing: " +
                    $"{accessToken.RawData}");
#endif
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="5c091-207">在上述範例中：</span><span class="sxs-lookup"><span data-stu-id="5c091-207">In the preceding example:</span></span>

* <span data-ttu-id="5c091-208"><xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>因為存取權杖可能已儲存在 ADAL 權杖快取中，所以會先嘗試使用「無訊息權杖取得」 () 。</span><span class="sxs-lookup"><span data-stu-id="5c091-208">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="5c091-209">從快取取得權杖比要求新權杖更快。</span><span class="sxs-lookup"><span data-stu-id="5c091-209">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="5c091-210">如果不是 (從快取取得存取 <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> 權杖 <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> ，或) 擲回，則 <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion> 會使用用戶端認證來) 使用者判斷提示 ( (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) ，代表使用者 () 取得權杖 <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="5c091-210">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="5c091-211">接下來， `Microsoft.Graph.GraphServiceClient` 可以繼續使用權杖來進行圖形 API 呼叫。</span><span class="sxs-lookup"><span data-stu-id="5c091-211">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="5c091-212">權杖會置於 ADAL 權杖快取中。</span><span class="sxs-lookup"><span data-stu-id="5c091-212">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="5c091-213">針對相同使用者的未來圖形 API 呼叫，會以無訊息方式從快取中取得權杖 <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="5c091-213">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

<span data-ttu-id="5c091-214">中的程式碼 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> 不會取得可轉移的成員資格。</span><span class="sxs-lookup"><span data-stu-id="5c091-214">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="5c091-215">若要變更程式碼以取得直接與可轉移的成員資格：</span><span class="sxs-lookup"><span data-stu-id="5c091-215">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="5c091-216">針對程式程式碼：</span><span class="sxs-lookup"><span data-stu-id="5c091-216">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="5c091-217">以下列內容取代上述程式程式碼：</span><span class="sxs-lookup"><span data-stu-id="5c091-217">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="5c091-218">針對程式程式碼：</span><span class="sxs-lookup"><span data-stu-id="5c091-218">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="5c091-219">以下列內容取代上述程式程式碼：</span><span class="sxs-lookup"><span data-stu-id="5c091-219">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="5c091-220">建立宣告時，中的程式碼 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> 不會區分 aad 安全性群組和 Aad 系統管理員角色。</span><span class="sxs-lookup"><span data-stu-id="5c091-220">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="5c091-221">若要讓應用程式區分群組和角色，請在 `entry.ODataType` 逐一查看群組和角色時檢查。</span><span class="sxs-lookup"><span data-stu-id="5c091-221">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="5c091-222">若要建立不同的安全性群組和角色宣告，請使用類似下列的程式碼：</span><span class="sxs-lookup"><span data-stu-id="5c091-222">To create separate security group and role claims, use code similar to the following:</span></span>

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        userIdentity.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        userIdentity.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a><span data-ttu-id="5c091-223">使用者定義角色</span><span class="sxs-lookup"><span data-stu-id="5c091-223">User-defined roles</span></span>

<span data-ttu-id="5c091-224">AAD 註冊的應用程式也可以設定為使用使用者定義的角色。</span><span class="sxs-lookup"><span data-stu-id="5c091-224">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="5c091-225">若要在 Azure 入口網站中設定應用程式以提供 `roles` 成員資格宣告，請參閱 [如何：在應用程式中新增應用程式角色，並在 Azure 檔中的權杖中接收這些角色](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) 。</span><span class="sxs-lookup"><span data-stu-id="5c091-225">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="5c091-226">下列範例假設應用程式已設定兩個角色：</span><span class="sxs-lookup"><span data-stu-id="5c091-226">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="5c091-227">雖然您無法將角色指派給沒有 Azure AD Premium 帳戶的安全性群組，但您可以將使用者指派給角色，並接收 `roles` 具有標準 Azure 帳戶的使用者宣告。</span><span class="sxs-lookup"><span data-stu-id="5c091-227">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="5c091-228">本節中的指導方針不需要 Azure AD Premium 帳戶。</span><span class="sxs-lookup"><span data-stu-id="5c091-228">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="5c091-229">在 Azure 入口網站中指派多個角色，方法是為每個額外的角色指派 **_重新新增使用者_** 。</span><span class="sxs-lookup"><span data-stu-id="5c091-229">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="5c091-230">AAD 所傳送的單一宣告會 `roles` 將使用者定義的角色顯示為 `appRoles` `value` JSON 陣列中的 s。</span><span class="sxs-lookup"><span data-stu-id="5c091-230">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="5c091-231">應用程式必須將角色的 JSON 陣列轉換成個別 `role` 宣告。</span><span class="sxs-lookup"><span data-stu-id="5c091-231">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="5c091-232">`CustomUserFactory`[使用者定義群組和 AAD 系統管理員角色](#user-defined-groups-and-administrator-roles)一節中顯示的會設定為 `roles` 使用 JSON 陣列值的宣告。</span><span class="sxs-lookup"><span data-stu-id="5c091-232">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="5c091-233">`CustomUserFactory`在託管解決方案的獨立應用程式或用戶端應用程式中新增並註冊， Blazor 如[使用者定義的群組和 AAD 系統管理員角色](#user-defined-groups-and-administrator-roles)一節所示。</span><span class="sxs-lookup"><span data-stu-id="5c091-233">Add and register the `CustomUserFactory` in the standalone app or client app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="5c091-234">不需要提供程式碼來移除原始宣告， `roles` 因為它會由架構自動移除。</span><span class="sxs-lookup"><span data-stu-id="5c091-234">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="5c091-235">在 `Program.Main` 託管解決方案的獨立應用程式或用戶端應用程式中 Blazor ，將名為 "" 的宣告指定 `role` 為角色宣告：</span><span class="sxs-lookup"><span data-stu-id="5c091-235">In `Program.Main` of the standalone app or client app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="5c091-236">元件授權方法此時可正常運作。</span><span class="sxs-lookup"><span data-stu-id="5c091-236">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="5c091-237">元件中的任何授權機制都可以使用 `admin` 角色來授權使用者：</span><span class="sxs-lookup"><span data-stu-id="5c091-237">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="5c091-238">[ `AuthorizeView` 元件](xref:blazor/security/index#authorizeview-component) (範例： `<AuthorizeView Roles="admin">`) </span><span class="sxs-lookup"><span data-stu-id="5c091-238">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="5c091-239">[ `[Authorize]` 屬性](xref:blazor/security/index#authorize-attribute)指示詞 (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)  (範例： `@attribute [Authorize(Roles = "admin")]`) </span><span class="sxs-lookup"><span data-stu-id="5c091-239">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="5c091-240">程式[邏輯](xref:blazor/security/index#procedural-logic) (範例： `if (user.IsInRole("admin")) { ... }`) </span><span class="sxs-lookup"><span data-stu-id="5c091-240">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="5c091-241">支援多個角色測試：</span><span class="sxs-lookup"><span data-stu-id="5c091-241">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="5c091-242">AAD 系統管理員角色物件識別碼</span><span class="sxs-lookup"><span data-stu-id="5c091-242">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="5c091-243">下表中提供的物件識別碼是用來建立宣告[policies](xref:security/authorization/policies)的原則 `group` 。</span><span class="sxs-lookup"><span data-stu-id="5c091-243">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="5c091-244">原則可讓應用程式在應用程式中授權使用者使用各種活動。</span><span class="sxs-lookup"><span data-stu-id="5c091-244">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="5c091-245">如需詳細資訊，請參閱 [使用者定義的群組和 AAD 系統管理員角色](#user-defined-groups-and-administrator-roles) 一節。</span><span class="sxs-lookup"><span data-stu-id="5c091-245">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="5c091-246">AAD 系統管理員角色</span><span class="sxs-lookup"><span data-stu-id="5c091-246">AAD Administrator Role</span></span> | <span data-ttu-id="5c091-247">物件識別碼</span><span class="sxs-lookup"><span data-stu-id="5c091-247">Object ID</span></span>
--- | ---
<span data-ttu-id="5c091-248">應用程式管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-248">Application administrator</span></span> | <span data-ttu-id="5c091-249">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="5c091-249">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="5c091-250">應用程式開發人員</span><span class="sxs-lookup"><span data-stu-id="5c091-250">Application developer</span></span> | <span data-ttu-id="5c091-251">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="5c091-251">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="5c091-252">驗證系統管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-252">Authentication administrator</span></span> | <span data-ttu-id="5c091-253">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="5c091-253">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="5c091-254">Azure DevOps 管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-254">Azure DevOps administrator</span></span> | <span data-ttu-id="5c091-255">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="5c091-255">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="5c091-256">Azure 資訊保護管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-256">Azure Information Protection administrator</span></span> | <span data-ttu-id="5c091-257">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="5c091-257">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="5c091-258">B2C IEF 金鑰集管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-258">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="5c091-259">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="5c091-259">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="5c091-260">B2C IEF 原則管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-260">B2C IEF Policy administrator</span></span> | <span data-ttu-id="5c091-261">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="5c091-261">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="5c091-262">B2C 使用者流程管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-262">B2C user flow administrator</span></span> | <span data-ttu-id="5c091-263">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="5c091-263">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="5c091-264">B2C 使用者流程屬性管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-264">B2C user flow attribute administrator</span></span> | <span data-ttu-id="5c091-265">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="5c091-265">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="5c091-266">計費管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-266">Billing administrator</span></span> | <span data-ttu-id="5c091-267">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="5c091-267">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="5c091-268">雲端應用程式系統管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-268">Cloud application administrator</span></span> | <span data-ttu-id="5c091-269">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="5c091-269">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="5c091-270">雲端裝置管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-270">Cloud device administrator</span></span> | <span data-ttu-id="5c091-271">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="5c091-271">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="5c091-272">規範管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-272">Compliance administrator</span></span> | <span data-ttu-id="5c091-273">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="5c091-273">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="5c091-274">合規性資料管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-274">Compliance data administrator</span></span> | <span data-ttu-id="5c091-275">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="5c091-275">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="5c091-276">條件式存取系統管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-276">Conditional Access administrator</span></span> | <span data-ttu-id="5c091-277">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="5c091-277">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="5c091-278">客戶 LockBox 存取核准者</span><span class="sxs-lookup"><span data-stu-id="5c091-278">Customer LockBox access approver</span></span> | <span data-ttu-id="5c091-279">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="5c091-279">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="5c091-280">電腦分析系統管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-280">Desktop Analytics administrator</span></span> | <span data-ttu-id="5c091-281">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="5c091-281">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="5c091-282">目錄讀取器</span><span class="sxs-lookup"><span data-stu-id="5c091-282">Directory readers</span></span> | <span data-ttu-id="5c091-283">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="5c091-283">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="5c091-284">Dynamics 365 管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-284">Dynamics 365 administrator</span></span> | <span data-ttu-id="5c091-285">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="5c091-285">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="5c091-286">Exchange 系統管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-286">Exchange administrator</span></span> | <span data-ttu-id="5c091-287">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="5c091-287">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="5c091-288">外部 Identity 提供者管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-288">External Identity Provider administrator</span></span> | <span data-ttu-id="5c091-289">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="5c091-289">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="5c091-290">全域管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-290">Global administrator</span></span> | <span data-ttu-id="5c091-291">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="5c091-291">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="5c091-292">全域讀取者</span><span class="sxs-lookup"><span data-stu-id="5c091-292">Global reader</span></span> | <span data-ttu-id="5c091-293">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="5c091-293">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="5c091-294">群組管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-294">Groups administrator</span></span> | <span data-ttu-id="5c091-295">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="5c091-295">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="5c091-296">來賓邀請者</span><span class="sxs-lookup"><span data-stu-id="5c091-296">Guest inviter</span></span> | <span data-ttu-id="5c091-297">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="5c091-297">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="5c091-298">服務台管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-298">Helpdesk administrator</span></span> | <span data-ttu-id="5c091-299">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="5c091-299">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="5c091-300">Intune 管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-300">Intune administrator</span></span> | <span data-ttu-id="5c091-301">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="5c091-301">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="5c091-302">Kaizala 管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-302">Kaizala administrator</span></span> | <span data-ttu-id="5c091-303">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="5c091-303">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="5c091-304">授權管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-304">License administrator</span></span> | <span data-ttu-id="5c091-305">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="5c091-305">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="5c091-306">訊息中心隱私權讀取者</span><span class="sxs-lookup"><span data-stu-id="5c091-306">Message center privacy reader</span></span> | <span data-ttu-id="5c091-307">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="5c091-307">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="5c091-308">訊息中心讀取者</span><span class="sxs-lookup"><span data-stu-id="5c091-308">Message center reader</span></span> | <span data-ttu-id="5c091-309">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="5c091-309">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="5c091-310">Office 應用程式管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-310">Office apps administrator</span></span> | <span data-ttu-id="5c091-311">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="5c091-311">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="5c091-312">密碼管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-312">Password administrator</span></span> | <span data-ttu-id="5c091-313">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="5c091-313">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="5c091-314">Power BI 管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-314">Power BI administrator</span></span> | <span data-ttu-id="5c091-315">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="5c091-315">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="5c091-316">Power Platform 管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-316">Power platform administrator</span></span> | <span data-ttu-id="5c091-317">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="5c091-317">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="5c091-318">特殊權限驗證管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-318">Privileged authentication administrator</span></span> | <span data-ttu-id="5c091-319">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="5c091-319">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="5c091-320">特殊權限角色管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-320">Privileged role administrator</span></span> | <span data-ttu-id="5c091-321">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="5c091-321">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="5c091-322">報表讀者</span><span class="sxs-lookup"><span data-stu-id="5c091-322">Reports reader</span></span> | <span data-ttu-id="5c091-323">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="5c091-323">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="5c091-324">搜尋管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-324">Search administrator</span></span> | <span data-ttu-id="5c091-325">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="5c091-325">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="5c091-326">搜尋編輯者</span><span class="sxs-lookup"><span data-stu-id="5c091-326">Search editor</span></span> | <span data-ttu-id="5c091-327">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="5c091-327">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="5c091-328">安全性系統管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-328">Security administrator</span></span> | <span data-ttu-id="5c091-329">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="5c091-329">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="5c091-330">安全性操作員</span><span class="sxs-lookup"><span data-stu-id="5c091-330">Security operator</span></span> | <span data-ttu-id="5c091-331">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="5c091-331">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="5c091-332">安全性讀取者</span><span class="sxs-lookup"><span data-stu-id="5c091-332">Security reader</span></span> | <span data-ttu-id="5c091-333">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="5c091-333">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="5c091-334">服務支援管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-334">Service support administrator</span></span> | <span data-ttu-id="5c091-335">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="5c091-335">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="5c091-336">SharePoint 管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-336">SharePoint administrator</span></span> | <span data-ttu-id="5c091-337">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="5c091-337">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="5c091-338">商務用 Skype 的管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-338">Skype for Business administrator</span></span> | <span data-ttu-id="5c091-339">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="5c091-339">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="5c091-340">Microsoft Teams 通訊系統管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-340">Teams Communications Administrator</span></span> | <span data-ttu-id="5c091-341">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="5c091-341">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="5c091-342">Microsoft Teams 通訊支援工程師</span><span class="sxs-lookup"><span data-stu-id="5c091-342">Teams Communications Support Engineer</span></span> | <span data-ttu-id="5c091-343">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="5c091-343">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="5c091-344">團隊通訊專家</span><span class="sxs-lookup"><span data-stu-id="5c091-344">Teams Communications Specialist</span></span> | <span data-ttu-id="5c091-345">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="5c091-345">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="5c091-346">Microsoft Teams 服務管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-346">Teams Service Administrator</span></span> | <span data-ttu-id="5c091-347">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="5c091-347">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="5c091-348">使用者管理員</span><span class="sxs-lookup"><span data-stu-id="5c091-348">User administrator</span></span> | <span data-ttu-id="5c091-349">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="5c091-349">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5c091-350">其他資源</span><span class="sxs-lookup"><span data-stu-id="5c091-350">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
