---
title: 使用受授權保護的使用者資料建立 ASP.NET Core 應用程式
author: rick-anderson
description: 瞭解如何建立 ASP.NET Core web 應用程式，並以授權保護使用者資料。 包含 HTTPS、驗證、安全性 ASP.NET Core Identity 。
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
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
uid: security/authorization/secure-data
ms.openlocfilehash: 662456af59c453df66ca48139a6de40d0e2cbf0d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589188"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="526aa-104">建立 ASP.NET Core web 應用程式，並以授權保護使用者資料</span><span class="sxs-lookup"><span data-stu-id="526aa-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="526aa-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 與 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="526aa-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="526aa-106">請參閱 [此 pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="526aa-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="526aa-107">本教學課程說明如何建立 ASP.NET Core web 應用程式，並以授權保護使用者資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="526aa-108">它會顯示已驗證 (註冊) 使用者建立的連絡人清單。</span><span class="sxs-lookup"><span data-stu-id="526aa-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="526aa-109">有三個安全性群組：</span><span class="sxs-lookup"><span data-stu-id="526aa-109">There are three security groups:</span></span>

* <span data-ttu-id="526aa-110">**註冊的使用者** 可以查看所有核准的資料，並可編輯/刪除他們自己的資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="526aa-111">**管理員** 可以核准或拒絕連絡人資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="526aa-112">只有核准的連絡人可以看到使用者。</span><span class="sxs-lookup"><span data-stu-id="526aa-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="526aa-113">系統 **管理員** 可以核准/拒絕和編輯/刪除任何資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="526aa-114">本檔中的影像與最新的範本不完全相符。</span><span class="sxs-lookup"><span data-stu-id="526aa-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="526aa-115">在下圖中，使用者 Rick (`rick@example.com`) 已登入。</span><span class="sxs-lookup"><span data-stu-id="526aa-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="526aa-116">Rick 只能查看核准的連絡人，並 **編輯** / **刪除** / 為連絡人 **建立新** 的連結。</span><span class="sxs-lookup"><span data-stu-id="526aa-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="526aa-117">只有 Rick 所建立的最後一筆記錄會顯示 [ **編輯** ] 和 [ **刪除** ] 連結。</span><span class="sxs-lookup"><span data-stu-id="526aa-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="526aa-118">在管理員或系統管理員將狀態變更為 [已核准] 之前，其他使用者將不會看到最後一筆記錄。</span><span class="sxs-lookup"><span data-stu-id="526aa-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![顯示 Rick 已登入的螢幕擷取畫面](secure-data/_static/rick.png)

<span data-ttu-id="526aa-120">在下圖中， `manager@contoso.com` 已登入並在管理員的角色中：</span><span class="sxs-lookup"><span data-stu-id="526aa-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![顯示 manager@contoso.com 已登入的螢幕擷取畫面](secure-data/_static/manager1.png)

<span data-ttu-id="526aa-122">下圖顯示連絡人的經理詳細資料檢視：</span><span class="sxs-lookup"><span data-stu-id="526aa-122">The following image shows the managers details view of a contact:</span></span>

![連絡人的經理觀點](secure-data/_static/manager.png)

<span data-ttu-id="526aa-124">[ **核准** ] 和 [ **拒絕** ] 按鈕只會顯示給管理員和系統管理員。</span><span class="sxs-lookup"><span data-stu-id="526aa-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="526aa-125">在下圖中， `admin@contoso.com` 已登入並以系統管理員的角色：</span><span class="sxs-lookup"><span data-stu-id="526aa-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![顯示 admin@contoso.com 已登入的螢幕擷取畫面](secure-data/_static/admin.png)

<span data-ttu-id="526aa-127">系統管理員具有擁有權限。</span><span class="sxs-lookup"><span data-stu-id="526aa-127">The administrator has all privileges.</span></span> <span data-ttu-id="526aa-128">她可以讀取/編輯/刪除任何連絡人，以及變更連絡人的狀態。</span><span class="sxs-lookup"><span data-stu-id="526aa-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="526aa-129">應用程式 [是由下列](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) `Contact` 模型建立：</span><span class="sxs-lookup"><span data-stu-id="526aa-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="526aa-130">此範例包含下列授權處理常式：</span><span class="sxs-lookup"><span data-stu-id="526aa-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="526aa-131">`ContactIsOwnerAuthorizationHandler`：確保使用者只能編輯其資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="526aa-132">`ContactManagerAuthorizationHandler`：可讓管理員核准或拒絕連絡人。</span><span class="sxs-lookup"><span data-stu-id="526aa-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="526aa-133">`ContactAdministratorsAuthorizationHandler`：可讓系統管理員核准或拒絕連絡人，以及編輯/刪除連絡人。</span><span class="sxs-lookup"><span data-stu-id="526aa-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="526aa-134">必要條件</span><span class="sxs-lookup"><span data-stu-id="526aa-134">Prerequisites</span></span>

<span data-ttu-id="526aa-135">本教學課程是 advanced。</span><span class="sxs-lookup"><span data-stu-id="526aa-135">This tutorial is advanced.</span></span> <span data-ttu-id="526aa-136">您應該熟悉：</span><span class="sxs-lookup"><span data-stu-id="526aa-136">You should be familiar with:</span></span>

* [<span data-ttu-id="526aa-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="526aa-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="526aa-138">驗證</span><span class="sxs-lookup"><span data-stu-id="526aa-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="526aa-139">帳戶確認和密碼復原</span><span class="sxs-lookup"><span data-stu-id="526aa-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="526aa-140">授權</span><span class="sxs-lookup"><span data-stu-id="526aa-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="526aa-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="526aa-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="526aa-142">入門和完成的應用程式</span><span class="sxs-lookup"><span data-stu-id="526aa-142">The starter and completed app</span></span>

<span data-ttu-id="526aa-143">[下載](xref:index#how-to-download-a-sample)[已完成](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples)的應用程式。</span><span class="sxs-lookup"><span data-stu-id="526aa-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="526aa-144">[測試](#test-the-completed-app) 完成的應用程式，讓您熟悉它的安全性功能。</span><span class="sxs-lookup"><span data-stu-id="526aa-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="526aa-145">入門應用程式</span><span class="sxs-lookup"><span data-stu-id="526aa-145">The starter app</span></span>

<span data-ttu-id="526aa-146">[下載](xref:index#how-to-download-a-sample)[入門](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/)應用程式。</span><span class="sxs-lookup"><span data-stu-id="526aa-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="526aa-147">執行應用程式，並按一下 [ **ContactManager** ] 連結，然後確認您可以建立、編輯和刪除連絡人。</span><span class="sxs-lookup"><span data-stu-id="526aa-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span> <span data-ttu-id="526aa-148">若要建立入門應用程式，請參閱 [建立入門應用程式](#create-the-starter-app)。</span><span class="sxs-lookup"><span data-stu-id="526aa-148">To create the starter app, see [Create the starter app](#create-the-starter-app).</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="526aa-149">保護使用者資料</span><span class="sxs-lookup"><span data-stu-id="526aa-149">Secure user data</span></span>

<span data-ttu-id="526aa-150">下列各節具有建立安全使用者資料應用程式的所有主要步驟。</span><span class="sxs-lookup"><span data-stu-id="526aa-150">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="526aa-151">您可能會發現參考已完成的專案是有説明的。</span><span class="sxs-lookup"><span data-stu-id="526aa-151">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="526aa-152">將連絡人資料與使用者系結</span><span class="sxs-lookup"><span data-stu-id="526aa-152">Tie the contact data to the user</span></span>

<span data-ttu-id="526aa-153">使用 ASP.NET [Identity](xref:security/authentication/identity) 使用者識別碼，以確保使用者可以編輯其資料，而不是其他使用者資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-153">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="526aa-154">將 `OwnerID` 和加入 `ContactStatus` 至 `Contact` 模型：</span><span class="sxs-lookup"><span data-stu-id="526aa-154">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="526aa-155">`OwnerID` 這是 `AspNetUser` 資料庫中資料表的使用者識別碼 [Identity](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="526aa-155">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="526aa-156">`Status`欄位會決定一般使用者是否可以看到連絡人。</span><span class="sxs-lookup"><span data-stu-id="526aa-156">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="526aa-157">建立新的遷移，並更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="526aa-157">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="526aa-158">將角色服務新增至 Identity</span><span class="sxs-lookup"><span data-stu-id="526aa-158">Add Role services to Identity</span></span>

<span data-ttu-id="526aa-159">附加 [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 以新增角色服務：</span><span class="sxs-lookup"><span data-stu-id="526aa-159">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="526aa-160">需要經過驗證的使用者</span><span class="sxs-lookup"><span data-stu-id="526aa-160">Require authenticated users</span></span>

<span data-ttu-id="526aa-161">設定回溯驗證原則以要求使用者進行驗證：</span><span class="sxs-lookup"><span data-stu-id="526aa-161">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="526aa-162">上述反白顯示的程式碼會設定回溯 [驗證原則](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy)。</span><span class="sxs-lookup"><span data-stu-id="526aa-162">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="526aa-163">除了 Razor 具有驗證屬性的頁面、控制器或動作方法之外，fallback 驗證原則需要驗證所有使用者。</span><span class="sxs-lookup"><span data-stu-id="526aa-163">The fallback authentication policy requires ***all*** users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="526aa-164">例如，使用 Razor 或的頁面、控制器或動作方法，會 `[AllowAnonymous]` `[Authorize(PolicyName="MyPolicy")]` 使用套用的驗證屬性，而不是回溯驗證原則。</span><span class="sxs-lookup"><span data-stu-id="526aa-164">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="526aa-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> 加入 <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> 目前的實例，這會強制驗證目前的使用者。</span><span class="sxs-lookup"><span data-stu-id="526aa-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> adds <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> to the current instance, which enforces that the current user is authenticated.</span></span>

<span data-ttu-id="526aa-166">回退驗證原則：</span><span class="sxs-lookup"><span data-stu-id="526aa-166">The fallback authentication policy:</span></span>

* <span data-ttu-id="526aa-167">會套用至未明確指定驗證原則的所有要求。</span><span class="sxs-lookup"><span data-stu-id="526aa-167">Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="526aa-168">針對端點路由所提供的要求，這會包含未指定授權屬性的任何端點。</span><span class="sxs-lookup"><span data-stu-id="526aa-168">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="526aa-169">針對其他中介軟體在授權中介軟體之後所提供的要求（例如 [靜態](xref:fundamentals/static-files)檔案），這會將原則套用至所有要求。</span><span class="sxs-lookup"><span data-stu-id="526aa-169">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="526aa-170">將回溯驗證原則設定為要求使用者進行驗證，可保護新新增 Razor 的頁面和控制器。</span><span class="sxs-lookup"><span data-stu-id="526aa-170">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="526aa-171">預設需要驗證比依賴新控制器和 Razor 頁面來包含屬性更安全 `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="526aa-171">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="526aa-172"><xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions>類別也包含 <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="526aa-172">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="526aa-173">`DefaultPolicy`是 `[Authorize]` 未指定任何原則時，與屬性搭配使用的原則。</span><span class="sxs-lookup"><span data-stu-id="526aa-173">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="526aa-174">`[Authorize]` 不包含命名原則，與不同 `[Authorize(PolicyName="MyPolicy")]` 。</span><span class="sxs-lookup"><span data-stu-id="526aa-174">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="526aa-175">如需原則的詳細資訊，請參閱 <xref:security/authorization/policies> 。</span><span class="sxs-lookup"><span data-stu-id="526aa-175">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="526aa-176">MVC 控制器和 Razor 頁面需要驗證所有使用者的另一種方法是新增授權篩選準則：</span><span class="sxs-lookup"><span data-stu-id="526aa-176">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="526aa-177">上述程式碼會使用授權篩選準則，設定 fallback 原則會使用端點路由。</span><span class="sxs-lookup"><span data-stu-id="526aa-177">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="526aa-178">設定回復原則是要求所有使用者進行驗證的慣用方式。</span><span class="sxs-lookup"><span data-stu-id="526aa-178">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="526aa-179">將 [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 新增至 `Index` 和 `Privacy` 頁面，讓匿名使用者在註冊之前可以取得網站的相關資訊：</span><span class="sxs-lookup"><span data-stu-id="526aa-179">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="526aa-180">設定測試帳戶</span><span class="sxs-lookup"><span data-stu-id="526aa-180">Configure the test account</span></span>

<span data-ttu-id="526aa-181">`SeedData`類別會建立兩個帳戶：系統管理員和管理員。</span><span class="sxs-lookup"><span data-stu-id="526aa-181">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="526aa-182">使用 [Secret Manager 工具](xref:security/app-secrets) 來設定這些帳戶的密碼。</span><span class="sxs-lookup"><span data-stu-id="526aa-182">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="526aa-183">將專案目錄中的密碼設定 (包含 *Program.cs*) 的目錄：</span><span class="sxs-lookup"><span data-stu-id="526aa-183">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="526aa-184">如果未指定強式密碼，則會在呼叫時擲回例外狀況 `SeedData.Initialize` 。</span><span class="sxs-lookup"><span data-stu-id="526aa-184">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="526aa-185">更新 `Main` 以使用測試密碼：</span><span class="sxs-lookup"><span data-stu-id="526aa-185">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="526aa-186">建立測試帳戶並更新連絡人</span><span class="sxs-lookup"><span data-stu-id="526aa-186">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="526aa-187">更新 `Initialize` 類別中的方法 `SeedData` ，以建立測試帳戶：</span><span class="sxs-lookup"><span data-stu-id="526aa-187">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="526aa-188">將系統管理員使用者識別碼和新增 `ContactStatus` 至連絡人。</span><span class="sxs-lookup"><span data-stu-id="526aa-188">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="526aa-189">讓其中一個連絡人成為「已提交」，另一個「已拒絕」。</span><span class="sxs-lookup"><span data-stu-id="526aa-189">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="526aa-190">將使用者識別碼和狀態新增至所有連絡人。</span><span class="sxs-lookup"><span data-stu-id="526aa-190">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="526aa-191">只會顯示一個連絡人：</span><span class="sxs-lookup"><span data-stu-id="526aa-191">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="526aa-192">建立擁有者、管理員和系統管理員授權處理常式</span><span class="sxs-lookup"><span data-stu-id="526aa-192">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="526aa-193">`ContactIsOwnerAuthorizationHandler`在 *授權* 資料夾中建立類別。</span><span class="sxs-lookup"><span data-stu-id="526aa-193">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="526aa-194">`ContactIsOwnerAuthorizationHandler`會確認對資源採取行動的使用者擁有資源。</span><span class="sxs-lookup"><span data-stu-id="526aa-194">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="526aa-195">`ContactIsOwnerAuthorizationHandler`呼叫[內容。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)如果目前已驗證的使用者為連絡人擁有者，則會成功。</span><span class="sxs-lookup"><span data-stu-id="526aa-195">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="526aa-196">授權處理常式通常：</span><span class="sxs-lookup"><span data-stu-id="526aa-196">Authorization handlers generally:</span></span>

* <span data-ttu-id="526aa-197">`context.Succeed`符合需求時傳回。</span><span class="sxs-lookup"><span data-stu-id="526aa-197">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="526aa-198">`Task.CompletedTask`不符合需求時傳回。</span><span class="sxs-lookup"><span data-stu-id="526aa-198">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="526aa-199">`Task.CompletedTask` 不是成功或失敗， &mdash; 允許其他授權處理常式執行。</span><span class="sxs-lookup"><span data-stu-id="526aa-199">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="526aa-200">如果您需要明確失敗，請傳回 [內容。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="526aa-200">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="526aa-201">應用程式可讓連絡人擁有者編輯/刪除/建立自己的資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-201">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="526aa-202">`ContactIsOwnerAuthorizationHandler` 不需要檢查需求參數中所傳遞的作業。</span><span class="sxs-lookup"><span data-stu-id="526aa-202">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="526aa-203">建立管理員授權處理常式</span><span class="sxs-lookup"><span data-stu-id="526aa-203">Create a manager authorization handler</span></span>

<span data-ttu-id="526aa-204">`ContactManagerAuthorizationHandler`在 *授權* 資料夾中建立類別。</span><span class="sxs-lookup"><span data-stu-id="526aa-204">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="526aa-205">`ContactManagerAuthorizationHandler`會驗證資源上的使用者是否為管理員。</span><span class="sxs-lookup"><span data-stu-id="526aa-205">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="526aa-206">只有管理員可以核准或拒絕 (新增或變更) 的內容變更。</span><span class="sxs-lookup"><span data-stu-id="526aa-206">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="526aa-207">建立系統管理員授權處理常式</span><span class="sxs-lookup"><span data-stu-id="526aa-207">Create an administrator authorization handler</span></span>

<span data-ttu-id="526aa-208">`ContactAdministratorsAuthorizationHandler`在 *授權* 資料夾中建立類別。</span><span class="sxs-lookup"><span data-stu-id="526aa-208">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="526aa-209">`ContactAdministratorsAuthorizationHandler`會驗證資源的使用者是否為系統管理員。</span><span class="sxs-lookup"><span data-stu-id="526aa-209">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="526aa-210">系統管理員可以進行所有作業。</span><span class="sxs-lookup"><span data-stu-id="526aa-210">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="526aa-211">註冊授權處理常式</span><span class="sxs-lookup"><span data-stu-id="526aa-211">Register the authorization handlers</span></span>

<span data-ttu-id="526aa-212">使用 Entity Framework Core 的服務必須使用[AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)註冊相依性[插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="526aa-212">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="526aa-213">`ContactIsOwnerAuthorizationHandler`使用 [Identity](xref:security/authentication/identity) 以 Entity Framework core 為基礎的 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="526aa-213">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="526aa-214">使用服務集合註冊處理常式，以便透過相依性插入來使用它們 `ContactsController` 。 [](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="526aa-214">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="526aa-215">將下列程式碼加入至結尾 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="526aa-215">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="526aa-216">`ContactAdministratorsAuthorizationHandler` 和 `ContactManagerAuthorizationHandler` 會新增為 singleton。</span><span class="sxs-lookup"><span data-stu-id="526aa-216">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="526aa-217">它們是 singleton 的，因為它們不會使用 EF，而且所需的所有資訊都在 `Context` 方法的參數中 `HandleRequirementAsync` 。</span><span class="sxs-lookup"><span data-stu-id="526aa-217">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="526aa-218">支援授權</span><span class="sxs-lookup"><span data-stu-id="526aa-218">Support authorization</span></span>

<span data-ttu-id="526aa-219">在本節中，您會更新 Razor 頁面並新增作業需求類別。</span><span class="sxs-lookup"><span data-stu-id="526aa-219">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="526aa-220">複習 contact 作業需求課程</span><span class="sxs-lookup"><span data-stu-id="526aa-220">Review the contact operations requirements class</span></span>

<span data-ttu-id="526aa-221">請參閱 `ContactOperations` 類別。</span><span class="sxs-lookup"><span data-stu-id="526aa-221">Review the `ContactOperations` class.</span></span> <span data-ttu-id="526aa-222">此類別包含應用程式支援的需求：</span><span class="sxs-lookup"><span data-stu-id="526aa-222">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="526aa-223">建立連絡人頁面的基類 Razor</span><span class="sxs-lookup"><span data-stu-id="526aa-223">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="526aa-224">建立包含 [連絡人] 頁面中所使用之服務的基類 Razor 。</span><span class="sxs-lookup"><span data-stu-id="526aa-224">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="526aa-225">基底類別會將初始化程式碼放在一個位置：</span><span class="sxs-lookup"><span data-stu-id="526aa-225">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="526aa-226">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="526aa-226">The preceding code:</span></span>

* <span data-ttu-id="526aa-227">新增 `IAuthorizationService` 服務以存取授權處理常式。</span><span class="sxs-lookup"><span data-stu-id="526aa-227">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="526aa-228">加入 Identity `UserManager` 服務。</span><span class="sxs-lookup"><span data-stu-id="526aa-228">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="526aa-229">加入 `ApplicationDbContext`。</span><span class="sxs-lookup"><span data-stu-id="526aa-229">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="526aa-230">更新 CreateModel</span><span class="sxs-lookup"><span data-stu-id="526aa-230">Update the CreateModel</span></span>

<span data-ttu-id="526aa-231">更新建立頁面模型的函式以使用 `DI_BasePageModel` 基類：</span><span class="sxs-lookup"><span data-stu-id="526aa-231">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="526aa-232">將 `CreateModel.OnPostAsync` 方法更新為：</span><span class="sxs-lookup"><span data-stu-id="526aa-232">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="526aa-233">將使用者識別碼加入至 `Contact` 模型。</span><span class="sxs-lookup"><span data-stu-id="526aa-233">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="526aa-234">呼叫授權處理常式來確認使用者有權建立連絡人。</span><span class="sxs-lookup"><span data-stu-id="526aa-234">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="526aa-235">更新 IndexModel</span><span class="sxs-lookup"><span data-stu-id="526aa-235">Update the IndexModel</span></span>

<span data-ttu-id="526aa-236">更新 `OnGetAsync` 方法，如此一來，只有已核准的連絡人會顯示給一般使用者：</span><span class="sxs-lookup"><span data-stu-id="526aa-236">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="526aa-237">更新 EditModel</span><span class="sxs-lookup"><span data-stu-id="526aa-237">Update the EditModel</span></span>

<span data-ttu-id="526aa-238">新增授權處理常式來確認使用者擁有該連絡人。</span><span class="sxs-lookup"><span data-stu-id="526aa-238">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="526aa-239">因為正在驗證資源授權，所以 `[Authorize]` 屬性不足。</span><span class="sxs-lookup"><span data-stu-id="526aa-239">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="526aa-240">評估屬性時，應用程式無法存取資源。</span><span class="sxs-lookup"><span data-stu-id="526aa-240">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="526aa-241">以資源為基礎的授權必須是必要的。</span><span class="sxs-lookup"><span data-stu-id="526aa-241">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="526aa-242">一旦應用程式可存取資源，就必須執行檢查，方法是將它載入頁面模型中，或在處理常式本身內載入。</span><span class="sxs-lookup"><span data-stu-id="526aa-242">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="526aa-243">您經常會藉由傳入資源金鑰來存取資源。</span><span class="sxs-lookup"><span data-stu-id="526aa-243">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="526aa-244">更新 DeleteModel</span><span class="sxs-lookup"><span data-stu-id="526aa-244">Update the DeleteModel</span></span>

<span data-ttu-id="526aa-245">更新 [刪除] 頁面模型，以使用授權處理常式來確認使用者擁有連絡人的 [刪除] 許可權。</span><span class="sxs-lookup"><span data-stu-id="526aa-245">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="526aa-246">將授權服務插入至 views</span><span class="sxs-lookup"><span data-stu-id="526aa-246">Inject the authorization service into the views</span></span>

<span data-ttu-id="526aa-247">目前，UI 會顯示使用者無法修改之連絡人的 [編輯] 和 [刪除] 連結。</span><span class="sxs-lookup"><span data-stu-id="526aa-247">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="526aa-248">將授權服務插入 *Pages/_ViewImports cshtml* 檔案，以便所有視圖都可使用：</span><span class="sxs-lookup"><span data-stu-id="526aa-248">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="526aa-249">上述標記會新增數個 `using` 語句。</span><span class="sxs-lookup"><span data-stu-id="526aa-249">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="526aa-250">更新 *Pages/Contacts/Index* 中的 [**編輯**] 和 [**刪除**] 連結，以便只針對具有適當許可權的使用者轉譯這些連結：</span><span class="sxs-lookup"><span data-stu-id="526aa-250">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="526aa-251">隱藏沒有變更資料許可權之使用者的連結，並不會保護應用程式的安全。</span><span class="sxs-lookup"><span data-stu-id="526aa-251">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="526aa-252">隱藏連結可只顯示有效的連結，讓應用程式更容易使用。</span><span class="sxs-lookup"><span data-stu-id="526aa-252">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="526aa-253">使用者可以攻擊產生的 Url，以叫用其未擁有之資料的編輯和刪除作業。</span><span class="sxs-lookup"><span data-stu-id="526aa-253">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="526aa-254">Razor頁面或控制器必須強制執行存取檢查以保護資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-254">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="526aa-255">更新詳細資料</span><span class="sxs-lookup"><span data-stu-id="526aa-255">Update Details</span></span>

<span data-ttu-id="526aa-256">更新 [詳細資料] 視圖，讓管理員可以核准或拒絕連絡人：</span><span class="sxs-lookup"><span data-stu-id="526aa-256">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="526aa-257">更新詳細資料頁面模型：</span><span class="sxs-lookup"><span data-stu-id="526aa-257">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="526aa-258">新增或移除角色的使用者</span><span class="sxs-lookup"><span data-stu-id="526aa-258">Add or remove a user to a role</span></span>

<span data-ttu-id="526aa-259">如需相關資訊，請參閱 [此問題](https://github.com/dotnet/AspNetCore.Docs/issues/8502) ：</span><span class="sxs-lookup"><span data-stu-id="526aa-259">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="526aa-260">正在移除使用者的許可權。</span><span class="sxs-lookup"><span data-stu-id="526aa-260">Removing privileges from a user.</span></span> <span data-ttu-id="526aa-261">例如，將聊天應用程式中的使用者靜音。</span><span class="sxs-lookup"><span data-stu-id="526aa-261">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="526aa-262">將許可權新增至使用者。</span><span class="sxs-lookup"><span data-stu-id="526aa-262">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="526aa-263">挑戰與禁止之間的差異</span><span class="sxs-lookup"><span data-stu-id="526aa-263">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="526aa-264">此應用程式會將預設原則設定為 [需要經過驗證的使用者](#rau)。</span><span class="sxs-lookup"><span data-stu-id="526aa-264">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="526aa-265">下列程式碼允許匿名使用者。</span><span class="sxs-lookup"><span data-stu-id="526aa-265">The following code allows anonymous users.</span></span> <span data-ttu-id="526aa-266">允許匿名使用者顯示挑戰與禁止之間的差異。</span><span class="sxs-lookup"><span data-stu-id="526aa-266">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="526aa-267">在上述程式碼中：</span><span class="sxs-lookup"><span data-stu-id="526aa-267">In the preceding code:</span></span>

* <span data-ttu-id="526aa-268">當使用者 **未通過驗證** 時， `ChallengeResult` 就會傳回。</span><span class="sxs-lookup"><span data-stu-id="526aa-268">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="526aa-269">當 `ChallengeResult` 傳回時，會將使用者重新導向至登入頁面。</span><span class="sxs-lookup"><span data-stu-id="526aa-269">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="526aa-270">當使用者經過驗證但未獲授權時， `ForbidResult` 會傳回。</span><span class="sxs-lookup"><span data-stu-id="526aa-270">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="526aa-271">當 `ForbidResult` 傳回時，會將使用者重新導向至 [拒絕存取] 頁面。</span><span class="sxs-lookup"><span data-stu-id="526aa-271">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="526aa-272">測試已完成的應用程式</span><span class="sxs-lookup"><span data-stu-id="526aa-272">Test the completed app</span></span>

<span data-ttu-id="526aa-273">如果您尚未設定植入使用者帳戶的密碼，請使用 [Secret Manager 工具](xref:security/app-secrets#secret-manager) 來設定密碼：</span><span class="sxs-lookup"><span data-stu-id="526aa-273">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="526aa-274">選擇強式密碼：使用八個或更多字元，以及至少一個大寫字元、數位和符號。</span><span class="sxs-lookup"><span data-stu-id="526aa-274">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="526aa-275">例如， `Passw0rd!` 符合強式密碼需求。</span><span class="sxs-lookup"><span data-stu-id="526aa-275">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="526aa-276">從專案的資料夾執行下列命令，其中 `<PW>` 是密碼：</span><span class="sxs-lookup"><span data-stu-id="526aa-276">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="526aa-277">如果應用程式有連絡人：</span><span class="sxs-lookup"><span data-stu-id="526aa-277">If the app has contacts:</span></span>

* <span data-ttu-id="526aa-278">刪除資料表中的所有記錄 `Contact` 。</span><span class="sxs-lookup"><span data-stu-id="526aa-278">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="526aa-279">重新開機應用程式以植入資料庫。</span><span class="sxs-lookup"><span data-stu-id="526aa-279">Restart the app to seed the database.</span></span>

<span data-ttu-id="526aa-280">測試完成的應用程式的簡單方法，就是啟動三個不同的瀏覽器 (或 incognito/InPrivate 會話) 。</span><span class="sxs-lookup"><span data-stu-id="526aa-280">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="526aa-281">在一個瀏覽器中，註冊新的使用者 (例如 `test@example.com`) 。</span><span class="sxs-lookup"><span data-stu-id="526aa-281">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="526aa-282">使用不同的使用者登入每個瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="526aa-282">Sign in to each browser with a different user.</span></span> <span data-ttu-id="526aa-283">確認下列作業：</span><span class="sxs-lookup"><span data-stu-id="526aa-283">Verify the following operations:</span></span>

* <span data-ttu-id="526aa-284">註冊的使用者可以查看所有核准的連絡人資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-284">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="526aa-285">註冊的使用者可以編輯/刪除自己的資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-285">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="526aa-286">管理員可以核准/拒絕連絡人資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-286">Managers can approve/reject contact data.</span></span> <span data-ttu-id="526aa-287">此 `Details` 視圖會顯示 [ **核准** ] 和 [ **拒絕** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="526aa-287">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="526aa-288">系統管理員可以核准/拒絕和編輯/刪除所有資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-288">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="526aa-289">User</span><span class="sxs-lookup"><span data-stu-id="526aa-289">User</span></span>                | <span data-ttu-id="526aa-290">由應用程式植入</span><span class="sxs-lookup"><span data-stu-id="526aa-290">Seeded by the app</span></span> | <span data-ttu-id="526aa-291">選項</span><span class="sxs-lookup"><span data-stu-id="526aa-291">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="526aa-292">No</span><span class="sxs-lookup"><span data-stu-id="526aa-292">No</span></span>                | <span data-ttu-id="526aa-293">編輯/刪除自己的資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-293">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="526aa-294">Yes</span><span class="sxs-lookup"><span data-stu-id="526aa-294">Yes</span></span>               | <span data-ttu-id="526aa-295">核准/拒絕和編輯/刪除自己的資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-295">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="526aa-296">Yes</span><span class="sxs-lookup"><span data-stu-id="526aa-296">Yes</span></span>               | <span data-ttu-id="526aa-297">核准/拒絕和編輯/刪除所有資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-297">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="526aa-298">在系統管理員的瀏覽器中建立連絡人。</span><span class="sxs-lookup"><span data-stu-id="526aa-298">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="526aa-299">從系統管理員連絡人複製 [刪除] 和 [編輯] 的 URL。</span><span class="sxs-lookup"><span data-stu-id="526aa-299">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="526aa-300">將這些連結貼到測試使用者的瀏覽器中，以確認測試使用者無法執行這些作業。</span><span class="sxs-lookup"><span data-stu-id="526aa-300">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="526aa-301">建立入門應用程式</span><span class="sxs-lookup"><span data-stu-id="526aa-301">Create the starter app</span></span>

* <span data-ttu-id="526aa-302">建立 Razor 名為 "ContactManager" 的頁面應用程式</span><span class="sxs-lookup"><span data-stu-id="526aa-302">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="526aa-303">使用 **個別的使用者帳戶** 建立應用程式。</span><span class="sxs-lookup"><span data-stu-id="526aa-303">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="526aa-304">將它命名為 "ContactManager"，讓命名空間符合範例中使用的命名空間。</span><span class="sxs-lookup"><span data-stu-id="526aa-304">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="526aa-305">`-uld` 指定 LocalDB 而不是 SQLite</span><span class="sxs-lookup"><span data-stu-id="526aa-305">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="526aa-306">新增 *模型/連絡人 .cs*：</span><span class="sxs-lookup"><span data-stu-id="526aa-306">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="526aa-307">Scaffold `Contact` 模型。</span><span class="sxs-lookup"><span data-stu-id="526aa-307">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="526aa-308">建立初始遷移並更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="526aa-308">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="526aa-309">如果您在命令中遇到錯誤 `dotnet aspnet-codegenerator razorpage` ，請參閱 [此 GitHub 問題](https://github.com/aspnet/Scaffolding/issues/984)。</span><span class="sxs-lookup"><span data-stu-id="526aa-309">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="526aa-310">更新 *Pages/Shared/_Layout cshtml* 檔案中的 **ContactManager** 錨點：</span><span class="sxs-lookup"><span data-stu-id="526aa-310">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="526aa-311">建立、編輯和刪除連絡人以測試應用程式</span><span class="sxs-lookup"><span data-stu-id="526aa-311">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="526aa-312">植入資料庫</span><span class="sxs-lookup"><span data-stu-id="526aa-312">Seed the database</span></span>

<span data-ttu-id="526aa-313">將 [>seeddata.cs](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) 類別新增至 [ *資料* ] 資料夾：</span><span class="sxs-lookup"><span data-stu-id="526aa-313">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="526aa-314">呼叫 `SeedData.Initialize` 來源 `Main` ：</span><span class="sxs-lookup"><span data-stu-id="526aa-314">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="526aa-315">測試應用程式是否植入資料庫。</span><span class="sxs-lookup"><span data-stu-id="526aa-315">Test that the app seeded the database.</span></span> <span data-ttu-id="526aa-316">如果 contact DB 中有任何資料列，種子方法就不會執行。</span><span class="sxs-lookup"><span data-stu-id="526aa-316">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="526aa-317">本教學課程說明如何建立 ASP.NET Core web 應用程式，並以授權保護使用者資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-317">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="526aa-318">它會顯示已驗證 (註冊) 使用者建立的連絡人清單。</span><span class="sxs-lookup"><span data-stu-id="526aa-318">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="526aa-319">有三個安全性群組：</span><span class="sxs-lookup"><span data-stu-id="526aa-319">There are three security groups:</span></span>

* <span data-ttu-id="526aa-320">**註冊的使用者** 可以查看所有核准的資料，並可編輯/刪除他們自己的資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-320">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="526aa-321">**管理員** 可以核准或拒絕連絡人資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-321">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="526aa-322">只有核准的連絡人可以看到使用者。</span><span class="sxs-lookup"><span data-stu-id="526aa-322">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="526aa-323">系統 **管理員** 可以核准/拒絕和編輯/刪除任何資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-323">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="526aa-324">在下圖中，使用者 Rick (`rick@example.com`) 已登入。</span><span class="sxs-lookup"><span data-stu-id="526aa-324">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="526aa-325">Rick 只能查看核准的連絡人，並 **編輯** / **刪除** / 為連絡人 **建立新** 的連結。</span><span class="sxs-lookup"><span data-stu-id="526aa-325">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="526aa-326">只有 Rick 所建立的最後一筆記錄會顯示 [ **編輯** ] 和 [ **刪除** ] 連結。</span><span class="sxs-lookup"><span data-stu-id="526aa-326">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="526aa-327">在管理員或系統管理員將狀態變更為 [已核准] 之前，其他使用者將不會看到最後一筆記錄。</span><span class="sxs-lookup"><span data-stu-id="526aa-327">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![顯示 Rick 已登入的螢幕擷取畫面](secure-data/_static/rick.png)

<span data-ttu-id="526aa-329">在下圖中， `manager@contoso.com` 已登入並在管理員的角色中：</span><span class="sxs-lookup"><span data-stu-id="526aa-329">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![顯示 manager@contoso.com 已登入的螢幕擷取畫面](secure-data/_static/manager1.png)

<span data-ttu-id="526aa-331">下圖顯示連絡人的經理詳細資料檢視：</span><span class="sxs-lookup"><span data-stu-id="526aa-331">The following image shows the managers details view of a contact:</span></span>

![連絡人的經理觀點](secure-data/_static/manager.png)

<span data-ttu-id="526aa-333">[ **核准** ] 和 [ **拒絕** ] 按鈕只會顯示給管理員和系統管理員。</span><span class="sxs-lookup"><span data-stu-id="526aa-333">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="526aa-334">在下圖中， `admin@contoso.com` 已登入並以系統管理員的角色：</span><span class="sxs-lookup"><span data-stu-id="526aa-334">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![顯示 admin@contoso.com 已登入的螢幕擷取畫面](secure-data/_static/admin.png)

<span data-ttu-id="526aa-336">系統管理員具有擁有權限。</span><span class="sxs-lookup"><span data-stu-id="526aa-336">The administrator has all privileges.</span></span> <span data-ttu-id="526aa-337">她可以讀取/編輯/刪除任何連絡人，以及變更連絡人的狀態。</span><span class="sxs-lookup"><span data-stu-id="526aa-337">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="526aa-338">應用程式 [是由下列](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) `Contact` 模型建立：</span><span class="sxs-lookup"><span data-stu-id="526aa-338">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="526aa-339">此範例包含下列授權處理常式：</span><span class="sxs-lookup"><span data-stu-id="526aa-339">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="526aa-340">`ContactIsOwnerAuthorizationHandler`：確保使用者只能編輯其資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-340">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="526aa-341">`ContactManagerAuthorizationHandler`：可讓管理員核准或拒絕連絡人。</span><span class="sxs-lookup"><span data-stu-id="526aa-341">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="526aa-342">`ContactAdministratorsAuthorizationHandler`：可讓系統管理員核准或拒絕連絡人，以及編輯/刪除連絡人。</span><span class="sxs-lookup"><span data-stu-id="526aa-342">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="526aa-343">必要條件</span><span class="sxs-lookup"><span data-stu-id="526aa-343">Prerequisites</span></span>

<span data-ttu-id="526aa-344">本教學課程是 advanced。</span><span class="sxs-lookup"><span data-stu-id="526aa-344">This tutorial is advanced.</span></span> <span data-ttu-id="526aa-345">您應該熟悉：</span><span class="sxs-lookup"><span data-stu-id="526aa-345">You should be familiar with:</span></span>

* [<span data-ttu-id="526aa-346">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="526aa-346">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="526aa-347">驗證</span><span class="sxs-lookup"><span data-stu-id="526aa-347">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="526aa-348">帳戶確認和密碼復原</span><span class="sxs-lookup"><span data-stu-id="526aa-348">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="526aa-349">授權</span><span class="sxs-lookup"><span data-stu-id="526aa-349">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="526aa-350">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="526aa-350">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="526aa-351">入門和完成的應用程式</span><span class="sxs-lookup"><span data-stu-id="526aa-351">The starter and completed app</span></span>

<span data-ttu-id="526aa-352">[下載](xref:index#how-to-download-a-sample)[已完成](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples)的應用程式。</span><span class="sxs-lookup"><span data-stu-id="526aa-352">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="526aa-353">[測試](#test-the-completed-app) 完成的應用程式，讓您熟悉它的安全性功能。</span><span class="sxs-lookup"><span data-stu-id="526aa-353">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="526aa-354">入門應用程式</span><span class="sxs-lookup"><span data-stu-id="526aa-354">The starter app</span></span>

<span data-ttu-id="526aa-355">[下載](xref:index#how-to-download-a-sample)[入門](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/)應用程式。</span><span class="sxs-lookup"><span data-stu-id="526aa-355">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="526aa-356">執行應用程式，並按一下 [ **ContactManager** ] 連結，然後確認您可以建立、編輯和刪除連絡人。</span><span class="sxs-lookup"><span data-stu-id="526aa-356">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="526aa-357">保護使用者資料</span><span class="sxs-lookup"><span data-stu-id="526aa-357">Secure user data</span></span>

<span data-ttu-id="526aa-358">下列各節具有建立安全使用者資料應用程式的所有主要步驟。</span><span class="sxs-lookup"><span data-stu-id="526aa-358">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="526aa-359">您可能會發現參考已完成的專案是有説明的。</span><span class="sxs-lookup"><span data-stu-id="526aa-359">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="526aa-360">將連絡人資料與使用者系結</span><span class="sxs-lookup"><span data-stu-id="526aa-360">Tie the contact data to the user</span></span>

<span data-ttu-id="526aa-361">使用 ASP.NET [Identity](xref:security/authentication/identity) 使用者識別碼，以確保使用者可以編輯其資料，而不是其他使用者資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-361">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="526aa-362">將 `OwnerID` 和加入 `ContactStatus` 至 `Contact` 模型：</span><span class="sxs-lookup"><span data-stu-id="526aa-362">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="526aa-363">`OwnerID` 這是 `AspNetUser` 資料庫中資料表的使用者識別碼 [Identity](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="526aa-363">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="526aa-364">`Status`欄位會決定一般使用者是否可以看到連絡人。</span><span class="sxs-lookup"><span data-stu-id="526aa-364">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="526aa-365">建立新的遷移，並更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="526aa-365">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="526aa-366">將角色服務新增至 Identity</span><span class="sxs-lookup"><span data-stu-id="526aa-366">Add Role services to Identity</span></span>

<span data-ttu-id="526aa-367">附加 [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 以新增角色服務：</span><span class="sxs-lookup"><span data-stu-id="526aa-367">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="526aa-368">需要經過驗證的使用者</span><span class="sxs-lookup"><span data-stu-id="526aa-368">Require authenticated users</span></span>

<span data-ttu-id="526aa-369">設定預設的驗證原則，要求使用者進行驗證：</span><span class="sxs-lookup"><span data-stu-id="526aa-369">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="526aa-370">您可以選擇不 Razor 使用屬性來進行頁面、控制器或動作方法層級的驗證 `[AllowAnonymous]` 。</span><span class="sxs-lookup"><span data-stu-id="526aa-370">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="526aa-371">將預設的驗證原則設定為要求使用者進行驗證，可保護新新增 Razor 的頁面和控制器。</span><span class="sxs-lookup"><span data-stu-id="526aa-371">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="526aa-372">預設需要驗證比依賴新控制器和 Razor 頁面來包含屬性更安全 `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="526aa-372">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="526aa-373">將 [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 加入至 Index、About 和 Contact 頁面，讓匿名使用者在註冊之前，可以取得網站的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="526aa-373">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="526aa-374">設定測試帳戶</span><span class="sxs-lookup"><span data-stu-id="526aa-374">Configure the test account</span></span>

<span data-ttu-id="526aa-375">`SeedData`類別會建立兩個帳戶：系統管理員和管理員。</span><span class="sxs-lookup"><span data-stu-id="526aa-375">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="526aa-376">使用 [Secret Manager 工具](xref:security/app-secrets) 來設定這些帳戶的密碼。</span><span class="sxs-lookup"><span data-stu-id="526aa-376">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="526aa-377">將專案目錄中的密碼設定 (包含 *Program.cs*) 的目錄：</span><span class="sxs-lookup"><span data-stu-id="526aa-377">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="526aa-378">如果未指定強式密碼，則會在呼叫時擲回例外狀況 `SeedData.Initialize` 。</span><span class="sxs-lookup"><span data-stu-id="526aa-378">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="526aa-379">更新 `Main` 以使用測試密碼：</span><span class="sxs-lookup"><span data-stu-id="526aa-379">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="526aa-380">建立測試帳戶並更新連絡人</span><span class="sxs-lookup"><span data-stu-id="526aa-380">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="526aa-381">更新 `Initialize` 類別中的方法 `SeedData` ，以建立測試帳戶：</span><span class="sxs-lookup"><span data-stu-id="526aa-381">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="526aa-382">將系統管理員使用者識別碼和新增 `ContactStatus` 至連絡人。</span><span class="sxs-lookup"><span data-stu-id="526aa-382">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="526aa-383">讓其中一個連絡人成為「已提交」，另一個「已拒絕」。</span><span class="sxs-lookup"><span data-stu-id="526aa-383">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="526aa-384">將使用者識別碼和狀態新增至所有連絡人。</span><span class="sxs-lookup"><span data-stu-id="526aa-384">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="526aa-385">只會顯示一個連絡人：</span><span class="sxs-lookup"><span data-stu-id="526aa-385">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="526aa-386">建立擁有者、管理員和系統管理員授權處理常式</span><span class="sxs-lookup"><span data-stu-id="526aa-386">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="526aa-387">建立 *授權* 資料夾，並 `ContactIsOwnerAuthorizationHandler` 在其中建立類別。</span><span class="sxs-lookup"><span data-stu-id="526aa-387">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="526aa-388">`ContactIsOwnerAuthorizationHandler`會確認對資源採取行動的使用者擁有資源。</span><span class="sxs-lookup"><span data-stu-id="526aa-388">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="526aa-389">`ContactIsOwnerAuthorizationHandler`呼叫[內容。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)如果目前已驗證的使用者為連絡人擁有者，則會成功。</span><span class="sxs-lookup"><span data-stu-id="526aa-389">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="526aa-390">授權處理常式通常：</span><span class="sxs-lookup"><span data-stu-id="526aa-390">Authorization handlers generally:</span></span>

* <span data-ttu-id="526aa-391">`context.Succeed`符合需求時傳回。</span><span class="sxs-lookup"><span data-stu-id="526aa-391">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="526aa-392">`Task.CompletedTask`不符合需求時傳回。</span><span class="sxs-lookup"><span data-stu-id="526aa-392">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="526aa-393">`Task.CompletedTask` 不是成功或失敗， &mdash; 允許其他授權處理常式執行。</span><span class="sxs-lookup"><span data-stu-id="526aa-393">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="526aa-394">如果您需要明確失敗，請傳回 [內容。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="526aa-394">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="526aa-395">應用程式可讓連絡人擁有者編輯/刪除/建立自己的資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-395">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="526aa-396">`ContactIsOwnerAuthorizationHandler` 不需要檢查需求參數中所傳遞的作業。</span><span class="sxs-lookup"><span data-stu-id="526aa-396">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="526aa-397">建立管理員授權處理常式</span><span class="sxs-lookup"><span data-stu-id="526aa-397">Create a manager authorization handler</span></span>

<span data-ttu-id="526aa-398">`ContactManagerAuthorizationHandler`在 *授權* 資料夾中建立類別。</span><span class="sxs-lookup"><span data-stu-id="526aa-398">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="526aa-399">`ContactManagerAuthorizationHandler`會驗證資源上的使用者是否為管理員。</span><span class="sxs-lookup"><span data-stu-id="526aa-399">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="526aa-400">只有管理員可以核准或拒絕 (新增或變更) 的內容變更。</span><span class="sxs-lookup"><span data-stu-id="526aa-400">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="526aa-401">建立系統管理員授權處理常式</span><span class="sxs-lookup"><span data-stu-id="526aa-401">Create an administrator authorization handler</span></span>

<span data-ttu-id="526aa-402">`ContactAdministratorsAuthorizationHandler`在 *授權* 資料夾中建立類別。</span><span class="sxs-lookup"><span data-stu-id="526aa-402">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="526aa-403">`ContactAdministratorsAuthorizationHandler`會驗證資源的使用者是否為系統管理員。</span><span class="sxs-lookup"><span data-stu-id="526aa-403">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="526aa-404">系統管理員可以進行所有作業。</span><span class="sxs-lookup"><span data-stu-id="526aa-404">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="526aa-405">註冊授權處理常式</span><span class="sxs-lookup"><span data-stu-id="526aa-405">Register the authorization handlers</span></span>

<span data-ttu-id="526aa-406">使用 Entity Framework Core 的服務必須使用[AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)註冊相依性[插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="526aa-406">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="526aa-407">`ContactIsOwnerAuthorizationHandler`使用 [Identity](xref:security/authentication/identity) 以 Entity Framework core 為基礎的 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="526aa-407">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="526aa-408">使用服務集合註冊處理常式，以便透過相依性插入來使用它們 `ContactsController` 。 [](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="526aa-408">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="526aa-409">將下列程式碼加入至結尾 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="526aa-409">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="526aa-410">`ContactAdministratorsAuthorizationHandler` 和 `ContactManagerAuthorizationHandler` 會新增為 singleton。</span><span class="sxs-lookup"><span data-stu-id="526aa-410">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="526aa-411">它們是 singleton 的，因為它們不會使用 EF，而且所需的所有資訊都在 `Context` 方法的參數中 `HandleRequirementAsync` 。</span><span class="sxs-lookup"><span data-stu-id="526aa-411">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="526aa-412">支援授權</span><span class="sxs-lookup"><span data-stu-id="526aa-412">Support authorization</span></span>

<span data-ttu-id="526aa-413">在本節中，您會更新 Razor 頁面並新增作業需求類別。</span><span class="sxs-lookup"><span data-stu-id="526aa-413">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="526aa-414">複習 contact 作業需求課程</span><span class="sxs-lookup"><span data-stu-id="526aa-414">Review the contact operations requirements class</span></span>

<span data-ttu-id="526aa-415">請參閱 `ContactOperations` 類別。</span><span class="sxs-lookup"><span data-stu-id="526aa-415">Review the `ContactOperations` class.</span></span> <span data-ttu-id="526aa-416">此類別包含應用程式支援的需求：</span><span class="sxs-lookup"><span data-stu-id="526aa-416">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="526aa-417">建立連絡人頁面的基類 Razor</span><span class="sxs-lookup"><span data-stu-id="526aa-417">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="526aa-418">建立包含 [連絡人] 頁面中所使用之服務的基類 Razor 。</span><span class="sxs-lookup"><span data-stu-id="526aa-418">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="526aa-419">基底類別會將初始化程式碼放在一個位置：</span><span class="sxs-lookup"><span data-stu-id="526aa-419">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="526aa-420">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="526aa-420">The preceding code:</span></span>

* <span data-ttu-id="526aa-421">新增 `IAuthorizationService` 服務以存取授權處理常式。</span><span class="sxs-lookup"><span data-stu-id="526aa-421">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="526aa-422">加入 Identity `UserManager` 服務。</span><span class="sxs-lookup"><span data-stu-id="526aa-422">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="526aa-423">加入 `ApplicationDbContext`。</span><span class="sxs-lookup"><span data-stu-id="526aa-423">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="526aa-424">更新 CreateModel</span><span class="sxs-lookup"><span data-stu-id="526aa-424">Update the CreateModel</span></span>

<span data-ttu-id="526aa-425">更新建立頁面模型的函式以使用 `DI_BasePageModel` 基類：</span><span class="sxs-lookup"><span data-stu-id="526aa-425">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="526aa-426">將 `CreateModel.OnPostAsync` 方法更新為：</span><span class="sxs-lookup"><span data-stu-id="526aa-426">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="526aa-427">將使用者識別碼加入至 `Contact` 模型。</span><span class="sxs-lookup"><span data-stu-id="526aa-427">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="526aa-428">呼叫授權處理常式來確認使用者有權建立連絡人。</span><span class="sxs-lookup"><span data-stu-id="526aa-428">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="526aa-429">更新 IndexModel</span><span class="sxs-lookup"><span data-stu-id="526aa-429">Update the IndexModel</span></span>

<span data-ttu-id="526aa-430">更新 `OnGetAsync` 方法，如此一來，只有已核准的連絡人會顯示給一般使用者：</span><span class="sxs-lookup"><span data-stu-id="526aa-430">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="526aa-431">更新 EditModel</span><span class="sxs-lookup"><span data-stu-id="526aa-431">Update the EditModel</span></span>

<span data-ttu-id="526aa-432">新增授權處理常式來確認使用者擁有該連絡人。</span><span class="sxs-lookup"><span data-stu-id="526aa-432">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="526aa-433">因為正在驗證資源授權，所以 `[Authorize]` 屬性不足。</span><span class="sxs-lookup"><span data-stu-id="526aa-433">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="526aa-434">評估屬性時，應用程式無法存取資源。</span><span class="sxs-lookup"><span data-stu-id="526aa-434">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="526aa-435">以資源為基礎的授權必須是必要的。</span><span class="sxs-lookup"><span data-stu-id="526aa-435">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="526aa-436">一旦應用程式可存取資源，就必須執行檢查，方法是將它載入頁面模型中，或在處理常式本身內載入。</span><span class="sxs-lookup"><span data-stu-id="526aa-436">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="526aa-437">您經常會藉由傳入資源金鑰來存取資源。</span><span class="sxs-lookup"><span data-stu-id="526aa-437">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="526aa-438">更新 DeleteModel</span><span class="sxs-lookup"><span data-stu-id="526aa-438">Update the DeleteModel</span></span>

<span data-ttu-id="526aa-439">更新 [刪除] 頁面模型，以使用授權處理常式來確認使用者擁有連絡人的 [刪除] 許可權。</span><span class="sxs-lookup"><span data-stu-id="526aa-439">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="526aa-440">將授權服務插入至 views</span><span class="sxs-lookup"><span data-stu-id="526aa-440">Inject the authorization service into the views</span></span>

<span data-ttu-id="526aa-441">目前，UI 會顯示使用者無法修改之連絡人的 [編輯] 和 [刪除] 連結。</span><span class="sxs-lookup"><span data-stu-id="526aa-441">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="526aa-442">將授權服務插入 *views/_ViewImports cshtml* 檔案，以便所有視圖都可使用：</span><span class="sxs-lookup"><span data-stu-id="526aa-442">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="526aa-443">上述標記會新增數個 `using` 語句。</span><span class="sxs-lookup"><span data-stu-id="526aa-443">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="526aa-444">更新 *Pages/Contacts/Index* 中的 [**編輯**] 和 [**刪除**] 連結，以便只針對具有適當許可權的使用者轉譯這些連結：</span><span class="sxs-lookup"><span data-stu-id="526aa-444">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="526aa-445">隱藏沒有變更資料許可權之使用者的連結，並不會保護應用程式的安全。</span><span class="sxs-lookup"><span data-stu-id="526aa-445">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="526aa-446">隱藏連結可只顯示有效的連結，讓應用程式更容易使用。</span><span class="sxs-lookup"><span data-stu-id="526aa-446">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="526aa-447">使用者可以攻擊產生的 Url，以叫用其未擁有之資料的編輯和刪除作業。</span><span class="sxs-lookup"><span data-stu-id="526aa-447">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="526aa-448">Razor頁面或控制器必須強制執行存取檢查以保護資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-448">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="526aa-449">更新詳細資料</span><span class="sxs-lookup"><span data-stu-id="526aa-449">Update Details</span></span>

<span data-ttu-id="526aa-450">更新 [詳細資料] 視圖，讓管理員可以核准或拒絕連絡人：</span><span class="sxs-lookup"><span data-stu-id="526aa-450">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="526aa-451">更新詳細資料頁面模型：</span><span class="sxs-lookup"><span data-stu-id="526aa-451">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="526aa-452">新增或移除角色的使用者</span><span class="sxs-lookup"><span data-stu-id="526aa-452">Add or remove a user to a role</span></span>

<span data-ttu-id="526aa-453">如需相關資訊，請參閱 [此問題](https://github.com/dotnet/AspNetCore.Docs/issues/8502) ：</span><span class="sxs-lookup"><span data-stu-id="526aa-453">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="526aa-454">正在移除使用者的許可權。</span><span class="sxs-lookup"><span data-stu-id="526aa-454">Removing privileges from a user.</span></span> <span data-ttu-id="526aa-455">例如，將聊天應用程式中的使用者靜音。</span><span class="sxs-lookup"><span data-stu-id="526aa-455">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="526aa-456">將許可權新增至使用者。</span><span class="sxs-lookup"><span data-stu-id="526aa-456">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="526aa-457">測試已完成的應用程式</span><span class="sxs-lookup"><span data-stu-id="526aa-457">Test the completed app</span></span>

<span data-ttu-id="526aa-458">如果您尚未設定植入使用者帳戶的密碼，請使用 [Secret Manager 工具](xref:security/app-secrets#secret-manager) 來設定密碼：</span><span class="sxs-lookup"><span data-stu-id="526aa-458">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="526aa-459">選擇強式密碼：使用八個或更多字元，以及至少一個大寫字元、數位和符號。</span><span class="sxs-lookup"><span data-stu-id="526aa-459">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="526aa-460">例如， `Passw0rd!` 符合強式密碼需求。</span><span class="sxs-lookup"><span data-stu-id="526aa-460">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="526aa-461">從專案的資料夾執行下列命令，其中 `<PW>` 是密碼：</span><span class="sxs-lookup"><span data-stu-id="526aa-461">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="526aa-462">卸載並更新資料庫</span><span class="sxs-lookup"><span data-stu-id="526aa-462">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="526aa-463">重新開機應用程式以植入資料庫。</span><span class="sxs-lookup"><span data-stu-id="526aa-463">Restart the app to seed the database.</span></span>

<span data-ttu-id="526aa-464">測試完成的應用程式的簡單方法，就是啟動三個不同的瀏覽器 (或 incognito/InPrivate 會話) 。</span><span class="sxs-lookup"><span data-stu-id="526aa-464">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="526aa-465">在一個瀏覽器中，註冊新的使用者 (例如 `test@example.com`) 。</span><span class="sxs-lookup"><span data-stu-id="526aa-465">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="526aa-466">使用不同的使用者登入每個瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="526aa-466">Sign in to each browser with a different user.</span></span> <span data-ttu-id="526aa-467">確認下列作業：</span><span class="sxs-lookup"><span data-stu-id="526aa-467">Verify the following operations:</span></span>

* <span data-ttu-id="526aa-468">註冊的使用者可以查看所有核准的連絡人資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-468">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="526aa-469">註冊的使用者可以編輯/刪除自己的資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-469">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="526aa-470">管理員可以核准/拒絕連絡人資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-470">Managers can approve/reject contact data.</span></span> <span data-ttu-id="526aa-471">此 `Details` 視圖會顯示 [ **核准** ] 和 [ **拒絕** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="526aa-471">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="526aa-472">系統管理員可以核准/拒絕和編輯/刪除所有資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-472">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="526aa-473">User</span><span class="sxs-lookup"><span data-stu-id="526aa-473">User</span></span>                | <span data-ttu-id="526aa-474">由應用程式植入</span><span class="sxs-lookup"><span data-stu-id="526aa-474">Seeded by the app</span></span> | <span data-ttu-id="526aa-475">選項</span><span class="sxs-lookup"><span data-stu-id="526aa-475">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="526aa-476">No</span><span class="sxs-lookup"><span data-stu-id="526aa-476">No</span></span>                | <span data-ttu-id="526aa-477">編輯/刪除自己的資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-477">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="526aa-478">Yes</span><span class="sxs-lookup"><span data-stu-id="526aa-478">Yes</span></span>               | <span data-ttu-id="526aa-479">核准/拒絕和編輯/刪除自己的資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-479">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="526aa-480">Yes</span><span class="sxs-lookup"><span data-stu-id="526aa-480">Yes</span></span>               | <span data-ttu-id="526aa-481">核准/拒絕和編輯/刪除所有資料。</span><span class="sxs-lookup"><span data-stu-id="526aa-481">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="526aa-482">在系統管理員的瀏覽器中建立連絡人。</span><span class="sxs-lookup"><span data-stu-id="526aa-482">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="526aa-483">從系統管理員連絡人複製 [刪除] 和 [編輯] 的 URL。</span><span class="sxs-lookup"><span data-stu-id="526aa-483">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="526aa-484">將這些連結貼到測試使用者的瀏覽器中，以確認測試使用者無法執行這些作業。</span><span class="sxs-lookup"><span data-stu-id="526aa-484">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="526aa-485">建立入門應用程式</span><span class="sxs-lookup"><span data-stu-id="526aa-485">Create the starter app</span></span>

* <span data-ttu-id="526aa-486">建立 Razor 名為 "ContactManager" 的頁面應用程式</span><span class="sxs-lookup"><span data-stu-id="526aa-486">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="526aa-487">使用 **個別的使用者帳戶** 建立應用程式。</span><span class="sxs-lookup"><span data-stu-id="526aa-487">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="526aa-488">將它命名為 "ContactManager"，讓命名空間符合範例中使用的命名空間。</span><span class="sxs-lookup"><span data-stu-id="526aa-488">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="526aa-489">`-uld` 指定 LocalDB 而不是 SQLite</span><span class="sxs-lookup"><span data-stu-id="526aa-489">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="526aa-490">新增 *模型/連絡人 .cs*：</span><span class="sxs-lookup"><span data-stu-id="526aa-490">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="526aa-491">Scaffold `Contact` 模型。</span><span class="sxs-lookup"><span data-stu-id="526aa-491">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="526aa-492">建立初始遷移並更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="526aa-492">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="526aa-493">更新 *Pages/_Layout cshtml* 檔案中的 **ContactManager** 錨點：</span><span class="sxs-lookup"><span data-stu-id="526aa-493">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="526aa-494">建立、編輯和刪除連絡人以測試應用程式</span><span class="sxs-lookup"><span data-stu-id="526aa-494">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="526aa-495">植入資料庫</span><span class="sxs-lookup"><span data-stu-id="526aa-495">Seed the database</span></span>

<span data-ttu-id="526aa-496">將 [>seeddata.cs](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) 類別加入至 [ *資料* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="526aa-496">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="526aa-497">呼叫 `SeedData.Initialize` 來源 `Main` ：</span><span class="sxs-lookup"><span data-stu-id="526aa-497">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="526aa-498">測試應用程式是否植入資料庫。</span><span class="sxs-lookup"><span data-stu-id="526aa-498">Test that the app seeded the database.</span></span> <span data-ttu-id="526aa-499">如果 contact DB 中有任何資料列，種子方法就不會執行。</span><span class="sxs-lookup"><span data-stu-id="526aa-499">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="526aa-500">其他資源</span><span class="sxs-lookup"><span data-stu-id="526aa-500">Additional resources</span></span>

* [<span data-ttu-id="526aa-501">在 Azure App Service 中建置 .NET Core 和 SQL Database Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="526aa-501">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="526aa-502">[ASP.NET 核心授權實驗室](https://github.com/blowdart/AspNetAuthorizationWorkshop)。</span><span class="sxs-lookup"><span data-stu-id="526aa-502">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="526aa-503">本教學課程會詳細說明本教學課程中所引進的安全性功能。</span><span class="sxs-lookup"><span data-stu-id="526aa-503">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="526aa-504">自訂以原則為基礎的授權</span><span class="sxs-lookup"><span data-stu-id="526aa-504">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
