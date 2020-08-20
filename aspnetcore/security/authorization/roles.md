---
title: ASP.NET Core 中以角色為基礎的授權
author: rick-anderson
description: 瞭解如何藉由將角色傳遞給授權屬性，來限制 ASP.NET Core 控制器和動作的存取權。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/roles
ms.openlocfilehash: 7673bb006c344e6f9baaa3cd99c4bdb4a6fc2862
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635173"
---
# <a name="role-based-authorization-in-aspnet-core"></a>ASP.NET Core 中以角色為基礎的授權

<a name="security-authorization-role-based"></a>

建立身分識別時，可以屬於一個或多個角色。 例如，Tracy 可能屬於系統管理員和使用者角色，而 Scott 可能只屬於使用者角色。 如何建立和管理這些角色取決於授權進程的備份存放區。 角色是透過[ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal)類別上的[IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole)方法，對開發人員公開。

## <a name="adding-role-checks"></a>新增角色檢查

以角色為基礎的授權檢查是 &mdash; 開發人員在其程式碼中，針對控制器或控制器內的動作進行宣告的檢查，指定目前使用者必須為其成員的角色，才能存取要求的資源。

例如，下列程式碼會限制對屬於 `AdministrationController` 角色成員之使用者的任何動作的存取權 `Administrator` ：

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

您可以將多個角色指定為逗點分隔清單：

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

只有身為角色成員或角色成員的使用者可以存取此控制器 `HRManager` `Finance` 。

如果您套用多個屬性，則存取使用者必須是指定之所有角色的成員。下列範例要求使用者必須是 `PowerUser` 和角色的成員 `ControlPanelUser` 。

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

您可以在動作層級套用其他角色授權屬性，以進一步限制存取：

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

在先前的程式碼片段中， `Administrator` 角色或角色的成員 `PowerUser` 可以存取控制器和 `SetTime` 動作，但只有角色的成員 `Administrator` 可以存取 `ShutDown` 動作。

您也可以鎖定控制器，但允許對個別動作進行匿名、未經驗證的存取。

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

::: moniker range=">= aspnetcore-2.0"

若是 Razor 頁面， `AuthorizeAttribute` 可以透過下列其中一種方式套用：

* 使用 [慣例](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)，或
* 將套用 `AuthorizeAttribute` 至 `PageModel` 實例：

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public class UpdateModel : PageModel
{
    public ActionResult OnPost()
    {
    }
}
```

> [!IMPORTANT]
> 篩選屬性（包括 `AuthorizeAttribute` ）只能套用至 PageModel，而且無法套用至特定頁面處理常式方法。
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>以原則為基礎的角色檢查

角色需求也可以使用新的原則語法來表示，而開發人員會在啟動時，于授權服務設定中註冊原則。 這通常會出現在 `ConfigureServices()` *Startup.cs* 檔案中。

::: moniker range=">= aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

::: moniker range="< aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

原則是使用屬性 `Policy` （attribute）上的屬性（property）來套用 `AuthorizeAttribute` ：

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

如果您想要在需求中指定多個允許的角色，則可以將它們指定為 `RequireRole` 方法的參數：

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

這個範例會授權屬於 `Administrator` 、 `PowerUser` 或角色的使用者 `BackupAdministrator` 。

### <a name="add-role-services-to-no-locidentity"></a>將角色服務新增至 Identity

附加 [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 以新增角色服務：

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

