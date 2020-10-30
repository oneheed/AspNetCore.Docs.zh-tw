---
title: Identity ASP.NET Core 中的模型自訂
author: ajcvickers
description: 本文描述如何自訂的基礎 Entity Framework Core 資料模型 ASP.NET Core Identity 。
ms.author: avickers
ms.date: 07/01/2019
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
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 6e520c76a3377e889166ca8d08b75754ef34b6a1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052041"
---
# <a name="no-locidentity-model-customization-in-aspnet-core"></a>Identity ASP.NET Core 中的模型自訂

依 [Arthur Vickers](https://github.com/ajcvickers)

ASP.NET Core Identity 提供在 ASP.NET Core apps 中管理和儲存使用者帳戶的架構。 Identity 當選取 **個別使用者帳戶** 做為驗證機制時，會加入至您的專案。 根據預設， Identity 會使用 Entity Framework (EF) 核心資料模型。 本文說明如何自訂 Identity 模型。

## <a name="no-locidentity-and-ef-core-migrations"></a>Identity 和 EF Core 遷移

檢查模型之前，請先瞭解如何 Identity 搭配使用 [EF Core 遷移](/ef/core/managing-schemas/migrations/) 來建立和更新資料庫。 最上層的程式是：

1. [在程式碼中](/ef/core/modeling/)定義或更新資料模型。
1. 新增遷移以將此模型轉譯成可套用至資料庫的變更。
1. 檢查是否已正確地表示您的意圖。
1. 套用遷移，將資料庫更新為與模型同步。
1. 重複步驟1到4以進一步精簡模型，並讓資料庫保持同步。

您可以使用下列其中一種方法來新增和套用遷移：

* 如果使用 Visual Studio， **封裝管理員主控台** (PMC) 視窗。 如需詳細資訊，請參閱 [EF CORE PMC 工具](/ef/core/miscellaneous/cli/powershell)。
* 如果使用命令列，則為 .NET Core CLI。 如需詳細資訊，請參閱 [EF Core .net 命令列工具](/ef/core/miscellaneous/cli/dotnet)。
* 當應用程式執行時，按一下錯誤頁面上的 [套用 **遷移** ] 按鈕。

ASP.NET Core 有開發階段錯誤頁面處理常式。 當應用程式執行時，處理常式可以套用遷移。 生產環境應用程式通常會從遷移產生 SQL 腳本，並在受控制的應用程式和資料庫部署中部署資料庫變更。

當使用建立新的應用程式時 Identity ，上述步驟1和2已完成。 也就是說，初始資料模型已經存在，而且初始的遷移已加入至專案。 初始遷移仍然需要套用至資料庫。 您可以透過下列其中一種方法來套用初始遷移：

* `Update-Database`在 PMC 中執行。
* `dotnet ef database update`在命令 shell 中執行。
* 當應用程式執行時，請按一下 [錯誤] 頁面上的 [套用 **遷移** ] 按鈕。

在對模型進行變更時，重複上述步驟。

## <a name="the-no-locidentity-model"></a>Identity模型

### <a name="entity-types"></a>實體類型

此 Identity 模型包含下列實體類型。

|實體類型|描述                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |代表使用者。                                         |
|`Role`     |代表角色。                                           |
|`UserClaim`|表示使用者擁有的宣告。                    |
|`UserToken`|表示使用者的驗證權杖。               |
|`UserLogin`|將使用者與登入產生關聯。                              |
|`RoleClaim`|代表授與角色內所有使用者的宣告。|
|`UserRole` |關聯使用者和角色的聯結實體。               |

### <a name="entity-type-relationships"></a>實體類型關聯性

[實體類型](#entity-types)會以下列方式彼此相關：

* 每個都 `User` 可以有許多 `UserClaims` 。
* 每個都 `User` 可以有許多 `UserLogins` 。
* 每個都 `User` 可以有許多 `UserTokens` 。
* 每個都 `Role` 可以有許多相關聯的 `RoleClaims` 。
* 每個都 `User` 可以有許多相關聯 `Roles` ，而且每個都 `Role` 可以與許多相關聯 `Users` 。 這是多對多關聯性，需要資料庫中的聯結資料表。 聯結資料表是由 `UserRole` 實體表示。

### <a name="default-model-configuration"></a>預設模型設定

Identity定義許多從 [DbCoNtext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)繼承以設定和使用模型的 *內容類別* 。 這項設定是在內容類別的[OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating)方法中，使用[EF CORE Code First 流暢 API](/ef/core/modeling/)來完成。 預設設定為：

```csharp
builder.Entity<TUser>(b =>
{
    // Primary key
    b.HasKey(u => u.Id);

    // Indexes for "normalized" username and email, to allow efficient lookups
    b.HasIndex(u => u.NormalizedUserName).HasName("UserNameIndex").IsUnique();
    b.HasIndex(u => u.NormalizedEmail).HasName("EmailIndex");

    // Maps to the AspNetUsers table
    b.ToTable("AspNetUsers");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(u => u.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.UserName).HasMaxLength(256);
    b.Property(u => u.NormalizedUserName).HasMaxLength(256);
    b.Property(u => u.Email).HasMaxLength(256);
    b.Property(u => u.NormalizedEmail).HasMaxLength(256);

    // The relationships between User and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each User can have many UserClaims
    b.HasMany<TUserClaim>().WithOne().HasForeignKey(uc => uc.UserId).IsRequired();

    // Each User can have many UserLogins
    b.HasMany<TUserLogin>().WithOne().HasForeignKey(ul => ul.UserId).IsRequired();

    // Each User can have many UserTokens
    b.HasMany<TUserToken>().WithOne().HasForeignKey(ut => ut.UserId).IsRequired();

    // Each User can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.UserId).IsRequired();
});

builder.Entity<TUserClaim>(b =>
{
    // Primary key
    b.HasKey(uc => uc.Id);

    // Maps to the AspNetUserClaims table
    b.ToTable("AspNetUserClaims");
});

builder.Entity<TUserLogin>(b =>
{
    // Composite primary key consisting of the LoginProvider and the key to use
    // with that provider
    b.HasKey(l => new { l.LoginProvider, l.ProviderKey });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(l => l.LoginProvider).HasMaxLength(128);
    b.Property(l => l.ProviderKey).HasMaxLength(128);

    // Maps to the AspNetUserLogins table
    b.ToTable("AspNetUserLogins");
});

builder.Entity<TUserToken>(b =>
{
    // Composite primary key consisting of the UserId, LoginProvider and Name
    b.HasKey(t => new { t.UserId, t.LoginProvider, t.Name });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(t => t.LoginProvider).HasMaxLength(maxKeyLength);
    b.Property(t => t.Name).HasMaxLength(maxKeyLength);

    // Maps to the AspNetUserTokens table
    b.ToTable("AspNetUserTokens");
});

builder.Entity<TRole>(b =>
{
    // Primary key
    b.HasKey(r => r.Id);

    // Index for "normalized" role name to allow efficient lookups
    b.HasIndex(r => r.NormalizedName).HasName("RoleNameIndex").IsUnique();

    // Maps to the AspNetRoles table
    b.ToTable("AspNetRoles");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(r => r.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.Name).HasMaxLength(256);
    b.Property(u => u.NormalizedName).HasMaxLength(256);

    // The relationships between Role and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each Role can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.RoleId).IsRequired();

    // Each Role can have many associated RoleClaims
    b.HasMany<TRoleClaim>().WithOne().HasForeignKey(rc => rc.RoleId).IsRequired();
});

builder.Entity<TRoleClaim>(b =>
{
    // Primary key
    b.HasKey(rc => rc.Id);

    // Maps to the AspNetRoleClaims table
    b.ToTable("AspNetRoleClaims");
});

builder.Entity<TUserRole>(b =>
{
    // Primary key
    b.HasKey(r => new { r.UserId, r.RoleId });

    // Maps to the AspNetUserRoles table
    b.ToTable("AspNetUserRoles");
});
```

### <a name="model-generic-types"></a>模型泛型型別

Identity 針對上面所列的每個實體類型，定義預設的 [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR) 類型。 這些類型的前面都有 *Identity* ：

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

類型不會直接使用這些類型，而是用來作為應用程式本身類型的基類。 `DbContext`由定義的類別 Identity 為泛型，因此可以將不同的 CLR 型別用於模型中的一或多個實體類型。 這些泛型型別也可讓 `User` 主鍵 (PK) 資料類型進行變更。

使用 Identity 與角色的支援時， <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> 應該使用類別。 例如：

```csharp
// Uses all the built-in Identity types
// Uses `string` as the key type
public class IdentityDbContext
    : IdentityDbContext<IdentityUser, IdentityRole, string>
{
}

// Uses the built-in Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityDbContext<TUser>
    : IdentityDbContext<TUser, IdentityRole, string>
        where TUser : IdentityUser
{
}

// Uses the built-in Identity types except with custom User and Role types
// The key type is defined by TKey
public class IdentityDbContext<TUser, TRole, TKey> : IdentityDbContext<
    TUser, TRole, TKey, IdentityUserClaim<TKey>, IdentityUserRole<TKey>,
    IdentityUserLogin<TKey>, IdentityRoleClaim<TKey>, IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TRole : IdentityRole<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments
// The key type is defined by TKey
public abstract class IdentityDbContext<
    TUser, TRole, TKey, TUserClaim, TUserRole, TUserLogin, TRoleClaim, TUserToken>
    : IdentityUserContext<TUser, TKey, TUserClaim, TUserLogin, TUserToken>
         where TUser : IdentityUser<TKey>
         where TRole : IdentityRole<TKey>
         where TKey : IEquatable<TKey>
         where TUserClaim : IdentityUserClaim<TKey>
         where TUserRole : IdentityUserRole<TKey>
         where TUserLogin : IdentityUserLogin<TKey>
         where TRoleClaim : IdentityRoleClaim<TKey>
         where TUserToken : IdentityUserToken<TKey>
```

您也可以 Identity 在沒有角色的情況下使用 (只有宣告) ，在這種情況下 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> 應該使用類別：

```csharp
// Uses the built-in non-role Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityUserContext<TUser>
    : IdentityUserContext<TUser, string>
        where TUser : IdentityUser
{
}

// Uses the built-in non-role Identity types except with a custom User type
// The key type is defined by TKey
public class IdentityUserContext<TUser, TKey> : IdentityUserContext<
    TUser, TKey, IdentityUserClaim<TKey>, IdentityUserLogin<TKey>,
    IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments, with no roles
// The key type is defined by TKey
public abstract class IdentityUserContext<
    TUser, TKey, TUserClaim, TUserLogin, TUserToken> : DbContext
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
        where TUserClaim : IdentityUserClaim<TKey>
        where TUserLogin : IdentityUserLogin<TKey>
        where TUserToken : IdentityUserToken<TKey>
{
}
```

## <a name="customize-the-model"></a>自訂模型

模型自訂的起點是衍生自適當的內容類型。 請參閱 [模型泛型型別](#model-generic-types) 一節。 這種內容類型通常是 `ApplicationDbContext` 由 ASP.NET Core 範本所建立。

內容用來設定模型的方式有兩種：

* 為泛型型別參數提供實體和索引鍵類型。
* 覆寫 `OnModelCreating` 以修改這些類型的對應。

覆寫時 `OnModelCreating` ， `base.OnModelCreating` 應該先呼叫，然後再呼叫覆寫設定。 EF Core 通常會有一個設定的最後一個獲勝原則。 例如，如果 `ToTable` 實體類型的方法先以一個資料表名稱呼叫，然後再以不同的資料表名稱再次呼叫，則會使用第二個呼叫中的資料表名稱。

### <a name="custom-user-data"></a>自訂使用者資料

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

[自訂使用者資料](xref:security/authentication/add-user-data) 的支援方式是繼承自 `IdentityUser` 。 建議您將此類型命名為 `ApplicationUser` ：

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

使用 `ApplicationUser` 類型作為內容的泛型引數：

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
    }
}
```

在類別中不需要覆寫 `OnModelCreating` `ApplicationDbContext` 。 EF Core 依照 `CustomTag` 慣例對應屬性。 但是，資料庫需要更新才能建立新的資料 `CustomTag` 行。 若要建立資料行，請加入遷移，然後更新資料庫，如[ Identity 和 EF Core 遷移](#identity-and-ef-core-migrations)所述。

更新 *Pages/Shared/_LoginPartial* ，並 `IdentityUser` 以 `ApplicationUser` 下列內容取代：

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

更新 *區域/ Identity / Identity HostingStartup.cs* `Startup.ConfigureServices` ，或取代 `IdentityUser` 為 `ApplicationUser` 。

```csharp
services.AddIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

在 ASP.NET Core 2.1 或更新版本中， Identity 是以 Razor 類別庫的形式提供。 如需詳細資訊，請參閱<xref:security/authentication/scaffold-identity>。 因此，上述程式碼需要呼叫 <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> 。 如果 Identity scaffolder 是用來將檔案新增 Identity 至專案，請移除的呼叫 `AddDefaultUI` 。 如需詳細資訊，請參閱

* [支架 Identity](xref:security/authentication/scaffold-identity)
* [新增、下載及刪除自訂使用者資料 Identity](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a>變更主要金鑰類型

在資料庫建立後，PK 資料行資料類型的變更會對許多資料庫系統造成問題。 變更 PK 通常需要卸載再重新建立資料表。 因此，當建立資料庫時，應該在初始的遷移中指定金鑰類型。

請遵循下列步驟來變更 PK 類型：

1. 如果資料庫是在 PK 變更之前建立的，請執行 `Drop-Database` (PMC) 或 `dotnet ef database drop` ( .NET Core CLI) 來刪除它。
2. 確認刪除資料庫之後，請移除 (PMC) 的初始遷移， `Remove-Migration` 或 `dotnet ef migrations remove` ( .NET Core CLI) 。
3. 將 `ApplicationDbContext` 類別更新為衍生自 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603> 。 為指定新的索引鍵類型 `TKey` 。 例如，若要使用 `Guid` 金鑰類型：

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<IdentityUser<Guid>, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    ::: moniker range=">= aspnetcore-2.0"

    在上述程式碼中，必須指定泛型類別， <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> 才能使用新的索引鍵類型。

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    在上述程式碼中，必須指定泛型類別， <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> 才能使用新的索引鍵類型。

    ::: moniker-end

    `Startup.ConfigureServices` 必須更新為使用一般使用者：

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<IdentityUser<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

4. 如果 `ApplicationUser` 正在使用自訂類別，請更新要繼承自的類別 `IdentityUser` 。 例如：

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    更新 `ApplicationDbContext` 以參考自訂 `ApplicationUser` 類別：

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<ApplicationUser, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    在中加入服務時，註冊自訂資料庫內容類別 Identity `Startup.ConfigureServices` ：

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    主要索引鍵的資料類型是藉由分析 [DbCoNtext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 物件來推斷。

    在 ASP.NET Core 2.1 或更新版本中， Identity 是以 Razor 類別庫的形式提供。 如需詳細資訊，請參閱<xref:security/authentication/scaffold-identity>。 因此，上述程式碼需要呼叫 <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> 。 如果 Identity scaffolder 是用來將檔案新增 Identity 至專案，請移除的呼叫 `AddDefaultUI` 。

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    主要索引鍵的資料類型是藉由分析 [DbCoNtext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 物件來推斷。

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>方法會接受 `TKey` 表示主鍵資料類型的類型。

    ::: moniker-end

5. 如果 `ApplicationRole` 正在使用自訂類別，請更新要繼承自的類別 `IdentityRole<TKey>` 。 例如：

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    更新 `ApplicationDbContext` 以參考自訂 `ApplicationRole` 類別。 例如，下列類別會參考自訂 `ApplicationUser` 和自訂 `ApplicationRole` ：

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    在中加入服務時，註冊自訂資料庫內容類別 Identity `Startup.ConfigureServices` ：

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    主要索引鍵的資料類型是藉由分析 [DbCoNtext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 物件來推斷。

    在 ASP.NET Core 2.1 或更新版本中， Identity 是以 Razor 類別庫的形式提供。 如需詳細資訊，請參閱<xref:security/authentication/scaffold-identity>。 因此，上述程式碼需要呼叫 <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> 。 如果 Identity scaffolder 是用來將檔案新增 Identity 至專案，請移除的呼叫 `AddDefaultUI` 。

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    在中加入服務時，註冊自訂資料庫內容類別 Identity `Startup.ConfigureServices` ：

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    主要索引鍵的資料類型是藉由分析 [DbCoNtext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 物件來推斷。

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    在中加入服務時，註冊自訂資料庫內容類別 Identity `Startup.ConfigureServices` ：

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>方法會接受 `TKey` 表示主鍵資料類型的類型。

    ::: moniker-end

### <a name="add-navigation-properties"></a>新增導覽屬性

變更關聯性的模型設定，會比進行其他變更更為困難。 必須小心取代現有的關聯性，而不是建立新的額外關聯性。 尤其是，變更的關聯性必須指定與現有關聯性 (FK) 屬性相同的外鍵。 例如，和之間的關聯 `Users` 性 `UserClaims` 依預設會依照下列方式指定：

```csharp
builder.Entity<TUser>(b =>
{
    // Each User can have many UserClaims
    b.HasMany<TUserClaim>()
     .WithOne()
     .HasForeignKey(uc => uc.UserId)
     .IsRequired();
});
```

此關聯性的 FK 會指定為 `UserClaim.UserId` 屬性。 `HasMany` 和 `WithOne` 會在沒有引數的情況下呼叫，以建立沒有導覽屬性的關聯性。

將導覽屬性加入至 `ApplicationUser` ，以允許 `UserClaims` 使用者參考相關聯的：

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

的 `TKey` `IdentityUserClaim<TKey>` 是為使用者的 PK 指定的型別。 在此情況下， `TKey` 是 `string` 因為正在使用預設值。 它 **不** 是實體類型的 PK 類型 `UserClaim` 。

現在導覽屬性存在，必須在中設定 `OnModelCreating` ：

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();
        });
    }
}
```

請注意，關聯性的設定與之前完全相同，只有在呼叫中指定的導覽屬性 `HasMany` 。

導覽屬性只存在於 EF 模型中，而不是資料庫。 由於關聯性的 FK 未變更，因此這種模型變更不需要更新資料庫。 這可以在進行變更之後新增遷移來檢查。 `Up`和 `Down` 方法是空的。

### <a name="add-all-user-navigation-properties"></a>新增所有使用者導覽屬性

使用上述的章節作為指引，下列範例會針對使用者上的所有關聯性設定單向導覽屬性：

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<IdentityUserRole<string>> UserRoles { get; set; }
}
```

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne()
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });
    }
}
```

### <a name="add-user-and-role-navigation-properties"></a>新增使用者和角色導覽屬性

使用上述的章節作為指引，下列範例會針對使用者和角色上的所有關聯性設定導覽屬性：

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        IdentityUserClaim<string>, ApplicationUserRole, IdentityUserLogin<string>,
        IdentityRoleClaim<string>, IdentityUserToken<string>>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();
        });

    }
}
```

注意：

* 此範例也包含 `UserRole` 聯結實體，這是將多對多關聯性從使用者導覽至角色所需的實體。
* 請記得變更導覽屬性的類型，以反映 `Application{...}` 目前正在使用的類型，而不是 `Identity{...}` 類型。
* 請記得 `Application{...}` 在泛型定義中使用 `ApplicationContext` 。

### <a name="add-all-navigation-properties"></a>新增所有導覽屬性

下列範例使用上述的區段做為指導方針，為所有實體類型上的所有關聯性設定導覽屬性：

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<ApplicationUserClaim> Claims { get; set; }
    public virtual ICollection<ApplicationUserLogin> Logins { get; set; }
    public virtual ICollection<ApplicationUserToken> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
    public virtual ICollection<ApplicationRoleClaim> RoleClaims { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserClaim : IdentityUserClaim<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationUserLogin : IdentityUserLogin<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationRoleClaim : IdentityRoleClaim<string>
{
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserToken : IdentityUserToken<string>
{
    public virtual ApplicationUser User { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        ApplicationUserClaim, ApplicationUserRole, ApplicationUserLogin,
        ApplicationRoleClaim, ApplicationUserToken>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne(e => e.User)
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne(e => e.User)
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne(e => e.User)
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();

            // Each Role can have many associated RoleClaims
            b.HasMany(e => e.RoleClaims)
                .WithOne(e => e.Role)
                .HasForeignKey(rc => rc.RoleId)
                .IsRequired();
        });
    }
}
```

### <a name="use-composite-keys"></a>使用複合索引鍵

上述各節示範了變更模型中所使用的索引鍵類型 Identity 。 Identity不支援或不建議將金鑰模型變更為使用複合索引鍵。 使用複合索引鍵包含 Identity 變更 Identity 管理員程式碼與模型互動的方式。 這項自訂已超出本檔的範圍。

### <a name="change-tablecolumn-names-and-facets"></a>變更資料表/資料行名稱和 facet

若要變更資料表和資料行的名稱，請呼叫 `base.OnModelCreating` 。 然後，新增設定以覆寫任何預設值。 例如，若要變更所有資料表的名稱 Identity ：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.ToTable("MyUsers");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.ToTable("MyUserClaims");
    });

    modelBuilder.Entity<IdentityUserLogin<string>>(b =>
    {
        b.ToTable("MyUserLogins");
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.ToTable("MyUserTokens");
    });

    modelBuilder.Entity<IdentityRole>(b =>
    {
        b.ToTable("MyRoles");
    });

    modelBuilder.Entity<IdentityRoleClaim<string>>(b =>
    {
        b.ToTable("MyRoleClaims");
    });

    modelBuilder.Entity<IdentityUserRole<string>>(b =>
    {
        b.ToTable("MyUserRoles");
    });
}
```

這些範例會使用預設 Identity 類型。 如果使用應用程式類型（例如 `ApplicationUser` ），請設定該類型，而不是預設類型。

下列範例會變更一些資料行名稱：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(e => e.Email).HasColumnName("EMail");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.Property(e => e.ClaimType).HasColumnName("CType");
        b.Property(e => e.ClaimValue).HasColumnName("CValue");
    });
}
```

某些類型的資料庫資料行可以使用特定 *facet* 進行設定 (例如，允許的最大 `string` 長度) 。 下列範例會針對模型中的數個屬性，設定資料行的最大長度 `string` ：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(u => u.UserName).HasMaxLength(128);
        b.Property(u => u.NormalizedUserName).HasMaxLength(128);
        b.Property(u => u.Email).HasMaxLength(128);
        b.Property(u => u.NormalizedEmail).HasMaxLength(128);
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.Property(t => t.LoginProvider).HasMaxLength(128);
        b.Property(t => t.Name).HasMaxLength(128);
    });
}
```

### <a name="map-to-a-different-schema"></a>對應至不同的架構

架構在不同的資料庫提供者之間可能會有不同的行為。 針對 SQL Server，預設值是在 *dbo* 架構中建立所有資料表。 您可以在不同的架構中建立資料表。 例如：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a>消極式載入

在本節中，會加入模型中延遲載入 proxy 的支援 Identity 。 消極式載入會很有用，因為它可讓您在不先確保載入屬性的情況下使用導覽屬性。

實體類型可以用數種方式進行消極式載入，如 [EF Core 檔](/ef/core/querying/related-data#lazy-loading)中所述。 為了簡單起見，請使用消極式載入 proxy，此 proxy 需要：

* [Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/)安裝套件。
* 對 <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> [ \<TContext> AddDbCoNtext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)內的呼叫。
* 具有導覽屬性的公用實體類型 `public virtual` 。

下列範例將示範如何 `UseLazyLoadingProxies` 在中呼叫 `Startup.ConfigureServices` ：

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

如需將導覽屬性加入至實體類型的指引，請參閱上述範例。

## <a name="additional-resources"></a>其他資源

* <xref:security/authentication/scaffold-identity>

::: moniker-end
