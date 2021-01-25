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
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a><span data-ttu-id="f6560-103">Azure Active Directory (AAD) 群組、系統管理員角色和應用程式角色</span><span class="sxs-lookup"><span data-stu-id="f6560-103">Azure Active Directory (AAD) groups, Administrator Roles, and App Roles</span></span>

<span data-ttu-id="f6560-104">[Luke Latham](https://github.com/guardrex) And [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="f6560-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

> [!NOTE]
> <span data-ttu-id="f6560-105">本文適用于 Blazor 使用 Microsoft 1.0 的 ASP.NET Core apps 3.1 版 Identity ，並將于不久後更新為 5.0 Identity 2.0。</span><span class="sxs-lookup"><span data-stu-id="f6560-105">This article applies to Blazor ASP.NET Core apps version 3.1 with Microsoft Identity 1.0 and will be updated to 5.0 with Identity 2.0 shortly.</span></span> <span data-ttu-id="f6560-106">如需詳細資訊，請參閱下列 GitHub 問題和提取要求。</span><span class="sxs-lookup"><span data-stu-id="f6560-106">For more information, see the following GitHub issue and pull request.</span></span> <span data-ttu-id="f6560-107">提取要求的 [檔案 **變更** ] 索引標籤包含發行項更新的草稿文字和範例。</span><span class="sxs-lookup"><span data-stu-id="f6560-107">The the **Files changed** tab of the pull request contains the draft text and examples for the updates to the article.</span></span> <span data-ttu-id="f6560-108">審核和最終更新之後，提取要求將會合並到即時檔集。</span><span class="sxs-lookup"><span data-stu-id="f6560-108">After review and final updates, the pull request will be merged into the live documentation set.</span></span>
>
> * <span data-ttu-id="f6560-109">問題： [ Blazor 使用 AAD 群組和角色進行 WASM， (dotnet/AspNetCore.Docs #17683) ](https://github.com/dotnet/AspNetCore.Docs/issues/17683)</span><span class="sxs-lookup"><span data-stu-id="f6560-109">Issue: [Blazor WASM with AAD groups and roles (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)</span></span>
> * <span data-ttu-id="f6560-110">提取要求： [ Blazor AAD 群組和角色主題 5.0 (dotnet/AspNetCore.Docs #20856) ](https://github.com/dotnet/AspNetCore.Docs/pull/20856)</span><span class="sxs-lookup"><span data-stu-id="f6560-110">Pull Request: [Blazor AAD groups and roles topic 5.0 (dotnet/AspNetCore.Docs #20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)</span></span>

<span data-ttu-id="f6560-111">Azure Active Directory (AAD) 提供數個可結合的授權方法 ASP.NET Core Identity ：</span><span class="sxs-lookup"><span data-stu-id="f6560-111">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="f6560-112">群組</span><span class="sxs-lookup"><span data-stu-id="f6560-112">Groups</span></span>
  * <span data-ttu-id="f6560-113">安全性</span><span class="sxs-lookup"><span data-stu-id="f6560-113">Security</span></span>
  * <span data-ttu-id="f6560-114">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="f6560-114">Microsoft 365</span></span>
  * <span data-ttu-id="f6560-115">散發</span><span class="sxs-lookup"><span data-stu-id="f6560-115">Distribution</span></span>
* <span data-ttu-id="f6560-116">角色</span><span class="sxs-lookup"><span data-stu-id="f6560-116">Roles</span></span>
  * <span data-ttu-id="f6560-117">AAD 系統管理員角色</span><span class="sxs-lookup"><span data-stu-id="f6560-117">AAD Administrator Roles</span></span>
  * <span data-ttu-id="f6560-118">應用程式角色</span><span class="sxs-lookup"><span data-stu-id="f6560-118">App Roles</span></span>

<span data-ttu-id="f6560-119">本文中的指導方針適用于 Blazor WebAssembly 下列主題中所述的 AAD 部署案例：</span><span class="sxs-lookup"><span data-stu-id="f6560-119">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="f6560-120">獨立的 Microsoft 帳戶</span><span class="sxs-lookup"><span data-stu-id="f6560-120">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="f6560-121">獨立的 AAD</span><span class="sxs-lookup"><span data-stu-id="f6560-121">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="f6560-122">使用 AAD 託管</span><span class="sxs-lookup"><span data-stu-id="f6560-122">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

<span data-ttu-id="f6560-123">本文的指導方針提供用戶端和伺服器應用程式的指示：</span><span class="sxs-lookup"><span data-stu-id="f6560-123">The article's guidance provides instructions for client and server apps:</span></span>

* <span data-ttu-id="f6560-124">**用戶端**：獨立 Blazor WebAssembly 應用程式或 *`Client`* 託管解決方案的應用程式 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="f6560-124">**CLIENT**: Standalone Blazor WebAssembly apps or the *`Client`* app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="f6560-125">**伺服器**：獨立 ASP.NET CORE server api/web api 應用程式或 *`Server`* 託管解決方案的應用程式 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="f6560-125">**SERVER**: Standalone ASP.NET Core server API/web API apps or the *`Server`* app of a hosted Blazor solution.</span></span>

## <a name="scopes"></a><span data-ttu-id="f6560-126">範圍</span><span class="sxs-lookup"><span data-stu-id="f6560-126">Scopes</span></span>

<span data-ttu-id="f6560-127">若要允許使用者設定檔、角色指派和群組成員資格資料的 [MICROSOFT GRAPH API](/graph/use-the-api) 呼叫， **用戶端** 會使用 `https://graph.microsoft.com/User.Read` (中的 () [圖形 API 許可權) 範圍](/graph/permissions-reference) 進行設定。</span><span class="sxs-lookup"><span data-stu-id="f6560-127">To permit [Microsoft Graph API](/graph/use-the-api) calls for user profile, role assignment, and group membership data, the **CLIENT** is configured with (`https://graph.microsoft.com/User.Read`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="f6560-128">針對角色和群組成員資格資料呼叫圖形 API 的 **伺服器** 應用程式， `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [圖形 API](/graph/permissions-reference) () 範圍 Azure 入口網站範圍中提供的許可權。</span><span class="sxs-lookup"><span data-stu-id="f6560-128">A **SERVER** app that calls Graph API for role and group membership data is provided `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="f6560-129">除了本文第一節所列的主題所述的 AAD 部署案例中所需的範圍之外，還需要這些範圍。</span><span class="sxs-lookup"><span data-stu-id="f6560-129">These scopes are required in addition to the scopes required in AAD deployment scenarios described by the topics listed in the first section of this article.</span></span>

> [!NOTE]
> <span data-ttu-id="f6560-130">「許可權」和「範圍」兩字都是在 Azure 入口網站以及各種 Microsoft 和外部檔集中交替使用。</span><span class="sxs-lookup"><span data-stu-id="f6560-130">The words "permission" and "scope" are used interchangeably in the Azure portal and in various Microsoft and external documentation sets.</span></span> <span data-ttu-id="f6560-131">本文使用「範圍」一字，在 Azure 入口網站中指派給應用程式的許可權。</span><span class="sxs-lookup"><span data-stu-id="f6560-131">This article uses the word "scope" throughout for the permissions assigned to an app in the Azure portal.</span></span>

## <a name="group-membership-claims-attribute"></a><span data-ttu-id="f6560-132">群組成員資格宣告屬性</span><span class="sxs-lookup"><span data-stu-id="f6560-132">Group Membership Claims attribute</span></span>

<span data-ttu-id="f6560-133">在 **用戶端** 和 **伺服器** 應用程式之 Azure 入口網站的應用程式資訊清單中，將 [ `groupMembershipClaims` 屬性](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)設定為 `All` 。</span><span class="sxs-lookup"><span data-stu-id="f6560-133">In the app's manifest in the the Azure portal for **CLIENT** and **SERVER** apps, set the [`groupMembershipClaims` attribute](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) to `All`.</span></span> <span data-ttu-id="f6560-134">的值 `All` 會取得已登入使用者所屬的所有安全性群組、通訊群組和角色。</span><span class="sxs-lookup"><span data-stu-id="f6560-134">A value of `All` results in obtaining all of the security groups, distribution groups, and roles that the signed-in user is a member of.</span></span>

1. <span data-ttu-id="f6560-135">開啟應用程式的 Azure 入口網站註冊。</span><span class="sxs-lookup"><span data-stu-id="f6560-135">Open the app's Azure portal registration.</span></span>
1. <span data-ttu-id="f6560-136">選取提要欄位中的 [**管理**  >  **資訊清單**]。</span><span class="sxs-lookup"><span data-stu-id="f6560-136">Select **Manage** > **Manifest** in the sidebar.</span></span>
1. <span data-ttu-id="f6560-137">尋找 `groupMembershipClaims` 屬性。</span><span class="sxs-lookup"><span data-stu-id="f6560-137">Find the `groupMembershipClaims` attribute.</span></span>
1. <span data-ttu-id="f6560-138">將值設定為 `All`。</span><span class="sxs-lookup"><span data-stu-id="f6560-138">Set the value to `All`.</span></span>
1. <span data-ttu-id="f6560-139">選取 [儲存] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="f6560-139">Select the **Save** button.</span></span>

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a><span data-ttu-id="f6560-140">自訂使用者帳戶</span><span class="sxs-lookup"><span data-stu-id="f6560-140">Custom user account</span></span>

<span data-ttu-id="f6560-141">在 Azure 入口網站中，將使用者指派給 AAD 安全性群組和 AAD 系統管理員角色。</span><span class="sxs-lookup"><span data-stu-id="f6560-141">Assign users to AAD security groups and AAD Administrator Roles in the Azure portal.</span></span>

<span data-ttu-id="f6560-142">本文中的範例：</span><span class="sxs-lookup"><span data-stu-id="f6560-142">The examples in this article:</span></span>

* <span data-ttu-id="f6560-143">假設使用者已指派給 Azure 入口網站 AAD 租使用者中的 AAD *計費管理員* 角色，以取得存取伺服器 API 資料的授權。</span><span class="sxs-lookup"><span data-stu-id="f6560-143">Assume that a user is assigned to the AAD *Billing Administrator* role in the Azure portal AAD tenant for authorization to access server API data.</span></span>
* <span data-ttu-id="f6560-144">使用 [授權原則](xref:security/authorization/policies) 來控制 **用戶端** 和 **伺服器** 應用程式中的存取。</span><span class="sxs-lookup"><span data-stu-id="f6560-144">Use [authorization policies](xref:security/authorization/policies) to control access within the **CLIENT** and **SERVER** apps.</span></span>

<span data-ttu-id="f6560-145">在 **用戶端** 應用程式中，擴充 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 以包含下列內容的屬性：</span><span class="sxs-lookup"><span data-stu-id="f6560-145">In the **CLIENT** app, extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include properties for:</span></span>

* <span data-ttu-id="f6560-146">`Roles`： [應用程式角色](#app-roles) 一節中涵蓋的 AAD 應用程式角色陣列 () </span><span class="sxs-lookup"><span data-stu-id="f6560-146">`Roles`: AAD App Roles array (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="f6560-147">`Wids`：知名識別碼宣告中的 AAD 系統管理員角色 [ (`wids`) ](/azure/active-directory/develop/access-tokens#payload-claims)</span><span class="sxs-lookup"><span data-stu-id="f6560-147">`Wids`: AAD Administrator Roles in [well-known IDs claims (`wids`)](/azure/active-directory/develop/access-tokens#payload-claims)</span></span>
* <span data-ttu-id="f6560-148">`Oid`：不可變的 [物件識別碼宣告 (`oid`) ](/azure/active-directory/develop/id-tokens#payload-claims) (可唯一識別租使用者內和跨租使用者的使用者) </span><span class="sxs-lookup"><span data-stu-id="f6560-148">`Oid`: Immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) (uniquely identifies a user within and across tenants)</span></span>

<span data-ttu-id="f6560-149">將空的陣列指派給每一個陣列屬性，以便 `null` 在迴圈中使用這些屬性時，不需要檢查 `foreach` 。</span><span class="sxs-lookup"><span data-stu-id="f6560-149">Assign an empty array to each array property so that checking for `null` isn't required when these properties are used in `foreach` loops.</span></span>

<span data-ttu-id="f6560-150">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="f6560-150">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="f6560-151">將套件參考新增至 **用戶端** 應用程式的專案檔 [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) 。</span><span class="sxs-lookup"><span data-stu-id="f6560-151">Add a package reference to the **CLIENT** app's project file for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="f6560-152">在文章的 *GRAPH sdk* 區段中，新增 graph sdk 公用程式類別和設定 <xref:blazor/security/webassembly/graph-api#graph-sdk> 。</span><span class="sxs-lookup"><span data-stu-id="f6560-152">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span> <span data-ttu-id="f6560-153">在 `GraphClientExtensions` 類別中，于 `User.Read` 方法中指定存取權杖的範圍 `AuthenticateRequestAsync` ：</span><span class="sxs-lookup"><span data-stu-id="f6560-153">In the `GraphClientExtensions` class, specify the `User.Read` scope for the access token in the `AuthenticateRequestAsync` method:</span></span>

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

<span data-ttu-id="f6560-154">將下列自訂使用者帳戶 factory 新增至 **用戶端** 應用程式。</span><span class="sxs-lookup"><span data-stu-id="f6560-154">Add the following custom user account factory to the **CLIENT** app.</span></span> <span data-ttu-id="f6560-155">自訂使用者 factory 是用來建立：</span><span class="sxs-lookup"><span data-stu-id="f6560-155">The custom user factory is used to establish:</span></span>

* <span data-ttu-id="f6560-156">應用程式角色宣告 (`appRole`)  ([應用程式角色](#app-roles) 一節中所涵蓋的) </span><span class="sxs-lookup"><span data-stu-id="f6560-156">App Role claims (`appRole`) (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="f6560-157">AAD 系統管理員角色宣告 (`directoryRole`) </span><span class="sxs-lookup"><span data-stu-id="f6560-157">AAD Administrator Role claims (`directoryRole`)</span></span>
* <span data-ttu-id="f6560-158">使用者的行動電話號碼範例使用者設定檔資料宣告 (`mobilePhone`) </span><span class="sxs-lookup"><span data-stu-id="f6560-158">An example user profile data claim for the user's mobile phone number (`mobilePhone`)</span></span>
* <span data-ttu-id="f6560-159">AAD 群組宣告 (`directoryGroup`) </span><span class="sxs-lookup"><span data-stu-id="f6560-159">AAD Group claims (`directoryGroup`)</span></span>

<span data-ttu-id="f6560-160">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="f6560-160">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="f6560-161">上述程式碼不包含可轉移的成員資格。</span><span class="sxs-lookup"><span data-stu-id="f6560-161">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="f6560-162">如果應用程式需要直接和可轉移的群組成員資格宣告，請 `MemberOf` `IUserMemberOfCollectionWithReferencesRequestBuilder` 使用 () 取代) 的屬性 (`TransitiveMemberOf` `IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="f6560-162">If the app requires direct and transitive group membership claims, replace the `MemberOf` property (`IUserMemberOfCollectionWithReferencesRequestBuilder`) with `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`).</span></span>

<span data-ttu-id="f6560-163">上述程式碼會忽略 () 的群組成員資格宣告 `groups` ，這些是 Aad 系統管理員角色 (`#microsoft.graph.directoryRole` 類型) ，因為 Microsoft 平臺2.0 所傳回的 GUID 值 Identity 是 Aad 系統管理員角色 **實體識別碼** ，而不是 [**角色範本識別碼**](/azure/active-directory/roles/permissions-reference#role-template-ids)。</span><span class="sxs-lookup"><span data-stu-id="f6560-163">The preceding code ignores group membership claims (`groups`) that are AAD Administrator Roles (`#microsoft.graph.directoryRole` type) because the GUID values returned by the Microsoft Identity Platform 2.0 are AAD Administrator Role **entity IDs** and not [**Role Template IDs**](/azure/active-directory/roles/permissions-reference#role-template-ids).</span></span> <span data-ttu-id="f6560-164">實體識別碼在 Microsoft 平臺2.0 中的租使用者之間並不穩定 Identity ，而且不應該用來為應用程式中的使用者建立授權原則。</span><span class="sxs-lookup"><span data-stu-id="f6560-164">Entity IDs aren't stable across tenants in Microsoft Identity Platform 2.0 and shouldn't be used to create authorization policies for users in apps.</span></span> <span data-ttu-id="f6560-165">一律將 **角色範本識別碼** 用於 **`wids` 宣告所提供** 的 AAD 系統管理員角色。</span><span class="sxs-lookup"><span data-stu-id="f6560-165">Always use **Role Template IDs** for AAD Administrator Roles **provided by `wids` claims**.</span></span>

<span data-ttu-id="f6560-166">在 `Program.Main` **用戶端** 應用程式中，將 MSAL authentication 設定為使用自訂使用者帳戶 factory。</span><span class="sxs-lookup"><span data-stu-id="f6560-166">In `Program.Main` of the **CLIENT** app, configure the MSAL authentication to use the custom user account factory.</span></span>

<span data-ttu-id="f6560-167">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="f6560-167">`Program.cs`:</span></span>

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

## <a name="authorization-configuration"></a><span data-ttu-id="f6560-168">授權設定</span><span class="sxs-lookup"><span data-stu-id="f6560-168">Authorization configuration</span></span>

<span data-ttu-id="f6560-169">在 **用戶端** 應用程式中，為每個 [應用程式角色](#app-roles)、AAD 系統管理員角色或中的安全性群組建立 [原則](xref:security/authorization/policies) `Program.Main` 。</span><span class="sxs-lookup"><span data-stu-id="f6560-169">In the **CLIENT** app, create a [policy](xref:security/authorization/policies) for each [App Role](#app-roles), AAD Administrator Role, or security group in `Program.Main`.</span></span> <span data-ttu-id="f6560-170">下列範例會建立 AAD *計費管理員* 角色的原則：</span><span class="sxs-lookup"><span data-stu-id="f6560-170">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="f6560-171">如需 AAD 系統管理員角色的完整識別碼清單，請參閱 Azure 檔中的 [角色範本識別碼](/azure/active-directory/roles/permissions-reference#role-template-ids) 。</span><span class="sxs-lookup"><span data-stu-id="f6560-171">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="f6560-172">如需授權原則的詳細資訊，請參閱 <xref:security/authorization/policies> 。</span><span class="sxs-lookup"><span data-stu-id="f6560-172">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="f6560-173">在下列範例中， **用戶端** 應用程式會使用上述原則來授權使用者。</span><span class="sxs-lookup"><span data-stu-id="f6560-173">In the following examples, the **CLIENT** app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="f6560-174">此[ `AuthorizeView` 元件](xref:blazor/security/index#authorizeview-component)適用于原則：</span><span class="sxs-lookup"><span data-stu-id="f6560-174">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="f6560-175">您可以使用[ `[Authorize]` 屬性](xref:blazor/security/index#authorize-attribute)指示詞 () ，根據原則來存取整個元件 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ：</span><span class="sxs-lookup"><span data-stu-id="f6560-175">Access to an entire component can be based on the policy using an [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="f6560-176">如果使用者未登入，系統會將他們重新導向至 AAD 登入頁面，然後在登入之後返回該元件。</span><span class="sxs-lookup"><span data-stu-id="f6560-176">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="f6560-177">原則檢查也可以在程式 [代碼中使用程式邏輯來執行](xref:blazor/security/index#procedural-logic)。</span><span class="sxs-lookup"><span data-stu-id="f6560-177">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic).</span></span>

<span data-ttu-id="f6560-178">`Pages/CheckPolicy.razor`:</span><span class="sxs-lookup"><span data-stu-id="f6560-178">`Pages/CheckPolicy.razor`:</span></span>

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

## <a name="authorize-server-apiweb-api-access"></a><span data-ttu-id="f6560-179">授權伺服器 API/web API 存取</span><span class="sxs-lookup"><span data-stu-id="f6560-179">Authorize server API/web API access</span></span>

<span data-ttu-id="f6560-180">當存取權杖包含、和宣告時，伺服器 API 應用程式可授權使用者使用安全性群組、AAD 系統管理員角色和應用程式角色的[授權原則](xref:security/authorization/policies)來存取安全的 API 端點 `groups` `wids` `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` 。</span><span class="sxs-lookup"><span data-stu-id="f6560-180">A **SERVER** API app can authorize users to access secure API endpoints with [authorization policies](xref:security/authorization/policies) for security groups, AAD Administrator Roles, and App Roles when an access token contains `groups`, `wids`, and `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` claims.</span></span> <span data-ttu-id="f6560-181">下列範例會在中 `Startup.ConfigureServices` 使用 `wids` (知名的識別碼/角色範本識別碼) 宣告來建立 AAD 計費管理員角色的原則：</span><span class="sxs-lookup"><span data-stu-id="f6560-181">The following example creates a policy for the AAD *Billing Administrator* role in `Startup.ConfigureServices` using the `wids` (well-known IDs/Role Template IDs) claims:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="f6560-182">如需 AAD 系統管理員角色的完整識別碼清單，請參閱 Azure 檔中的 [角色範本識別碼](/azure/active-directory/roles/permissions-reference#role-template-ids) 。</span><span class="sxs-lookup"><span data-stu-id="f6560-182">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="f6560-183">如需授權原則的詳細資訊，請參閱 <xref:security/authorization/policies> 。</span><span class="sxs-lookup"><span data-stu-id="f6560-183">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="f6560-184">若要存取 **伺服器** 應用程式中的控制器，您可以根據 (API 檔：) 的原則名稱來使用 [ `[Authorize]` 屬性](xref:security/authorization/simple) <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> 。</span><span class="sxs-lookup"><span data-stu-id="f6560-184">Access to a controller in the **SERVER** app can be based on using an [`[Authorize]` attribute](xref:security/authorization/simple) with the name of the policy (API documentation: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).</span></span>

<span data-ttu-id="f6560-185">下列範例會限制對 Azure 計費管理員的帳單資料存取， `BillingDataController` 原則名稱為 `BillingAdministrator` ：</span><span class="sxs-lookup"><span data-stu-id="f6560-185">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdministrator`:</span></span>

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

<span data-ttu-id="f6560-186">如需詳細資訊，請參閱<xref:security/authorization/policies>。</span><span class="sxs-lookup"><span data-stu-id="f6560-186">For more information, see <xref:security/authorization/policies>.</span></span>

## <a name="app-roles"></a><span data-ttu-id="f6560-187">應用程式角色</span><span class="sxs-lookup"><span data-stu-id="f6560-187">App Roles</span></span>

<span data-ttu-id="f6560-188">若要在 Azure 入口網站中設定應用程式以提供應用程式角色成員資格宣告，請參閱 [如何：在應用程式中新增應用程式角色，並在 Azure 檔中的權杖中接收這些角色](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) 。</span><span class="sxs-lookup"><span data-stu-id="f6560-188">To configure the app in the Azure portal to provide App Roles membership claims, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="f6560-189">下列範例假設 **用戶端** 和 **伺服器** 應用程式都是以兩個角色設定，而角色則指派給測試使用者：</span><span class="sxs-lookup"><span data-stu-id="f6560-189">The following example assumes that the **CLIENT** and **SERVER** apps are configured with two roles, and the roles are assigned to a test user:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="f6560-190">開發託管 Blazor WebAssembly 應用程式或獨立應用程式的用戶端-伺服器組 (獨立 Blazor WebAssembly 應用程式和獨立 ASP.NET CORE server api/web api 應用程式) 時， `appRoles` 用戶端和伺服器 Azure 入口網站應用程式註冊的資訊清單屬性都必須包含相同設定的角色。</span><span class="sxs-lookup"><span data-stu-id="f6560-190">When developing a hosted Blazor WebAssembly app or a client-server pair of standalone apps (a standalone Blazor WebAssembly app and a standalone ASP.NET Core server API/web API app), the `appRoles` manifest property of both the client and the server Azure portal app registrations must include the same configured roles.</span></span> <span data-ttu-id="f6560-191">在用戶端應用程式的資訊清單中建立角色之後，請將它們完整複製到伺服器應用程式的資訊清單。</span><span class="sxs-lookup"><span data-stu-id="f6560-191">After establishing the roles in the client app's manifest, copy them in their entirety to the server app's manifest.</span></span> <span data-ttu-id="f6560-192">如果您未在 `appRoles` 用戶端與伺服器應用程式註冊之間建立資訊清單的鏡像，則不會針對伺服器 api/WEB API 的已驗證使用者建立角色宣告，即使其存取權杖具有正確的角色宣告也一樣。</span><span class="sxs-lookup"><span data-stu-id="f6560-192">If you don't mirror the manifest `appRoles` between the client and server app registrations, role claims aren't established for authenticated users of the server API/web API, even if their access token has the correct roles claims.</span></span>

> [!NOTE]
> <span data-ttu-id="f6560-193">雖然您無法將角色指派給沒有 Azure AD Premium 帳戶的群組，但您可以將角色指派給使用者，並為具有標準 Azure 帳戶的使用者接收角色宣告。</span><span class="sxs-lookup"><span data-stu-id="f6560-193">Although you can't assign roles to groups without an Azure AD Premium account, you can assign roles to users and receive a roles claim for users with a standard Azure account.</span></span> <span data-ttu-id="f6560-194">本節中的指導方針不需要 AAD Premium 帳戶。</span><span class="sxs-lookup"><span data-stu-id="f6560-194">The guidance in this section doesn't require an AAD Premium account.</span></span>
>
> <span data-ttu-id="f6560-195">在 Azure 入口網站中指派多個角色，方法是為每個額外的角色指派 **_重新新增使用者_** 。</span><span class="sxs-lookup"><span data-stu-id="f6560-195">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="f6560-196">`CustomAccountFactory`[[自訂使用者帳戶](#custom-user-account)] 區段中顯示的會設定為 `roles` 使用 JSON 陣列值的宣告。</span><span class="sxs-lookup"><span data-stu-id="f6560-196">The `CustomAccountFactory` shown in the [Custom user account](#custom-user-account) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="f6560-197">`CustomAccountFactory`在 **用戶端** 應用程式中新增並註冊，如 [自訂使用者帳戶](#custom-user-account)一節所示。</span><span class="sxs-lookup"><span data-stu-id="f6560-197">Add and register the `CustomAccountFactory` in the **CLIENT** app as shown in the [Custom user account](#custom-user-account) section.</span></span> <span data-ttu-id="f6560-198">不需要提供程式碼來移除原始宣告， `roles` 因為它會由架構自動移除。</span><span class="sxs-lookup"><span data-stu-id="f6560-198">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="f6560-199">在 `Program.Main` **用戶端** 應用程式的中，將名為 "" 的宣告指定為 `appRole` 檢查的角色宣告 <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> ：</span><span class="sxs-lookup"><span data-stu-id="f6560-199">In `Program.Main` of a **CLIENT** app, specify the claim named "`appRole`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> <span data-ttu-id="f6560-200">如果您想要使用宣告 `directoryRoles` () 新增系統管理員角色，請將 " `directoryRoles` " 指派給 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="f6560-200">If you prefer to use the `directoryRoles` claim (ADD Administrator Roles), assign "`directoryRoles`" to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="f6560-201">在 `Startup.ConfigureServices` **伺服器** 應用程式的中，將名為 "" 的宣告指定為 `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` 檢查的角色宣告 <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> ：</span><span class="sxs-lookup"><span data-stu-id="f6560-201">In `Startup.ConfigureServices` of a **SERVER** app, specify the claim named "`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

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
> <span data-ttu-id="f6560-202">如果您想要使用宣告 `wids` () 新增系統管理員角色，請將 " `wids` " 指派給 <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="f6560-202">If you prefer to use the `wids` claim (ADD Administrator Roles), assign "`wids`" to the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="f6560-203">元件授權方法此時可正常運作。</span><span class="sxs-lookup"><span data-stu-id="f6560-203">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="f6560-204">**用戶端** 應用程式元件中的任何授權機制都可以使用 `admin` 角色來授權使用者：</span><span class="sxs-lookup"><span data-stu-id="f6560-204">Any of the authorization mechanisms in components of the **CLIENT** app can use the `admin` role to authorize the user:</span></span>

* [<span data-ttu-id="f6560-205">`AuthorizeView` 元件</span><span class="sxs-lookup"><span data-stu-id="f6560-205">`AuthorizeView` component</span></span>](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* <span data-ttu-id="f6560-206">[ `[Authorize]` 屬性](xref:blazor/security/index#authorize-attribute)指示詞 (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) </span><span class="sxs-lookup"><span data-stu-id="f6560-206">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="f6560-207">程式邏輯</span><span class="sxs-lookup"><span data-stu-id="f6560-207">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="f6560-208">支援多個角色測試：</span><span class="sxs-lookup"><span data-stu-id="f6560-208">Multiple role tests are supported:</span></span>

* <span data-ttu-id="f6560-209">要求使用者必須在 `admin` 具有元件的 **或** `developer` 角色中 `AuthorizeView` ：</span><span class="sxs-lookup"><span data-stu-id="f6560-209">Require that the user be in **either** the `admin` **or** `developer` role with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* <span data-ttu-id="f6560-210">要求使用者必須 **同時** 位於 `admin` **和** `developer` 角色的 `AuthorizeView` 元件中：</span><span class="sxs-lookup"><span data-stu-id="f6560-210">Require that the user be in **both** the `admin` **and** `developer` roles with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* <span data-ttu-id="f6560-211">要求使用者必須在 `admin` 具有屬性的 **或** `developer` 角色中 `[Authorize]` ：</span><span class="sxs-lookup"><span data-stu-id="f6560-211">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="f6560-212">要求使用者必須 **同時** 位於 `admin` **和** `developer` 角色的 `[Authorize]` 屬性中：</span><span class="sxs-lookup"><span data-stu-id="f6560-212">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="f6560-213">需要 **使用者在** `admin` **或** `developer` 角色中具有程式碼：</span><span class="sxs-lookup"><span data-stu-id="f6560-213">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

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

* <span data-ttu-id="f6560-214">藉由 **將** 條件式 `admin`  `developer` [或 (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators)變更為上述範例中的 [條件式和 (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) ，要求使用者在和角色中具有程式性程式碼：</span><span class="sxs-lookup"><span data-stu-id="f6560-214">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

<span data-ttu-id="f6560-215">**伺服器** 應用程式控制器中的任何授權機制都可以使用該 `admin` 角色來授權使用者：</span><span class="sxs-lookup"><span data-stu-id="f6560-215">Any of the authorization mechanisms in controllers of the **SERVER** app can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="f6560-216">[ `[Authorize]` 屬性](xref:blazor/security/index#authorize-attribute)指示詞 (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) </span><span class="sxs-lookup"><span data-stu-id="f6560-216">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="f6560-217">程式邏輯</span><span class="sxs-lookup"><span data-stu-id="f6560-217">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="f6560-218">支援多個角色測試：</span><span class="sxs-lookup"><span data-stu-id="f6560-218">Multiple role tests are supported:</span></span>

* <span data-ttu-id="f6560-219">要求使用者必須在 `admin` 具有屬性的 **或** `developer` 角色中 `[Authorize]` ：</span><span class="sxs-lookup"><span data-stu-id="f6560-219">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="f6560-220">要求使用者必須 **同時** 位於 `admin` **和** `developer` 角色的 `[Authorize]` 屬性中：</span><span class="sxs-lookup"><span data-stu-id="f6560-220">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="f6560-221">需要 **使用者在** `admin` **或** `developer` 角色中具有程式碼：</span><span class="sxs-lookup"><span data-stu-id="f6560-221">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

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

* <span data-ttu-id="f6560-222">藉由 **將** 條件式 `admin`  `developer` [或 (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators)變更為上述範例中的 [條件式和 (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) ，要求使用者在和角色中具有程式性程式碼：</span><span class="sxs-lookup"><span data-stu-id="f6560-222">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a><span data-ttu-id="f6560-223">其他資源</span><span class="sxs-lookup"><span data-stu-id="f6560-223">Additional resources</span></span>

* [<span data-ttu-id="f6560-224">Azure 檔)  (角色範本識別碼 </span><span class="sxs-lookup"><span data-stu-id="f6560-224">Role template IDs (Azure documentation)</span></span>](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [<span data-ttu-id="f6560-225">`groupMembershipClaims` (Azure 檔的屬性) </span><span class="sxs-lookup"><span data-stu-id="f6560-225">`groupMembershipClaims` attribute (Azure documentation)</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [<span data-ttu-id="f6560-226">如何：在應用程式中新增應用程式角色，並在 Azure 檔 (的權杖中接收這些角色) </span><span class="sxs-lookup"><span data-stu-id="f6560-226">How to: Add app roles in your application and receive them in the token (Azure documentation)</span></span>](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [<span data-ttu-id="f6560-227"> (Azure 檔的應用程式角色) </span><span class="sxs-lookup"><span data-stu-id="f6560-227">Application roles (Azure documentation)</span></span>](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
