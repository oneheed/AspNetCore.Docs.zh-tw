---
title: ASP.NET Core 中以資源為基礎的授權
author: scottaddie
description: 瞭解當授權屬性不足夠時，如何在 ASP.NET Core 應用程式中執行以資源為基礎的授權。
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authorization/resourcebased
ms.openlocfilehash: 202ca681a66ddf7f729d8835e2f77da846583df1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060166"
---
# <a name="resource-based-authorization-in-aspnet-core"></a><span data-ttu-id="cae86-103">ASP.NET Core 中以資源為基礎的授權</span><span class="sxs-lookup"><span data-stu-id="cae86-103">Resource-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="cae86-104">授權策略取決於所存取的資源。</span><span class="sxs-lookup"><span data-stu-id="cae86-104">Authorization strategy depends upon the resource being accessed.</span></span> <span data-ttu-id="cae86-105">請考慮具有 author 屬性的檔。</span><span class="sxs-lookup"><span data-stu-id="cae86-105">Consider a document that has an author property.</span></span> <span data-ttu-id="cae86-106">只允許作者更新檔。</span><span class="sxs-lookup"><span data-stu-id="cae86-106">Only the author is allowed to update the document.</span></span> <span data-ttu-id="cae86-107">因此，必須先從資料存放區取出檔，授權評估才能進行。</span><span class="sxs-lookup"><span data-stu-id="cae86-107">Consequently, the document must be retrieved from the data store before authorization evaluation can occur.</span></span>

<span data-ttu-id="cae86-108">屬性評估發生于資料系結之前，以及在執行載入檔的頁面處理常式或動作之前。</span><span class="sxs-lookup"><span data-stu-id="cae86-108">Attribute evaluation occurs before data binding and before execution of the page handler or action that loads the document.</span></span> <span data-ttu-id="cae86-109">基於這些理由，具有屬性的宣告式授權 `[Authorize]` 並不足夠。</span><span class="sxs-lookup"><span data-stu-id="cae86-109">For these reasons, declarative authorization with an `[Authorize]` attribute doesn't suffice.</span></span> <span data-ttu-id="cae86-110">相反地，您可以 &mdash; 使用稱為 *命令式授權* 的樣式來叫用自訂授權方法。</span><span class="sxs-lookup"><span data-stu-id="cae86-110">Instead, you can invoke a custom authorization method&mdash;a style known as *imperative authorization* .</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="cae86-111">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/3_0) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="cae86-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/3_0) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
<span data-ttu-id="cae86-112">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/2_2) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="cae86-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/2_2) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
<span data-ttu-id="cae86-113">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/1_1) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="cae86-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/1_1) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

<span data-ttu-id="cae86-114">使用以[授權保護的使用者資料建立 ASP.NET Core 應用程式](xref:security/authorization/secure-data)包含使用以資源為基礎之授權的範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="cae86-114">[Create an ASP.NET Core app with user data protected by authorization](xref:security/authorization/secure-data) contains a sample app that uses resource-based authorization.</span></span>

## <a name="use-imperative-authorization"></a><span data-ttu-id="cae86-115">使用命令式授權</span><span class="sxs-lookup"><span data-stu-id="cae86-115">Use imperative authorization</span></span>

<span data-ttu-id="cae86-116">授權會實作為 [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) 服務，並在類別中的服務集合中註冊 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="cae86-116">Authorization is implemented as an [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) service and is registered in the service collection within the `Startup` class.</span></span> <span data-ttu-id="cae86-117">這項服務可透過相依性 [插入](xref:fundamentals/dependency-injection) 來提供給頁面處理常式或動作。</span><span class="sxs-lookup"><span data-stu-id="cae86-117">The service is made available via [dependency injection](xref:fundamentals/dependency-injection) to page handlers or actions.</span></span>

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

<span data-ttu-id="cae86-118">`IAuthorizationService` 有兩個 `AuthorizeAsync` 方法多載：一個接受資源和原則名稱，另一個則接受資源和要評估的需求清單。</span><span class="sxs-lookup"><span data-stu-id="cae86-118">`IAuthorizationService` has two `AuthorizeAsync` method overloads: one accepting the resource and the policy name and the other accepting the resource and a list of requirements to evaluate.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

<a name="security-authorization-resource-based-imperative"></a>

<span data-ttu-id="cae86-119">在下列範例中，要保護的資源會載入至自訂 `Document` 物件。</span><span class="sxs-lookup"><span data-stu-id="cae86-119">In the following example, the resource to be secured is loaded into a custom `Document` object.</span></span> <span data-ttu-id="cae86-120">系統會叫用多載 `AuthorizeAsync` ，以判斷是否允許目前的使用者編輯提供的檔。</span><span class="sxs-lookup"><span data-stu-id="cae86-120">An `AuthorizeAsync` overload is invoked to determine whether the current user is allowed to edit the provided document.</span></span> <span data-ttu-id="cae86-121">自訂的「EditPolicy」授權原則會納入決策中。</span><span class="sxs-lookup"><span data-stu-id="cae86-121">A custom "EditPolicy" authorization policy is factored into the decision.</span></span> <span data-ttu-id="cae86-122">如需建立授權原則的詳細資訊，請參閱 [自訂以原則為基礎的授權](xref:security/authorization/policies) 。</span><span class="sxs-lookup"><span data-stu-id="cae86-122">See [Custom policy-based authorization](xref:security/authorization/policies) for more on creating authorization policies.</span></span>

> [!NOTE]
> <span data-ttu-id="cae86-123">下列程式碼範例假設驗證已執行，並設定 `User` 屬性。</span><span class="sxs-lookup"><span data-stu-id="cae86-123">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a><span data-ttu-id="cae86-124">撰寫以資源為基礎的處理常式</span><span class="sxs-lookup"><span data-stu-id="cae86-124">Write a resource-based handler</span></span>

<span data-ttu-id="cae86-125">針對以資源為基礎的授權撰寫處理常式，與 [撰寫一般需求處理常式](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler)並沒有太大差異。</span><span class="sxs-lookup"><span data-stu-id="cae86-125">Writing a handler for resource-based authorization isn't much different than [writing a plain requirements handler](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span></span> <span data-ttu-id="cae86-126">建立自訂需求類別，並執行需求處理常式類別。</span><span class="sxs-lookup"><span data-stu-id="cae86-126">Create a custom requirement class, and implement a requirement handler class.</span></span> <span data-ttu-id="cae86-127">如需建立需求類別的詳細資訊，請參閱 [需求](xref:security/authorization/policies#requirements)。</span><span class="sxs-lookup"><span data-stu-id="cae86-127">For more information on creating a requirement class, see [Requirements](xref:security/authorization/policies#requirements).</span></span>

<span data-ttu-id="cae86-128">處理常式類別會指定需求和資源類型。</span><span class="sxs-lookup"><span data-stu-id="cae86-128">The handler class specifies both the requirement and resource type.</span></span> <span data-ttu-id="cae86-129">例如，利用和資源的處理常式 `SameAuthorRequirement` `Document` 如下：</span><span class="sxs-lookup"><span data-stu-id="cae86-129">For example, a handler utilizing a `SameAuthorRequirement` and a `Document` resource follows:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

<span data-ttu-id="cae86-130">在上述範例中，假設 `SameAuthorRequirement` 是更泛型類別的特殊案例 `SpecificAuthorRequirement` 。</span><span class="sxs-lookup"><span data-stu-id="cae86-130">In the preceding example, imagine that `SameAuthorRequirement` is a special case of a more generic `SpecificAuthorRequirement` class.</span></span> <span data-ttu-id="cae86-131">`SpecificAuthorRequirement`未顯示的類別 () 包含 `Name` 代表作者名稱的屬性。</span><span class="sxs-lookup"><span data-stu-id="cae86-131">The `SpecificAuthorRequirement` class (not shown) contains a `Name` property representing the name of the author.</span></span> <span data-ttu-id="cae86-132">`Name`屬性可以設定為目前的使用者。</span><span class="sxs-lookup"><span data-stu-id="cae86-132">The `Name` property could be set to the current user.</span></span>

<span data-ttu-id="cae86-133">在下列情況中註冊需求和處理常式 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="cae86-133">Register the requirement and handler in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=4-8,10)]
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/2_2/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

### <a name="operational-requirements"></a><span data-ttu-id="cae86-134">營運需求</span><span class="sxs-lookup"><span data-stu-id="cae86-134">Operational requirements</span></span>

<span data-ttu-id="cae86-135">如果您要根據 CRUD 的結果進行決策 (建立、讀取、更新、刪除) 作業，請使用 [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) helper 類別。</span><span class="sxs-lookup"><span data-stu-id="cae86-135">If you're making decisions based on the outcomes of CRUD (Create, Read, Update, Delete) operations, use the [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) helper class.</span></span> <span data-ttu-id="cae86-136">這個類別可讓您為每個作業類型撰寫單一處理程式，而不是個別的類別。</span><span class="sxs-lookup"><span data-stu-id="cae86-136">This class enables you to write a single handler instead of an individual class for each operation type.</span></span> <span data-ttu-id="cae86-137">若要使用它，請提供一些操作名稱：</span><span class="sxs-lookup"><span data-stu-id="cae86-137">To use it, provide some operation names:</span></span>

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

<span data-ttu-id="cae86-138">處理常式會使用需求和資源依照下列方式執行 `OperationAuthorizationRequirement` `Document` ：</span><span class="sxs-lookup"><span data-stu-id="cae86-138">The handler is implemented as follows, using an `OperationAuthorizationRequirement` requirement and a `Document` resource:</span></span>

 ::: moniker range=">= aspnetcore-2.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

<span data-ttu-id="cae86-139">上述處理常式會使用資源、使用者的身分識別和需求的屬性來驗證作業 `Name` 。</span><span class="sxs-lookup"><span data-stu-id="cae86-139">The preceding handler validates the operation using the resource, the user's identity, and the requirement's `Name` property.</span></span>

## <a name="challenge-and-forbid-with-an-operational-resource-handler"></a><span data-ttu-id="cae86-140">操作資源處理常式的挑戰和禁止</span><span class="sxs-lookup"><span data-stu-id="cae86-140">Challenge and forbid with an operational resource handler</span></span>

<span data-ttu-id="cae86-141">本節說明如何處理挑戰和禁止動作結果，以及挑戰和禁止的不同之處。</span><span class="sxs-lookup"><span data-stu-id="cae86-141">This section shows how the challenge and forbid action results are processed and how challenge and forbid differ.</span></span>

<span data-ttu-id="cae86-142">若要呼叫操作資源處理常式，請 `AuthorizeAsync` 在頁面處理常式或動作中叫用時，指定作業。</span><span class="sxs-lookup"><span data-stu-id="cae86-142">To call an operational resource handler, specify the operation when invoking `AuthorizeAsync` in your page handler or action.</span></span> <span data-ttu-id="cae86-143">下列範例會決定是否允許已驗證的使用者查看提供的檔。</span><span class="sxs-lookup"><span data-stu-id="cae86-143">The following example determines whether the authenticated user is permitted to view the provided document.</span></span>

> [!NOTE]
> <span data-ttu-id="cae86-144">下列程式碼範例假設驗證已執行，並設定 `User` 屬性。</span><span class="sxs-lookup"><span data-stu-id="cae86-144">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

<span data-ttu-id="cae86-145">如果授權成功，則會傳回用來查看檔的頁面。</span><span class="sxs-lookup"><span data-stu-id="cae86-145">If authorization succeeds, the page for viewing the document is returned.</span></span> <span data-ttu-id="cae86-146">如果授權失敗，但使用者已通過驗證，則傳回會 `ForbidResult` 通知任何驗證中介軟體授權失敗。</span><span class="sxs-lookup"><span data-stu-id="cae86-146">If authorization fails but the user is authenticated, returning `ForbidResult` informs any authentication middleware that authorization failed.</span></span> <span data-ttu-id="cae86-147">`ChallengeResult`必須執行驗證時，才會傳回。</span><span class="sxs-lookup"><span data-stu-id="cae86-147">A `ChallengeResult` is returned when authentication must be performed.</span></span> <span data-ttu-id="cae86-148">如果是互動式瀏覽器用戶端，將使用者重新導向至登入頁面可能會很適當。</span><span class="sxs-lookup"><span data-stu-id="cae86-148">For interactive browser clients, it may be appropriate to redirect the user to a login page.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

<span data-ttu-id="cae86-149">如果授權成功，則會傳回檔的視圖。</span><span class="sxs-lookup"><span data-stu-id="cae86-149">If authorization succeeds, the view for the document is returned.</span></span> <span data-ttu-id="cae86-150">如果授權失敗，傳回會 `ChallengeResult` 通知任何驗證中介軟體授權失敗，中介軟體可以採取適當的回應。</span><span class="sxs-lookup"><span data-stu-id="cae86-150">If authorization fails, returning `ChallengeResult` informs any authentication middleware that authorization failed, and the middleware can take the appropriate response.</span></span> <span data-ttu-id="cae86-151">適當的回應可能會傳回401或403狀態碼。</span><span class="sxs-lookup"><span data-stu-id="cae86-151">An appropriate response could be returning a 401 or 403 status code.</span></span> <span data-ttu-id="cae86-152">針對互動式瀏覽器用戶端，這可能表示將使用者重新導向至登入頁面。</span><span class="sxs-lookup"><span data-stu-id="cae86-152">For interactive browser clients, it could mean redirecting the user to a login page.</span></span>

::: moniker-end
