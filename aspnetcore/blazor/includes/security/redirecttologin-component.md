---
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
ms.openlocfilehash: 9b7b302485a5c9ab7d1b1da6ce4d8ab7d1c26f9c
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551414"
---
<span data-ttu-id="fb321-101">`RedirectToLogin`元件 (`Shared/RedirectToLogin.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="fb321-101">The `RedirectToLogin` component (`Shared/RedirectToLogin.razor`):</span></span>

* <span data-ttu-id="fb321-102">管理將未經授權的使用者重新導向至登入頁面。</span><span class="sxs-lookup"><span data-stu-id="fb321-102">Manages redirecting unauthorized users to the login page.</span></span>
* <span data-ttu-id="fb321-103">保留使用者嘗試存取的目前 URL，以便在驗證成功時，將其傳回至該頁面。</span><span class="sxs-lookup"><span data-stu-id="fb321-103">Preserves the current URL that the user is attempting to access so that they can be returned to that page if authentication is successful.</span></span>

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(
            $"authentication/login?returnUrl={Uri.EscapeDataString(Navigation.Uri)}");
    }
}
```
