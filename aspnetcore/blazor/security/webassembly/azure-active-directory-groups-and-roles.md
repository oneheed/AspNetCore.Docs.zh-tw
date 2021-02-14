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
ms.openlocfilehash: c180580ec56313e444f2daf2b7d08c4d909b498a
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280528"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a><span data-ttu-id="39ec8-103">Azure Active Directory (AAD) 群組、系統管理員角色和應用程式角色</span><span class="sxs-lookup"><span data-stu-id="39ec8-103">Azure Active Directory (AAD) groups, Administrator Roles, and App Roles</span></span>

<span data-ttu-id="39ec8-104">Azure Active Directory (AAD) 提供數個可結合的授權方法 ASP.NET Core Identity ：</span><span class="sxs-lookup"><span data-stu-id="39ec8-104">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="39ec8-105">群組</span><span class="sxs-lookup"><span data-stu-id="39ec8-105">Groups</span></span>
  * <span data-ttu-id="39ec8-106">安全性</span><span class="sxs-lookup"><span data-stu-id="39ec8-106">Security</span></span>
  * <span data-ttu-id="39ec8-107">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="39ec8-107">Microsoft 365</span></span>
  * <span data-ttu-id="39ec8-108">散發</span><span class="sxs-lookup"><span data-stu-id="39ec8-108">Distribution</span></span>
* <span data-ttu-id="39ec8-109">角色</span><span class="sxs-lookup"><span data-stu-id="39ec8-109">Roles</span></span>
  * <span data-ttu-id="39ec8-110">AAD 系統管理員角色</span><span class="sxs-lookup"><span data-stu-id="39ec8-110">AAD Administrator Roles</span></span>
  * <span data-ttu-id="39ec8-111">應用程式角色</span><span class="sxs-lookup"><span data-stu-id="39ec8-111">App Roles</span></span>

<span data-ttu-id="39ec8-112">本文中的指導方針適用于 Blazor WebAssembly 下列主題中所述的 AAD 部署案例：</span><span class="sxs-lookup"><span data-stu-id="39ec8-112">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="39ec8-113">獨立的 Microsoft 帳戶</span><span class="sxs-lookup"><span data-stu-id="39ec8-113">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="39ec8-114">獨立的 AAD</span><span class="sxs-lookup"><span data-stu-id="39ec8-114">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="39ec8-115">使用 AAD 託管</span><span class="sxs-lookup"><span data-stu-id="39ec8-115">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

<span data-ttu-id="39ec8-116">本文的指導方針提供用戶端和伺服器應用程式的指示：</span><span class="sxs-lookup"><span data-stu-id="39ec8-116">The article's guidance provides instructions for client and server apps:</span></span>

* <span data-ttu-id="39ec8-117">**用戶端**：獨立 Blazor WebAssembly 應用程式或 *`Client`* 託管解決方案的應用程式 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="39ec8-117">**CLIENT**: Standalone Blazor WebAssembly apps or the *`Client`* app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="39ec8-118">**伺服器**：獨立 ASP.NET CORE server api/web api 應用程式或 *`Server`* 託管解決方案的應用程式 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="39ec8-118">**SERVER**: Standalone ASP.NET Core server API/web API apps or the *`Server`* app of a hosted Blazor solution.</span></span>

## <a name="scopes"></a><span data-ttu-id="39ec8-119">範圍</span><span class="sxs-lookup"><span data-stu-id="39ec8-119">Scopes</span></span>

<span data-ttu-id="39ec8-120">若要允許使用者設定檔、角色指派和群組成員資格資料的 [MICROSOFT GRAPH API](/graph/use-the-api) 呼叫， **用戶端** 會使用 `https://graph.microsoft.com/User.Read` (中的 () [圖形 API 許可權) 範圍](/graph/permissions-reference) 進行設定。</span><span class="sxs-lookup"><span data-stu-id="39ec8-120">To permit [Microsoft Graph API](/graph/use-the-api) calls for user profile, role assignment, and group membership data, the **CLIENT** is configured with (`https://graph.microsoft.com/User.Read`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="39ec8-121">針對角色和群組成員資格資料呼叫圖形 API 的 **伺服器** 應用程式， `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [圖形 API](/graph/permissions-reference) () 範圍 Azure 入口網站範圍中提供的許可權。</span><span class="sxs-lookup"><span data-stu-id="39ec8-121">A **SERVER** app that calls Graph API for role and group membership data is provided `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="39ec8-122">除了本文第一節所列的主題所述的 AAD 部署案例中所需的範圍之外，還需要這些範圍。</span><span class="sxs-lookup"><span data-stu-id="39ec8-122">These scopes are required in addition to the scopes required in AAD deployment scenarios described by the topics listed in the first section of this article.</span></span>

> [!NOTE]
> <span data-ttu-id="39ec8-123">「許可權」和「範圍」兩字都是在 Azure 入口網站以及各種 Microsoft 和外部檔集中交替使用。</span><span class="sxs-lookup"><span data-stu-id="39ec8-123">The words "permission" and "scope" are used interchangeably in the Azure portal and in various Microsoft and external documentation sets.</span></span> <span data-ttu-id="39ec8-124">本文使用「範圍」一字，在 Azure 入口網站中指派給應用程式的許可權。</span><span class="sxs-lookup"><span data-stu-id="39ec8-124">This article uses the word "scope" throughout for the permissions assigned to an app in the Azure portal.</span></span>

## <a name="group-membership-claims-attribute"></a><span data-ttu-id="39ec8-125">群組成員資格宣告屬性</span><span class="sxs-lookup"><span data-stu-id="39ec8-125">Group Membership Claims attribute</span></span>

<span data-ttu-id="39ec8-126">在 **用戶端** 和 **伺服器** 應用程式之 Azure 入口網站的應用程式資訊清單中，將 [ `groupMembershipClaims` 屬性](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)設定為 `All` 。</span><span class="sxs-lookup"><span data-stu-id="39ec8-126">In the app's manifest in the the Azure portal for **CLIENT** and **SERVER** apps, set the [`groupMembershipClaims` attribute](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) to `All`.</span></span> <span data-ttu-id="39ec8-127">的值 `All` 會取得已登入使用者所屬的所有安全性群組、通訊群組和角色。</span><span class="sxs-lookup"><span data-stu-id="39ec8-127">A value of `All` results in obtaining all of the security groups, distribution groups, and roles that the signed-in user is a member of.</span></span>

1. <span data-ttu-id="39ec8-128">開啟應用程式的 Azure 入口網站註冊。</span><span class="sxs-lookup"><span data-stu-id="39ec8-128">Open the app's Azure portal registration.</span></span>
1. <span data-ttu-id="39ec8-129">選取提要欄位中的 [**管理**  >  **資訊清單**]。</span><span class="sxs-lookup"><span data-stu-id="39ec8-129">Select **Manage** > **Manifest** in the sidebar.</span></span>
1. <span data-ttu-id="39ec8-130">尋找 `groupMembershipClaims` 屬性。</span><span class="sxs-lookup"><span data-stu-id="39ec8-130">Find the `groupMembershipClaims` attribute.</span></span>
1. <span data-ttu-id="39ec8-131">將值設定為 `All`。</span><span class="sxs-lookup"><span data-stu-id="39ec8-131">Set the value to `All`.</span></span>
1. <span data-ttu-id="39ec8-132">選取 [儲存] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="39ec8-132">Select the **Save** button.</span></span>

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a><span data-ttu-id="39ec8-133">自訂使用者帳戶</span><span class="sxs-lookup"><span data-stu-id="39ec8-133">Custom user account</span></span>

<span data-ttu-id="39ec8-134">在 Azure 入口網站中，將使用者指派給 AAD 安全性群組和 AAD 系統管理員角色。</span><span class="sxs-lookup"><span data-stu-id="39ec8-134">Assign users to AAD security groups and AAD Administrator Roles in the Azure portal.</span></span>

<span data-ttu-id="39ec8-135">本文中的範例：</span><span class="sxs-lookup"><span data-stu-id="39ec8-135">The examples in this article:</span></span>

* <span data-ttu-id="39ec8-136">假設使用者已指派給 Azure 入口網站 AAD 租使用者中的 AAD *計費管理員* 角色，以取得存取伺服器 API 資料的授權。</span><span class="sxs-lookup"><span data-stu-id="39ec8-136">Assume that a user is assigned to the AAD *Billing Administrator* role in the Azure portal AAD tenant for authorization to access server API data.</span></span>
* <span data-ttu-id="39ec8-137">使用 [授權原則](xref:security/authorization/policies) 來控制 **用戶端** 和 **伺服器** 應用程式中的存取。</span><span class="sxs-lookup"><span data-stu-id="39ec8-137">Use [authorization policies](xref:security/authorization/policies) to control access within the **CLIENT** and **SERVER** apps.</span></span>

<span data-ttu-id="39ec8-138">在 **用戶端** 應用程式中，擴充 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 以包含下列內容的屬性：</span><span class="sxs-lookup"><span data-stu-id="39ec8-138">In the **CLIENT** app, extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include properties for:</span></span>

* <span data-ttu-id="39ec8-139">`Roles`： [應用程式角色](#app-roles) 一節中涵蓋的 AAD 應用程式角色陣列 () </span><span class="sxs-lookup"><span data-stu-id="39ec8-139">`Roles`: AAD App Roles array (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="39ec8-140">`Wids`：知名識別碼宣告中的 AAD 系統管理員角色 [ (`wids`) ](/azure/active-directory/develop/access-tokens#payload-claims)</span><span class="sxs-lookup"><span data-stu-id="39ec8-140">`Wids`: AAD Administrator Roles in [well-known IDs claims (`wids`)](/azure/active-directory/develop/access-tokens#payload-claims)</span></span>
* <span data-ttu-id="39ec8-141">`Oid`：不可變的 [物件識別碼宣告 (`oid`) ](/azure/active-directory/develop/id-tokens#payload-claims) (可唯一識別租使用者內和跨租使用者的使用者) </span><span class="sxs-lookup"><span data-stu-id="39ec8-141">`Oid`: Immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) (uniquely identifies a user within and across tenants)</span></span>

<span data-ttu-id="39ec8-142">將空的陣列指派給每一個陣列屬性，以便 `null` 在迴圈中使用這些屬性時，不需要檢查 `foreach` 。</span><span class="sxs-lookup"><span data-stu-id="39ec8-142">Assign an empty array to each array property so that checking for `null` isn't required when these properties are used in `foreach` loops.</span></span>

<span data-ttu-id="39ec8-143">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="39ec8-143">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="39ec8-144">將套件參考新增至 **用戶端** 應用程式的專案檔 [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) 。</span><span class="sxs-lookup"><span data-stu-id="39ec8-144">Add a package reference to the **CLIENT** app's project file for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="39ec8-145">在文章的 *GRAPH sdk* 區段中，新增 graph sdk 公用程式類別和設定 <xref:blazor/security/webassembly/graph-api#graph-sdk> 。</span><span class="sxs-lookup"><span data-stu-id="39ec8-145">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span> <span data-ttu-id="39ec8-146">在 `GraphClientExtensions` 類別中，于 `User.Read` 方法中指定存取權杖的範圍 `AuthenticateRequestAsync` ：</span><span class="sxs-lookup"><span data-stu-id="39ec8-146">In the `GraphClientExtensions` class, specify the `User.Read` scope for the access token in the `AuthenticateRequestAsync` method:</span></span>

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

<span data-ttu-id="39ec8-147">將下列自訂使用者帳戶 factory 新增至 **用戶端** 應用程式。</span><span class="sxs-lookup"><span data-stu-id="39ec8-147">Add the following custom user account factory to the **CLIENT** app.</span></span> <span data-ttu-id="39ec8-148">自訂使用者 factory 是用來建立：</span><span class="sxs-lookup"><span data-stu-id="39ec8-148">The custom user factory is used to establish:</span></span>

* <span data-ttu-id="39ec8-149">應用程式角色宣告 (`appRole`)  ([應用程式角色](#app-roles) 一節中所涵蓋的) </span><span class="sxs-lookup"><span data-stu-id="39ec8-149">App Role claims (`appRole`) (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="39ec8-150">AAD 系統管理員角色宣告 (`directoryRole`) </span><span class="sxs-lookup"><span data-stu-id="39ec8-150">AAD Administrator Role claims (`directoryRole`)</span></span>
* <span data-ttu-id="39ec8-151">使用者的行動電話號碼範例使用者設定檔資料宣告 (`mobilePhone`) </span><span class="sxs-lookup"><span data-stu-id="39ec8-151">An example user profile data claim for the user's mobile phone number (`mobilePhone`)</span></span>
* <span data-ttu-id="39ec8-152">AAD 群組宣告 (`directoryGroup`) </span><span class="sxs-lookup"><span data-stu-id="39ec8-152">AAD Group claims (`directoryGroup`)</span></span>

<span data-ttu-id="39ec8-153">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="39ec8-153">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="39ec8-154">上述程式碼不包含可轉移的成員資格。</span><span class="sxs-lookup"><span data-stu-id="39ec8-154">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="39ec8-155">如果應用程式需要直接和可轉移的群組成員資格宣告，請 `MemberOf` `IUserMemberOfCollectionWithReferencesRequestBuilder` 使用 () 取代) 的屬性 (`TransitiveMemberOf` `IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="39ec8-155">If the app requires direct and transitive group membership claims, replace the `MemberOf` property (`IUserMemberOfCollectionWithReferencesRequestBuilder`) with `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`).</span></span>

<span data-ttu-id="39ec8-156">上述程式碼會忽略 () 的群組成員資格宣告 `groups` ，這些是 Aad 系統管理員角色 (`#microsoft.graph.directoryRole` 類型) ，因為 Microsoft 平臺2.0 所傳回的 GUID 值 Identity 是 Aad 系統管理員角色 **實體識別碼** ，而不是 [**角色範本識別碼**](/azure/active-directory/roles/permissions-reference#role-template-ids)。</span><span class="sxs-lookup"><span data-stu-id="39ec8-156">The preceding code ignores group membership claims (`groups`) that are AAD Administrator Roles (`#microsoft.graph.directoryRole` type) because the GUID values returned by the Microsoft Identity Platform 2.0 are AAD Administrator Role **entity IDs** and not [**Role Template IDs**](/azure/active-directory/roles/permissions-reference#role-template-ids).</span></span> <span data-ttu-id="39ec8-157">實體識別碼在 Microsoft 平臺2.0 中的租使用者之間並不穩定 Identity ，而且不應該用來為應用程式中的使用者建立授權原則。</span><span class="sxs-lookup"><span data-stu-id="39ec8-157">Entity IDs aren't stable across tenants in Microsoft Identity Platform 2.0 and shouldn't be used to create authorization policies for users in apps.</span></span> <span data-ttu-id="39ec8-158">一律將 **角色範本識別碼** 用於 **`wids` 宣告所提供** 的 AAD 系統管理員角色。</span><span class="sxs-lookup"><span data-stu-id="39ec8-158">Always use **Role Template IDs** for AAD Administrator Roles **provided by `wids` claims**.</span></span>

<span data-ttu-id="39ec8-159">在 `Program.Main` **用戶端** 應用程式中，將 MSAL authentication 設定為使用自訂使用者帳戶 factory。</span><span class="sxs-lookup"><span data-stu-id="39ec8-159">In `Program.Main` of the **CLIENT** app, configure the MSAL authentication to use the custom user account factory.</span></span>

<span data-ttu-id="39ec8-160">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="39ec8-160">`Program.cs`:</span></span>

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

## <a name="authorization-configuration"></a><span data-ttu-id="39ec8-161">授權設定</span><span class="sxs-lookup"><span data-stu-id="39ec8-161">Authorization configuration</span></span>

<span data-ttu-id="39ec8-162">在 **用戶端** 應用程式中，為每個 [應用程式角色](#app-roles)、AAD 系統管理員角色或中的安全性群組建立 [原則](xref:security/authorization/policies) `Program.Main` 。</span><span class="sxs-lookup"><span data-stu-id="39ec8-162">In the **CLIENT** app, create a [policy](xref:security/authorization/policies) for each [App Role](#app-roles), AAD Administrator Role, or security group in `Program.Main`.</span></span> <span data-ttu-id="39ec8-163">下列範例會建立 AAD *計費管理員* 角色的原則：</span><span class="sxs-lookup"><span data-stu-id="39ec8-163">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="39ec8-164">如需 AAD 系統管理員角色的完整識別碼清單，請參閱 Azure 檔中的 [角色範本識別碼](/azure/active-directory/roles/permissions-reference#role-template-ids) 。</span><span class="sxs-lookup"><span data-stu-id="39ec8-164">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="39ec8-165">如需授權原則的詳細資訊，請參閱 <xref:security/authorization/policies> 。</span><span class="sxs-lookup"><span data-stu-id="39ec8-165">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="39ec8-166">在下列範例中， **用戶端** 應用程式會使用上述原則來授權使用者。</span><span class="sxs-lookup"><span data-stu-id="39ec8-166">In the following examples, the **CLIENT** app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="39ec8-167">此[ `AuthorizeView` 元件](xref:blazor/security/index#authorizeview-component)適用于原則：</span><span class="sxs-lookup"><span data-stu-id="39ec8-167">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="39ec8-168">您可以使用[ `[Authorize]` 屬性](xref:blazor/security/index#authorize-attribute)指示詞 () ，根據原則來存取整個元件 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ：</span><span class="sxs-lookup"><span data-stu-id="39ec8-168">Access to an entire component can be based on the policy using an [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="39ec8-169">如果使用者未登入，系統會將他們重新導向至 AAD 登入頁面，然後在登入之後返回該元件。</span><span class="sxs-lookup"><span data-stu-id="39ec8-169">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="39ec8-170">原則檢查也可以在程式 [代碼中使用程式邏輯來執行](xref:blazor/security/index#procedural-logic)。</span><span class="sxs-lookup"><span data-stu-id="39ec8-170">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic).</span></span>

<span data-ttu-id="39ec8-171">`Pages/CheckPolicy.razor`:</span><span class="sxs-lookup"><span data-stu-id="39ec8-171">`Pages/CheckPolicy.razor`:</span></span>

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

## <a name="authorize-server-apiweb-api-access"></a><span data-ttu-id="39ec8-172">授權伺服器 API/web API 存取</span><span class="sxs-lookup"><span data-stu-id="39ec8-172">Authorize server API/web API access</span></span>

<span data-ttu-id="39ec8-173">當存取權杖包含、和宣告時，伺服器 API 應用程式可授權使用者使用安全性群組、AAD 系統管理員角色和應用程式角色的[授權原則](xref:security/authorization/policies)來存取安全的 API 端點 `groups` `wids` `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` 。</span><span class="sxs-lookup"><span data-stu-id="39ec8-173">A **SERVER** API app can authorize users to access secure API endpoints with [authorization policies](xref:security/authorization/policies) for security groups, AAD Administrator Roles, and App Roles when an access token contains `groups`, `wids`, and `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` claims.</span></span> <span data-ttu-id="39ec8-174">下列範例會在中 `Startup.ConfigureServices` 使用 `wids` (知名的識別碼/角色範本識別碼) 宣告來建立 AAD 計費管理員角色的原則：</span><span class="sxs-lookup"><span data-stu-id="39ec8-174">The following example creates a policy for the AAD *Billing Administrator* role in `Startup.ConfigureServices` using the `wids` (well-known IDs/Role Template IDs) claims:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="39ec8-175">如需 AAD 系統管理員角色的完整識別碼清單，請參閱 Azure 檔中的 [角色範本識別碼](/azure/active-directory/roles/permissions-reference#role-template-ids) 。</span><span class="sxs-lookup"><span data-stu-id="39ec8-175">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="39ec8-176">如需授權原則的詳細資訊，請參閱 <xref:security/authorization/policies> 。</span><span class="sxs-lookup"><span data-stu-id="39ec8-176">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="39ec8-177">若要存取 **伺服器** 應用程式中的控制器，您可以根據 (API 檔：) 的原則名稱來使用 [ `[Authorize]` 屬性](xref:security/authorization/simple) <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> 。</span><span class="sxs-lookup"><span data-stu-id="39ec8-177">Access to a controller in the **SERVER** app can be based on using an [`[Authorize]` attribute](xref:security/authorization/simple) with the name of the policy (API documentation: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).</span></span>

<span data-ttu-id="39ec8-178">下列範例會限制對 Azure 計費管理員的帳單資料存取， `BillingDataController` 原則名稱為 `BillingAdministrator` ：</span><span class="sxs-lookup"><span data-stu-id="39ec8-178">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdministrator`:</span></span>

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

<span data-ttu-id="39ec8-179">如需詳細資訊，請參閱<xref:security/authorization/policies>。</span><span class="sxs-lookup"><span data-stu-id="39ec8-179">For more information, see <xref:security/authorization/policies>.</span></span>

## <a name="app-roles"></a><span data-ttu-id="39ec8-180">應用程式角色</span><span class="sxs-lookup"><span data-stu-id="39ec8-180">App Roles</span></span>

<span data-ttu-id="39ec8-181">若要在 Azure 入口網站中設定應用程式以提供應用程式角色成員資格宣告，請參閱 [如何：在應用程式中新增應用程式角色，並在 Azure 檔中的權杖中接收這些角色](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) 。</span><span class="sxs-lookup"><span data-stu-id="39ec8-181">To configure the app in the Azure portal to provide App Roles membership claims, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="39ec8-182">下列範例假設 **用戶端** 和 **伺服器** 應用程式都是以兩個角色設定，而角色則指派給測試使用者：</span><span class="sxs-lookup"><span data-stu-id="39ec8-182">The following example assumes that the **CLIENT** and **SERVER** apps are configured with two roles, and the roles are assigned to a test user:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="39ec8-183">開發託管 Blazor WebAssembly 應用程式或獨立應用程式的用戶端-伺服器組 (獨立 Blazor WebAssembly 應用程式和獨立 ASP.NET CORE server api/web api 應用程式) 時， `appRoles` 用戶端和伺服器 Azure 入口網站應用程式註冊的資訊清單屬性都必須包含相同設定的角色。</span><span class="sxs-lookup"><span data-stu-id="39ec8-183">When developing a hosted Blazor WebAssembly app or a client-server pair of standalone apps (a standalone Blazor WebAssembly app and a standalone ASP.NET Core server API/web API app), the `appRoles` manifest property of both the client and the server Azure portal app registrations must include the same configured roles.</span></span> <span data-ttu-id="39ec8-184">在用戶端應用程式的資訊清單中建立角色之後，請將它們完整複製到伺服器應用程式的資訊清單。</span><span class="sxs-lookup"><span data-stu-id="39ec8-184">After establishing the roles in the client app's manifest, copy them in their entirety to the server app's manifest.</span></span> <span data-ttu-id="39ec8-185">如果您未在 `appRoles` 用戶端與伺服器應用程式註冊之間建立資訊清單的鏡像，則不會針對伺服器 api/WEB API 的已驗證使用者建立角色宣告，即使其存取權杖具有正確的角色宣告也一樣。</span><span class="sxs-lookup"><span data-stu-id="39ec8-185">If you don't mirror the manifest `appRoles` between the client and server app registrations, role claims aren't established for authenticated users of the server API/web API, even if their access token has the correct roles claims.</span></span>

> [!NOTE]
> <span data-ttu-id="39ec8-186">雖然您無法將角色指派給沒有 Azure AD Premium 帳戶的群組，但您可以將角色指派給使用者，並為具有標準 Azure 帳戶的使用者接收角色宣告。</span><span class="sxs-lookup"><span data-stu-id="39ec8-186">Although you can't assign roles to groups without an Azure AD Premium account, you can assign roles to users and receive a roles claim for users with a standard Azure account.</span></span> <span data-ttu-id="39ec8-187">本節中的指導方針不需要 AAD Premium 帳戶。</span><span class="sxs-lookup"><span data-stu-id="39ec8-187">The guidance in this section doesn't require an AAD Premium account.</span></span>
>
> <span data-ttu-id="39ec8-188">在 Azure 入口網站中指派多個角色，方法是為每個額外的角色指派 **_重新新增使用者_** 。</span><span class="sxs-lookup"><span data-stu-id="39ec8-188">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="39ec8-189">`CustomAccountFactory`[[自訂使用者帳戶](#custom-user-account)] 區段中顯示的會設定為 `roles` 使用 JSON 陣列值的宣告。</span><span class="sxs-lookup"><span data-stu-id="39ec8-189">The `CustomAccountFactory` shown in the [Custom user account](#custom-user-account) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="39ec8-190">`CustomAccountFactory`在 **用戶端** 應用程式中新增並註冊，如 [自訂使用者帳戶](#custom-user-account)一節所示。</span><span class="sxs-lookup"><span data-stu-id="39ec8-190">Add and register the `CustomAccountFactory` in the **CLIENT** app as shown in the [Custom user account](#custom-user-account) section.</span></span> <span data-ttu-id="39ec8-191">不需要提供程式碼來移除原始宣告， `roles` 因為它會由架構自動移除。</span><span class="sxs-lookup"><span data-stu-id="39ec8-191">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="39ec8-192">在 `Program.Main` **用戶端** 應用程式的中，將名為 "" 的宣告指定為 `appRole` 檢查的角色宣告 <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> ：</span><span class="sxs-lookup"><span data-stu-id="39ec8-192">In `Program.Main` of a **CLIENT** app, specify the claim named "`appRole`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> <span data-ttu-id="39ec8-193">如果您想要使用宣告 `directoryRoles` () 新增系統管理員角色，請將 " `directoryRoles` " 指派給 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="39ec8-193">If you prefer to use the `directoryRoles` claim (ADD Administrator Roles), assign "`directoryRoles`" to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="39ec8-194">在 `Startup.ConfigureServices` **伺服器** 應用程式的中，將名為 "" 的宣告指定為 `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` 檢查的角色宣告 <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> ：</span><span class="sxs-lookup"><span data-stu-id="39ec8-194">In `Startup.ConfigureServices` of a **SERVER** app, specify the claim named "`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

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
> <span data-ttu-id="39ec8-195">如果您想要使用宣告 `wids` () 新增系統管理員角色，請將 " `wids` " 指派給 <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="39ec8-195">If you prefer to use the `wids` claim (ADD Administrator Roles), assign "`wids`" to the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="39ec8-196">元件授權方法此時可正常運作。</span><span class="sxs-lookup"><span data-stu-id="39ec8-196">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="39ec8-197">**用戶端** 應用程式元件中的任何授權機制都可以使用 `admin` 角色來授權使用者：</span><span class="sxs-lookup"><span data-stu-id="39ec8-197">Any of the authorization mechanisms in components of the **CLIENT** app can use the `admin` role to authorize the user:</span></span>

* [<span data-ttu-id="39ec8-198">`AuthorizeView` 元件</span><span class="sxs-lookup"><span data-stu-id="39ec8-198">`AuthorizeView` component</span></span>](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* <span data-ttu-id="39ec8-199">[ `[Authorize]` 屬性](xref:blazor/security/index#authorize-attribute)指示詞 (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) </span><span class="sxs-lookup"><span data-stu-id="39ec8-199">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="39ec8-200">程式邏輯</span><span class="sxs-lookup"><span data-stu-id="39ec8-200">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="39ec8-201">支援多個角色測試：</span><span class="sxs-lookup"><span data-stu-id="39ec8-201">Multiple role tests are supported:</span></span>

* <span data-ttu-id="39ec8-202">要求使用者必須在 `admin` 具有元件的 **或** `developer` 角色中 `AuthorizeView` ：</span><span class="sxs-lookup"><span data-stu-id="39ec8-202">Require that the user be in **either** the `admin` **or** `developer` role with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* <span data-ttu-id="39ec8-203">要求使用者必須 **同時** 位於 `admin` **和** `developer` 角色的 `AuthorizeView` 元件中：</span><span class="sxs-lookup"><span data-stu-id="39ec8-203">Require that the user be in **both** the `admin` **and** `developer` roles with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* <span data-ttu-id="39ec8-204">要求使用者必須在 `admin` 具有屬性的 **或** `developer` 角色中 `[Authorize]` ：</span><span class="sxs-lookup"><span data-stu-id="39ec8-204">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="39ec8-205">要求使用者必須 **同時** 位於 `admin` **和** `developer` 角色的 `[Authorize]` 屬性中：</span><span class="sxs-lookup"><span data-stu-id="39ec8-205">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="39ec8-206">需要 **使用者在** `admin` **或** `developer` 角色中具有程式碼：</span><span class="sxs-lookup"><span data-stu-id="39ec8-206">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

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

* <span data-ttu-id="39ec8-207">藉由 **將** 條件式 `admin`  `developer` [或 (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators)變更為上述範例中的 [條件式和 (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) ，要求使用者在和角色中具有程式性程式碼：</span><span class="sxs-lookup"><span data-stu-id="39ec8-207">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

<span data-ttu-id="39ec8-208">**伺服器** 應用程式控制器中的任何授權機制都可以使用該 `admin` 角色來授權使用者：</span><span class="sxs-lookup"><span data-stu-id="39ec8-208">Any of the authorization mechanisms in controllers of the **SERVER** app can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="39ec8-209">[ `[Authorize]` 屬性](xref:blazor/security/index#authorize-attribute)指示詞 (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) </span><span class="sxs-lookup"><span data-stu-id="39ec8-209">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="39ec8-210">程式邏輯</span><span class="sxs-lookup"><span data-stu-id="39ec8-210">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="39ec8-211">支援多個角色測試：</span><span class="sxs-lookup"><span data-stu-id="39ec8-211">Multiple role tests are supported:</span></span>

* <span data-ttu-id="39ec8-212">要求使用者必須在 `admin` 具有屬性的 **或** `developer` 角色中 `[Authorize]` ：</span><span class="sxs-lookup"><span data-stu-id="39ec8-212">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="39ec8-213">要求使用者必須 **同時** 位於 `admin` **和** `developer` 角色的 `[Authorize]` 屬性中：</span><span class="sxs-lookup"><span data-stu-id="39ec8-213">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="39ec8-214">需要 **使用者在** `admin` **或** `developer` 角色中具有程式碼：</span><span class="sxs-lookup"><span data-stu-id="39ec8-214">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

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

* <span data-ttu-id="39ec8-215">藉由 **將** 條件式 `admin`  `developer` [或 (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators)變更為上述範例中的 [條件式和 (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) ，要求使用者在和角色中具有程式性程式碼：</span><span class="sxs-lookup"><span data-stu-id="39ec8-215">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a><span data-ttu-id="39ec8-216">其他資源</span><span class="sxs-lookup"><span data-stu-id="39ec8-216">Additional resources</span></span>

* [<span data-ttu-id="39ec8-217">Azure 檔)  (角色範本識別碼 </span><span class="sxs-lookup"><span data-stu-id="39ec8-217">Role template IDs (Azure documentation)</span></span>](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [<span data-ttu-id="39ec8-218">`groupMembershipClaims` (Azure 檔的屬性) </span><span class="sxs-lookup"><span data-stu-id="39ec8-218">`groupMembershipClaims` attribute (Azure documentation)</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [<span data-ttu-id="39ec8-219">如何：在應用程式中新增應用程式角色，並在 Azure 檔 (的權杖中接收這些角色) </span><span class="sxs-lookup"><span data-stu-id="39ec8-219">How to: Add app roles in your application and receive them in the token (Azure documentation)</span></span>](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [<span data-ttu-id="39ec8-220"> (Azure 檔的應用程式角色) </span><span class="sxs-lookup"><span data-stu-id="39ec8-220">Application roles (Azure documentation)</span></span>](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
