---
title: 使用驗證程式庫保護 ASP.NET Core 的 Blazor WebAssembly 獨立應用程式
author: guardrex
description: 瞭解如何使用驗證程式庫保護 ASP.NET Core 的 Blazor WebAssembly 獨立應用程式。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 795709853941f35b1645f72d6865fe1ebf935112
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712359"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="f7144-103">使用驗證程式庫保護 ASP.NET Core 的 Blazor WebAssembly 獨立應用程式</span><span class="sxs-lookup"><span data-stu-id="f7144-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="f7144-104">由 [Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f7144-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f7144-105">*針對 Azure Active Directory (AAD) 和 Azure Active Directory B2C (AAD B2C) ，請不要依照本主題中的指導方針進行。請參閱此目錄節點中的 AAD 和 AAD B2C 主題。*</span><span class="sxs-lookup"><span data-stu-id="f7144-105">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="f7144-106">若要建立使用程式庫的 [獨立 Blazor WebAssembly 應用程式](xref:blazor/hosting-models#blazor-webassembly) [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) ，請遵循您選擇的工具指導方針。</span><span class="sxs-lookup"><span data-stu-id="f7144-106">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library, follow the guidance for your choice of tooling.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f7144-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7144-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f7144-108">若要建立 Blazor WebAssembly 具有驗證機制的新專案：</span><span class="sxs-lookup"><span data-stu-id="f7144-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="f7144-109">在 [**建立新的 ASP.NET Core Web 應用程式**] 對話方塊中選擇\*\* Blazor WebAssembly 應用程式**範本之後，請選取 [**驗證**] 下的 [**變更\*\*]。</span><span class="sxs-lookup"><span data-stu-id="f7144-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="f7144-110">使用「**儲存使用者帳戶應用程式內**」選項選取**個別使用者帳戶**，以使用 ASP.NET Core 的系統將使用者儲存在應用程式內 [Identity](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="f7144-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="f7144-111">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f7144-111">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="f7144-112">Blazor WebAssembly在空的資料夾中建立具有驗證機制的新專案。</span><span class="sxs-lookup"><span data-stu-id="f7144-112">Create a new Blazor WebAssembly project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="f7144-113">`Individual` `-au|--auth` 使用 ASP.NET Core 的系統，指定可選擇在應用程式中儲存使用者的驗證機制 [Identity](xref:security/authentication/identity) ：</span><span class="sxs-lookup"><span data-stu-id="f7144-113">Specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="f7144-114">預留位置</span><span class="sxs-lookup"><span data-stu-id="f7144-114">Placeholder</span></span>  | <span data-ttu-id="f7144-115">範例</span><span class="sxs-lookup"><span data-stu-id="f7144-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="f7144-116">使用選項指定的輸出位置會 `-o|--output` 建立專案資料夾（如果不存在），而且會成為應用程式名稱的一部分。</span><span class="sxs-lookup"><span data-stu-id="f7144-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="f7144-117">如需詳細資訊，請參閱 [`dotnet new`](/dotnet/core/tools/dotnet-new) .Net Core 指南中的命令。</span><span class="sxs-lookup"><span data-stu-id="f7144-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f7144-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f7144-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f7144-119">若要建立 Blazor WebAssembly 具有驗證機制的新專案：</span><span class="sxs-lookup"><span data-stu-id="f7144-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="f7144-120">在 [**設定新的 Blazor WebAssembly 應用程式**] 步驟中，從 [**驗證**] 下拉式清單中選取 [**應用程式內) 的個別驗證 (** 。</span><span class="sxs-lookup"><span data-stu-id="f7144-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="f7144-121">應用程式會針對使用 ASP.NET Core 儲存在應用程式中的個別使用者建立 [Identity](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="f7144-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="f7144-122">驗證套件</span><span class="sxs-lookup"><span data-stu-id="f7144-122">Authentication package</span></span>

<span data-ttu-id="f7144-123">建立應用程式以使用個別的使用者帳戶時，應用程式會 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 在應用程式的專案檔中自動收到套件的套件參考。</span><span class="sxs-lookup"><span data-stu-id="f7144-123">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="f7144-124">此套件提供一組基本類型，可協助應用程式驗證使用者，並取得權杖以呼叫受保護的 Api。</span><span class="sxs-lookup"><span data-stu-id="f7144-124">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="f7144-125">如果將驗證新增至應用程式，請手動將套件新增到應用程式的專案檔：</span><span class="sxs-lookup"><span data-stu-id="f7144-125">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="f7144-126">針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （ [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)）中找到符合應用程式共用架構版本的最新穩定版本套件。</span><span class="sxs-lookup"><span data-stu-id="f7144-126">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="f7144-127">驗證服務支援</span><span class="sxs-lookup"><span data-stu-id="f7144-127">Authentication service support</span></span>

<span data-ttu-id="f7144-128">驗證使用者的支援是在服務容器中註冊，並具有 <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 由封裝提供的擴充方法 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 。</span><span class="sxs-lookup"><span data-stu-id="f7144-128">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="f7144-129">此方法會設定應用程式 Identity (IP) 與提供者互動所需的服務。</span><span class="sxs-lookup"><span data-stu-id="f7144-129">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="f7144-130">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="f7144-130">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="f7144-131">設定是由檔案提供 `wwwroot/appsettings.json` ：</span><span class="sxs-lookup"><span data-stu-id="f7144-131">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="f7144-132">獨立應用程式的驗證支援是使用 OpenID Connect (OIDC) 提供。</span><span class="sxs-lookup"><span data-stu-id="f7144-132">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="f7144-133"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>方法會接受回呼，以使用 OIDC 來設定驗證應用程式所需的參數。</span><span class="sxs-lookup"><span data-stu-id="f7144-133">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="f7144-134">設定應用程式所需的值可以從符合 OIDC 規範的 IP 取得。</span><span class="sxs-lookup"><span data-stu-id="f7144-134">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="f7144-135">當您註冊應用程式時取得值，這通常會發生在其線上入口網站中。</span><span class="sxs-lookup"><span data-stu-id="f7144-135">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="f7144-136">存取權杖範圍</span><span class="sxs-lookup"><span data-stu-id="f7144-136">Access token scopes</span></span>

<span data-ttu-id="f7144-137">Blazor WebAssembly範本不會自動將應用程式設定為要求安全 API 的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="f7144-137">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="f7144-138">若要布建存取權杖作為登入流程的一部分，請將範圍新增至的預設權杖範圍 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> ：</span><span class="sxs-lookup"><span data-stu-id="f7144-138">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="f7144-139">如需詳細資訊，請參閱下列 *其他案例* 文章的各節：</span><span class="sxs-lookup"><span data-stu-id="f7144-139">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="f7144-140">要求其他存取權杖</span><span class="sxs-lookup"><span data-stu-id="f7144-140">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="f7144-141">將權杖附加至傳出要求</span><span class="sxs-lookup"><span data-stu-id="f7144-141">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="f7144-142">匯入檔案</span><span class="sxs-lookup"><span data-stu-id="f7144-142">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="f7144-143">索引頁面</span><span class="sxs-lookup"><span data-stu-id="f7144-143">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="f7144-144">應用程式元件</span><span class="sxs-lookup"><span data-stu-id="f7144-144">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="f7144-145">RedirectToLogin 元件</span><span class="sxs-lookup"><span data-stu-id="f7144-145">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="f7144-146">LoginDisplay 元件</span><span class="sxs-lookup"><span data-stu-id="f7144-146">LoginDisplay component</span></span>

<span data-ttu-id="f7144-147">`LoginDisplay`元件 (`Shared/LoginDisplay.razor`) 會轉譯在 `MainLayout` 元件 (`Shared/MainLayout.razor`) 並管理下列行為：</span><span class="sxs-lookup"><span data-stu-id="f7144-147">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="f7144-148">針對已驗證的使用者：</span><span class="sxs-lookup"><span data-stu-id="f7144-148">For authenticated users:</span></span>
  * <span data-ttu-id="f7144-149">顯示目前的使用者名稱。</span><span class="sxs-lookup"><span data-stu-id="f7144-149">Displays the current username.</span></span>
  * <span data-ttu-id="f7144-150">提供用來登出應用程式的按鈕。</span><span class="sxs-lookup"><span data-stu-id="f7144-150">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="f7144-151">若為匿名使用者，會提供登入的選項。</span><span class="sxs-lookup"><span data-stu-id="f7144-151">For anonymous users, offers the option to log in.</span></span>

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

## <a name="authentication-component"></a><span data-ttu-id="f7144-152">驗證元件</span><span class="sxs-lookup"><span data-stu-id="f7144-152">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="f7144-153">其他資源</span><span class="sxs-lookup"><span data-stu-id="f7144-153">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="f7144-154">具有安全預設用戶端之應用程式中未經驗證或未經授權的 web API 要求</span><span class="sxs-lookup"><span data-stu-id="f7144-154">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
