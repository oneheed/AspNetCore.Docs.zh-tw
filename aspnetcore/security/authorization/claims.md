---
title: ASP.NET Core 中以宣告為基礎的授權
author: rick-anderson
description: 瞭解如何在 ASP.NET Core 應用程式中新增授權的宣告檢查。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authorization/claims
ms.openlocfilehash: d6317da6bca69b4c46d74a2f76d81af4059d1cd8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060270"
---
# <a name="claims-based-authorization-in-aspnet-core"></a><span data-ttu-id="e615c-103">ASP.NET Core 中以宣告為基礎的授權</span><span class="sxs-lookup"><span data-stu-id="e615c-103">Claims-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-claims-based"></a>

<span data-ttu-id="e615c-104">建立身分識別時，可能會指派一或多個由受信任的合作物件發出的宣告。</span><span class="sxs-lookup"><span data-stu-id="e615c-104">When an identity is created it may be assigned one or more claims issued by a trusted party.</span></span> <span data-ttu-id="e615c-105">宣告是一組名稱值組，代表主體是什麼，而不是主體可做的動作。</span><span class="sxs-lookup"><span data-stu-id="e615c-105">A claim is a name value pair that represents what the subject is, not what the subject can do.</span></span> <span data-ttu-id="e615c-106">例如，您可能有由本機駕駛授權授權單位簽發的驅動程式授權。</span><span class="sxs-lookup"><span data-stu-id="e615c-106">For example, you may have a driver's license, issued by a local driving license authority.</span></span> <span data-ttu-id="e615c-107">您的駕駛執照有您的出生日期。</span><span class="sxs-lookup"><span data-stu-id="e615c-107">Your driver's license has your date of birth on it.</span></span> <span data-ttu-id="e615c-108">在此情況下，宣告名稱為 `DateOfBirth` ，宣告值會是您的出生日期，例如， `8th June 1970` 簽發者會是駕駛授權授權單位。</span><span class="sxs-lookup"><span data-stu-id="e615c-108">In this case the claim name would be `DateOfBirth`, the claim value would be your date of birth, for example `8th June 1970` and the issuer would be the driving license authority.</span></span> <span data-ttu-id="e615c-109">宣告式授權最簡單的方式是檢查宣告的值，並允許依據該值來存取資源。</span><span class="sxs-lookup"><span data-stu-id="e615c-109">Claims based authorization, at its simplest, checks the value of a claim and allows access to a resource based upon that value.</span></span> <span data-ttu-id="e615c-110">例如，如果您想要存取晚上的俱樂部，授權程式可能是：</span><span class="sxs-lookup"><span data-stu-id="e615c-110">For example if you want access to a night club the authorization process might be:</span></span>

<span data-ttu-id="e615c-111">大門安全長會評估您出生日期的價值，以及他們是否信任簽發者 (駕駛授權授權單位) 再授與存取權。</span><span class="sxs-lookup"><span data-stu-id="e615c-111">The door security officer would evaluate the value of your date of birth claim and whether they trust the issuer (the driving license authority) before granting you access.</span></span>

<span data-ttu-id="e615c-112">身分識別可以包含多個具有多個值的宣告，而且可以包含多個相同類型的宣告。</span><span class="sxs-lookup"><span data-stu-id="e615c-112">An identity can contain multiple claims with multiple values and can contain multiple claims of the same type.</span></span>

## <a name="adding-claims-checks"></a><span data-ttu-id="e615c-113">新增宣告檢查</span><span class="sxs-lookup"><span data-stu-id="e615c-113">Adding claims checks</span></span>

<span data-ttu-id="e615c-114">宣告式授權檢查是宣告式的，開發人員會在其程式碼中，對控制器或控制器內的動作內嵌這些授權檢查、指定目前使用者必須擁有的宣告，並選擇性地指定宣告必須保留以存取所要求資源的值。</span><span class="sxs-lookup"><span data-stu-id="e615c-114">Claim based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying claims which the current user must possess, and optionally the value the claim must hold to access the requested resource.</span></span> <span data-ttu-id="e615c-115">宣告需求是以原則為基礎，開發人員必須建立並註冊表示宣告需求的原則。</span><span class="sxs-lookup"><span data-stu-id="e615c-115">Claims requirements are policy based, the developer must build and register a policy expressing the claims requirements.</span></span>

<span data-ttu-id="e615c-116">最簡單的宣告原則類型會尋找宣告是否存在，而且不會檢查值。</span><span class="sxs-lookup"><span data-stu-id="e615c-116">The simplest type of claim policy looks for the presence of a claim and doesn't check the value.</span></span>

<span data-ttu-id="e615c-117">首先，您必須建立並註冊原則。</span><span class="sxs-lookup"><span data-stu-id="e615c-117">First you need to build and register the policy.</span></span> <span data-ttu-id="e615c-118">這會發生在授權服務設定中，通常會 `ConfigureServices()` 在您的 *Startup.cs* 檔案中納入。</span><span class="sxs-lookup"><span data-stu-id="e615c-118">This takes place as part of the Authorization service configuration, which normally takes part in `ConfigureServices()` in your *Startup.cs* file.</span></span>

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

<span data-ttu-id="e615c-119">在此情況下，此 `EmployeeOnly` 原則會檢查目前身分識別的宣告是否存在 `EmployeeNumber` 。</span><span class="sxs-lookup"><span data-stu-id="e615c-119">In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.</span></span>

<span data-ttu-id="e615c-120">然後，您可以使用屬性（ `Policy` attribute）上的屬性（property） `AuthorizeAttribute` 來指定原則名稱。</span><span class="sxs-lookup"><span data-stu-id="e615c-120">You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

<span data-ttu-id="e615c-121">`AuthorizeAttribute`屬性可套用至整個控制器，在此例中，只允許符合原則的身分識別存取控制器上的任何動作。</span><span class="sxs-lookup"><span data-stu-id="e615c-121">The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

<span data-ttu-id="e615c-122">如果您有受屬性保護的控制器 `AuthorizeAttribute` ，但您想要允許匿名存取特定動作，請套用 `AllowAnonymousAttribute` 屬性。</span><span class="sxs-lookup"><span data-stu-id="e615c-122">If you have a controller that's protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.</span></span>

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

<span data-ttu-id="e615c-123">大部分的宣告都有值。</span><span class="sxs-lookup"><span data-stu-id="e615c-123">Most claims come with a value.</span></span> <span data-ttu-id="e615c-124">您可以在建立原則時，指定允許值的清單。</span><span class="sxs-lookup"><span data-stu-id="e615c-124">You can specify a list of allowed values when creating the policy.</span></span> <span data-ttu-id="e615c-125">下列範例只有員工編號為1、2、3、4或5的員工才能成功。</span><span class="sxs-lookup"><span data-stu-id="e615c-125">The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.</span></span>

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
### <a name="add-a-generic-claim-check"></a><span data-ttu-id="e615c-126">新增一般宣告檢查</span><span class="sxs-lookup"><span data-stu-id="e615c-126">Add a generic claim check</span></span>

<span data-ttu-id="e615c-127">如果宣告值不是單一值或需要轉換，請使用 [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion)。</span><span class="sxs-lookup"><span data-stu-id="e615c-127">If the claim value isn't a single value or a transformation is required, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span></span> <span data-ttu-id="e615c-128">如需詳細資訊，請參閱 [使用 func 來完成原則](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy)。</span><span class="sxs-lookup"><span data-stu-id="e615c-128">For more information, see [Use a func to fulfill a policy](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy).</span></span>

## <a name="multiple-policy-evaluation"></a><span data-ttu-id="e615c-129">多個原則評估</span><span class="sxs-lookup"><span data-stu-id="e615c-129">Multiple Policy Evaluation</span></span>

<span data-ttu-id="e615c-130">如果您將多個原則套用至控制器或動作，則在授與存取權之前，所有原則都必須通過。</span><span class="sxs-lookup"><span data-stu-id="e615c-130">If you apply multiple policies to a controller or action, then all policies must pass before access is granted.</span></span> <span data-ttu-id="e615c-131">例如：</span><span class="sxs-lookup"><span data-stu-id="e615c-131">For example:</span></span>

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

<span data-ttu-id="e615c-132">在上述範例中，任何符合原則的身分識別都 `EmployeeOnly` 可以存取 `Payslip` 動作，因為該原則會在控制器上強制執行。</span><span class="sxs-lookup"><span data-stu-id="e615c-132">In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller.</span></span> <span data-ttu-id="e615c-133">不過，若要呼叫此 `UpdateSalary` 動作，身分識別必須 *同時* 滿足 `EmployeeOnly` 原則和 `HumanResources` 原則。</span><span class="sxs-lookup"><span data-stu-id="e615c-133">However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.</span></span>

<span data-ttu-id="e615c-134">如果您想要更複雜的原則，例如接受出生日期、計算其存留期，然後檢查年齡是否為21或更舊，則您需要撰寫 [自訂原則處理常式](xref:security/authorization/policies)。</span><span class="sxs-lookup"><span data-stu-id="e615c-134">If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](xref:security/authorization/policies).</span></span>
