---
title: 使用 ASP.NET Core 中的特定配置進行授權
author: rick-anderson
description: 本文說明如何在使用多個驗證方法時，將身分識別限制為特定的配置。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: c4cbec1b829fb8fd47f7b6924b6870bd5dd7097d
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057300"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a>使用 ASP.NET Core 中的特定配置進行授權

如需 ASP.NET Core 中的驗證架構簡介，請參閱 [驗證配置](xref:security/authentication/index#authentication-scheme)。

在某些案例中，例如 (Spa) 的單一頁面應用程式，通常會使用多個驗證方法。 例如，應用程式可能會使用 cookie 驗證來登入，並針對 JavaScript 要求進行 JWT 持有人驗證。 在某些情況下，應用程式可能會有多個驗證處理常式的實例。 例如，有兩個 cookie 處理常式，其中一個包含基本身分識別，而另一個處理常式在已觸發多重要素驗證 (MFA) 時建立。 可能會觸發 MFA，因為使用者要求的作業需要額外的安全性。 如需在使用者要求需要 MFA 的資源時強制執行 MFA 的詳細資訊，請參閱 GitHub 問題 [保護區段與 mfa](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195)。

驗證方案是在驗證期間設定驗證服務時所命名。 例如：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .AddCookie(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

在上述程式碼中，已加入兩個驗證處理常式：一個用於 cookie s，另一個用於持有人。

>[!NOTE]
>指定預設配置 `HttpContext.User` 會導致屬性設定為該身分識別。 如果不想要該行為，請叫用無參數形式的來停用該行為 `AddAuthentication` 。

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a>選取具有授權屬性的配置

在授權的時候，應用程式會指出要使用的處理常式。 藉由將驗證配置的逗號分隔清單傳遞給，以選取應用程式將授權的處理常式 `[Authorize]` 。 `[Authorize]`屬性會指定要使用的驗證配置或配置，而不論是否已設定預設值。 例如：

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

在上述範例中， cookie 和持有人處理常式都會執行，而且有機會建立和附加目前使用者的身分識別。 只要指定單一配置，就會執行對應的處理常式。

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

在上述程式碼中，只會執行具有「持有人」配置的處理常式。 系統會忽略任何以任何身分識別為基礎的身分識別 cookie 。

## <a name="selecting-the-scheme-with-policies"></a>選取具有原則的配置

如果您想要在 [原則](xref:security/authorization/policies)中指定所需的配置，您可以在 `AuthenticationSchemes` 新增原則時設定集合：

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

在上述範例中，"Over18" 原則只會針對「持有人」處理常式所建立的身分識別執行。 藉由設定 `[Authorize]` 屬性的屬性來使用原則 `Policy` ：

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a>使用多個驗證配置

有些應用程式可能需要支援多種類型的驗證。 例如，您的應用程式可能會從 Azure Active Directory 和使用者資料庫驗證使用者。 另一個範例是從 Active Directory 同盟服務和 Azure Active Directory B2C 驗證使用者的應用程式。 在此情況下，應用程式應該接受來自數個簽發者的 JWT 持有人權杖。

新增您想要接受的所有驗證方案。 例如，下列程式碼會 `Startup.ConfigureServices` 新增具有不同簽發者的兩個 JWT 持有人驗證配置：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> 只有一個 JWT 持有人驗證會使用預設的驗證配置來註冊 `JwtBearerDefaults.AuthenticationScheme` 。 您必須使用唯一的驗證配置來註冊額外的驗證。

下一步是更新預設授權原則，以接受這兩種驗證配置。 例如：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

由於預設授權原則遭到覆寫，因此可以 `[Authorize]` 在控制器中使用屬性。 然後，控制器會接受第一個或第二個簽發者所發出的 JWT 要求。

::: moniker-end

請參閱 [此 GitHub](https://github.com/dotnet/aspnetcore/issues/26002) 有關使用多個驗證配置的問題。
