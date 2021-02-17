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
ms.openlocfilehash: bdb60608ba4bd99b24e458f0543b1f4226cadf44
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552631"
---
<span data-ttu-id="5b2b2-101">`LoginDisplay`元件 (`Shared/LoginDisplay.razor`) 會轉譯在 `MainLayout` 元件 (`Shared/MainLayout.razor`) 並管理下列行為：</span><span class="sxs-lookup"><span data-stu-id="5b2b2-101">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="5b2b2-102">針對已驗證的使用者：</span><span class="sxs-lookup"><span data-stu-id="5b2b2-102">For authenticated users:</span></span>
  * <span data-ttu-id="5b2b2-103">顯示目前的使用者名稱。</span><span class="sxs-lookup"><span data-stu-id="5b2b2-103">Displays the current username.</span></span>
  * <span data-ttu-id="5b2b2-104">提供用來登出應用程式的按鈕。</span><span class="sxs-lookup"><span data-stu-id="5b2b2-104">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="5b2b2-105">若為匿名使用者，會提供登入的選項。</span><span class="sxs-lookup"><span data-stu-id="5b2b2-105">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginLogout">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginLogout(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```
