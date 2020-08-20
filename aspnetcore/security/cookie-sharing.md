---
title: cookie在 ASP.NET apps 之間共用驗證
author: rick-anderson
description: 瞭解如何 cookie 在 ASP.NET 4.x 和 ASP.NET Core apps 之間共用驗證。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
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
uid: security/cookie-sharing
ms.openlocfilehash: 6ac808d11790ae27e82606b442ff215d95b93e41
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631364"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a>cookie在 ASP.NET apps 之間共用驗證

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

網站通常包含個別的 web 應用程式一起運作。 若要提供單一登入 (SSO) 體驗，網站內的 web apps 必須共用驗證 cookie 。 為了支援此案例，資料保護堆疊允許共用 Katana cookie authentication 和 ASP.NET Core cookie 驗證票證。

在下列範例中：

* 驗證 cookie 名稱會設定為的通用值 `.AspNet.SharedCookie` 。
* `AuthenticationType`會 `Identity.Application` 明確或依預設設定為。
* 一般的應用程式名稱可用來讓資料保護系統共用 () 的資料保護金鑰 `SharedCookieApp` 。
* `Identity.Application` 會用來做為驗證配置。 無論使用哪種配置，都必須以一致的方式在共用應用程式 *內和* 共用的應用程式中使用， cookie 以做為預設配置或明確設定它。 配置是在加密和解密時使用 cookie ，因此必須跨應用程式使用一致的配置。
* 使用一般的 [資料保護金鑰](xref:security/data-protection/implementation/key-management) 儲存位置。
  * 在 ASP.NET Core apps 中， <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 是用來設定金鑰儲存位置。
  * 在 .NET Framework 應用程式中， Cookie 驗證中介軟體會使用的實作為 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> 。 `DataProtectionProvider` 提供用於加密和解密驗證承載資料的資料保護服務 cookie 。 此 `DataProtectionProvider` 實例會與應用程式其他元件所使用的資料保護系統隔離。 [DataProtectionProvider 建立 (DirectoryInfo，Action \<IDataProtectionBuilder>) 接受， ](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) <xref:System.IO.DirectoryInfo> 以指定資料保護金鑰儲存的位置。
* `DataProtectionProvider` 需要 [AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet 套件：
  * 在 ASP.NET Core 2.x 應用程式中，參考 [AspNetCore 應用程式中繼套件](xref:fundamentals/metapackage-app)。
  * 在 .NET Framework 應用程式中，將套件參考新增至 [AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)。
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 設定一般應用程式名稱。

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a>共用驗證 cookie s ASP.NET Core Identity

使用 ASP.NET Core Identity 時：

* 您必須在應用程式之間共用資料保護金鑰和應用程式名稱。 在下列範例中，會提供常見的金鑰儲存位置給 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 方法。 您 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 可以使用來設定通用的共用應用程式名稱 (`SharedCookieApp` 在下列範例中) 。 如需詳細資訊，請參閱<xref:security/data-protection/configuration/overview>。
* 使用 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> 擴充方法來設定的資料保護服務 cookie 。
* 預設驗證類型為 `Identity.Application` 。

在 `Startup.ConfigureServices` 中：

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a>cookie無需共用驗證ASP.NET Core Identity

直接使用時若 cookie 沒有 ASP.NET Core Identity ，請在中設定資料保護和驗證 `Startup.ConfigureServices` 。 在下列範例中，驗證類型設為 `Identity.Application` ：

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a>cookie跨不同的基底路徑共用

驗證會 cookie 使用[HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)作為其預設值[ Cookie 。路徑](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path)。 如果 cookie 必須在不同的基底路徑之間共用應用程式，則 `Path` 必須覆寫：

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a>cookie跨子域共用

裝載跨子域共用的應用程式時 cookie ，請在中指定通用網域[ Cookie 。網域](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain)屬性。 若要 cookie 在的應用程式之間共用 `contoso.com` ，例如 `first_subdomain.contoso.com` 和 `second_subdomain.contoso.com` ，請將指定 `Cookie.Domain` 為 `.contoso.com` ：

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>加密待用資料保護金鑰

對於生產環境部署，請將設定 `DataProtectionProvider` 為使用 DPAPI 或 X509Certificate 來加密待用的金鑰。 如需詳細資訊，請參閱<xref:security/data-protection/implementation/key-encryption-at-rest>。 在下列範例中，會提供憑證指紋給 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> ：

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a>cookie在 ASP.NET 4.x 和 ASP.NET Core apps 之間共用驗證

您可以設定使用 Katana Authentication 中介軟體的 ASP.NET 4.x 應用程式， Cookie 以產生 cookie 與 ASP.NET Core Cookie authentication 中介軟體相容的驗證。 這可讓您在數個步驟中升級大型網站的個別應用程式，同時提供整個網站的順暢 SSO 體驗。

當應用程式使用 Katana Cookie Authentication 中介軟體時，它會呼叫 `UseCookieAuthentication` 專案的 *Startup.Auth.cs* 檔。 使用 Visual Studio 2013 和更新版本建立的 ASP.NET 4.x web 應用程式專案預設會使用 Katana Cookie Authentication 中介軟體。 雖然 `UseCookieAuthentication` 已淘汰且不支援 ASP.NET Core 的應用程式，但 `UseCookieAuthentication` 在使用 Katana Authentication 中介軟體的 ASP.NET 4.x 應用程式中呼叫 Cookie 是有效的。

ASP.NET 4.x 應用程式必須以 .NET Framework 4.5.1 或更新版本為目標。 否則，將無法安裝所需的 NuGet 套件。

若要 cookie 在 ASP.NET 4.x 應用程式和 ASP.NET Core 應用程式之間共用驗證，請依照 [ cookie ASP.NET Core Apps 之間的共用驗證](#share-authentication-cookies-with-aspnet-core-identity) 一節中所述，設定 ASP.NET Core 應用程式，然後設定 ASP.NET 4.x 應用程式，如下所示。

確認應用程式的套件已更新至最新版本。 將 [Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) 套件安裝到每個 ASP.NET 4.x 應用程式。

找出並修改呼叫 `UseCookieAuthentication` ：

* 變更 cookie 名稱，使其符合 Cookie 範例) 中 ASP.NET Core Authentication 中介軟體 (所使用的名稱 `.AspNet.SharedCookie` 。
* 在下列範例中，驗證類型設為 `Identity.Application` 。
* 提供 `DataProtectionProvider` 初始化為一般資料保護金鑰儲存位置的實例。
* 確認應用程式名稱已設定為在 cookie 範例)  (共用驗證的所有應用程式所使用的通用應用程式名稱 `SharedCookieApp` 。

如果未設定 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` 和 `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` ，請將設定 <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> 為可區別唯一使用者的宣告。

*App_Start/startup.auth.cs*：

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

當產生使用者識別時，驗證類型 (`Identity.Application`) 必須符合 `AuthenticationType` `UseCookieAuthentication` *App_Start/startup.auth.cs*中的 set with 所定義的類型。

*模型/ IdentityModels.cs*：

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a>使用一般使用者資料庫

當應用程式使用相同 Identity 的) 版本 (相同的架構時 Identity ，請確認 Identity 每個應用程式的系統都指向相同的使用者資料庫。 否則，當身分識別系統嘗試比對驗證中的資訊 cookie 與其資料庫中的資訊時，就會在執行時間產生失敗。

當 Identity 架構不同的應用程式時，通常是因為應用程式使用不同的 Identity 版本，在 Identity 不需要重新對應和新增其他應用程式架構中的資料行的情況下，不可能共用以最新版本為基礎的通用資料庫 Identity 。 將其他應用程式升級為使用最新版本通常會更有效率， Identity 讓應用程式可以共用一般資料庫。

## <a name="additional-resources"></a>其他資源

* <xref:host-and-deploy/web-farm>
