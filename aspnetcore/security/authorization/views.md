---
title: ASP.NET Core MVC 中以視圖為基礎的授權
author: rick-anderson
description: 本檔示範如何在 ASP.NET Core 視圖內插入及利用授權服務 Razor 。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/views
ms.openlocfilehash: 775ebdffe2b0753de18bf07d9ff1193235a45b17
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629882"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a>ASP.NET Core MVC 中以視圖為基礎的授權

開發人員通常會想要根據目前的使用者身分識別來顯示、隱藏或修改 UI。 您可以透過相依性 [插入](xref:fundamentals/dependency-injection)來存取 MVC 視圖內的授權服務。 若要將授權服務插入至某個 Razor 視圖，請使用指示詞 `@inject` ：

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

如果您想要每個視圖中的授權服務，請將指示詞放 `@inject` 入*Views*目錄的 *_ViewImports. cshtml*檔案中。 如需詳細資訊，請參閱[在檢視中插入相依性](xref:mvc/views/dependency-injection)。

使用插入的授權服務，以 `AuthorizeAsync` 您在以 [資源為基礎的授權](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)期間檢查的相同方式進行叫用：

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

在某些情況下，資源將會是您的 view 模型。 `AuthorizeAsync`在以[資源為基礎的授權](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)期間，以完全相同的方式叫用：

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

在上述程式碼中，模型會以資源的形式傳遞，原則評估應考慮考慮。

> [!WARNING]
> 請勿依賴切換應用程式 UI 元素的可見度作為唯一的授權檢查。 隱藏 UI 元素可能無法完全防止存取其相關聯的控制器動作。 例如，請考慮上述程式碼片段中的按鈕。 `Edit`如果使用者知道相對資源 URL 是 */Document/Edit/1*，則使用者可以叫用該動作方法。 基於這個理由， `Edit` 動作方法應該執行它自己的授權檢查。
