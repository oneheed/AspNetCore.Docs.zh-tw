---
title: 使用驗證程式庫保護 ASP.NET Core 的 Blazor WebAssembly 獨立應用程式
author: guardrex
description: 瞭解如何使用驗證程式庫保護 ASP.NET Core 的 Blazor WebAssembly 獨立應用程式。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: b16533ef662b341053498162ce4ecb62445f2061
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854417"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-the-authentication-library"></a>使用驗證程式庫保護 ASP.NET Core 的 Blazor WebAssembly 獨立應用程式

由 [Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)

*針對 Azure Active Directory (AAD) 和 Azure Active Directory B2C (AAD B2C) ，請不要依照本主題中的指導方針進行。請參閱此目錄節點中的 AAD 和 AAD B2C 主題。*

若要建立使用程式庫的 [獨立 Blazor WebAssembly 應用程式](xref:blazor/hosting-models#blazor-webassembly) [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) ，請遵循您選擇的工具指導方針。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

若要建立 Blazor WebAssembly 具有驗證機制的新專案：

1. 在 [**建立新的 ASP.NET Core Web 應用程式**] 對話方塊中選擇 **Blazor WebAssembly 應用程式** 範本之後，請選取 [**驗證**] 下的 [**變更**]。

1. 使用「**儲存使用者帳戶應用程式內**」選項選取 **個別使用者帳戶**，以使用 ASP.NET Core 的系統將使用者儲存在應用程式內 [Identity](xref:security/authentication/identity) 。

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code / .NET Core CLI](#tab/visual-studio-code+netcore-cli)

Blazor WebAssembly在空的資料夾中建立具有驗證機制的新專案。 `Individual` `-au|--auth` 使用 ASP.NET Core 的系統，指定可選擇在應用程式中儲存使用者的驗證機制 [Identity](xref:security/authentication/identity) ：

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| 預留位置  | 範例        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

使用 `-o|--output` 選項指定的輸出位置會建立專案資料夾 (如果不存在)，並成為應用程式名稱的一部分。

如需詳細資訊，請參閱 [`dotnet new`](/dotnet/core/tools/dotnet-new) .Net Core 指南中的命令。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

若要建立 Blazor WebAssembly 具有驗證機制的新專案：

1. 在 [**設定新的 Blazor WebAssembly 應用程式**] 步驟中，從 [**驗證**] 下拉式清單中選取 [**應用程式內) 的個別驗證 (** 。

1. 應用程式會針對使用 ASP.NET Core 儲存在應用程式中的個別使用者建立 [Identity](xref:security/authentication/identity) 。

---

## <a name="authentication-package"></a>驗證套件

建立應用程式以使用個別的使用者帳戶時，應用程式會 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 在應用程式的專案檔中自動收到套件的套件參考。 此套件提供一組基本類型，可協助應用程式驗證使用者，並取得權杖以呼叫受保護的 Api。

如果將驗證新增至應用程式，請手動將套件新增到應用程式的專案檔：

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （ [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)）中找到符合應用程式共用架構版本的最新穩定版本套件。

## <a name="authentication-service-support"></a>驗證服務支援

驗證使用者的支援是在服務容器中註冊，並具有 <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 由封裝提供的擴充方法 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 。 此方法會設定應用程式 Identity (IP) 與提供者互動所需的服務。

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

設定是由檔案提供 `wwwroot/appsettings.json` ：

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

獨立應用程式的驗證支援是使用 OpenID Connect (OIDC) 提供。 <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>方法會接受回呼，以使用 OIDC 來設定驗證應用程式所需的參數。 設定應用程式所需的值可以從符合 OIDC 規範的 IP 取得。 當您註冊應用程式時取得值，這通常會發生在其線上入口網站中。

## <a name="access-token-scopes"></a>存取權杖範圍

此 Blazor WebAssembly 範本會自動為和設定預設範圍 `openid` `profile` 。

Blazor WebAssembly範本不會自動將應用程式設定為要求安全 API 的存取權杖。 若要布建存取權杖作為登入流程的一部分，請將範圍新增至的預設權杖範圍 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> ：

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

如需詳細資訊，請參閱下列 *其他案例* 文章的各節：

* [要求其他存取權杖](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [將權杖附加至傳出要求](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>匯入檔案

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a>索引頁面

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

## <a name="app-component"></a>應用程式元件

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin 元件

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay 元件

`LoginDisplay`元件 (`Shared/LoginDisplay.razor`) 會轉譯在 `MainLayout` 元件 (`Shared/MainLayout.razor`) 並管理下列行為：

* 針對已驗證的使用者：
  * 顯示目前的使用者名稱。
  * 提供用來登出應用程式的按鈕。
* 若為匿名使用者，會提供登入的選項。

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a>驗證元件

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a>其他資源

* <xref:blazor/security/webassembly/additional-scenarios>
* [具有安全預設用戶端之應用程式中未經驗證或未經授權的 web API 要求](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
