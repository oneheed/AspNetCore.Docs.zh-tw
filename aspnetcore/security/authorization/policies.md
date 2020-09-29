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
# <a name="policy-based-authorization-in-aspnet-core"></a>ASP.NET Core 中以原則為基礎的授權

::: moniker range=">= aspnetcore-3.0"

在幕後，以 [角色為基礎的授權](xref:security/authorization/roles) 和 [宣告型授權](xref:security/authorization/claims) 會使用需求、需求處理常式和預先設定的原則。 這些組建區塊支援程式碼中授權評估的運算式。 結果是更豐富、可重複使用、可測試的授權結構。

授權原則包含一個或多個需求。 在方法中，它會註冊為授權服務設定的一部分 `Startup.ConfigureServices` ：

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

在上述範例中，會建立 "AtLeast21" 原則。 這項要求的 &mdash; 最小存留期為要求的參數所提供的單一需求。

## <a name="iauthorizationservice"></a>IAuthorizationService 

判斷授權是否成功的主要服務是 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ：

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

上述程式碼反白顯示 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)的兩種方法。

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> 是沒有方法的標記服務，以及用來追蹤授權是否成功的機制。

每個 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> 都負責檢查是否符合需求：
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

此 <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 類別是處理常式用來標示是否符合需求的程式：

```csharp
 context.Succeed(requirement)
```

下列程式碼顯示授權服務) 預設執行的簡化 (和批註批註：

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

下列程式碼顯示一般 `ConfigureServices` ：

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

使用 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 或 `[Authorize(Policy = "Something")]` 進行授權。

## <a name="apply-policies-to-mvc-controllers"></a>將原則套用至 MVC 控制器

如果您使用 Razor 的是頁面，請參閱本檔中的 [將原則套用至 Razor 頁面](#apply-policies-to-razor-pages) 。

使用具有原則名稱的屬性，將原則套用至控制器 `[Authorize]` 。 例如：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a>將原則套用至 Razor 頁面

Razor使用具有原則名稱的屬性，將原則套用至頁面 `[Authorize]` 。 例如：

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

原則 ***無法*** 在 Razor 頁面處理常式層級套用，它們必須套用至頁面。

您可以 Razor 使用 [授權慣例](xref:security/authorization/razor-pages-authorization)，將原則套用至頁面。

## <a name="requirements"></a>規格需求

授權需求是一組資料參數的集合，原則可用來評估目前的使用者主體。 在我們的「AtLeast21」原則中，要求是 &mdash; 最短存留期的單一參數。 需求會實 [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)，這是空的標記介面。 參數化的最小存留期需求可能會以下列方式執行：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

如果授權原則包含多個授權需求，則所有需求都必須通過，原則評估才會成功。 換句話說，新增至單一授權原則的多個授權需求會以 **和** 為基礎來處理。

> [!NOTE]
> 需求不需要有資料或屬性。

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>授權處理常式

授權處理常式負責評估需求的屬性。 授權處理常式會根據提供的 [AuthorizationHandlerCoNtext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 來評估需求，以判斷是否允許存取。

需求可以有 [多個處理常式](#security-authorization-policies-based-multiple-handlers)。 處理常式可能會[繼承 \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)，其中 `TRequirement` 是要處理的需求。 或者，處理常式可能會執行 [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 來處理一種以上的需求。

### <a name="use-a-handler-for-one-requirement"></a>針對一項需求使用處理程式

<a name="security-authorization-handler-example"></a>

以下是一對一關聯性的範例，其中的最小年齡處理常式會利用單一需求：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

上述程式碼會判斷目前的使用者主體是否有由已知且受信任的簽發者發行的「出生日期」。 遺漏宣告時無法進行授權，在這種情況下，會傳回已完成的工作。 當宣告存在時，就會計算使用者的年齡。 如果使用者符合需求所定義的最短使用期限，則會將授權視為成功。 當授權成功時， `context.Succeed` 會以滿足的需求來叫用其唯一參數。

### <a name="use-a-handler-for-multiple-requirements"></a>針對多個需求使用處理程式

下列是一對多關聯性的範例，其中的許可權處理常式可以處理三種不同類型的需求：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

上述程式碼會[PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; 包含未標示為成功之需求的屬性。 針對 `ReadPermission` 需求，使用者必須是擁有者或贊助者，才能存取要求的資源。 在或需求的情況 `EditPermission` 下 `DeletePermission` ，他或她必須是存取所要求資源的擁有者。

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>處理常式註冊

在設定期間，會在服務集合中註冊處理常式。 例如：

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

上述程式碼會藉 `MinimumAgeHandler` 由叫用來註冊為單一登入 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` 。 您可以使用任何內建 [服務存留期](xref:fundamentals/dependency-injection#service-lifetimes)來註冊處理常式。

## <a name="what-should-a-handler-return"></a>處理常式會傳回什麼？

請注意， `Handle` [處理常式範例](#security-authorization-handler-example) 中的方法不會傳回任何值。 指出成功或失敗的狀態為何？

* 處理常式會藉由呼叫來表示成功 `context.Succeed(IAuthorizationRequirement requirement)` ，並通過已成功驗證的要求。

* 處理常式通常不需要處理失敗，因為相同需求的其他處理常式可能會成功。

* 為了保證失敗，即使其他需求處理常式成功，也請呼叫 `context.Fail` 。

如果處理常式呼叫 `context.Succeed` 或 `context.Fail` ，則仍會呼叫所有其他處理常式。 這可讓要求產生副作用（例如記錄），即使另一個處理常式已成功通過驗證或要求失敗，也會發生這種情況。 當設定為時 `false` ， [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 屬性會在呼叫時，將處理常式的執行 `context.Fail` 過程縮短。 `InvokeHandlersAfterFailure` 預設為 `true` ，在此情況下會呼叫所有處理常式。

> [!NOTE]
> 即使驗證失敗，也會呼叫授權處理常式。

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>為什麼需要要求多個處理常式？

如果您想要以 **或** 基礎進行評估，請針對單一需求執行多個處理常式。 例如，Microsoft 的大門只會以重要卡片開啟。 如果您在家離開您的金鑰卡，則接待員會印出暫存的貼紙，並為您開啟門。 在此案例中，您會有單一需求 *BuildingEntry*，但有多個處理常式，每一個都檢查單一需求。

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

確定這兩個處理常式都 [已註冊](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)。 當原則評估時，如果其中一個處理常式成功 `BuildingEntryRequirement` ，則原則評估會成功。

## <a name="use-a-func-to-fulfill-a-policy"></a>使用 func 來完成原則

在某些情況下，在程式碼中可以輕鬆地表示原則。 使用原則產生器設定原則時，可能會提供 `Func<AuthorizationHandlerContext, bool>` `RequireAssertion` 。

例如，可以重寫先前的，如下所示 `BadgeEntryHandler` ：

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a>存取處理常式中的 MVC 要求內容

`HandleRequirementAsync`您在授權處理常式中執行的方法有兩個參數： `AuthorizationHandlerContext` 和 `TRequirement` 您正在處理的。 MVC 之類的架構或可 SignalR 自由將任何物件加入至的 `Resource` 屬性， `AuthorizationHandlerContext` 以傳遞額外的資訊。

使用端點路由時，授權一般是由授權中介軟體處理。 在此情況下， `Resource` 屬性為的實例 <xref:Microsoft.AspNetCore.Http.Endpoint> 。 端點可以用來探查您要路由傳送的基礎資源。 例如：

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

端點不會提供對目前的存取權 `HttpContext` 。 使用端點路由時，請使用在 `IHttpContextAcessor` `HttpContext` 授權處理常式內進行存取。 如需詳細資訊，請參閱 [使用自訂群組件的 HttpCoNtext](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components)。

使用傳統路由，或當授權在 MVC 的授權篩選準則中發生時，的值 `Resource` 就是 <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> 實例。 這個屬性可讓您存取 `HttpContext` 、 `RouteData` 和 MVC 和頁面所提供的其他專案 Razor 。

屬性的使用 `Resource` 是架構特有的。 使用屬性中的資訊會將 `Resource` 您的授權原則限制為特定架構。 您應 `Resource` 使用關鍵字來轉換屬性 `is` ，然後確認轉換是否成功，以確保您的程式碼在 `InvalidCastException` 其他架構上執行時不會損毀：

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

## <a name="globally-require-all-users-to-be-authenticated"></a>全域要求所有使用者進行驗證

[!INCLUDE[](~/includes/requireAuth.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

在幕後，以 [角色為基礎的授權](xref:security/authorization/roles) 和 [宣告型授權](xref:security/authorization/claims) 會使用需求、需求處理常式和預先設定的原則。 這些組建區塊支援程式碼中授權評估的運算式。 結果是更豐富、可重複使用、可測試的授權結構。

授權原則包含一個或多個需求。 在方法中，它會註冊為授權服務設定的一部分 `Startup.ConfigureServices` ：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

在上述範例中，會建立 "AtLeast21" 原則。 這項要求的 &mdash; 最小存留期為要求的參數所提供的單一需求。

## <a name="iauthorizationservice"></a>IAuthorizationService 

判斷授權是否成功的主要服務是 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ：

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

上述程式碼反白顯示 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)的兩種方法。

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> 是沒有方法的標記服務，以及用來追蹤授權是否成功的機制。

每個 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> 都負責檢查是否符合需求：
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

此 <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 類別是處理常式用來標示是否符合需求的程式：

```csharp
 context.Succeed(requirement)
```

下列程式碼顯示授權服務) 預設執行的簡化 (和批註批註：

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

下列程式碼顯示一般 `ConfigureServices` ：

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

使用 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 或 `[Authorize(Policy = "Something")]` 進行授權。

## <a name="apply-policies-to-mvc-controllers"></a>將原則套用至 MVC 控制器

如果您使用 Razor 的是頁面，請參閱本檔中的 [將原則套用至 Razor 頁面](#apply-policies-to-razor-pages) 。

使用具有原則名稱的屬性，將原則套用至控制器 `[Authorize]` 。 例如：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a>將原則套用至 Razor 頁面

Razor使用具有原則名稱的屬性，將原則套用至頁面 `[Authorize]` 。 例如：

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

您也可以 Razor 使用 [授權慣例](xref:security/authorization/razor-pages-authorization)，將原則套用至頁面。

## <a name="requirements"></a>規格需求

授權需求是一組資料參數的集合，原則可用來評估目前的使用者主體。 在我們的「AtLeast21」原則中，要求是 &mdash; 最短存留期的單一參數。 需求會實 [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)，這是空的標記介面。 參數化的最小存留期需求可能會以下列方式執行：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

如果授權原則包含多個授權需求，則所有需求都必須通過，原則評估才會成功。 換句話說，新增至單一授權原則的多個授權需求會以 **和** 為基礎來處理。

> [!NOTE]
> 需求不需要有資料或屬性。

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>授權處理常式

授權處理常式負責評估需求的屬性。 授權處理常式會根據提供的 [AuthorizationHandlerCoNtext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 來評估需求，以判斷是否允許存取。

需求可以有 [多個處理常式](#security-authorization-policies-based-multiple-handlers)。 處理常式可能會[繼承 \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)，其中 `TRequirement` 是要處理的需求。 或者，處理常式可能會執行 [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 來處理一種以上的需求。

### <a name="use-a-handler-for-one-requirement"></a>針對一項需求使用處理程式

<a name="security-authorization-handler-example"></a>

以下是一對一關聯性的範例，其中的最小年齡處理常式會利用單一需求：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

上述程式碼會判斷目前的使用者主體是否有由已知且受信任的簽發者發行的「出生日期」。 遺漏宣告時無法進行授權，在這種情況下，會傳回已完成的工作。 當宣告存在時，就會計算使用者的年齡。 如果使用者符合需求所定義的最短使用期限，則會將授權視為成功。 當授權成功時， `context.Succeed` 會以滿足的需求來叫用其唯一參數。

### <a name="use-a-handler-for-multiple-requirements"></a>針對多個需求使用處理程式

下列是一對多關聯性的範例，其中的許可權處理常式可以處理三種不同類型的需求：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

上述程式碼會[PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; 包含未標示為成功之需求的屬性。 針對 `ReadPermission` 需求，使用者必須是擁有者或贊助者，才能存取要求的資源。 在或需求的情況 `EditPermission` 下 `DeletePermission` ，他或她必須是存取所要求資源的擁有者。

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>處理常式註冊

在設定期間，會在服務集合中註冊處理常式。 例如：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

上述程式碼會藉 `MinimumAgeHandler` 由叫用來註冊為單一登入 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` 。 您可以使用任何內建 [服務存留期](xref:fundamentals/dependency-injection#service-lifetimes)來註冊處理常式。

## <a name="what-should-a-handler-return"></a>處理常式會傳回什麼？

請注意， `Handle` [處理常式範例](#security-authorization-handler-example) 中的方法不會傳回任何值。 指出成功或失敗的狀態為何？

* 處理常式會藉由呼叫來表示成功 `context.Succeed(IAuthorizationRequirement requirement)` ，並通過已成功驗證的要求。

* 處理常式通常不需要處理失敗，因為相同需求的其他處理常式可能會成功。

* 為了保證失敗，即使其他需求處理常式成功，也請呼叫 `context.Fail` 。

如果處理常式呼叫 `context.Succeed` 或 `context.Fail` ，則仍會呼叫所有其他處理常式。 這可讓要求產生副作用（例如記錄），即使另一個處理常式已成功通過驗證或要求失敗，也會發生這種情況。 當設定為時 `false` ， [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 屬性會在呼叫時，將處理常式的執行 `context.Fail` 過程縮短。 `InvokeHandlersAfterFailure` 預設為 `true` ，在此情況下會呼叫所有處理常式。

> [!NOTE]
> 即使驗證失敗，也會呼叫授權處理常式。

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>為什麼需要要求多個處理常式？

如果您想要以 **或** 基礎進行評估，請針對單一需求執行多個處理常式。 例如，Microsoft 的大門只會以重要卡片開啟。 如果您在家離開您的金鑰卡，則接待員會印出暫存的貼紙，並為您開啟門。 在此案例中，您會有單一需求 *BuildingEntry*，但有多個處理常式，每一個都檢查單一需求。

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

確定這兩個處理常式都 [已註冊](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)。 當原則評估時，如果其中一個處理常式成功 `BuildingEntryRequirement` ，則原則評估會成功。

## <a name="use-a-func-to-fulfill-a-policy"></a>使用 func 來完成原則

在某些情況下，在程式碼中可以輕鬆地表示原則。 使用原則產生器設定原則時，可能會提供 `Func<AuthorizationHandlerContext, bool>` `RequireAssertion` 。

例如，可以重寫先前的，如下所示 `BadgeEntryHandler` ：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a>存取處理常式中的 MVC 要求內容

`HandleRequirementAsync`您在授權處理常式中執行的方法有兩個參數： `AuthorizationHandlerContext` 和 `TRequirement` 您正在處理的。 MVC 之類的架構或可 SignalR 自由將任何物件加入至的 `Resource` 屬性， `AuthorizationHandlerContext` 以傳遞額外的資訊。

例如，MVC 會在屬性中傳遞 [AuthorizationFilterCoNtext](/dotnet/api/?term=AuthorizationFilterContext) 的實例 `Resource` 。 這個屬性可讓您存取 `HttpContext` 、 `RouteData` 和 MVC 和頁面所提供的其他專案 Razor 。

屬性的使用 `Resource` 是架構特有的。 使用屬性中的資訊會將 `Resource` 您的授權原則限制為特定架構。 您應 `Resource` 使用關鍵字來轉換屬性 `is` ，然後確認轉換是否成功，以確保您的程式碼在 `InvalidCastException` 其他架構上執行時不會損毀：

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
