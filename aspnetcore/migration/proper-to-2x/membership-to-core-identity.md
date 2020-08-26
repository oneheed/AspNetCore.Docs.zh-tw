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
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-no-locidentity"></a>從 ASP.NET 成員資格驗證遷移至 ASP.NET Core 2。0 Identity

作者：[Isaac Levin](https://isaaclevin.com)

本文示範如何使用成員資格驗證將 ASP.NET 應用程式的資料庫架構遷移至 ASP.NET Core 2.0 Identity 。

> [!NOTE]
> 本檔提供將 ASP.NET 成員資格型應用程式的資料庫架構遷移到用於之資料庫架構所需的步驟 ASP.NET Core Identity 。 如需從 ASP.NET 成員資格式驗證遷移至 ASP.NET 的詳細資訊 Identity ，請參閱將[現有的應用程式從 SQL Identity 成員資格遷移至 ASP.NET ](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity)。 如需的詳細資訊 ASP.NET Core Identity ，請參閱 [ Identity ASP.NET Core 的簡介](xref:security/authentication/identity)。

## <a name="review-of-membership-schema"></a>成員資格架構的評論

在 ASP.NET 2.0 之前，開發人員會負責為其應用程式建立整個驗證和授權程式。 使用 ASP.NET 2.0，引進了成員資格，提供在 ASP.NET apps 中處理安全性的未定案解決方案。 開發人員現在可以使用命令，在 SQL Server 資料庫中啟動架構 <https://docs.microsoft.com/previous-versions/ms229862(v=vs.140)> 。 執行此命令之後，會在資料庫中建立下列資料表。

  ![成員資格資料表](identity/_static/membership-tables.png)

若要將現有的應用程式遷移至 ASP.NET Core 2.0 Identity ，必須將這些資料表中的資料移轉到新架構所使用的資料表 Identity 。

## <a name="no-locaspnet-core-identity-20-schema"></a>ASP.NET Core Identity 2.0 架構

ASP.NET Core 2.0 遵循 [Identity](/aspnet/identity/index) ASP.NET 4.5 中所引進的原則。 雖然這項原則是共用的，但架構之間的實施也不同，即使是 ASP.NET Core 版本 (請參閱 [遷移驗證和 Identity ASP.NET Core 2.0](xref:migration/1x-to-2x/index)) 。

若要查看 ASP.NET Core 2.0 的架構，最快的方式 Identity 是建立新的 ASP.NET Core 2.0 應用程式。 遵循 Visual Studio 2017 中的下列步驟：

1. 選取 [File] \(檔案\) >  [New] \(新增\) >  [Project] \(專案\)。
1. 建立名為*Core Identity Sample*的新**ASP.NET Core Web 應用程式**專案。
1. 在下拉式清單中選取 **ASP.NET Core 2.0** ，然後選取 [ **Web 應用程式**]。 此範本會產生[ Razor 頁面](xref:razor-pages/index)應用程式。 按一下 **[確定]** 之前，請按一下 [ **變更驗證**]。
1. 為範本選擇 **個別的使用者帳戶** Identity 。 最後，按一下 **[確定**]，然後按一下 **[確定]**。 Visual Studio 使用範本建立專案 ASP.NET Core Identity 。
1. 選取 [**工具**  >  **NuGet 封裝管理員**  >  **封裝管理員主控台**]，以開啟**封裝管理員主控台** (PMC) ] 視窗。
1. 流覽至 PMC 中的專案根目錄，然後執行 [ (EF) Core](/ef/core) `Update-Database` 命令 Entity Framework。

    ASP.NET Core 2.0 會 Identity 使用 EF Core 來與儲存驗證資料的資料庫互動。 為了讓新建立的應用程式能夠運作，需要有資料庫來儲存此資料。 建立新的應用程式之後，在資料庫環境中檢查架構最快的方法就是使用 [EF Core 遷移](/ef/core/managing-schemas/migrations/)來建立資料庫。 此程式會在本機或其他位置建立資料庫，以模擬該架構。 如需詳細資訊，請參閱上述檔。

    EF Core 命令使用 *appsettings.js*中指定之資料庫的連接字串。 下列連接字串會以 *localhost* 上的資料庫為目標，名為 *asp-net-core 身分識別*。 在此設定中，EF Core 設定為使用 `DefaultConnection` 連接字串。

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. 選取 [ **View**  >  **SQL Server 物件總管**]。 展開對應至在appsettings.js的屬性中指定之資料庫名稱的節點 `ConnectionStrings:DefaultConnection` 。 *appsettings.json*

    此 `Update-Database` 命令會建立以架構指定的資料庫，以及應用程式初始化所需的任何資料。 下圖描述使用上述步驟所建立的資料表結構。

    ![：：：非 loc (Identity) ：：： Tables](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a>移轉結構描述

在資料表結構和欄位中，成員資格和的欄位都有細微的差異 ASP.NET Core Identity 。 此模式已大幅變更 ASP.NET 和 ASP.NET Core apps 的驗證/授權。 仍使用的主要物件 Identity 是 *使用者* 和 *角色*。 以下是 *使用者*、 *角色*和 *UserRoles*的對應資料表。

### <a name="users"></a>使用者

|Identity<br> (`dbo.AspNetUsers`) 資料行  |類型     |成員資格<br> (`dbo.aspnet_Users`  /  `dbo.aspnet_Membership`) 資料行|類型      |
|-------------------------------------------|-----------------------------------------------------------------------|
| `Id`                            | `string`| `aspnet_Users.UserId`                                      | `string` |
| `UserName`                      | `string`| `aspnet_Users.UserName`                                    | `string` |
| `Email`                         | `string`| `aspnet_Membership.Email`                                  | `string` |
| `NormalizedUserName`            | `string`| `aspnet_Users.LoweredUserName`                             | `string` |
| `NormalizedEmail`               | `string`| `aspnet_Membership.LoweredEmail`                           | `string` |
| `PhoneNumber`                   | `string`| `aspnet_Users.MobileAlias`                                 | `string` |
| `LockoutEnabled`                | `bit`   | `aspnet_Membership.IsLockedOut`                            | `bit`    |

> [!NOTE]
> 並非所有欄位對應都像是成員資格的一對一關聯性 ASP.NET Core Identity 。 上表採用預設的成員資格使用者架構，並將其對應至 ASP.NET Core Identity 架構。 任何其他用於成員資格的自訂欄位都需要手動對應。 在此對應中，沒有任何對應的密碼，因為密碼準則和密碼 salts 不會在兩者之間遷移。 **建議您將密碼保留為 null，並要求使用者重設其密碼。** 在中 ASP.NET Core Identity ， `LockoutEnd` 如果使用者被鎖定，則應該設定為未來的某個日期。這會顯示在遷移腳本中。

### <a name="roles"></a>角色

|Identity<br> (`dbo.AspNetRoles`) 資料行|類型|成員資格<br> (`dbo.aspnet_Roles`) 資料行|類型|
|----------------------------------------|-----------------------------------|
|`Id`                           |`string`|`RoleId`         | `string`        |
|`Name`                         |`string`|`RoleName`       | `string`        |
|`NormalizedName`               |`string`|`LoweredRoleName`| `string`        |

### <a name="user-roles"></a>使用者角色

|Identity<br> (`dbo.AspNetUserRoles`) 資料行|類型|成員資格<br> (`dbo.aspnet_UsersInRoles`) 資料行|類型|
|-------------------------|----------|--------------|---------------------------|
|`RoleId`                 |`string`  |`RoleId`      |`string`                   |
|`UserId`                 |`string`  |`UserId`      |`string`                   |

建立 *使用者* 和 *角色*的遷移腳本時，請參考上述對應表。 下列範例假設您在資料庫伺服器上有兩個資料庫。 其中一個資料庫包含現有的 ASP.NET 成員資格架構和資料。 其他 *核心 Identity 範例* 資料庫是使用稍早所述的步驟所建立。 內嵌包含批註，以取得更多詳細資料。

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

完成上述腳本之後，稍 ASP.NET Core Identity 早建立的應用程式會填入成員資格使用者。 使用者必須在登入之前變更其密碼。

> [!NOTE]
> 如果成員系統擁有的使用者名稱不符合其電子郵件地址，則必須對稍早建立的應用程式進行變更以容納此應用程式。 預設範本預期 `UserName` 和 `Email` 都是相同的。 在不同的情況下，必須修改登入程式以使用 `UserName` 而不是 `Email` 。

在登入頁面的 [ `PageModel` 位於 *Pages\Account\Login.cshtml.cs*] 中， `[EmailAddress]` 從 [ *電子郵件* ] 屬性中移除該屬性。 將其重新命名為 *UserName*。 這需要 `EmailAddress` 在 *View* 和 *PageModel*中提及的任何位置進行變更。 結果看起來如下：

 ![已修正登入](identity/_static/fixed-login.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已瞭解如何將 SQL 成員資格的使用者移植到 ASP.NET Core 2.0 Identity 。 如需有關的詳細資訊 ASP.NET Core Identity ，請參閱[簡介 Identity ](xref:security/authentication/identity)。
