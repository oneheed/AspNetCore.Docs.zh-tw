---
title: ASP.NET Core 中以資源為基礎的授權
author: scottaddie
description: 瞭解當授權屬性不足夠時，如何在 ASP.NET Core 應用程式中執行以資源為基礎的授權。
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
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
uid: security/authorization/resourcebased
ms.openlocfilehash: bb47f3452d29dfeea0e4d3c4a9c22a06869a3fe7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631351"
---
# <a name="resource-based-authorization-in-aspnet-core"></a>ASP.NET Core 中以資源為基礎的授權

授權策略取決於所存取的資源。 請考慮具有 author 屬性的檔。 只允許作者更新檔。 因此，必須先從資料存放區取出檔，授權評估才能進行。

屬性評估發生于資料系結之前，以及在執行載入檔的頁面處理常式或動作之前。 基於這些理由，具有屬性的宣告式授權 `[Authorize]` 並不足夠。 相反地，您可以 &mdash; 使用稱為 *命令式授權*的樣式來叫用自訂授權方法。

::: moniker range=">= aspnetcore-3.0"
[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/3_0) ([如何下載](xref:index#how-to-download-a-sample))。
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/2_2) ([如何下載](xref:index#how-to-download-a-sample))。
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/1_1) ([如何下載](xref:index#how-to-download-a-sample))。
::: moniker-end

使用以[授權保護的使用者資料建立 ASP.NET Core 應用程式](xref:security/authorization/secure-data)包含使用以資源為基礎之授權的範例應用程式。

## <a name="use-imperative-authorization"></a>使用命令式授權

授權會實作為 [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) 服務，並在類別中的服務集合中註冊 `Startup` 。 這項服務可透過相依性 [插入](xref:fundamentals/dependency-injection) 來提供給頁面處理常式或動作。

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

`IAuthorizationService` 有兩個 `AuthorizeAsync` 方法多載：一個接受資源和原則名稱，另一個則接受資源和要評估的需求清單。

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

在下列範例中，要保護的資源會載入至自訂 `Document` 物件。 系統會叫用多載 `AuthorizeAsync` ，以判斷是否允許目前的使用者編輯提供的檔。 自訂的「EditPolicy」授權原則會納入決策中。 如需建立授權原則的詳細資訊，請參閱 [自訂以原則為基礎的授權](xref:security/authorization/policies) 。

> [!NOTE]
> 下列程式碼範例假設驗證已執行，並設定 `User` 屬性。

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a>撰寫以資源為基礎的處理常式

針對以資源為基礎的授權撰寫處理常式，與 [撰寫一般需求處理常式](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler)並沒有太大差異。 建立自訂需求類別，並執行需求處理常式類別。 如需建立需求類別的詳細資訊，請參閱 [需求](xref:security/authorization/policies#requirements)。

處理常式類別會指定需求和資源類型。 例如，利用和資源的處理常式 `SameAuthorRequirement` `Document` 如下：

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

在上述範例中，假設 `SameAuthorRequirement` 是更泛型類別的特殊案例 `SpecificAuthorRequirement` 。 `SpecificAuthorRequirement`未顯示的類別 () 包含 `Name` 代表作者名稱的屬性。 `Name`屬性可以設定為目前的使用者。

在下列情況中註冊需求和處理常式 `Startup.ConfigureServices` ：

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=4-8,10)]
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/2_2/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

### <a name="operational-requirements"></a>營運需求

如果您要根據 CRUD 的結果進行決策 (建立、讀取、更新、刪除) 作業，請使用 [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) helper 類別。 這個類別可讓您為每個作業類型撰寫單一處理程式，而不是個別的類別。 若要使用它，請提供一些操作名稱：

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

處理常式會使用需求和資源依照下列方式執行 `OperationAuthorizationRequirement` `Document` ：

 ::: moniker range=">= aspnetcore-2.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

上述處理常式會使用資源、使用者的身分識別和需求的屬性來驗證作業 `Name` 。

## <a name="challenge-and-forbid-with-an-operational-resource-handler"></a>操作資源處理常式的挑戰和禁止

本節說明如何處理挑戰和禁止動作結果，以及挑戰和禁止的不同之處。

若要呼叫操作資源處理常式，請 `AuthorizeAsync` 在頁面處理常式或動作中叫用時，指定作業。 下列範例會決定是否允許已驗證的使用者查看提供的檔。

> [!NOTE]
> 下列程式碼範例假設驗證已執行，並設定 `User` 屬性。

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

如果授權成功，則會傳回用來查看檔的頁面。 如果授權失敗，但使用者已通過驗證，則傳回會 `ForbidResult` 通知任何驗證中介軟體授權失敗。 `ChallengeResult`必須執行驗證時，才會傳回。 如果是互動式瀏覽器用戶端，將使用者重新導向至登入頁面可能會很適當。

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

如果授權成功，則會傳回檔的視圖。 如果授權失敗，傳回會 `ChallengeResult` 通知任何驗證中介軟體授權失敗，中介軟體可以採取適當的回應。 適當的回應可能會傳回401或403狀態碼。 針對互動式瀏覽器用戶端，這可能表示將使用者重新導向至登入頁面。

::: moniker-end
