---
title: ASP.NET Core 中以宣告為基礎的授權
author: rick-anderson
description: 瞭解如何在 ASP.NET Core 應用程式中新增授權的宣告檢查。
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
uid: security/authorization/claims
ms.openlocfilehash: 0615e9f13b0eca7d7ac924d90ae2004e41a51586
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632599"
---
# <a name="claims-based-authorization-in-aspnet-core"></a>ASP.NET Core 中以宣告為基礎的授權

<a name="security-authorization-claims-based"></a>

建立身分識別時，可能會指派一或多個由受信任的合作物件發出的宣告。 宣告是一組名稱值組，代表主體是什麼，而不是主體可做的動作。 例如，您可能有由本機駕駛授權授權單位簽發的驅動程式授權。 您的駕駛執照有您的出生日期。 在此情況下，宣告名稱為 `DateOfBirth` ，宣告值會是您的出生日期，例如， `8th June 1970` 簽發者會是駕駛授權授權單位。 宣告式授權最簡單的方式是檢查宣告的值，並允許依據該值來存取資源。 例如，如果您想要存取晚上的俱樂部，授權程式可能是：

大門安全長會評估您出生日期的價值，以及他們是否信任簽發者 (駕駛授權授權單位) 再授與存取權。

身分識別可以包含多個具有多個值的宣告，而且可以包含多個相同類型的宣告。

## <a name="adding-claims-checks"></a>新增宣告檢查

宣告式授權檢查是宣告式的，開發人員會在其程式碼中，對控制器或控制器內的動作內嵌這些授權檢查、指定目前使用者必須擁有的宣告，並選擇性地指定宣告必須保留以存取所要求資源的值。 宣告需求是以原則為基礎，開發人員必須建立並註冊表示宣告需求的原則。

最簡單的宣告原則類型會尋找宣告是否存在，而且不會檢查值。

首先，您必須建立並註冊原則。 這會發生在授權服務設定中，通常會 `ConfigureServices()` 在您的 *Startup.cs* 檔案中納入。

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
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
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

在此情況下，此 `EmployeeOnly` 原則會檢查目前身分識別的宣告是否存在 `EmployeeNumber` 。

然後，您可以使用屬性（ `Policy` attribute）上的屬性（property） `AuthorizeAttribute` 來指定原則名稱。

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

`AuthorizeAttribute`屬性可套用至整個控制器，在此例中，只允許符合原則的身分識別存取控制器上的任何動作。

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

如果您有受屬性保護的控制器 `AuthorizeAttribute` ，但您想要允許匿名存取特定動作，請套用 `AllowAnonymousAttribute` 屬性。

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

大部分的宣告都有值。 您可以在建立原則時，指定允許值的清單。 下列範例只有員工編號為1、2、3、4或5的員工才能成功。

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
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
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end
### <a name="add-a-generic-claim-check"></a>新增一般宣告檢查

如果宣告值不是單一值或需要轉換，請使用 [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion)。 如需詳細資訊，請參閱 [使用 func 來完成原則](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy)。

## <a name="multiple-policy-evaluation"></a>多個原則評估

如果您將多個原則套用至控制器或動作，則在授與存取權之前，所有原則都必須通過。 例如：

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

在上述範例中，任何符合原則的身分識別都 `EmployeeOnly` 可以存取 `Payslip` 動作，因為該原則會在控制器上強制執行。 不過，若要呼叫此 `UpdateSalary` 動作，身分識別必須 *同時* 滿足 `EmployeeOnly` 原則和 `HumanResources` 原則。

如果您想要更複雜的原則，例如接受出生日期、計算其存留期，然後檢查年齡是否為21或更舊，則您需要撰寫 [自訂原則處理常式](xref:security/authorization/policies)。
