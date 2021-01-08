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
ms.openlocfilehash: a4f3234aa4b4b02244d17615a9033db3094d3580
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024778"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="2185f-103">使用驗證程式庫保護 ASP.NET Core 的 Blazor WebAssembly 獨立應用程式</span><span class="sxs-lookup"><span data-stu-id="2185f-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="2185f-104">由 [Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2185f-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2185f-105">*針對 Azure Active Directory (AAD) 和 Azure Active Directory B2C (AAD B2C) ，請不要依照本主題中的指導方針進行。請參閱此目錄節點中的 AAD 和 AAD B2C 主題。*</span><span class="sxs-lookup"><span data-stu-id="2185f-105">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="2185f-106">若要建立使用程式庫的 [獨立 Blazor WebAssembly 應用程式](xref:blazor/hosting-models#blazor-webassembly) [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) ，請遵循您選擇的工具指導方針。</span><span class="sxs-lookup"><span data-stu-id="2185f-106">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library, follow the guidance for your choice of tooling.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2185f-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2185f-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2185f-108">若要建立 Blazor WebAssembly 具有驗證機制的新專案：</span><span class="sxs-lookup"><span data-stu-id="2185f-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="2185f-109">在 [**建立新的 ASP.NET Core Web 應用程式**] 對話方塊中選擇 **Blazor WebAssembly 應用程式** 範本之後，請選取 [**驗證**] 下的 [**變更**]。</span><span class="sxs-lookup"><span data-stu-id="2185f-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="2185f-110">使用「**儲存使用者帳戶應用程式內**」選項選取 **個別使用者帳戶**，以使用 ASP.NET Core 的系統將使用者儲存在應用程式內 [Identity](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="2185f-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="2185f-111">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2185f-111">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="2185f-112">Blazor WebAssembly在空的資料夾中建立具有驗證機制的新專案。</span><span class="sxs-lookup"><span data-stu-id="2185f-112">Create a new Blazor WebAssembly project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="2185f-113">`Individual` `-au|--auth` 使用 ASP.NET Core 的系統，指定可選擇在應用程式中儲存使用者的驗證機制 [Identity](xref:security/authentication/identity) ：</span><span class="sxs-lookup"><span data-stu-id="2185f-113">Specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="2185f-114">預留位置</span><span class="sxs-lookup"><span data-stu-id="2185f-114">Placeholder</span></span>  | <span data-ttu-id="2185f-115">範例</span><span class="sxs-lookup"><span data-stu-id="2185f-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="2185f-116">使用 `-o|--output` 選項指定的輸出位置會建立專案資料夾 (如果不存在)，並成為應用程式名稱的一部分。</span><span class="sxs-lookup"><span data-stu-id="2185f-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="2185f-117">如需詳細資訊，請參閱 [`dotnet new`](/dotnet/core/tools/dotnet-new) .Net Core 指南中的命令。</span><span class="sxs-lookup"><span data-stu-id="2185f-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2185f-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2185f-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2185f-119">若要建立 Blazor WebAssembly 具有驗證機制的新專案：</span><span class="sxs-lookup"><span data-stu-id="2185f-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="2185f-120">在 [**設定新的 Blazor WebAssembly 應用程式**] 步驟中，從 [**驗證**] 下拉式清單中選取 [**應用程式內) 的個別驗證 (** 。</span><span class="sxs-lookup"><span data-stu-id="2185f-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="2185f-121">應用程式會針對使用 ASP.NET Core 儲存在應用程式中的個別使用者建立 [Identity](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="2185f-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="2185f-122">驗證套件</span><span class="sxs-lookup"><span data-stu-id="2185f-122">Authentication package</span></span>

<span data-ttu-id="2185f-123">建立應用程式以使用個別的使用者帳戶時，應用程式會 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 在應用程式的專案檔中自動收到套件的套件參考。</span><span class="sxs-lookup"><span data-stu-id="2185f-123">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="2185f-124">此套件提供一組基本類型，可協助應用程式驗證使用者，並取得權杖以呼叫受保護的 Api。</span><span class="sxs-lookup"><span data-stu-id="2185f-124">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="2185f-125">如果將驗證新增至應用程式，請手動將套件新增到應用程式的專案檔：</span><span class="sxs-lookup"><span data-stu-id="2185f-125">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="2185f-126">針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （ [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)）中找到符合應用程式共用架構版本的最新穩定版本套件。</span><span class="sxs-lookup"><span data-stu-id="2185f-126">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="2185f-127">驗證服務支援</span><span class="sxs-lookup"><span data-stu-id="2185f-127">Authentication service support</span></span>

<span data-ttu-id="2185f-128">驗證使用者的支援是在服務容器中註冊，並具有 <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 由封裝提供的擴充方法 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 。</span><span class="sxs-lookup"><span data-stu-id="2185f-128">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="2185f-129">此方法會設定應用程式 Identity (IP) 與提供者互動所需的服務。</span><span class="sxs-lookup"><span data-stu-id="2185f-129">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="2185f-130">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="2185f-130">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="2185f-131">設定是由檔案提供 `wwwroot/appsettings.json` ：</span><span class="sxs-lookup"><span data-stu-id="2185f-131">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="2185f-132">獨立應用程式的驗證支援是使用 OpenID Connect (OIDC) 提供。</span><span class="sxs-lookup"><span data-stu-id="2185f-132">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="2185f-133"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>方法會接受回呼，以使用 OIDC 來設定驗證應用程式所需的參數。</span><span class="sxs-lookup"><span data-stu-id="2185f-133">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="2185f-134">設定應用程式所需的值可以從符合 OIDC 規範的 IP 取得。</span><span class="sxs-lookup"><span data-stu-id="2185f-134">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="2185f-135">當您註冊應用程式時取得值，這通常會發生在其線上入口網站中。</span><span class="sxs-lookup"><span data-stu-id="2185f-135">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="2185f-136">存取權杖範圍</span><span class="sxs-lookup"><span data-stu-id="2185f-136">Access token scopes</span></span>

<span data-ttu-id="2185f-137">此 Blazor WebAssembly 範本會自動為和設定預設範圍 `openid` `profile` 。</span><span class="sxs-lookup"><span data-stu-id="2185f-137">The Blazor WebAssembly template automatically configures default scopes for `openid` and `profile`.</span></span>

<span data-ttu-id="2185f-138">Blazor WebAssembly範本不會自動將應用程式設定為要求安全 API 的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="2185f-138">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="2185f-139">若要布建存取權杖作為登入流程的一部分，請將範圍新增至的預設權杖範圍 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> ：</span><span class="sxs-lookup"><span data-stu-id="2185f-139">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="2185f-140">如需詳細資訊，請參閱下列 *其他案例* 文章的各節：</span><span class="sxs-lookup"><span data-stu-id="2185f-140">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="2185f-141">要求其他存取權杖</span><span class="sxs-lookup"><span data-stu-id="2185f-141">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="2185f-142">將權杖附加至傳出要求</span><span class="sxs-lookup"><span data-stu-id="2185f-142">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="2185f-143">匯入檔案</span><span class="sxs-lookup"><span data-stu-id="2185f-143">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="2185f-144">索引頁面</span><span class="sxs-lookup"><span data-stu-id="2185f-144">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="2185f-145">應用程式元件</span><span class="sxs-lookup"><span data-stu-id="2185f-145">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="2185f-146">RedirectToLogin 元件</span><span class="sxs-lookup"><span data-stu-id="2185f-146">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="2185f-147">LoginDisplay 元件</span><span class="sxs-lookup"><span data-stu-id="2185f-147">LoginDisplay component</span></span>

<span data-ttu-id="2185f-148">`LoginDisplay`元件 (`Shared/LoginDisplay.razor`) 會轉譯在 `MainLayout` 元件 (`Shared/MainLayout.razor`) 並管理下列行為：</span><span class="sxs-lookup"><span data-stu-id="2185f-148">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="2185f-149">針對已驗證的使用者：</span><span class="sxs-lookup"><span data-stu-id="2185f-149">For authenticated users:</span></span>
  * <span data-ttu-id="2185f-150">顯示目前的使用者名稱。</span><span class="sxs-lookup"><span data-stu-id="2185f-150">Displays the current username.</span></span>
  * <span data-ttu-id="2185f-151">提供用來登出應用程式的按鈕。</span><span class="sxs-lookup"><span data-stu-id="2185f-151">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="2185f-152">若為匿名使用者，會提供登入的選項。</span><span class="sxs-lookup"><span data-stu-id="2185f-152">For anonymous users, offers the option to log in.</span></span>

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

## <a name="authentication-component"></a><span data-ttu-id="2185f-153">驗證元件</span><span class="sxs-lookup"><span data-stu-id="2185f-153">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="2185f-154">其他資源</span><span class="sxs-lookup"><span data-stu-id="2185f-154">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="2185f-155">具有安全預設用戶端之應用程式中未經驗證或未經授權的 web API 要求</span><span class="sxs-lookup"><span data-stu-id="2185f-155">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <span data-ttu-id="2185f-156"><xref:host-and-deploy/proxy-load-balancer>：包含下列相關指引：</span><span class="sxs-lookup"><span data-stu-id="2185f-156"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="2185f-157">使用轉送的標頭中介軟體，在 proxy 伺服器和內部網路之間保留 HTTPS 配置資訊。</span><span class="sxs-lookup"><span data-stu-id="2185f-157">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="2185f-158">其他案例和使用案例，包括手動設定設定、正確要求路由的要求路徑變更，以及轉送 Linux 和非 IIS 反向 proxy 的要求配置。</span><span class="sxs-lookup"><span data-stu-id="2185f-158">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
