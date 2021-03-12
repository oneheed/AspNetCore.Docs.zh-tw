---
title: 的自訂儲存體提供者 ASP.NET Core Identity
author: ardalis
description: 瞭解如何設定的自訂存放裝置提供者 ASP.NET Core Identity 。
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
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
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: f1c4366e4e4afa3dd86a816a649ad0a8b2ce817b
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588623"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a><span data-ttu-id="a9515-103">的自訂儲存體提供者 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="a9515-103">Custom storage providers for ASP.NET Core Identity</span></span>

<span data-ttu-id="a9515-104">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a9515-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a9515-105">ASP.NET Core Identity 是可延伸的系統，可讓您建立自訂的儲存體提供者，並將它連接到您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="a9515-105">ASP.NET Core Identity is an extensible system which enables you to create a custom storage provider and connect it to your app.</span></span> <span data-ttu-id="a9515-106">本主題說明如何建立的自訂儲存提供者 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="a9515-106">This topic describes how to create a customized storage provider for ASP.NET Core Identity.</span></span> <span data-ttu-id="a9515-107">其中涵蓋建立您自己的儲存提供者的重要概念，但不是逐步解說。</span><span class="sxs-lookup"><span data-stu-id="a9515-107">It covers the important concepts for creating your own storage provider, but isn't a step-by-step walkthrough.</span></span>

<span data-ttu-id="a9515-108">[從 GitHub 查看或下載範例](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample)。</span><span class="sxs-lookup"><span data-stu-id="a9515-108">[View or download sample from GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample).</span></span>

## <a name="introduction"></a><span data-ttu-id="a9515-109">簡介</span><span class="sxs-lookup"><span data-stu-id="a9515-109">Introduction</span></span>

<span data-ttu-id="a9515-110">根據預設， ASP.NET Core Identity 系統會使用 Entity Framework Core 將使用者資訊儲存在 SQL Server 資料庫中。</span><span class="sxs-lookup"><span data-stu-id="a9515-110">By default, the ASP.NET Core Identity system stores user information in a SQL Server database using Entity Framework Core.</span></span> <span data-ttu-id="a9515-111">對於許多應用程式而言，這種方法的運作效果很好。</span><span class="sxs-lookup"><span data-stu-id="a9515-111">For many apps, this approach works well.</span></span> <span data-ttu-id="a9515-112">不過，您可能會想要使用不同的持續性機制或資料結構描述。</span><span class="sxs-lookup"><span data-stu-id="a9515-112">However, you may prefer to use a different persistence mechanism or data schema.</span></span> <span data-ttu-id="a9515-113">例如：</span><span class="sxs-lookup"><span data-stu-id="a9515-113">For example:</span></span>

* <span data-ttu-id="a9515-114">您可以使用 [Azure 資料表儲存體](/azure/storage/) 或另一個資料存放區。</span><span class="sxs-lookup"><span data-stu-id="a9515-114">You use [Azure Table Storage](/azure/storage/) or another data store.</span></span>
* <span data-ttu-id="a9515-115">您的資料庫資料表具有不同的結構。</span><span class="sxs-lookup"><span data-stu-id="a9515-115">Your database tables have a different structure.</span></span> 
* <span data-ttu-id="a9515-116">您可能會想要使用不同的資料存取方法，例如 [Dapper](https://github.com/StackExchange/Dapper)。</span><span class="sxs-lookup"><span data-stu-id="a9515-116">You may wish to use a different data access approach, such as [Dapper](https://github.com/StackExchange/Dapper).</span></span> 

<span data-ttu-id="a9515-117">在上述每一種情況下，您可以為您的儲存機制撰寫自訂的提供者，並將該提供者插入您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="a9515-117">In each of these cases, you can write a customized provider for your storage mechanism and plug that provider into your app.</span></span>

<span data-ttu-id="a9515-118">ASP.NET Core Identity 包含在 Visual Studio 中具有 [個別使用者帳戶] 選項的專案範本中。</span><span class="sxs-lookup"><span data-stu-id="a9515-118">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="a9515-119">使用 .NET Core CLI 時，請新增 `-au Individual` ：</span><span class="sxs-lookup"><span data-stu-id="a9515-119">When using the .NET Core CLI, add `-au Individual`:</span></span>

```dotnetcli
dotnet new mvc -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a><span data-ttu-id="a9515-120">ASP.NET Core Identity架構</span><span class="sxs-lookup"><span data-stu-id="a9515-120">The ASP.NET Core Identity architecture</span></span>

<span data-ttu-id="a9515-121">ASP.NET Core Identity 包含稱為管理員和存放區的類別。</span><span class="sxs-lookup"><span data-stu-id="a9515-121">ASP.NET Core Identity consists of classes called managers and stores.</span></span> <span data-ttu-id="a9515-122">*管理員* 是應用程式開發人員用來執行作業的高階類別，例如建立 Identity 使用者。</span><span class="sxs-lookup"><span data-stu-id="a9515-122">*Managers* are high-level classes which an app developer uses to perform operations, such as creating an Identity user.</span></span> <span data-ttu-id="a9515-123">存放 *區* 是較低層級的類別，可指定如何保存實體（例如使用者和角色）。</span><span class="sxs-lookup"><span data-stu-id="a9515-123">*Stores* are lower-level classes that specify how entities, such as users and roles, are persisted.</span></span> <span data-ttu-id="a9515-124">存放區會遵循存放庫模式，並與持續性機制緊密結合。</span><span class="sxs-lookup"><span data-stu-id="a9515-124">Stores follow the repository pattern and are closely coupled with the persistence mechanism.</span></span> <span data-ttu-id="a9515-125">管理員會與存放區分離，這表示您可以取代持續性機制，而不需要變更您的應用程式程式碼 (設定) 除外。</span><span class="sxs-lookup"><span data-stu-id="a9515-125">Managers are decoupled from stores, which means you can replace the persistence mechanism without changing your application code (except for configuration).</span></span>

<span data-ttu-id="a9515-126">下圖顯示 web 應用程式與管理員的互動方式，以及存放區與資料存取層的互動。</span><span class="sxs-lookup"><span data-stu-id="a9515-126">The following diagram shows how a web app interacts with the managers, while stores interact with the data access layer.</span></span>

![ASP.NET Core 應用程式與管理員合作 (例如，' UserManager '、' RoleManager ') 。](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

<span data-ttu-id="a9515-129">若要建立自訂的儲存提供者，請建立資料來源、資料存取層，以及與此資料存取層互動的存放區類別 (上圖中的綠色和灰色方塊) 。</span><span class="sxs-lookup"><span data-stu-id="a9515-129">To create a custom storage provider, create the data source, the data access layer, and the store classes that interact with this data access layer (the green and grey boxes in the diagram above).</span></span> <span data-ttu-id="a9515-130">您不需要自訂管理者或應用程式程式碼， () 上述的藍色方塊來與其互動。</span><span class="sxs-lookup"><span data-stu-id="a9515-130">You don't need to customize the managers or your app code that interacts with them (the blue boxes above).</span></span>

<span data-ttu-id="a9515-131">當您建立的新實例 `UserManager` 或 `RoleManager` 提供使用者類別的型別，並以引數的形式傳遞存放區類別的實例時。</span><span class="sxs-lookup"><span data-stu-id="a9515-131">When creating a new instance of `UserManager` or `RoleManager` you provide the type of the user class and pass an instance of the store class as an argument.</span></span> <span data-ttu-id="a9515-132">這種方法可讓您將自訂類別插入 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="a9515-132">This approach enables you to plug your customized classes into ASP.NET Core.</span></span> 

<span data-ttu-id="a9515-133">將[應用程式重新設定為使用新的存放裝置提供者](#reconfigure-app-to-use-a-new-storage-provider)會示範如何具現化 `UserManager` 和 `RoleManager` 使用自訂存放區。</span><span class="sxs-lookup"><span data-stu-id="a9515-133">[Reconfigure app to use new storage provider](#reconfigure-app-to-use-a-new-storage-provider) shows how to instantiate `UserManager` and `RoleManager` with a customized store.</span></span>

## <a name="aspnet-core-identity-stores-data-types"></a><span data-ttu-id="a9515-134">ASP.NET Core Identity 儲存資料類型</span><span class="sxs-lookup"><span data-stu-id="a9515-134">ASP.NET Core Identity stores data types</span></span>

<span data-ttu-id="a9515-135">[ASP.NET Core Identity](https://github.com/aspnet/identity) 下列各節會詳細說明資料類型：</span><span class="sxs-lookup"><span data-stu-id="a9515-135">[ASP.NET Core Identity](https://github.com/aspnet/identity) data types are detailed in the following sections:</span></span>

### <a name="users"></a><span data-ttu-id="a9515-136">使用者</span><span class="sxs-lookup"><span data-stu-id="a9515-136">Users</span></span>

<span data-ttu-id="a9515-137">您網站的已註冊使用者。</span><span class="sxs-lookup"><span data-stu-id="a9515-137">Registered users of your web site.</span></span> <span data-ttu-id="a9515-138">[ Identity 使用者](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)類型可延伸或用作您自己的自訂類型的範例。</span><span class="sxs-lookup"><span data-stu-id="a9515-138">The [IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) type may be extended or used as an example for your own custom type.</span></span> <span data-ttu-id="a9515-139">您不需要繼承自特定型別，就能執行您自己的自訂身分識別儲存體解決方案。</span><span class="sxs-lookup"><span data-stu-id="a9515-139">You don't need to inherit from a particular type to implement your own custom identity storage solution.</span></span>

### <a name="user-claims"></a><span data-ttu-id="a9515-140">使用者宣告</span><span class="sxs-lookup"><span data-stu-id="a9515-140">User Claims</span></span>

<span data-ttu-id="a9515-141"> (或 [宣告](/dotnet/api/system.security.claims.claim) 的一組語句，) 代表使用者身分識別的使用者。</span><span class="sxs-lookup"><span data-stu-id="a9515-141">A set of statements (or [Claims](/dotnet/api/system.security.claims.claim)) about the user that represent the user's identity.</span></span> <span data-ttu-id="a9515-142">可以針對使用者的身分識別啟用較高的運算式，而不是透過角色來達成。</span><span class="sxs-lookup"><span data-stu-id="a9515-142">Can enable greater expression of the user's identity than can be achieved through roles.</span></span>

### <a name="user-logins"></a><span data-ttu-id="a9515-143">使用者登入</span><span class="sxs-lookup"><span data-stu-id="a9515-143">User Logins</span></span>

<span data-ttu-id="a9515-144">外部驗證提供者的相關資訊 (例如 Facebook 或 Microsoft 帳戶) 在使用者登入時使用。</span><span class="sxs-lookup"><span data-stu-id="a9515-144">Information about the external authentication provider (like Facebook or a Microsoft account) to use when logging in a user.</span></span> [<span data-ttu-id="a9515-145">範例</span><span class="sxs-lookup"><span data-stu-id="a9515-145">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a><span data-ttu-id="a9515-146">角色</span><span class="sxs-lookup"><span data-stu-id="a9515-146">Roles</span></span>

<span data-ttu-id="a9515-147">您網站的授權群組。</span><span class="sxs-lookup"><span data-stu-id="a9515-147">Authorization groups for your site.</span></span> <span data-ttu-id="a9515-148">包含角色識別碼和角色名稱 (例如 "Admin" 或 "Employee" ) 。</span><span class="sxs-lookup"><span data-stu-id="a9515-148">Includes the role Id and role name (like "Admin" or "Employee").</span></span> [<span data-ttu-id="a9515-149">範例</span><span class="sxs-lookup"><span data-stu-id="a9515-149">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a><span data-ttu-id="a9515-150">資料存取層</span><span class="sxs-lookup"><span data-stu-id="a9515-150">The data access layer</span></span>

<span data-ttu-id="a9515-151">本主題假設您已熟悉要使用的持續性機制，以及如何建立該機制的實體。</span><span class="sxs-lookup"><span data-stu-id="a9515-151">This topic assumes you are familiar with the persistence mechanism that you are going to use and how to create entities for that mechanism.</span></span> <span data-ttu-id="a9515-152">本主題不會提供有關如何建立存放庫或資料存取類別的詳細資料;在使用時，它會提供有關設計決策的一些建議 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="a9515-152">This topic doesn't provide details about how to create the repositories or data access classes; it provides some suggestions about design decisions when working with ASP.NET Core Identity.</span></span>

<span data-ttu-id="a9515-153">設計自訂存放區提供者的資料存取層時，您有很多自由。</span><span class="sxs-lookup"><span data-stu-id="a9515-153">You have a lot of freedom when designing the data access layer for a customized store provider.</span></span> <span data-ttu-id="a9515-154">您只需要為您想要在應用程式中使用的功能建立持續性機制。</span><span class="sxs-lookup"><span data-stu-id="a9515-154">You only need to create persistence mechanisms for features that you intend to use in your app.</span></span> <span data-ttu-id="a9515-155">例如，如果您未在應用程式中使用角色，則不需要建立角色或使用者角色關聯的儲存體。</span><span class="sxs-lookup"><span data-stu-id="a9515-155">For example, if you are not using roles in your app, you don't need to create storage for roles or user role associations.</span></span> <span data-ttu-id="a9515-156">您的技術和現有的基礎結構可能需要與的預設執行非常不同的結構 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="a9515-156">Your technology and existing infrastructure may require a structure that's very different from the default implementation of ASP.NET Core Identity.</span></span> <span data-ttu-id="a9515-157">在您的資料存取層中，您會提供邏輯來處理儲存體執行的結構。</span><span class="sxs-lookup"><span data-stu-id="a9515-157">In your data access layer, you provide the logic to work with the structure of your storage implementation.</span></span>

<span data-ttu-id="a9515-158">資料存取層提供將資料儲存 ASP.NET Core Identity 至資料來源的邏輯。</span><span class="sxs-lookup"><span data-stu-id="a9515-158">The data access layer provides the logic to save the data from ASP.NET Core Identity to a data source.</span></span> <span data-ttu-id="a9515-159">您自訂的儲存提供者的資料存取層可能包含下列類別來儲存使用者和角色資訊。</span><span class="sxs-lookup"><span data-stu-id="a9515-159">The data access layer for your customized storage provider might include the following classes to store user and role information.</span></span>

### <a name="context-class"></a><span data-ttu-id="a9515-160">Context 類別</span><span class="sxs-lookup"><span data-stu-id="a9515-160">Context class</span></span>

<span data-ttu-id="a9515-161">封裝資訊以連接到您的持續性機制，並執行查詢。</span><span class="sxs-lookup"><span data-stu-id="a9515-161">Encapsulates the information to connect to your persistence mechanism and execute queries.</span></span> <span data-ttu-id="a9515-162">有幾個資料類別需要這個類別的實例，通常是透過相依性插入來提供。</span><span class="sxs-lookup"><span data-stu-id="a9515-162">Several data classes require an instance of this class, typically provided through dependency injection.</span></span> <span data-ttu-id="a9515-163">[範例](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1)。</span><span class="sxs-lookup"><span data-stu-id="a9515-163">[Example](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1).</span></span>

### <a name="user-storage"></a><span data-ttu-id="a9515-164">使用者儲存體</span><span class="sxs-lookup"><span data-stu-id="a9515-164">User Storage</span></span>

<span data-ttu-id="a9515-165">儲存和取出使用者資訊 (例如使用者名稱和密碼雜湊) 。</span><span class="sxs-lookup"><span data-stu-id="a9515-165">Stores and retrieves user information (such as user name and password hash).</span></span> [<span data-ttu-id="a9515-166">範例</span><span class="sxs-lookup"><span data-stu-id="a9515-166">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a><span data-ttu-id="a9515-167">角色儲存體</span><span class="sxs-lookup"><span data-stu-id="a9515-167">Role Storage</span></span>

<span data-ttu-id="a9515-168">儲存和取出角色資訊 (例如角色名稱) 。</span><span class="sxs-lookup"><span data-stu-id="a9515-168">Stores and retrieves role information (such as the role name).</span></span> [<span data-ttu-id="a9515-169">範例</span><span class="sxs-lookup"><span data-stu-id="a9515-169">Example</span></span>](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a><span data-ttu-id="a9515-170">UserClaims 儲存體</span><span class="sxs-lookup"><span data-stu-id="a9515-170">UserClaims Storage</span></span>

<span data-ttu-id="a9515-171">儲存並取出使用者宣告資訊， (例如宣告類型和值) 。</span><span class="sxs-lookup"><span data-stu-id="a9515-171">Stores and retrieves user claim information (such as the claim type and value).</span></span> [<span data-ttu-id="a9515-172">範例</span><span class="sxs-lookup"><span data-stu-id="a9515-172">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a><span data-ttu-id="a9515-173">UserLogins 儲存體</span><span class="sxs-lookup"><span data-stu-id="a9515-173">UserLogins Storage</span></span>

<span data-ttu-id="a9515-174">儲存和取出使用者登入資訊 (例如外部驗證提供者) 。</span><span class="sxs-lookup"><span data-stu-id="a9515-174">Stores and retrieves user login information (such as an external authentication provider).</span></span> [<span data-ttu-id="a9515-175">範例</span><span class="sxs-lookup"><span data-stu-id="a9515-175">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a><span data-ttu-id="a9515-176">UserRole 儲存體</span><span class="sxs-lookup"><span data-stu-id="a9515-176">UserRole Storage</span></span>

<span data-ttu-id="a9515-177">儲存並取出指派給哪些使用者的角色。</span><span class="sxs-lookup"><span data-stu-id="a9515-177">Stores and retrieves which roles are assigned to which users.</span></span> [<span data-ttu-id="a9515-178">範例</span><span class="sxs-lookup"><span data-stu-id="a9515-178">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

<span data-ttu-id="a9515-179">**秘訣：** 只執行您想要在應用程式中使用的類別。</span><span class="sxs-lookup"><span data-stu-id="a9515-179">**TIP:** Only implement the classes you intend to use in your app.</span></span>

<span data-ttu-id="a9515-180">在資料存取類別中，提供程式碼來執行持續性機制的資料作業。</span><span class="sxs-lookup"><span data-stu-id="a9515-180">In the data access classes, provide code to perform data operations for your persistence mechanism.</span></span> <span data-ttu-id="a9515-181">例如，在自訂提供者內，您可能會有下列程式碼，在 *store* 類別中建立新的使用者：</span><span class="sxs-lookup"><span data-stu-id="a9515-181">For example, within a custom provider, you might have the following code to create a new user in the *store* class:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

<span data-ttu-id="a9515-182">建立使用者的執行邏輯位於 `_usersTable.CreateAsync` 方法中，如下所示。</span><span class="sxs-lookup"><span data-stu-id="a9515-182">The implementation logic for creating the user is in the `_usersTable.CreateAsync` method, shown below.</span></span>

## <a name="customize-the-user-class"></a><span data-ttu-id="a9515-183">自訂使用者類別</span><span class="sxs-lookup"><span data-stu-id="a9515-183">Customize the user class</span></span>

<span data-ttu-id="a9515-184">當您在執行儲存提供者時，請建立相當於[ Identity 使用者類別](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)的使用者類別。</span><span class="sxs-lookup"><span data-stu-id="a9515-184">When implementing a storage provider, create a user class which is equivalent to the [IdentityUser class](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser).</span></span>

<span data-ttu-id="a9515-185">您的使用者類別至少必須包含 `Id` 和 `UserName` 屬性。</span><span class="sxs-lookup"><span data-stu-id="a9515-185">At a minimum, your user class must include an `Id` and a `UserName` property.</span></span>

<span data-ttu-id="a9515-186">`IdentityUser`類別會定義在 `UserManager` 執行要求的作業時，所呼叫的屬性。</span><span class="sxs-lookup"><span data-stu-id="a9515-186">The `IdentityUser` class defines the properties that the `UserManager` calls when performing requested operations.</span></span> <span data-ttu-id="a9515-187">屬性的預設類型 `Id` 為字串，但您可以繼承自 `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` 並指定不同的類型。</span><span class="sxs-lookup"><span data-stu-id="a9515-187">The default type of the `Id` property is a string, but you can inherit from `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` and specify a different type.</span></span> <span data-ttu-id="a9515-188">架構預期儲存體執行會處理資料類型轉換。</span><span class="sxs-lookup"><span data-stu-id="a9515-188">The framework expects the storage implementation to handle data type conversions.</span></span>

## <a name="customize-the-user-store"></a><span data-ttu-id="a9515-189">自訂使用者存放區</span><span class="sxs-lookup"><span data-stu-id="a9515-189">Customize the user store</span></span>

<span data-ttu-id="a9515-190">建立 `UserStore` 類別，以提供使用者上所有資料作業的方法。</span><span class="sxs-lookup"><span data-stu-id="a9515-190">Create a `UserStore` class that provides the methods for all data operations on the user.</span></span> <span data-ttu-id="a9515-191">這個類別相當於[UserStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1)類別。</span><span class="sxs-lookup"><span data-stu-id="a9515-191">This class is equivalent to the [UserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) class.</span></span> <span data-ttu-id="a9515-192">在您的 `UserStore` 類別中，您 `IUserStore<TUser>` 必須執行和選用的介面。</span><span class="sxs-lookup"><span data-stu-id="a9515-192">In your `UserStore` class, implement `IUserStore<TUser>` and the optional interfaces required.</span></span> <span data-ttu-id="a9515-193">您可以根據應用程式中提供的功能，選取要執行的選用介面。</span><span class="sxs-lookup"><span data-stu-id="a9515-193">You select which optional interfaces to implement based on the functionality provided in your app.</span></span>

### <a name="optional-interfaces"></a><span data-ttu-id="a9515-194">選擇性介面</span><span class="sxs-lookup"><span data-stu-id="a9515-194">Optional interfaces</span></span>

* [<span data-ttu-id="a9515-195">IUserRoleStore</span><span class="sxs-lookup"><span data-stu-id="a9515-195">IUserRoleStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)
* [<span data-ttu-id="a9515-196">IUserClaimStore</span><span class="sxs-lookup"><span data-stu-id="a9515-196">IUserClaimStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)
* [<span data-ttu-id="a9515-197">IUserPasswordStore</span><span class="sxs-lookup"><span data-stu-id="a9515-197">IUserPasswordStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)
* [<span data-ttu-id="a9515-198">IUserSecurityStampStore</span><span class="sxs-lookup"><span data-stu-id="a9515-198">IUserSecurityStampStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)
* [<span data-ttu-id="a9515-199">IUserEmailStore</span><span class="sxs-lookup"><span data-stu-id="a9515-199">IUserEmailStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)
* [<span data-ttu-id="a9515-200">IUserPhoneNumberStore</span><span class="sxs-lookup"><span data-stu-id="a9515-200">IUserPhoneNumberStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)
* [<span data-ttu-id="a9515-201">IQueryableUserStore</span><span class="sxs-lookup"><span data-stu-id="a9515-201">IQueryableUserStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)
* [<span data-ttu-id="a9515-202">IUserLoginStore</span><span class="sxs-lookup"><span data-stu-id="a9515-202">IUserLoginStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)
* [<span data-ttu-id="a9515-203">IUserTwoFactorStore</span><span class="sxs-lookup"><span data-stu-id="a9515-203">IUserTwoFactorStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)
* [<span data-ttu-id="a9515-204">IUserLockoutStore</span><span class="sxs-lookup"><span data-stu-id="a9515-204">IUserLockoutStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)

<span data-ttu-id="a9515-205">選用的介面會繼承自 `IUserStore<TUser>` 。</span><span class="sxs-lookup"><span data-stu-id="a9515-205">The optional interfaces inherit from `IUserStore<TUser>`.</span></span> <span data-ttu-id="a9515-206">您可以在 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs)中看到部分實作為範例使用者存放區。</span><span class="sxs-lookup"><span data-stu-id="a9515-206">You can see a partially implemented sample user store in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs).</span></span>

<span data-ttu-id="a9515-207">在 `UserStore` 類別內，您可以使用您建立來執行作業的資料存取類別。</span><span class="sxs-lookup"><span data-stu-id="a9515-207">Within the `UserStore` class, you use the data access classes that you created to perform operations.</span></span> <span data-ttu-id="a9515-208">這些會使用相依性插入來傳遞。</span><span class="sxs-lookup"><span data-stu-id="a9515-208">These are passed in using dependency injection.</span></span> <span data-ttu-id="a9515-209">例如，在具有 Dapper 執行的 SQL Server 中， `UserStore` 類別具有使用的 `CreateAsync` 實例 `DapperUsersTable` 插入新記錄的方法：</span><span class="sxs-lookup"><span data-stu-id="a9515-209">For example, in the SQL Server with Dapper implementation, the `UserStore` class has the `CreateAsync` method which uses an instance of `DapperUsersTable` to insert a new record:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a><span data-ttu-id="a9515-210">自訂使用者存放區時要執行的介面</span><span class="sxs-lookup"><span data-stu-id="a9515-210">Interfaces to implement when customizing user store</span></span>

* <span data-ttu-id="a9515-211">**IUserStore**</span><span class="sxs-lookup"><span data-stu-id="a9515-211">**IUserStore**</span></span>  
 <span data-ttu-id="a9515-212">[IUserStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1)介面是您必須在使用者存放區中執行的唯一介面。</span><span class="sxs-lookup"><span data-stu-id="a9515-212">The [IUserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) interface is the only interface you must implement in the user store.</span></span> <span data-ttu-id="a9515-213">它會定義用來建立、更新、刪除和取出使用者的方法。</span><span class="sxs-lookup"><span data-stu-id="a9515-213">It defines methods for creating, updating, deleting, and retrieving users.</span></span>
* <span data-ttu-id="a9515-214">**IUserClaimStore**</span><span class="sxs-lookup"><span data-stu-id="a9515-214">**IUserClaimStore**</span></span>  
 <span data-ttu-id="a9515-215">[IUserClaimStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)介面會定義您所執行的方法，以啟用使用者宣告。</span><span class="sxs-lookup"><span data-stu-id="a9515-215">The [IUserClaimStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) interface defines the methods you implement to enable user claims.</span></span> <span data-ttu-id="a9515-216">它包含加入、移除和取出使用者宣告的方法。</span><span class="sxs-lookup"><span data-stu-id="a9515-216">It contains methods for adding, removing and retrieving user claims.</span></span>
* <span data-ttu-id="a9515-217">**IUserLoginStore**</span><span class="sxs-lookup"><span data-stu-id="a9515-217">**IUserLoginStore**</span></span>  
 <span data-ttu-id="a9515-218">[IUserLoginStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)會定義您為了啟用外部驗證提供者所執行的方法。</span><span class="sxs-lookup"><span data-stu-id="a9515-218">The [IUserLoginStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) defines the methods you implement to enable external authentication providers.</span></span> <span data-ttu-id="a9515-219">它包含加入、移除和取出使用者登入的方法，以及根據登入資訊來取得使用者的方法。</span><span class="sxs-lookup"><span data-stu-id="a9515-219">It contains methods for adding, removing and retrieving user logins, and a method for retrieving a user based on the login information.</span></span>
* <span data-ttu-id="a9515-220">**IUserRoleStore**</span><span class="sxs-lookup"><span data-stu-id="a9515-220">**IUserRoleStore**</span></span>  
 <span data-ttu-id="a9515-221">[IUserRoleStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)介面會定義您所執行的方法，以將使用者對應至角色。</span><span class="sxs-lookup"><span data-stu-id="a9515-221">The [IUserRoleStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) interface defines the methods you implement to map a user to a role.</span></span> <span data-ttu-id="a9515-222">它包含加入、移除和取出使用者角色的方法，以及檢查是否已將使用者指派給角色的方法。</span><span class="sxs-lookup"><span data-stu-id="a9515-222">It contains methods to add, remove, and retrieve a user's roles, and a method to check if a user is assigned to a role.</span></span>
* <span data-ttu-id="a9515-223">**IUserPasswordStore**</span><span class="sxs-lookup"><span data-stu-id="a9515-223">**IUserPasswordStore**</span></span>  
 <span data-ttu-id="a9515-224">[IUserPasswordStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)介面會定義您所執行的方法，以保存已雜湊的密碼。</span><span class="sxs-lookup"><span data-stu-id="a9515-224">The [IUserPasswordStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) interface defines the methods you implement to persist hashed passwords.</span></span> <span data-ttu-id="a9515-225">它包含取得和設定雜湊密碼的方法，以及指出使用者是否已設定密碼的方法。</span><span class="sxs-lookup"><span data-stu-id="a9515-225">It contains methods for getting and setting the hashed password, and a method that indicates whether the user has set a password.</span></span>
* <span data-ttu-id="a9515-226">**IUserSecurityStampStore**</span><span class="sxs-lookup"><span data-stu-id="a9515-226">**IUserSecurityStampStore**</span></span>  
 <span data-ttu-id="a9515-227">[IUserSecurityStampStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)介面會定義您所執行的方法，以使用安全性戳記來指出使用者的帳戶資訊是否已變更。</span><span class="sxs-lookup"><span data-stu-id="a9515-227">The [IUserSecurityStampStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) interface defines the methods you implement to use a security stamp for indicating whether the user's account information has changed.</span></span> <span data-ttu-id="a9515-228">當使用者變更密碼或新增或移除登入時，就會更新此戳記。</span><span class="sxs-lookup"><span data-stu-id="a9515-228">This stamp is updated when a user changes the password, or adds or removes logins.</span></span> <span data-ttu-id="a9515-229">它包含取得和設定安全性戳記的方法。</span><span class="sxs-lookup"><span data-stu-id="a9515-229">It contains methods for getting and setting the security stamp.</span></span>
* <span data-ttu-id="a9515-230">**IUserTwoFactorStore**</span><span class="sxs-lookup"><span data-stu-id="a9515-230">**IUserTwoFactorStore**</span></span>  
 <span data-ttu-id="a9515-231">[IUserTwoFactorStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)介面會定義您所執行的方法，以支援雙因素驗證。</span><span class="sxs-lookup"><span data-stu-id="a9515-231">The [IUserTwoFactorStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) interface defines the methods you implement to support two factor authentication.</span></span> <span data-ttu-id="a9515-232">它包含取得和設定是否為使用者啟用雙因素驗證的方法。</span><span class="sxs-lookup"><span data-stu-id="a9515-232">It contains methods for getting and setting whether two factor authentication is enabled for a user.</span></span>
* <span data-ttu-id="a9515-233">**IUserPhoneNumberStore**</span><span class="sxs-lookup"><span data-stu-id="a9515-233">**IUserPhoneNumberStore**</span></span>  
 <span data-ttu-id="a9515-234">[IUserPhoneNumberStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)介面會定義您所執行的方法，以儲存使用者的電話號碼。</span><span class="sxs-lookup"><span data-stu-id="a9515-234">The [IUserPhoneNumberStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) interface defines the methods you implement to store user phone numbers.</span></span> <span data-ttu-id="a9515-235">它包含取得和設定電話號碼的方法，以及電話號碼是否已確認。</span><span class="sxs-lookup"><span data-stu-id="a9515-235">It contains methods for getting and setting the phone number and whether the phone number is confirmed.</span></span>
* <span data-ttu-id="a9515-236">**IUserEmailStore**</span><span class="sxs-lookup"><span data-stu-id="a9515-236">**IUserEmailStore**</span></span>  
 <span data-ttu-id="a9515-237">[IUserEmailStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)介面會定義您所執行的方法，以儲存使用者的電子郵件地址。</span><span class="sxs-lookup"><span data-stu-id="a9515-237">The [IUserEmailStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) interface defines the methods you implement to store user email addresses.</span></span> <span data-ttu-id="a9515-238">它包含取得和設定電子郵件地址的方法，以及是否確認電子郵件。</span><span class="sxs-lookup"><span data-stu-id="a9515-238">It contains methods for getting and setting the email address and whether the email is confirmed.</span></span>
* <span data-ttu-id="a9515-239">**IUserLockoutStore**</span><span class="sxs-lookup"><span data-stu-id="a9515-239">**IUserLockoutStore**</span></span>  
 <span data-ttu-id="a9515-240">[IUserLockoutStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)介面會定義您所執行的方法，以儲存有關鎖定帳戶的資訊。</span><span class="sxs-lookup"><span data-stu-id="a9515-240">The [IUserLockoutStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) interface defines the methods you implement to store information about locking an account.</span></span> <span data-ttu-id="a9515-241">它包含追蹤失敗的存取嘗試和鎖定的方法。</span><span class="sxs-lookup"><span data-stu-id="a9515-241">It contains methods for tracking failed access attempts and lockouts.</span></span>
* <span data-ttu-id="a9515-242">**IQueryableUserStore**</span><span class="sxs-lookup"><span data-stu-id="a9515-242">**IQueryableUserStore**</span></span>  
 <span data-ttu-id="a9515-243">[IQueryableUserStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)介面會定義您所要執行的成員，以提供可查詢的使用者存放區。</span><span class="sxs-lookup"><span data-stu-id="a9515-243">The [IQueryableUserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) interface defines the members you implement to provide a queryable user store.</span></span>

<span data-ttu-id="a9515-244">您只會執行應用程式中所需的介面。</span><span class="sxs-lookup"><span data-stu-id="a9515-244">You implement only the interfaces that are needed in your app.</span></span> <span data-ttu-id="a9515-245">例如：</span><span class="sxs-lookup"><span data-stu-id="a9515-245">For example:</span></span>

```csharp
public class UserStore : IUserStore<IdentityUser>,
                         IUserClaimStore<IdentityUser>,
                         IUserLoginStore<IdentityUser>,
                         IUserRoleStore<IdentityUser>,
                         IUserPasswordStore<IdentityUser>,
                         IUserSecurityStampStore<IdentityUser>
{
    // interface implementations not shown
}
```

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a><span data-ttu-id="a9515-246">IdentityUserClaim、 Identity UserLogin 和 Identity UserRole</span><span class="sxs-lookup"><span data-stu-id="a9515-246">IdentityUserClaim, IdentityUserLogin, and IdentityUserRole</span></span>

<span data-ttu-id="a9515-247">`Microsoft.AspNet.Identity.EntityFramework`命名空間包含[ Identity UserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1)、 [ Identity UserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)和[ Identity UserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1)類別的實作為。</span><span class="sxs-lookup"><span data-stu-id="a9515-247">The `Microsoft.AspNet.Identity.EntityFramework` namespace contains implementations of the [IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [IdentityUserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin), and [IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) classes.</span></span> <span data-ttu-id="a9515-248">如果您使用這些功能，您可能會想要建立自己的類別版本，並定義應用程式的屬性。</span><span class="sxs-lookup"><span data-stu-id="a9515-248">If you are using these features, you may want to create your own versions of these classes and define the properties for your app.</span></span> <span data-ttu-id="a9515-249">不過，在執行基本作業 (例如新增或移除使用者的宣告) 時，有時不會將這些實體載入記憶體中會更有效率。</span><span class="sxs-lookup"><span data-stu-id="a9515-249">However, sometimes it's more efficient to not load these entities into memory when performing basic operations (such as adding or removing a user's claim).</span></span> <span data-ttu-id="a9515-250">相反地，後端存放區類別可以直接在資料來源上執行這些作業。</span><span class="sxs-lookup"><span data-stu-id="a9515-250">Instead, the backend store classes can execute these operations directly on the data source.</span></span> <span data-ttu-id="a9515-251">例如， `UserStore.GetClaimsAsync` 方法可以呼叫 `userClaimTable.FindByUserId(user.Id)` 方法來直接對該資料表執行查詢，並傳回宣告清單。</span><span class="sxs-lookup"><span data-stu-id="a9515-251">For example, the `UserStore.GetClaimsAsync` method can call the `userClaimTable.FindByUserId(user.Id)` method to execute a query on that table directly and return a list of claims.</span></span>

## <a name="customize-the-role-class"></a><span data-ttu-id="a9515-252">自訂角色類別</span><span class="sxs-lookup"><span data-stu-id="a9515-252">Customize the role class</span></span>

<span data-ttu-id="a9515-253">在執行角色儲存提供者時，您可以建立自訂角色類型。</span><span class="sxs-lookup"><span data-stu-id="a9515-253">When implementing a role storage provider, you can create a custom role type.</span></span> <span data-ttu-id="a9515-254">它不需要執行特定的介面，但它必須有 `Id` ，而且通常會有 `Name` 屬性。</span><span class="sxs-lookup"><span data-stu-id="a9515-254">It need not implement a particular interface, but it must have an `Id` and typically it will have a `Name` property.</span></span>

<span data-ttu-id="a9515-255">以下是範例角色類別：</span><span class="sxs-lookup"><span data-stu-id="a9515-255">The following is an example role class:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a><span data-ttu-id="a9515-256">自訂角色存放區</span><span class="sxs-lookup"><span data-stu-id="a9515-256">Customize the role store</span></span>

<span data-ttu-id="a9515-257">您可以建立 `RoleStore` 類別，以提供角色上所有資料作業的方法。</span><span class="sxs-lookup"><span data-stu-id="a9515-257">You can create a `RoleStore` class that provides the methods for all data operations on roles.</span></span> <span data-ttu-id="a9515-258">這個類別相當於[RoleStore &lt; TRole &gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)類別。</span><span class="sxs-lookup"><span data-stu-id="a9515-258">This class is equivalent to the [RoleStore&lt;TRole&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) class.</span></span> <span data-ttu-id="a9515-259">在 `RoleStore` 類別中，您會執行 `IRoleStore<TRole>` 和（選擇性） `IQueryableRoleStore<TRole>` 介面。</span><span class="sxs-lookup"><span data-stu-id="a9515-259">In the `RoleStore` class, you implement the `IRoleStore<TRole>` and optionally the `IQueryableRoleStore<TRole>` interface.</span></span>

* <span data-ttu-id="a9515-260">**IRoleStore &lt; TRole&gt;**</span><span class="sxs-lookup"><span data-stu-id="a9515-260">**IRoleStore&lt;TRole&gt;**</span></span>  
 <span data-ttu-id="a9515-261">[IRoleStore &lt; TRole &gt; ](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1)介面會定義要在角色存放區類別中執行的方法。</span><span class="sxs-lookup"><span data-stu-id="a9515-261">The [IRoleStore&lt;TRole&gt;](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) interface defines the methods to implement in the role store class.</span></span> <span data-ttu-id="a9515-262">它包含建立、更新、刪除及抓取角色的方法。</span><span class="sxs-lookup"><span data-stu-id="a9515-262">It contains methods for creating, updating, deleting, and retrieving roles.</span></span>
* <span data-ttu-id="a9515-263">**RoleStore &lt; TRole&gt;**</span><span class="sxs-lookup"><span data-stu-id="a9515-263">**RoleStore&lt;TRole&gt;**</span></span>  
 <span data-ttu-id="a9515-264">若要自訂 `RoleStore` ，請建立可執行 `IRoleStore<TRole>` 介面的類別。</span><span class="sxs-lookup"><span data-stu-id="a9515-264">To customize `RoleStore`, create a class that implements the `IRoleStore<TRole>` interface.</span></span> 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a><span data-ttu-id="a9515-265">重新設定應用程式以使用新的存放裝置提供者</span><span class="sxs-lookup"><span data-stu-id="a9515-265">Reconfigure app to use a new storage provider</span></span>

<span data-ttu-id="a9515-266">一旦您已完成儲存體提供者，您就可以設定應用程式來使用它。</span><span class="sxs-lookup"><span data-stu-id="a9515-266">Once you have implemented a storage provider, you configure your app to use it.</span></span> <span data-ttu-id="a9515-267">如果您的應用程式使用預設提供者，請將它取代為您的自訂提供者。</span><span class="sxs-lookup"><span data-stu-id="a9515-267">If your app used the default provider, replace it with your custom provider.</span></span>

1. <span data-ttu-id="a9515-268">移除 `Microsoft.AspNetCore.EntityFramework.Identity` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="a9515-268">Remove the `Microsoft.AspNetCore.EntityFramework.Identity` NuGet package.</span></span>
1. <span data-ttu-id="a9515-269">如果儲存提供者位於個別的專案或封裝中，請在其中加入參考。</span><span class="sxs-lookup"><span data-stu-id="a9515-269">If the storage provider resides in a separate project or package, add a reference to it.</span></span>
1. <span data-ttu-id="a9515-270">使用 `Microsoft.AspNetCore.EntityFramework.Identity` 您的儲存提供者命名空間的 using 語句來取代所有參考。</span><span class="sxs-lookup"><span data-stu-id="a9515-270">Replace all references to `Microsoft.AspNetCore.EntityFramework.Identity` with a using statement for the namespace of your storage provider.</span></span>
1. <span data-ttu-id="a9515-271">在 `ConfigureServices` 方法中，將 `AddIdentity` 方法變更為使用您的自訂類型。</span><span class="sxs-lookup"><span data-stu-id="a9515-271">In the `ConfigureServices` method, change the `AddIdentity` method to use your custom types.</span></span> <span data-ttu-id="a9515-272">您可以針對此用途建立自己的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="a9515-272">You can create your own extension methods for this purpose.</span></span> <span data-ttu-id="a9515-273">如需範例，請參閱[ Identity ServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) 。</span><span class="sxs-lookup"><span data-stu-id="a9515-273">See [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) for an example.</span></span>
1. <span data-ttu-id="a9515-274">如果您使用的是角色，請更新 `RoleManager` 以使用您的 `RoleStore` 類別。</span><span class="sxs-lookup"><span data-stu-id="a9515-274">If you are using Roles, update the `RoleManager` to use your `RoleStore` class.</span></span>
1. <span data-ttu-id="a9515-275">將連接字串與認證更新至您的應用程式設定。</span><span class="sxs-lookup"><span data-stu-id="a9515-275">Update the connection string and credentials to your app's configuration.</span></span>

<span data-ttu-id="a9515-276">範例：</span><span class="sxs-lookup"><span data-stu-id="a9515-276">Example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add identity types
    services.AddIdentity<ApplicationUser, ApplicationRole>()
        .AddDefaultTokenProviders();

    // Identity Services
    services.AddTransient<IUserStore<ApplicationUser>, CustomUserStore>();
    services.AddTransient<IRoleStore<ApplicationRole>, CustomRoleStore>();
    string connectionString = Configuration.GetConnectionString("DefaultConnection");
    services.AddTransient<SqlConnection>(e => new SqlConnection(connectionString));
    services.AddTransient<DapperUsersTable>();

    // additional configuration
}
```

## <a name="references"></a><span data-ttu-id="a9515-277">參考資料</span><span class="sxs-lookup"><span data-stu-id="a9515-277">References</span></span>

* [<span data-ttu-id="a9515-278">ASP.NET 4.x 的自訂儲存體提供者 Identity</span><span class="sxs-lookup"><span data-stu-id="a9515-278">Custom Storage Providers for ASP.NET 4.x Identity</span></span>](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
* <span data-ttu-id="a9515-279">[ASP.NET Core Identity](https://github.com/dotnet/AspNetCore/tree/main/src/Identity)：此存放庫包含已維護的商店提供者連結。</span><span class="sxs-lookup"><span data-stu-id="a9515-279">[ASP.NET Core Identity](https://github.com/dotnet/AspNetCore/tree/main/src/Identity): This repository includes links to community maintained store providers.</span></span>
