---
title: ASP.NET Core 驗證的總覽
author: mjrousos
description: 瞭解 ASP.NET Core 中的驗證。
ms.author: riande
ms.custom: mvc
ms.date: 1/24/2021
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
uid: security/authentication/index
ms.openlocfilehash: 72036e9c4c92ee5dd82ac4a67e766fb0e5c8f924
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057287"
---
# <a name="overview-of-aspnet-core-authentication"></a>ASP.NET Core 驗證的總覽

由 [Mike Rousos](https://github.com/mjrousos)

驗證是決定使用者身分識別的程式。 [授權](xref:security/authorization/introduction) 是判斷使用者是否有權存取資源的程式。 在 ASP.NET Core 中，驗證是由 `IAuthenticationService` 驗證 [中介軟體](xref:fundamentals/middleware/index)所使用的來處理。 驗證服務會使用已註冊的驗證處理常式來完成驗證相關的動作。 驗證相關動作的範例包括：

* 驗證使用者。
* 當未驗證的使用者嘗試存取受限制的資源時回應。

註冊的驗證處理常式和其設定選項稱為「配置」。

驗證配置的指定方式是在中註冊驗證服務 `Startup.ConfigureServices` ：

* 藉由在呼叫 (（例如或）之後呼叫配置特定的擴充方法 `services.AddAuthentication` `AddJwtBearer` ，例如 `AddCookie`) 。 這些擴充方法會使用 [AuthenticationBuilder AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) ，以適當的設定來註冊配置。
* 通常會透過直接呼叫 [AuthenticationBuilder AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) 。

例如，下列程式碼會註冊的驗證服務和處理常式， cookie 以及 JWT 持有人驗證配置：

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

`AddAuthentication`參數 `JwtBearerDefaults.AuthenticationScheme` 是未要求特定配置時，預設要使用的配置名稱。

如果使用了多個配置，則 (的授權原則或授權屬性) 可以 [指定驗證配置 () 或所依賴的配置 ](xref:security/authorization/limitingidentitybyscheme) ，以驗證使用者。 在上述範例中，您 cookie 可以藉由預設指定其名稱 (來使用驗證配置 `CookieAuthenticationDefaults.AuthenticationScheme` ，但在呼叫) 時可以提供不同的名稱 `AddCookie` 。

在某些情況下，的呼叫會 `AddAuthentication` 由其他擴充方法自動進行。 例如，在使用時 [ASP.NET Core Identity](xref:security/authentication/identity) ， `AddAuthentication` 會在內部呼叫。

藉 `Startup.Configure` 由呼叫 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 應用程式上的擴充方法，即可在中新增驗證中介軟體 `IApplicationBuilder` 。 呼叫會 `UseAuthentication` 註冊使用先前註冊之驗證配置的中介軟體。 在 `UseAuthentication` 與要驗證之使用者相依的任何中介軟體之前呼叫。 使用端點路由時，的呼叫 `UseAuthentication` 必須移至：

* 之後 `UseRouting` ，您就可以使用路由資訊進行驗證決策。
* 之前 `UseEndpoints` ，會先驗證使用者，然後再存取端點。

## <a name="authentication-concepts"></a>驗證概念

驗證負責提供 <xref:System.Security.Claims.ClaimsPrincipal> 授權以進行許可權決策。 有多個驗證配置方法可選取哪一個驗證處理常式負責產生一組正確的宣告：

  * [驗證配置](xref:security/authorization/limitingidentitybyscheme)，也將在下一節中討論。
  * 預設的驗證配置，在下一節中討論。
  * 直接設定 [HttpCoNtext. 使用者](xref:Microsoft.AspNetCore.Http.HttpContext.User)。

不會自動探查架構。 如果未指定預設配置，則必須在授權屬性中指定配置，否則會擲回下列錯誤：

  InvalidOperationException：未指定 authenticationScheme，而且找不到 DefaultAuthenticateScheme。 您可以使用 AddAuthentication (string defaultScheme) 或 AddAuthentication (Action &lt; AuthenticationOptions configureOptions) 來設定預設配置 &gt; 。

### <a name="authentication-scheme"></a>驗證配置

[驗證配置](xref:security/authorization/limitingidentitybyscheme)可以選取哪些驗證處理常式負責產生正確的宣告集。 如需詳細資訊，請參閱 [使用特定配置進行授權](xref:security/authorization/limitingidentitybyscheme)。

驗證配置是對應至的名稱：

* 驗證處理常式。
* 用於設定該特定處理常式實例的選項。

配置可作為一種機制，用來參考相關處理常式的驗證、挑戰和禁止行為。 例如，授權原則可以使用配置名稱來指定 (或配置) 應該用來驗證使用者的驗證配置。 設定驗證時，通常會指定預設的驗證配置。 除非資源要求特定配置，否則會使用預設配置。 也可以：

* 指定要用於驗證、挑戰和禁止動作的不同預設配置。
* 使用 [原則](xref:security/authentication/policyschemes)配置將多個配置結合成一個。

### <a name="authentication-handler"></a>驗證處理常式

驗證處理常式：

* 是實作為配置行為的型別。
* 衍生自 <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> 或 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> 。
* 擁有驗證使用者的主要責任。

根據驗證配置的設定和傳入的要求內容，驗證處理常式：

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket>如果驗證成功，則表示使用者身分識別的結構物件。
* 如果驗證失敗，則傳回「沒有結果」或「失敗」。
* 在使用者嘗試存取資源時，有挑戰和禁止動作的方法：
  * 他們未獲授權，無法存取 (禁止) 。
  * 未經驗證 (挑戰) 。

### <a name="authenticate"></a>Authenticate

驗證配置的驗證動作會負責根據要求內容來建立使用者的身分識別。 它會傳回 <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> ，指出驗證是否成功，以及使用者在驗證票證中的身分識別。 請參閱 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>。 驗證範例包括：

* cookie從建立使用者身分識別的驗證配置 cookie 。
* JWT 持有人配置還原序列化和驗證 JWT 持有人權杖，以建立使用者的身分識別。

### <a name="challenge"></a>挑戰

當未驗證的使用者要求需要驗證的端點時，會在授權中叫用驗證挑戰。 例如，當匿名使用者要求受限的資源，或按一下登入連結時，就會發出驗證挑戰。 授權會使用指定的驗證配置 (s) 來叫用挑戰，如果未指定，則會使用預設值。 請參閱 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>。 驗證挑戰範例包括：

* 將 cookie 使用者重新導向至登入頁面的驗證配置。
* JWT 持有人配置傳回401結果與 `www-authenticate: bearer` 標頭。

挑戰動作應該讓使用者知道要使用哪個驗證機制來存取要求的資源。

### <a name="forbid"></a>禁止

當已驗證的使用者嘗試存取不允許存取的資源時，授權會呼叫驗證配置的禁止動作。 請參閱 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>。 驗證禁止的範例包括：
* 將 cookie 使用者重新導向至表示禁止存取之頁面的驗證配置。
* JWT 持有人配置傳回403結果。
* 重新導向至使用者可要求存取資源之頁面的自訂驗證配置。

禁止的動作可讓使用者知道：

* 它們是經過驗證的。
* 他們不允許存取要求的資源。

請參閱下列連結以取得挑戰和禁止的差異：

* [使用操作資源處理常式的挑戰和禁止](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler)。
* [挑戰與禁止之間的差異](xref:security/authorization/secure-data#challenge)。

## <a name="authentication-providers-per-tenant"></a>每個租使用者的驗證提供者

ASP.NET Core framework 沒有內建解決方案可進行多租使用者驗證。
雖然客戶當然可以使用內建功能來撰寫，但我們建議客戶基於此目的來查看 [Orchard Core](https://www.orchardcore.net/) 。

Orchard Core 為：

* 以 ASP.NET Core 為基礎的開放原始碼模組化和多租使用者應用程式架構。
* 內容管理系統 (CMS) 以該應用程式架構為基礎。

如需每個租使用者的驗證提供者範例，請參閱 [Orchard Core](https://github.com/OrchardCMS/OrchardCore) 來源。

## <a name="additional-resources"></a>其他資源

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
* [全球需要經過驗證的使用者](xref:security/authorization/secure-data#rau)
* [使用多個驗證配置的 GitHub 問題](https://github.com/dotnet/aspnetcore/issues/26002)
