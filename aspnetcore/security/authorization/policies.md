---
title: ASP.NET Core 中以原則為基礎的授權
author: rick-anderson
description: 瞭解如何建立和使用授權原則處理常式，以在 ASP.NET Core 應用程式中強制執行授權需求。
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
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
uid: security/authorization/policies
ms.openlocfilehash: af282ec1f82b2ac31fd0b46b2406110e24e9211b
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424239"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="9a24c-103">ASP.NET Core 中以原則為基礎的授權</span><span class="sxs-lookup"><span data-stu-id="9a24c-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9a24c-104">在幕後，以 [角色為基礎的授權](xref:security/authorization/roles) 和 [宣告型授權](xref:security/authorization/claims) 會使用需求、需求處理常式和預先設定的原則。</span><span class="sxs-lookup"><span data-stu-id="9a24c-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="9a24c-105">這些組建區塊支援程式碼中授權評估的運算式。</span><span class="sxs-lookup"><span data-stu-id="9a24c-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="9a24c-106">結果是更豐富、可重複使用、可測試的授權結構。</span><span class="sxs-lookup"><span data-stu-id="9a24c-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="9a24c-107">授權原則包含一個或多個需求。</span><span class="sxs-lookup"><span data-stu-id="9a24c-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="9a24c-108">在方法中，它會註冊為授權服務設定的一部分 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="9a24c-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="9a24c-109">在上述範例中，會建立 "AtLeast21" 原則。</span><span class="sxs-lookup"><span data-stu-id="9a24c-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="9a24c-110">這項要求的 &mdash; 最小存留期為要求的參數所提供的單一需求。</span><span class="sxs-lookup"><span data-stu-id="9a24c-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="9a24c-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="9a24c-111">IAuthorizationService</span></span> 

<span data-ttu-id="9a24c-112">判斷授權是否成功的主要服務是 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ：</span><span class="sxs-lookup"><span data-stu-id="9a24c-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="9a24c-113">上述程式碼反白顯示 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)的兩種方法。</span><span class="sxs-lookup"><span data-stu-id="9a24c-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="9a24c-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> 是沒有方法的標記服務，以及用來追蹤授權是否成功的機制。</span><span class="sxs-lookup"><span data-stu-id="9a24c-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="9a24c-115">每個 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> 都負責檢查是否符合需求：</span><span class="sxs-lookup"><span data-stu-id="9a24c-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="9a24c-116">此 <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 類別是處理常式用來標示是否符合需求的程式：</span><span class="sxs-lookup"><span data-stu-id="9a24c-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="9a24c-117">下列程式碼顯示授權服務) 預設執行的簡化 (和批註批註：</span><span class="sxs-lookup"><span data-stu-id="9a24c-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="9a24c-118">下列程式碼顯示一般 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="9a24c-118">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.AddRazorPages();
}
```

<span data-ttu-id="9a24c-119">使用 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 或 `[Authorize(Policy = "Something")]` 進行授權。</span><span class="sxs-lookup"><span data-stu-id="9a24c-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="9a24c-120">將原則套用至 MVC 控制器</span><span class="sxs-lookup"><span data-stu-id="9a24c-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="9a24c-121">如果您使用 Razor 的是頁面，請參閱本檔中的 [將原則套用至 Razor 頁面](#apply-policies-to-razor-pages) 。</span><span class="sxs-lookup"><span data-stu-id="9a24c-121">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="9a24c-122">使用具有原則名稱的屬性，將原則套用至控制器 `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="9a24c-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="9a24c-123">例如：</span><span class="sxs-lookup"><span data-stu-id="9a24c-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="9a24c-124">將原則套用至 Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="9a24c-124">Apply policies to Razor Pages</span></span>

<span data-ttu-id="9a24c-125">Razor使用具有原則名稱的屬性，將原則套用至頁面 `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="9a24c-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="9a24c-126">例如：</span><span class="sxs-lookup"><span data-stu-id="9a24c-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="9a24c-127">原則 ***無法*** 在 Razor 頁面處理常式層級套用，它們必須套用至頁面。</span><span class="sxs-lookup"><span data-stu-id="9a24c-127">Policies can ***not*** be applied at the Razor Page handler level, they must be applied to the Page.</span></span>

<span data-ttu-id="9a24c-128">您可以 Razor 使用 [授權慣例](xref:security/authorization/razor-pages-authorization)，將原則套用至頁面。</span><span class="sxs-lookup"><span data-stu-id="9a24c-128">Policies can be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="9a24c-129">規格需求</span><span class="sxs-lookup"><span data-stu-id="9a24c-129">Requirements</span></span>

<span data-ttu-id="9a24c-130">授權需求是一組資料參數的集合，原則可用來評估目前的使用者主體。</span><span class="sxs-lookup"><span data-stu-id="9a24c-130">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="9a24c-131">在我們的「AtLeast21」原則中，要求是 &mdash; 最短存留期的單一參數。</span><span class="sxs-lookup"><span data-stu-id="9a24c-131">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="9a24c-132">需求會實 [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)，這是空的標記介面。</span><span class="sxs-lookup"><span data-stu-id="9a24c-132">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="9a24c-133">參數化的最小存留期需求可能會以下列方式執行：</span><span class="sxs-lookup"><span data-stu-id="9a24c-133">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="9a24c-134">如果授權原則包含多個授權需求，則所有需求都必須通過，原則評估才會成功。</span><span class="sxs-lookup"><span data-stu-id="9a24c-134">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="9a24c-135">換句話說，新增至單一授權原則的多個授權需求會以 **和** 為基礎來處理。</span><span class="sxs-lookup"><span data-stu-id="9a24c-135">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="9a24c-136">需求不需要有資料或屬性。</span><span class="sxs-lookup"><span data-stu-id="9a24c-136">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="9a24c-137">授權處理常式</span><span class="sxs-lookup"><span data-stu-id="9a24c-137">Authorization handlers</span></span>

<span data-ttu-id="9a24c-138">授權處理常式負責評估需求的屬性。</span><span class="sxs-lookup"><span data-stu-id="9a24c-138">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="9a24c-139">授權處理常式會根據提供的 [AuthorizationHandlerCoNtext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 來評估需求，以判斷是否允許存取。</span><span class="sxs-lookup"><span data-stu-id="9a24c-139">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="9a24c-140">需求可以有 [多個處理常式](#security-authorization-policies-based-multiple-handlers)。</span><span class="sxs-lookup"><span data-stu-id="9a24c-140">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="9a24c-141">處理常式可能會[繼承 \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)，其中 `TRequirement` 是要處理的需求。</span><span class="sxs-lookup"><span data-stu-id="9a24c-141">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="9a24c-142">或者，處理常式可能會執行 [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 來處理一種以上的需求。</span><span class="sxs-lookup"><span data-stu-id="9a24c-142">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="9a24c-143">針對一項需求使用處理程式</span><span class="sxs-lookup"><span data-stu-id="9a24c-143">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="9a24c-144">以下是一對一關聯性的範例，其中的最小年齡處理常式會利用單一需求：</span><span class="sxs-lookup"><span data-stu-id="9a24c-144">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="9a24c-145">上述程式碼會判斷目前的使用者主體是否有由已知且受信任的簽發者發行的「出生日期」。</span><span class="sxs-lookup"><span data-stu-id="9a24c-145">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="9a24c-146">遺漏宣告時無法進行授權，在這種情況下，會傳回已完成的工作。</span><span class="sxs-lookup"><span data-stu-id="9a24c-146">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="9a24c-147">當宣告存在時，就會計算使用者的年齡。</span><span class="sxs-lookup"><span data-stu-id="9a24c-147">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="9a24c-148">如果使用者符合需求所定義的最短使用期限，則會將授權視為成功。</span><span class="sxs-lookup"><span data-stu-id="9a24c-148">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="9a24c-149">當授權成功時， `context.Succeed` 會以滿足的需求來叫用其唯一參數。</span><span class="sxs-lookup"><span data-stu-id="9a24c-149">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="9a24c-150">針對多個需求使用處理程式</span><span class="sxs-lookup"><span data-stu-id="9a24c-150">Use a handler for multiple requirements</span></span>

<span data-ttu-id="9a24c-151">下列是一對多關聯性的範例，其中的許可權處理常式可以處理三種不同類型的需求：</span><span class="sxs-lookup"><span data-stu-id="9a24c-151">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="9a24c-152">上述程式碼會[PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; 包含未標示為成功之需求的屬性。</span><span class="sxs-lookup"><span data-stu-id="9a24c-152">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="9a24c-153">針對 `ReadPermission` 需求，使用者必須是擁有者或贊助者，才能存取要求的資源。</span><span class="sxs-lookup"><span data-stu-id="9a24c-153">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="9a24c-154">在或需求的情況 `EditPermission` 下 `DeletePermission` ，他或她必須是存取所要求資源的擁有者。</span><span class="sxs-lookup"><span data-stu-id="9a24c-154">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="9a24c-155">處理常式註冊</span><span class="sxs-lookup"><span data-stu-id="9a24c-155">Handler registration</span></span>

<span data-ttu-id="9a24c-156">在設定期間，會在服務集合中註冊處理常式。</span><span class="sxs-lookup"><span data-stu-id="9a24c-156">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="9a24c-157">例如：</span><span class="sxs-lookup"><span data-stu-id="9a24c-157">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="9a24c-158">上述程式碼會藉 `MinimumAgeHandler` 由叫用來註冊為單一登入 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` 。</span><span class="sxs-lookup"><span data-stu-id="9a24c-158">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="9a24c-159">您可以使用任何內建 [服務存留期](xref:fundamentals/dependency-injection#service-lifetimes)來註冊處理常式。</span><span class="sxs-lookup"><span data-stu-id="9a24c-159">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="9a24c-160">處理常式會傳回什麼？</span><span class="sxs-lookup"><span data-stu-id="9a24c-160">What should a handler return?</span></span>

<span data-ttu-id="9a24c-161">請注意， `Handle` [處理常式範例](#security-authorization-handler-example) 中的方法不會傳回任何值。</span><span class="sxs-lookup"><span data-stu-id="9a24c-161">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="9a24c-162">指出成功或失敗的狀態為何？</span><span class="sxs-lookup"><span data-stu-id="9a24c-162">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="9a24c-163">處理常式會藉由呼叫來表示成功 `context.Succeed(IAuthorizationRequirement requirement)` ，並通過已成功驗證的要求。</span><span class="sxs-lookup"><span data-stu-id="9a24c-163">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="9a24c-164">處理常式通常不需要處理失敗，因為相同需求的其他處理常式可能會成功。</span><span class="sxs-lookup"><span data-stu-id="9a24c-164">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="9a24c-165">為了保證失敗，即使其他需求處理常式成功，也請呼叫 `context.Fail` 。</span><span class="sxs-lookup"><span data-stu-id="9a24c-165">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="9a24c-166">如果處理常式呼叫 `context.Succeed` 或 `context.Fail` ，則仍會呼叫所有其他處理常式。</span><span class="sxs-lookup"><span data-stu-id="9a24c-166">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="9a24c-167">這可讓要求產生副作用（例如記錄），即使另一個處理常式已成功通過驗證或要求失敗，也會發生這種情況。</span><span class="sxs-lookup"><span data-stu-id="9a24c-167">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="9a24c-168">當設定為時 `false` ， [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 屬性會在呼叫時，將處理常式的執行 `context.Fail` 過程縮短。</span><span class="sxs-lookup"><span data-stu-id="9a24c-168">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="9a24c-169">`InvokeHandlersAfterFailure` 預設為 `true` ，在此情況下會呼叫所有處理常式。</span><span class="sxs-lookup"><span data-stu-id="9a24c-169">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="9a24c-170">即使驗證失敗，也會呼叫授權處理常式。</span><span class="sxs-lookup"><span data-stu-id="9a24c-170">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="9a24c-171">為什麼需要要求多個處理常式？</span><span class="sxs-lookup"><span data-stu-id="9a24c-171">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="9a24c-172">如果您想要以 **或** 基礎進行評估，請針對單一需求執行多個處理常式。</span><span class="sxs-lookup"><span data-stu-id="9a24c-172">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="9a24c-173">例如，Microsoft 的大門只會以重要卡片開啟。</span><span class="sxs-lookup"><span data-stu-id="9a24c-173">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="9a24c-174">如果您在家離開您的金鑰卡，則接待員會印出暫存的貼紙，並為您開啟門。</span><span class="sxs-lookup"><span data-stu-id="9a24c-174">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="9a24c-175">在此案例中，您會有單一需求 *BuildingEntry*，但有多個處理常式，每一個都檢查單一需求。</span><span class="sxs-lookup"><span data-stu-id="9a24c-175">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="9a24c-176">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="9a24c-176">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="9a24c-177">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="9a24c-177">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="9a24c-178">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="9a24c-178">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="9a24c-179">確定這兩個處理常式都 [已註冊](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)。</span><span class="sxs-lookup"><span data-stu-id="9a24c-179">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="9a24c-180">當原則評估時，如果其中一個處理常式成功 `BuildingEntryRequirement` ，則原則評估會成功。</span><span class="sxs-lookup"><span data-stu-id="9a24c-180">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="9a24c-181">使用 func 來完成原則</span><span class="sxs-lookup"><span data-stu-id="9a24c-181">Use a func to fulfill a policy</span></span>

<span data-ttu-id="9a24c-182">在某些情況下，在程式碼中可以輕鬆地表示原則。</span><span class="sxs-lookup"><span data-stu-id="9a24c-182">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="9a24c-183">使用原則產生器設定原則時，可能會提供 `Func<AuthorizationHandlerContext, bool>` `RequireAssertion` 。</span><span class="sxs-lookup"><span data-stu-id="9a24c-183">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="9a24c-184">例如，可以重寫先前的，如下所示 `BadgeEntryHandler` ：</span><span class="sxs-lookup"><span data-stu-id="9a24c-184">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="9a24c-185">存取處理常式中的 MVC 要求內容</span><span class="sxs-lookup"><span data-stu-id="9a24c-185">Access MVC request context in handlers</span></span>

<span data-ttu-id="9a24c-186">`HandleRequirementAsync`您在授權處理常式中執行的方法有兩個參數： `AuthorizationHandlerContext` 和 `TRequirement` 您正在處理的。</span><span class="sxs-lookup"><span data-stu-id="9a24c-186">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="9a24c-187">MVC 之類的架構或可 SignalR 自由將任何物件加入至的 `Resource` 屬性， `AuthorizationHandlerContext` 以傳遞額外的資訊。</span><span class="sxs-lookup"><span data-stu-id="9a24c-187">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="9a24c-188">使用端點路由時，授權一般是由授權中介軟體處理。</span><span class="sxs-lookup"><span data-stu-id="9a24c-188">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="9a24c-189">在此情況下， `Resource` 屬性為的實例 <xref:Microsoft.AspNetCore.Http.Endpoint> 。</span><span class="sxs-lookup"><span data-stu-id="9a24c-189">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="9a24c-190">端點可以用來探查您要路由傳送的基礎資源。</span><span class="sxs-lookup"><span data-stu-id="9a24c-190">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="9a24c-191">例如：</span><span class="sxs-lookup"><span data-stu-id="9a24c-191">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="9a24c-192">端點不會提供對目前的存取權 `HttpContext` 。</span><span class="sxs-lookup"><span data-stu-id="9a24c-192">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="9a24c-193">使用端點路由時，請使用在 `IHttpContextAcessor` `HttpContext` 授權處理常式內進行存取。</span><span class="sxs-lookup"><span data-stu-id="9a24c-193">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="9a24c-194">如需詳細資訊，請參閱 [使用自訂群組件的 HttpCoNtext](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components)。</span><span class="sxs-lookup"><span data-stu-id="9a24c-194">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="9a24c-195">使用傳統路由，或當授權在 MVC 的授權篩選準則中發生時，的值 `Resource` 就是 <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> 實例。</span><span class="sxs-lookup"><span data-stu-id="9a24c-195">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="9a24c-196">這個屬性可讓您存取 `HttpContext` 、 `RouteData` 和 MVC 和頁面所提供的其他專案 Razor 。</span><span class="sxs-lookup"><span data-stu-id="9a24c-196">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="9a24c-197">屬性的使用 `Resource` 是架構特有的。</span><span class="sxs-lookup"><span data-stu-id="9a24c-197">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="9a24c-198">使用屬性中的資訊會將 `Resource` 您的授權原則限制為特定架構。</span><span class="sxs-lookup"><span data-stu-id="9a24c-198">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="9a24c-199">您應 `Resource` 使用關鍵字來轉換屬性 `is` ，然後確認轉換是否成功，以確保您的程式碼在 `InvalidCastException` 其他架構上執行時不會損毀：</span><span class="sxs-lookup"><span data-stu-id="9a24c-199">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

## <a name="globally-require-all-users-to-be-authenticated"></a><span data-ttu-id="9a24c-200">全域要求所有使用者進行驗證</span><span class="sxs-lookup"><span data-stu-id="9a24c-200">Globally require all users to be authenticated</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9a24c-201">在幕後，以 [角色為基礎的授權](xref:security/authorization/roles) 和 [宣告型授權](xref:security/authorization/claims) 會使用需求、需求處理常式和預先設定的原則。</span><span class="sxs-lookup"><span data-stu-id="9a24c-201">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="9a24c-202">這些組建區塊支援程式碼中授權評估的運算式。</span><span class="sxs-lookup"><span data-stu-id="9a24c-202">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="9a24c-203">結果是更豐富、可重複使用、可測試的授權結構。</span><span class="sxs-lookup"><span data-stu-id="9a24c-203">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="9a24c-204">授權原則包含一個或多個需求。</span><span class="sxs-lookup"><span data-stu-id="9a24c-204">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="9a24c-205">在方法中，它會註冊為授權服務設定的一部分 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="9a24c-205">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="9a24c-206">在上述範例中，會建立 "AtLeast21" 原則。</span><span class="sxs-lookup"><span data-stu-id="9a24c-206">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="9a24c-207">這項要求的 &mdash; 最小存留期為要求的參數所提供的單一需求。</span><span class="sxs-lookup"><span data-stu-id="9a24c-207">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="9a24c-208">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="9a24c-208">IAuthorizationService</span></span> 

<span data-ttu-id="9a24c-209">判斷授權是否成功的主要服務是 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ：</span><span class="sxs-lookup"><span data-stu-id="9a24c-209">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="9a24c-210">上述程式碼反白顯示 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)的兩種方法。</span><span class="sxs-lookup"><span data-stu-id="9a24c-210">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="9a24c-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> 是沒有方法的標記服務，以及用來追蹤授權是否成功的機制。</span><span class="sxs-lookup"><span data-stu-id="9a24c-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="9a24c-212">每個 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> 都負責檢查是否符合需求：</span><span class="sxs-lookup"><span data-stu-id="9a24c-212">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="9a24c-213">此 <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 類別是處理常式用來標示是否符合需求的程式：</span><span class="sxs-lookup"><span data-stu-id="9a24c-213">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="9a24c-214">下列程式碼顯示授權服務) 預設執行的簡化 (和批註批註：</span><span class="sxs-lookup"><span data-stu-id="9a24c-214">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="9a24c-215">下列程式碼顯示一般 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="9a24c-215">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

<span data-ttu-id="9a24c-216">使用 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 或 `[Authorize(Policy = "Something")]` 進行授權。</span><span class="sxs-lookup"><span data-stu-id="9a24c-216">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="9a24c-217">將原則套用至 MVC 控制器</span><span class="sxs-lookup"><span data-stu-id="9a24c-217">Apply policies to MVC controllers</span></span>

<span data-ttu-id="9a24c-218">如果您使用 Razor 的是頁面，請參閱本檔中的 [將原則套用至 Razor 頁面](#apply-policies-to-razor-pages) 。</span><span class="sxs-lookup"><span data-stu-id="9a24c-218">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="9a24c-219">使用具有原則名稱的屬性，將原則套用至控制器 `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="9a24c-219">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="9a24c-220">例如：</span><span class="sxs-lookup"><span data-stu-id="9a24c-220">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="9a24c-221">將原則套用至 Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="9a24c-221">Apply policies to Razor Pages</span></span>

<span data-ttu-id="9a24c-222">Razor使用具有原則名稱的屬性，將原則套用至頁面 `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="9a24c-222">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="9a24c-223">例如：</span><span class="sxs-lookup"><span data-stu-id="9a24c-223">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="9a24c-224">您也可以 Razor 使用 [授權慣例](xref:security/authorization/razor-pages-authorization)，將原則套用至頁面。</span><span class="sxs-lookup"><span data-stu-id="9a24c-224">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="9a24c-225">規格需求</span><span class="sxs-lookup"><span data-stu-id="9a24c-225">Requirements</span></span>

<span data-ttu-id="9a24c-226">授權需求是一組資料參數的集合，原則可用來評估目前的使用者主體。</span><span class="sxs-lookup"><span data-stu-id="9a24c-226">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="9a24c-227">在我們的「AtLeast21」原則中，要求是 &mdash; 最短存留期的單一參數。</span><span class="sxs-lookup"><span data-stu-id="9a24c-227">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="9a24c-228">需求會實 [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)，這是空的標記介面。</span><span class="sxs-lookup"><span data-stu-id="9a24c-228">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="9a24c-229">參數化的最小存留期需求可能會以下列方式執行：</span><span class="sxs-lookup"><span data-stu-id="9a24c-229">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="9a24c-230">如果授權原則包含多個授權需求，則所有需求都必須通過，原則評估才會成功。</span><span class="sxs-lookup"><span data-stu-id="9a24c-230">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="9a24c-231">換句話說，新增至單一授權原則的多個授權需求會以 **和** 為基礎來處理。</span><span class="sxs-lookup"><span data-stu-id="9a24c-231">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="9a24c-232">需求不需要有資料或屬性。</span><span class="sxs-lookup"><span data-stu-id="9a24c-232">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="9a24c-233">授權處理常式</span><span class="sxs-lookup"><span data-stu-id="9a24c-233">Authorization handlers</span></span>

<span data-ttu-id="9a24c-234">授權處理常式負責評估需求的屬性。</span><span class="sxs-lookup"><span data-stu-id="9a24c-234">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="9a24c-235">授權處理常式會根據提供的 [AuthorizationHandlerCoNtext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 來評估需求，以判斷是否允許存取。</span><span class="sxs-lookup"><span data-stu-id="9a24c-235">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="9a24c-236">需求可以有 [多個處理常式](#security-authorization-policies-based-multiple-handlers)。</span><span class="sxs-lookup"><span data-stu-id="9a24c-236">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="9a24c-237">處理常式可能會[繼承 \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)，其中 `TRequirement` 是要處理的需求。</span><span class="sxs-lookup"><span data-stu-id="9a24c-237">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="9a24c-238">或者，處理常式可能會執行 [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 來處理一種以上的需求。</span><span class="sxs-lookup"><span data-stu-id="9a24c-238">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="9a24c-239">針對一項需求使用處理程式</span><span class="sxs-lookup"><span data-stu-id="9a24c-239">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="9a24c-240">以下是一對一關聯性的範例，其中的最小年齡處理常式會利用單一需求：</span><span class="sxs-lookup"><span data-stu-id="9a24c-240">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="9a24c-241">上述程式碼會判斷目前的使用者主體是否有由已知且受信任的簽發者發行的「出生日期」。</span><span class="sxs-lookup"><span data-stu-id="9a24c-241">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="9a24c-242">遺漏宣告時無法進行授權，在這種情況下，會傳回已完成的工作。</span><span class="sxs-lookup"><span data-stu-id="9a24c-242">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="9a24c-243">當宣告存在時，就會計算使用者的年齡。</span><span class="sxs-lookup"><span data-stu-id="9a24c-243">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="9a24c-244">如果使用者符合需求所定義的最短使用期限，則會將授權視為成功。</span><span class="sxs-lookup"><span data-stu-id="9a24c-244">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="9a24c-245">當授權成功時， `context.Succeed` 會以滿足的需求來叫用其唯一參數。</span><span class="sxs-lookup"><span data-stu-id="9a24c-245">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="9a24c-246">針對多個需求使用處理程式</span><span class="sxs-lookup"><span data-stu-id="9a24c-246">Use a handler for multiple requirements</span></span>

<span data-ttu-id="9a24c-247">下列是一對多關聯性的範例，其中的許可權處理常式可以處理三種不同類型的需求：</span><span class="sxs-lookup"><span data-stu-id="9a24c-247">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="9a24c-248">上述程式碼會[PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; 包含未標示為成功之需求的屬性。</span><span class="sxs-lookup"><span data-stu-id="9a24c-248">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="9a24c-249">針對 `ReadPermission` 需求，使用者必須是擁有者或贊助者，才能存取要求的資源。</span><span class="sxs-lookup"><span data-stu-id="9a24c-249">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="9a24c-250">在或需求的情況 `EditPermission` 下 `DeletePermission` ，他或她必須是存取所要求資源的擁有者。</span><span class="sxs-lookup"><span data-stu-id="9a24c-250">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="9a24c-251">處理常式註冊</span><span class="sxs-lookup"><span data-stu-id="9a24c-251">Handler registration</span></span>

<span data-ttu-id="9a24c-252">在設定期間，會在服務集合中註冊處理常式。</span><span class="sxs-lookup"><span data-stu-id="9a24c-252">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="9a24c-253">例如：</span><span class="sxs-lookup"><span data-stu-id="9a24c-253">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="9a24c-254">上述程式碼會藉 `MinimumAgeHandler` 由叫用來註冊為單一登入 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` 。</span><span class="sxs-lookup"><span data-stu-id="9a24c-254">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="9a24c-255">您可以使用任何內建 [服務存留期](xref:fundamentals/dependency-injection#service-lifetimes)來註冊處理常式。</span><span class="sxs-lookup"><span data-stu-id="9a24c-255">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="9a24c-256">處理常式會傳回什麼？</span><span class="sxs-lookup"><span data-stu-id="9a24c-256">What should a handler return?</span></span>

<span data-ttu-id="9a24c-257">請注意， `Handle` [處理常式範例](#security-authorization-handler-example) 中的方法不會傳回任何值。</span><span class="sxs-lookup"><span data-stu-id="9a24c-257">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="9a24c-258">指出成功或失敗的狀態為何？</span><span class="sxs-lookup"><span data-stu-id="9a24c-258">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="9a24c-259">處理常式會藉由呼叫來表示成功 `context.Succeed(IAuthorizationRequirement requirement)` ，並通過已成功驗證的要求。</span><span class="sxs-lookup"><span data-stu-id="9a24c-259">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="9a24c-260">處理常式通常不需要處理失敗，因為相同需求的其他處理常式可能會成功。</span><span class="sxs-lookup"><span data-stu-id="9a24c-260">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="9a24c-261">為了保證失敗，即使其他需求處理常式成功，也請呼叫 `context.Fail` 。</span><span class="sxs-lookup"><span data-stu-id="9a24c-261">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="9a24c-262">如果處理常式呼叫 `context.Succeed` 或 `context.Fail` ，則仍會呼叫所有其他處理常式。</span><span class="sxs-lookup"><span data-stu-id="9a24c-262">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="9a24c-263">這可讓要求產生副作用（例如記錄），即使另一個處理常式已成功通過驗證或要求失敗，也會發生這種情況。</span><span class="sxs-lookup"><span data-stu-id="9a24c-263">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="9a24c-264">當設定為時 `false` ， [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 屬性會在呼叫時，將處理常式的執行 `context.Fail` 過程縮短。</span><span class="sxs-lookup"><span data-stu-id="9a24c-264">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="9a24c-265">`InvokeHandlersAfterFailure` 預設為 `true` ，在此情況下會呼叫所有處理常式。</span><span class="sxs-lookup"><span data-stu-id="9a24c-265">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="9a24c-266">即使驗證失敗，也會呼叫授權處理常式。</span><span class="sxs-lookup"><span data-stu-id="9a24c-266">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="9a24c-267">為什麼需要要求多個處理常式？</span><span class="sxs-lookup"><span data-stu-id="9a24c-267">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="9a24c-268">如果您想要以 **或** 基礎進行評估，請針對單一需求執行多個處理常式。</span><span class="sxs-lookup"><span data-stu-id="9a24c-268">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="9a24c-269">例如，Microsoft 的大門只會以重要卡片開啟。</span><span class="sxs-lookup"><span data-stu-id="9a24c-269">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="9a24c-270">如果您在家離開您的金鑰卡，則接待員會印出暫存的貼紙，並為您開啟門。</span><span class="sxs-lookup"><span data-stu-id="9a24c-270">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="9a24c-271">在此案例中，您會有單一需求 *BuildingEntry*，但有多個處理常式，每一個都檢查單一需求。</span><span class="sxs-lookup"><span data-stu-id="9a24c-271">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="9a24c-272">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="9a24c-272">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="9a24c-273">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="9a24c-273">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="9a24c-274">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="9a24c-274">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="9a24c-275">確定這兩個處理常式都 [已註冊](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)。</span><span class="sxs-lookup"><span data-stu-id="9a24c-275">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="9a24c-276">當原則評估時，如果其中一個處理常式成功 `BuildingEntryRequirement` ，則原則評估會成功。</span><span class="sxs-lookup"><span data-stu-id="9a24c-276">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="9a24c-277">使用 func 來完成原則</span><span class="sxs-lookup"><span data-stu-id="9a24c-277">Use a func to fulfill a policy</span></span>

<span data-ttu-id="9a24c-278">在某些情況下，在程式碼中可以輕鬆地表示原則。</span><span class="sxs-lookup"><span data-stu-id="9a24c-278">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="9a24c-279">使用原則產生器設定原則時，可能會提供 `Func<AuthorizationHandlerContext, bool>` `RequireAssertion` 。</span><span class="sxs-lookup"><span data-stu-id="9a24c-279">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="9a24c-280">例如，可以重寫先前的，如下所示 `BadgeEntryHandler` ：</span><span class="sxs-lookup"><span data-stu-id="9a24c-280">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="9a24c-281">存取處理常式中的 MVC 要求內容</span><span class="sxs-lookup"><span data-stu-id="9a24c-281">Access MVC request context in handlers</span></span>

<span data-ttu-id="9a24c-282">`HandleRequirementAsync`您在授權處理常式中執行的方法有兩個參數： `AuthorizationHandlerContext` 和 `TRequirement` 您正在處理的。</span><span class="sxs-lookup"><span data-stu-id="9a24c-282">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="9a24c-283">MVC 之類的架構或可 SignalR 自由將任何物件加入至的 `Resource` 屬性， `AuthorizationHandlerContext` 以傳遞額外的資訊。</span><span class="sxs-lookup"><span data-stu-id="9a24c-283">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="9a24c-284">例如，MVC 會在屬性中傳遞 [AuthorizationFilterCoNtext](/dotnet/api/?term=AuthorizationFilterContext) 的實例 `Resource` 。</span><span class="sxs-lookup"><span data-stu-id="9a24c-284">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="9a24c-285">這個屬性可讓您存取 `HttpContext` 、 `RouteData` 和 MVC 和頁面所提供的其他專案 Razor 。</span><span class="sxs-lookup"><span data-stu-id="9a24c-285">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="9a24c-286">屬性的使用 `Resource` 是架構特有的。</span><span class="sxs-lookup"><span data-stu-id="9a24c-286">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="9a24c-287">使用屬性中的資訊會將 `Resource` 您的授權原則限制為特定架構。</span><span class="sxs-lookup"><span data-stu-id="9a24c-287">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="9a24c-288">您應 `Resource` 使用關鍵字來轉換屬性 `is` ，然後確認轉換是否成功，以確保您的程式碼在 `InvalidCastException` 其他架構上執行時不會損毀：</span><span class="sxs-lookup"><span data-stu-id="9a24c-288">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
