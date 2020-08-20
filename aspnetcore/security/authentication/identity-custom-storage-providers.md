---
title: 的自訂儲存體提供者 ASP.NET Core Identity
author: ardalis
description: 瞭解如何設定的自訂存放裝置提供者 ASP.NET Core Identity 。
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
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
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: a8414efeece1afd55d0f30d232ef360d0a21714c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630129"
---
# <a name="custom-storage-providers-for-no-locaspnet-core-identity"></a>的自訂儲存體提供者 ASP.NET Core Identity

作者：[Steve Smith](https://ardalis.com/)

ASP.NET Core Identity 是可延伸的系統，可讓您建立自訂的儲存體提供者，並將它連接到您的應用程式。 本主題說明如何建立的自訂儲存提供者 ASP.NET Core Identity 。 其中涵蓋建立您自己的儲存提供者的重要概念，但不是逐步解說。

[從 GitHub 查看或下載範例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample)。

## <a name="introduction"></a>簡介

根據預設， ASP.NET Core Identity 系統會使用 Entity Framework Core 將使用者資訊儲存在 SQL Server 的資料庫中。 對於許多應用程式而言，這種方法的運作效果很好。 不過，您可能會想要使用不同的持續性機制或資料結構描述。 例如：

* 您可以使用 [Azure 資料表儲存體](/azure/storage/) 或另一個資料存放區。
* 您的資料庫資料表具有不同的結構。 
* 您可能會想要使用不同的資料存取方法，例如 [Dapper](https://github.com/StackExchange/Dapper)。 

在上述每一種情況下，您可以為您的儲存機制撰寫自訂的提供者，並將該提供者插入您的應用程式。

ASP.NET Core Identity 包含在具有「個別使用者帳戶」選項 Visual Studio 的專案範本中。

使用 .NET Core CLI 時，請新增 `-au Individual` ：

```dotnetcli
dotnet new mvc -au Individual
```

## <a name="the-no-locaspnet-core-identity-architecture"></a>ASP.NET Core Identity架構

ASP.NET Core Identity 包含稱為管理員和存放區的類別。 *管理員* 是應用程式開發人員用來執行作業的高階類別，例如建立 Identity 使用者。 存放*區*是較低層級的類別，可指定如何保存實體（例如使用者和角色）。 存放區會遵循存放庫模式，並與持續性機制緊密結合。 管理員會與存放區分離，這表示您可以取代持續性機制，而不需要變更您的應用程式程式碼 (設定) 除外。

下圖顯示 web 應用程式與管理員的互動方式，以及存放區與資料存取層的互動。

![ASP.NET Core Apps 適用于管理員 (例如 ' UserManager '、' RoleManager ') 。 管理員可以使用商店 (例如 ' UserStore ') 使用 Entity Framework Core 之類的程式庫與資料來源進行通訊。](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

若要建立自訂的儲存提供者，請建立資料來源、資料存取層，以及與此資料存取層互動的存放區類別 (上圖中的綠色和灰色方塊) 。 您不需要自訂管理者或應用程式程式碼， () 上述的藍色方塊來與其互動。

當您建立的新實例 `UserManager` 或 `RoleManager` 提供使用者類別的型別，並以引數的形式傳遞存放區類別的實例時。 這種方法可讓您將自訂類別插入 ASP.NET Core 中。 

將[應用程式重新設定為使用新的存放裝置提供者](#reconfigure-app-to-use-a-new-storage-provider)會示範如何具現化 `UserManager` 和 `RoleManager` 使用自訂存放區。

## <a name="no-locaspnet-core-identity-stores-data-types"></a>ASP.NET Core Identity 儲存資料類型

[ASP.NET Core Identity](https://github.com/aspnet/identity) 下列各節會詳細說明資料類型：

### <a name="users"></a>使用者

您網站的已註冊使用者。 [ Identity 使用者](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)類型可延伸或用作您自己的自訂類型的範例。 您不需要繼承自特定型別，就能執行您自己的自訂身分識別儲存體解決方案。

### <a name="user-claims"></a>使用者宣告

 (或 [宣告](/dotnet/api/system.security.claims.claim) 的一組語句，) 代表使用者身分識別的使用者。 可以針對使用者的身分識別啟用較高的運算式，而不是透過角色來達成。

### <a name="user-logins"></a>使用者登入

外部驗證提供者的相關資訊 (像是 Facebook 或 Microsoft 帳戶) 在使用者登入時使用。 [範例](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a>角色

您網站的授權群組。 包含角色識別碼和角色名稱 (例如 "Admin" 或 "Employee" ) 。 [範例](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a>資料存取層

本主題假設您已熟悉要使用的持續性機制，以及如何建立該機制的實體。 本主題不會提供有關如何建立存放庫或資料存取類別的詳細資料;在使用時，它會提供有關設計決策的一些建議 ASP.NET Core Identity 。

設計自訂存放區提供者的資料存取層時，您有很多自由。 您只需要為您想要在應用程式中使用的功能建立持續性機制。 例如，如果您未在應用程式中使用角色，則不需要建立角色或使用者角色關聯的儲存體。 您的技術和現有的基礎結構可能需要與的預設執行非常不同的結構 ASP.NET Core Identity 。 在您的資料存取層中，您會提供邏輯來處理儲存體執行的結構。

資料存取層提供將資料儲存 ASP.NET Core Identity 至資料來源的邏輯。 您自訂的儲存提供者的資料存取層可能包含下列類別來儲存使用者和角色資訊。

### <a name="context-class"></a>Context 類別

封裝資訊以連接到您的持續性機制，並執行查詢。 有幾個資料類別需要這個類別的實例，通常是透過相依性插入來提供。 [範例](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1)。

### <a name="user-storage"></a>使用者儲存體

儲存和取出使用者資訊 (例如使用者名稱和密碼雜湊) 。 [範例](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a>角色儲存體

儲存和取出角色資訊 (例如角色名稱) 。 [範例](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a>UserClaims 儲存體

儲存並取出使用者宣告資訊， (例如宣告類型和值) 。 [範例](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a>UserLogins 儲存體

儲存和取出使用者登入資訊 (例如外部驗證提供者) 。 [範例](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a>UserRole 儲存體

儲存並取出指派給哪些使用者的角色。 [範例](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

**秘訣：** 只執行您想要在應用程式中使用的類別。

在資料存取類別中，提供程式碼來執行持續性機制的資料作業。 例如，在自訂提供者內，您可能會有下列程式碼，在 *store* 類別中建立新的使用者：

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

建立使用者的執行邏輯位於 `_usersTable.CreateAsync` 方法中，如下所示。

## <a name="customize-the-user-class"></a>自訂使用者類別

當您在執行儲存提供者時，請建立相當於[ Identity 使用者類別](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)的使用者類別。

您的使用者類別至少必須包含 `Id` 和 `UserName` 屬性。

`IdentityUser`類別會定義在 `UserManager` 執行要求的作業時，所呼叫的屬性。 屬性的預設類型 `Id` 為字串，但您可以繼承自 `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` 並指定不同的類型。 架構預期儲存體執行會處理資料類型轉換。

## <a name="customize-the-user-store"></a>自訂使用者存放區

建立 `UserStore` 類別，以提供使用者上所有資料作業的方法。 這個類別相當於[UserStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1)類別。 在您的 `UserStore` 類別中，您 `IUserStore<TUser>` 必須執行和選用的介面。 您可以根據應用程式中提供的功能，選取要執行的選用介面。

### <a name="optional-interfaces"></a>選擇性介面

* [IUserRoleStore](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)
* [IUserClaimStore](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)
* [IUserPasswordStore](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)
* [IUserSecurityStampStore](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)
* [IUserEmailStore](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)
* [IUserPhoneNumberStore](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)
* [IQueryableUserStore](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)
* [IUserLoginStore](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)
* [IUserTwoFactorStore](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)
* [IUserLockoutStore](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)

選用的介面會繼承自 `IUserStore<TUser>` 。 您可以在 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs)中看到部分實作為範例使用者存放區。

在 `UserStore` 類別內，您可以使用您建立來執行作業的資料存取類別。 這些會使用相依性插入來傳遞。 例如，在 Dapper 執行的 SQL Server 中， `UserStore` 類別具有 `CreateAsync` 使用的實例 `DapperUsersTable` 插入新記錄的方法：

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a>自訂使用者存放區時要執行的介面

* **IUserStore**  
 [IUserStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1)介面是您必須在使用者存放區中執行的唯一介面。 它會定義用來建立、更新、刪除和取出使用者的方法。
* **IUserClaimStore**  
 [IUserClaimStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)介面會定義您所執行的方法，以啟用使用者宣告。 它包含加入、移除和取出使用者宣告的方法。
* **IUserLoginStore**  
 [IUserLoginStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)會定義您為了啟用外部驗證提供者所執行的方法。 它包含加入、移除和取出使用者登入的方法，以及根據登入資訊來取得使用者的方法。
* **IUserRoleStore**  
 [IUserRoleStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)介面會定義您所執行的方法，以將使用者對應至角色。 它包含加入、移除和取出使用者角色的方法，以及檢查是否已將使用者指派給角色的方法。
* **IUserPasswordStore**  
 [IUserPasswordStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)介面會定義您所執行的方法，以保存已雜湊的密碼。 它包含取得和設定雜湊密碼的方法，以及指出使用者是否已設定密碼的方法。
* **IUserSecurityStampStore**  
 [IUserSecurityStampStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)介面會定義您所執行的方法，以使用安全性戳記來指出使用者的帳戶資訊是否已變更。 當使用者變更密碼或新增或移除登入時，就會更新此戳記。 它包含取得和設定安全性戳記的方法。
* **IUserTwoFactorStore**  
 [IUserTwoFactorStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)介面會定義您所執行的方法，以支援雙因素驗證。 它包含取得和設定是否為使用者啟用雙因素驗證的方法。
* **IUserPhoneNumberStore**  
 [IUserPhoneNumberStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)介面會定義您所執行的方法，以儲存使用者的電話號碼。 它包含取得和設定電話號碼的方法，以及電話號碼是否已確認。
* **IUserEmailStore**  
 [IUserEmailStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)介面會定義您所執行的方法，以儲存使用者的電子郵件地址。 它包含取得和設定電子郵件地址的方法，以及是否確認電子郵件。
* **IUserLockoutStore**  
 [IUserLockoutStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)介面會定義您所執行的方法，以儲存有關鎖定帳戶的資訊。 它包含追蹤失敗的存取嘗試和鎖定的方法。
* **IQueryableUserStore**  
 [IQueryableUserStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)介面會定義您所要執行的成員，以提供可查詢的使用者存放區。

您只會執行應用程式中所需的介面。 例如：

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

### <a name="no-locidentityuserclaim-no-locidentityuserlogin-and-no-locidentityuserrole"></a>IdentityUserClaim、 Identity UserLogin 和 Identity UserRole

`Microsoft.AspNet.Identity.EntityFramework`命名空間包含[ Identity UserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1)、 [ Identity UserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)和[ Identity UserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1)類別的實作為。 如果您使用這些功能，您可能會想要建立自己的類別版本，並定義應用程式的屬性。 不過，在執行基本作業 (例如新增或移除使用者的宣告) 時，有時不會將這些實體載入記憶體中會更有效率。 相反地，後端存放區類別可以直接在資料來源上執行這些作業。 例如， `UserStore.GetClaimsAsync` 方法可以呼叫 `userClaimTable.FindByUserId(user.Id)` 方法來直接對該資料表執行查詢，並傳回宣告清單。

## <a name="customize-the-role-class"></a>自訂角色類別

在執行角色儲存提供者時，您可以建立自訂角色類型。 它不需要執行特定的介面，但它必須有 `Id` ，而且通常會有 `Name` 屬性。

以下是範例角色類別：

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a>自訂角色存放區

您可以建立 `RoleStore` 類別，以提供角色上所有資料作業的方法。 這個類別相當於[RoleStore &lt; TRole &gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)類別。 在 `RoleStore` 類別中，您會執行 `IRoleStore<TRole>` 和（選擇性） `IQueryableRoleStore<TRole>` 介面。

* **IRoleStore &lt; TRole&gt;**  
 [IRoleStore &lt; TRole &gt; ](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1)介面會定義要在角色存放區類別中執行的方法。 它包含建立、更新、刪除及抓取角色的方法。
* **RoleStore &lt; TRole&gt;**  
 若要自訂 `RoleStore` ，請建立可執行 `IRoleStore<TRole>` 介面的類別。 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a>重新設定應用程式以使用新的存放裝置提供者

一旦您已完成儲存體提供者，您就可以設定應用程式來使用它。 如果您的應用程式使用預設提供者，請將它取代為您的自訂提供者。

1. 移除 `Microsoft.AspNetCore.EntityFramework.Identity` NuGet 套件。
1. 如果儲存提供者位於個別的專案或封裝中，請在其中加入參考。
1. 使用 `Microsoft.AspNetCore.EntityFramework.Identity` 您的儲存提供者命名空間的 using 語句來取代所有參考。
1. 在 `ConfigureServices` 方法中，將 `AddIdentity` 方法變更為使用您的自訂類型。 您可以針對此用途建立自己的擴充方法。 如需範例，請參閱[ Identity ServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) 。
1. 如果您使用的是角色，請更新 `RoleManager` 以使用您的 `RoleStore` 類別。
1. 將連接字串與認證更新至您的應用程式設定。

範例：

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

## <a name="references"></a>參考

* [ASP.NET 4.x 的自訂儲存體提供者 Identity](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
* [ASP.NET Core Identity](https://github.com/dotnet/AspNetCore/tree/master/src/Identity)：此存放庫包含已維護的商店提供者連結。
