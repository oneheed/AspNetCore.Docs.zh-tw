---
title: 從 ASP.NET 成員資格驗證遷移至 ASP.NET Core 2。0 Identity
author: isaac2004
description: 瞭解如何使用成員資格驗證將現有的 ASP.NET 應用程式遷移至 ASP.NET Core 2.0 Identity 。
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
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
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: a9ec02381b156a6599042d8e504a476036246302
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865563"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-no-locidentity"></a><span data-ttu-id="b0c4b-103">從 ASP.NET 成員資格驗證遷移至 ASP.NET Core 2。0 Identity</span><span class="sxs-lookup"><span data-stu-id="b0c4b-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="b0c4b-104">作者：[Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="b0c4b-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="b0c4b-105">本文示範如何使用成員資格驗證將 ASP.NET 應用程式的資料庫架構遷移至 ASP.NET Core 2.0 Identity 。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="b0c4b-106">本檔提供將 ASP.NET 成員資格型應用程式的資料庫架構遷移到用於之資料庫架構所需的步驟 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="b0c4b-107">如需從 ASP.NET 成員資格式驗證遷移至 ASP.NET 的詳細資訊 Identity ，請參閱將[現有的應用程式從 SQL Identity 成員資格遷移至 ASP.NET ](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity)。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="b0c4b-108">如需的詳細資訊 ASP.NET Core Identity ，請參閱 [ Identity ASP.NET Core 的簡介](xref:security/authentication/identity)。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="b0c4b-109">成員資格架構的評論</span><span class="sxs-lookup"><span data-stu-id="b0c4b-109">Review of Membership schema</span></span>

<span data-ttu-id="b0c4b-110">在 ASP.NET 2.0 之前，開發人員會負責為其應用程式建立整個驗證和授權程式。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="b0c4b-111">使用 ASP.NET 2.0，引進了成員資格，提供在 ASP.NET apps 中處理安全性的未定案解決方案。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="b0c4b-112">開發人員現在可以使用命令，在 SQL Server 資料庫中啟動架構 <https://docs.microsoft.com/previous-versions/ms229862(v=vs.140)> 。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-112">Developers were now able to bootstrap a schema into a SQL Server database with the <https://docs.microsoft.com/previous-versions/ms229862(v=vs.140)> command.</span></span> <span data-ttu-id="b0c4b-113">執行此命令之後，會在資料庫中建立下列資料表。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-113">After running this command, the following tables were created in the database.</span></span>

  ![成員資格資料表](identity/_static/membership-tables.png)

<span data-ttu-id="b0c4b-115">若要將現有的應用程式遷移至 ASP.NET Core 2.0 Identity ，必須將這些資料表中的資料移轉到新架構所使用的資料表 Identity 。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="no-locaspnet-core-identity-20-schema"></a><span data-ttu-id="b0c4b-116">ASP.NET Core Identity 2.0 架構</span><span class="sxs-lookup"><span data-stu-id="b0c4b-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="b0c4b-117">ASP.NET Core 2.0 遵循 [Identity](/aspnet/identity/index) ASP.NET 4.5 中所引進的原則。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="b0c4b-118">雖然這項原則是共用的，但架構之間的實施也不同，即使是 ASP.NET Core 版本 (請參閱 [遷移驗證和 Identity ASP.NET Core 2.0](xref:migration/1x-to-2x/index)) 。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="b0c4b-119">若要查看 ASP.NET Core 2.0 的架構，最快的方式 Identity 是建立新的 ASP.NET Core 2.0 應用程式。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="b0c4b-120">遵循 Visual Studio 2017 中的下列步驟：</span><span class="sxs-lookup"><span data-stu-id="b0c4b-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="b0c4b-121">選取 [File] \(檔案\) >  [New] \(新增\) >  [Project] \(專案\)。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-121">Select **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="b0c4b-122">建立名為*Core Identity Sample*的新**ASP.NET Core Web 應用程式**專案。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample*.</span></span>
1. <span data-ttu-id="b0c4b-123">在下拉式清單中選取 **ASP.NET Core 2.0** ，然後選取 [ **Web 應用程式**]。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application**.</span></span> <span data-ttu-id="b0c4b-124">此範本會產生[ Razor 頁面](xref:razor-pages/index)應用程式。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="b0c4b-125">按一下 **[確定]** 之前，請按一下 [ **變更驗證**]。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-125">Before clicking **OK**, click **Change Authentication**.</span></span>
1. <span data-ttu-id="b0c4b-126">為範本選擇 **個別的使用者帳戶** Identity 。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="b0c4b-127">最後，按一下 **[確定**]，然後按一下 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-127">Finally, click **OK**, then **OK**.</span></span> <span data-ttu-id="b0c4b-128">Visual Studio 使用範本建立專案 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="b0c4b-129">選取 [**工具**  >  **NuGet 封裝管理員**  >  **封裝管理員主控台**]，以開啟**封裝管理員主控台** (PMC) ] 視窗。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="b0c4b-130">流覽至 PMC 中的專案根目錄，然後執行 [ (EF) Core](/ef/core) `Update-Database` 命令 Entity Framework。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="b0c4b-131">ASP.NET Core 2.0 會 Identity 使用 EF Core 來與儲存驗證資料的資料庫互動。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="b0c4b-132">為了讓新建立的應用程式能夠運作，需要有資料庫來儲存此資料。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="b0c4b-133">建立新的應用程式之後，在資料庫環境中檢查架構最快的方法就是使用 [EF Core 遷移](/ef/core/managing-schemas/migrations/)來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="b0c4b-134">此程式會在本機或其他位置建立資料庫，以模擬該架構。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="b0c4b-135">如需詳細資訊，請參閱上述檔。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="b0c4b-136">EF Core 命令使用 *appsettings.js*中指定之資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-136">EF Core commands use the connection string for the database specified in *appsettings.json*.</span></span> <span data-ttu-id="b0c4b-137">下列連接字串會以 *localhost* 上的資料庫為目標，名為 *asp-net-core 身分識別*。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-137">The following connection string targets a database on *localhost* named *asp-net-core-identity*.</span></span> <span data-ttu-id="b0c4b-138">在此設定中，EF Core 設定為使用 `DefaultConnection` 連接字串。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="b0c4b-139">選取 [ **View**  >  **SQL Server 物件總管**]。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-139">Select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="b0c4b-140">展開對應至在appsettings.js的屬性中指定之資料庫名稱的節點 `ConnectionStrings:DefaultConnection` 。 *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="b0c4b-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json*.</span></span>

    <span data-ttu-id="b0c4b-141">此 `Update-Database` 命令會建立以架構指定的資料庫，以及應用程式初始化所需的任何資料。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="b0c4b-142">下圖描述使用上述步驟所建立的資料表結構。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    ![：：：非 loc (Identity) ：：： Tables](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a><span data-ttu-id="b0c4b-144">移轉結構描述</span><span class="sxs-lookup"><span data-stu-id="b0c4b-144">Migrate the schema</span></span>

<span data-ttu-id="b0c4b-145">在資料表結構和欄位中，成員資格和的欄位都有細微的差異 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="b0c4b-146">此模式已大幅變更 ASP.NET 和 ASP.NET Core apps 的驗證/授權。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="b0c4b-147">仍使用的主要物件 Identity 是 *使用者* 和 *角色*。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-147">The key objects that are still used with Identity are *Users* and *Roles*.</span></span> <span data-ttu-id="b0c4b-148">以下是 *使用者*、 *角色*和 *UserRoles*的對應資料表。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-148">Here are mapping tables for *Users*, *Roles*, and *UserRoles*.</span></span>

### <a name="users"></a><span data-ttu-id="b0c4b-149">使用者</span><span class="sxs-lookup"><span data-stu-id="b0c4b-149">Users</span></span>

|Identity<br><span data-ttu-id="b0c4b-150"> (`dbo.AspNetUsers`) 資料行</span><span class="sxs-lookup"><span data-stu-id="b0c4b-150">(`dbo.AspNetUsers`) column</span></span>  |<span data-ttu-id="b0c4b-151">類型</span><span class="sxs-lookup"><span data-stu-id="b0c4b-151">Type</span></span>     |<span data-ttu-id="b0c4b-152">成員資格</span><span class="sxs-lookup"><span data-stu-id="b0c4b-152">Membership</span></span><br><span data-ttu-id="b0c4b-153"> (`dbo.aspnet_Users`  /  `dbo.aspnet_Membership`) 資料行</span><span class="sxs-lookup"><span data-stu-id="b0c4b-153">(`dbo.aspnet_Users` / `dbo.aspnet_Membership`) column</span></span>|<span data-ttu-id="b0c4b-154">類型</span><span class="sxs-lookup"><span data-stu-id="b0c4b-154">Type</span></span>      |
|-------------------------------------------|-----------------------------------------------------------------------|
| `Id`                            | `string`| `aspnet_Users.UserId`                                      | `string` |
| `UserName`                      | `string`| `aspnet_Users.UserName`                                    | `string` |
| `Email`                         | `string`| `aspnet_Membership.Email`                                  | `string` |
| `NormalizedUserName`            | `string`| `aspnet_Users.LoweredUserName`                             | `string` |
| `NormalizedEmail`               | `string`| `aspnet_Membership.LoweredEmail`                           | `string` |
| `PhoneNumber`                   | `string`| `aspnet_Users.MobileAlias`                                 | `string` |
| `LockoutEnabled`                | `bit`   | `aspnet_Membership.IsLockedOut`                            | `bit`    |

> [!NOTE]
> <span data-ttu-id="b0c4b-155">並非所有欄位對應都像是成員資格的一對一關聯性 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-155">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="b0c4b-156">上表採用預設的成員資格使用者架構，並將其對應至 ASP.NET Core Identity 架構。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-156">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="b0c4b-157">任何其他用於成員資格的自訂欄位都需要手動對應。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-157">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="b0c4b-158">在此對應中，沒有任何對應的密碼，因為密碼準則和密碼 salts 不會在兩者之間遷移。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-158">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="b0c4b-159">**建議您將密碼保留為 null，並要求使用者重設其密碼。**</span><span class="sxs-lookup"><span data-stu-id="b0c4b-159">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="b0c4b-160">在中 ASP.NET Core Identity ， `LockoutEnd` 如果使用者被鎖定，則應該設定為未來的某個日期。這會顯示在遷移腳本中。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-160">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="b0c4b-161">角色</span><span class="sxs-lookup"><span data-stu-id="b0c4b-161">Roles</span></span>

|Identity<br><span data-ttu-id="b0c4b-162"> (`dbo.AspNetRoles`) 資料行</span><span class="sxs-lookup"><span data-stu-id="b0c4b-162">(`dbo.AspNetRoles`) column</span></span>|<span data-ttu-id="b0c4b-163">類型</span><span class="sxs-lookup"><span data-stu-id="b0c4b-163">Type</span></span>|<span data-ttu-id="b0c4b-164">成員資格</span><span class="sxs-lookup"><span data-stu-id="b0c4b-164">Membership</span></span><br><span data-ttu-id="b0c4b-165"> (`dbo.aspnet_Roles`) 資料行</span><span class="sxs-lookup"><span data-stu-id="b0c4b-165">(`dbo.aspnet_Roles`) column</span></span>|<span data-ttu-id="b0c4b-166">類型</span><span class="sxs-lookup"><span data-stu-id="b0c4b-166">Type</span></span>|
|----------------------------------------|-----------------------------------|
|`Id`                           |`string`|`RoleId`         | `string`        |
|`Name`                         |`string`|`RoleName`       | `string`        |
|`NormalizedName`               |`string`|`LoweredRoleName`| `string`        |

### <a name="user-roles"></a><span data-ttu-id="b0c4b-167">使用者角色</span><span class="sxs-lookup"><span data-stu-id="b0c4b-167">User Roles</span></span>

|Identity<br><span data-ttu-id="b0c4b-168"> (`dbo.AspNetUserRoles`) 資料行</span><span class="sxs-lookup"><span data-stu-id="b0c4b-168">(`dbo.AspNetUserRoles`) column</span></span>|<span data-ttu-id="b0c4b-169">類型</span><span class="sxs-lookup"><span data-stu-id="b0c4b-169">Type</span></span>|<span data-ttu-id="b0c4b-170">成員資格</span><span class="sxs-lookup"><span data-stu-id="b0c4b-170">Membership</span></span><br><span data-ttu-id="b0c4b-171"> (`dbo.aspnet_UsersInRoles`) 資料行</span><span class="sxs-lookup"><span data-stu-id="b0c4b-171">(`dbo.aspnet_UsersInRoles`) column</span></span>|<span data-ttu-id="b0c4b-172">類型</span><span class="sxs-lookup"><span data-stu-id="b0c4b-172">Type</span></span>|
|-------------------------|----------|--------------|---------------------------|
|`RoleId`                 |`string`  |`RoleId`      |`string`                   |
|`UserId`                 |`string`  |`UserId`      |`string`                   |

<span data-ttu-id="b0c4b-173">建立 *使用者* 和 *角色*的遷移腳本時，請參考上述對應表。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-173">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles*.</span></span> <span data-ttu-id="b0c4b-174">下列範例假設您在資料庫伺服器上有兩個資料庫。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-174">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="b0c4b-175">其中一個資料庫包含現有的 ASP.NET 成員資格架構和資料。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-175">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="b0c4b-176">其他 *核心 Identity 範例* 資料庫是使用稍早所述的步驟所建立。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-176">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="b0c4b-177">內嵌包含批註，以取得更多詳細資料。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-177">Comments are included inline for more details.</span></span>

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO CoreIdentitySample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN CoreIdentitySample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

<span data-ttu-id="b0c4b-178">完成上述腳本之後，稍 ASP.NET Core Identity 早建立的應用程式會填入成員資格使用者。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-178">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="b0c4b-179">使用者必須在登入之前變更其密碼。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-179">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="b0c4b-180">如果成員系統擁有的使用者名稱不符合其電子郵件地址，則必須對稍早建立的應用程式進行變更以容納此應用程式。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-180">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="b0c4b-181">預設範本預期 `UserName` 和 `Email` 都是相同的。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-181">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="b0c4b-182">在不同的情況下，必須修改登入程式以使用 `UserName` 而不是 `Email` 。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-182">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="b0c4b-183">在登入頁面的 [ `PageModel` 位於 *Pages\Account\Login.cshtml.cs*] 中， `[EmailAddress]` 從 [ *電子郵件* ] 屬性中移除該屬性。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-183">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs*, remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="b0c4b-184">將其重新命名為 *UserName*。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-184">Rename it to *UserName*.</span></span> <span data-ttu-id="b0c4b-185">這需要 `EmailAddress` 在 *View* 和 *PageModel*中提及的任何位置進行變更。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-185">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel*.</span></span> <span data-ttu-id="b0c4b-186">結果看起來如下：</span><span class="sxs-lookup"><span data-stu-id="b0c4b-186">The result looks like the following:</span></span>

 ![已修正登入](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="b0c4b-188">後續步驟</span><span class="sxs-lookup"><span data-stu-id="b0c4b-188">Next steps</span></span>

<span data-ttu-id="b0c4b-189">在本教學課程中，您已瞭解如何將 SQL 成員資格的使用者移植到 ASP.NET Core 2.0 Identity 。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-189">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="b0c4b-190">如需有關的詳細資訊 ASP.NET Core Identity ，請參閱[簡介 Identity ](xref:security/authentication/identity)。</span><span class="sxs-lookup"><span data-stu-id="b0c4b-190">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
