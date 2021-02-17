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
ms.openlocfilehash: d632ab0604f81f7b6067d4535b0f5da0afe2e0ad
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552242"
---
`App`元件 (`App.razor`) 類似于 `App` 在應用程式中找到的元件 Blazor Server ：

* <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState>元件會管理將公開 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> 給應用程式的其餘部分。
* <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView>元件可確保目前的使用者已獲授權可存取指定的頁面或轉譯 `RedirectToLogin` 元件。
* `RedirectToLogin`元件會管理將未經授權的使用者重新導向至登入頁面。

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!include[](../prefer-exact-matches.md)]
