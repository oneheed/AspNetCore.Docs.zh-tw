---
title: 使用 Azure Active Directory 保護 ASP.NET Core 的 Blazor WebAssembly 獨立應用程式
author: guardrex
description: 瞭解如何使用 Azure Active Directory 保護 ASP.NET Core 的 Blazor WebAssembly 獨立應用程式。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 50aed0e0bc9058e54518512b9570ca0fdcde9b4e
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712424"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="eb3cc-103">使用 Azure Active Directory 保護 ASP.NET Core 的 Blazor WebAssembly 獨立應用程式</span><span class="sxs-lookup"><span data-stu-id="eb3cc-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="eb3cc-104">由 [Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="eb3cc-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="eb3cc-105">若要建立使用[Azure Active Directory (AAD) ](https://azure.microsoft.com/services/active-directory/)進行驗證的[獨立 Blazor WebAssembly 應用程式](xref:blazor/hosting-models#blazor-webassembly)：</span><span class="sxs-lookup"><span data-stu-id="eb3cc-105">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="eb3cc-106">[建立 AAD 租使用者與 web 應用程式](/azure/active-directory/develop/v2-overview)：</span><span class="sxs-lookup"><span data-stu-id="eb3cc-106">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="eb3cc-107">在 Azure 入口網站的**Azure Active Directory**  >  **應用程式註冊**區域中註冊 AAD 應用程式：</span><span class="sxs-lookup"><span data-stu-id="eb3cc-107">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="eb3cc-108">提供應用程式的**名稱** (例如\*\* Blazor 獨立 AAD\*\*) 。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-108">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="eb3cc-109">選擇 **支援的帳戶類型**。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-109">Choose a **Supported account types**.</span></span> <span data-ttu-id="eb3cc-110">您可以 **只在此體驗中選取此組織目錄中的帳戶** 。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-110">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="eb3cc-111">將 [重新 **導向 URI** ] 下拉式清單保持設定為 [ **Web** ]，並提供下列重新導向 uri： `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-111">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="eb3cc-112">在 Kestrel 上執行之應用程式的預設埠是5001。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-112">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="eb3cc-113">如果應用程式是在不同的 Kestrel 埠上執行，請使用應用程式的埠。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-113">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="eb3cc-114">針對 IIS Express，可在 [ **調試** 程式] 面板的應用程式屬性中找到應用程式隨機產生的埠。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-114">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="eb3cc-115">由於應用程式目前不存在，且 IIS Express 埠未知，因此在建立應用程式之後，請返回此步驟，並更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-115">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="eb3cc-116">本主題稍後會出現一個批註，提醒 IIS Express 使用者更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-116">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="eb3cc-117">停用 **[授與系統**  >  **管理員同意 openid 和 offline_access 許可權**] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-117">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="eb3cc-118">選取 [註冊]。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-118">Select **Register**.</span></span>

<span data-ttu-id="eb3cc-119">記錄下列資訊：</span><span class="sxs-lookup"><span data-stu-id="eb3cc-119">Record the following information:</span></span>

* <span data-ttu-id="eb3cc-120">應用程式 (用戶端) 識別碼 (例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`) </span><span class="sxs-lookup"><span data-stu-id="eb3cc-120">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="eb3cc-121">目錄 (租使用者) 識別碼 (例如 `e86c78e2-8bb4-4c41-aefd-918e0565a45e`) </span><span class="sxs-lookup"><span data-stu-id="eb3cc-121">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="eb3cc-122">在 [**驗證**  >  **平臺**設定]  >  **Web**：</span><span class="sxs-lookup"><span data-stu-id="eb3cc-122">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="eb3cc-123">確認的重新 **導向 URI** `https://localhost:{PORT}/authentication/login-callback` 存在。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-123">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="eb3cc-124">針對 **[隱含授**與]，選取 **存取權杖** 和 **識別碼權杖**的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-124">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="eb3cc-125">此體驗可接受應用程式的其餘預設值。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-125">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="eb3cc-126">選取 [儲存] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-126">Select the **Save** button.</span></span>

<span data-ttu-id="eb3cc-127">在空的資料夾中建立應用程式。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-127">Create the app in an empty folder.</span></span> <span data-ttu-id="eb3cc-128">將下列命令中的預留位置取代為稍早記錄的資訊，然後在命令 shell 中執行命令：</span><span class="sxs-lookup"><span data-stu-id="eb3cc-128">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="eb3cc-129">預留位置</span><span class="sxs-lookup"><span data-stu-id="eb3cc-129">Placeholder</span></span>   | <span data-ttu-id="eb3cc-130">Azure 入口網站名稱</span><span class="sxs-lookup"><span data-stu-id="eb3cc-130">Azure portal name</span></span>       | <span data-ttu-id="eb3cc-131">範例</span><span class="sxs-lookup"><span data-stu-id="eb3cc-131">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="eb3cc-132">應用程式 (用戶端) 識別碼</span><span class="sxs-lookup"><span data-stu-id="eb3cc-132">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="eb3cc-133">目錄 (租用戶) 識別碼</span><span class="sxs-lookup"><span data-stu-id="eb3cc-133">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="eb3cc-134">使用選項指定的輸出位置會 `-o|--output` 建立專案資料夾（如果不存在），而且會成為應用程式名稱的一部分。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-134">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="eb3cc-135">在 Azure 入口網站中，會針對**Authentication**  >  **Platform configurations**  >  **Web**  >  使用預設設定在 Kestrel 伺服器上執行的應用程式，將應用程式的驗證平臺設定 Web 重新**導向 URI**設定為使用埠5001。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-135">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="eb3cc-136">如果應用程式是在隨機的 IIS Express 埠上執行，則可以在 [ **調試** 程式] 面板的應用程式屬性中找到應用程式的埠。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-136">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="eb3cc-137">如果先前未使用應用程式的已知埠設定埠，請返回 Azure 入口網站中的應用程式註冊，然後以正確的埠更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-137">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="eb3cc-138">建立應用程式之後，您應該能夠：</span><span class="sxs-lookup"><span data-stu-id="eb3cc-138">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="eb3cc-139">使用 AAD 使用者帳戶登入應用程式。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-139">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="eb3cc-140">要求 Microsoft Api 的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-140">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="eb3cc-141">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="eb3cc-141">For more information, see:</span></span>
  * [<span data-ttu-id="eb3cc-142">存取權杖範圍</span><span class="sxs-lookup"><span data-stu-id="eb3cc-142">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="eb3cc-143">[快速入門：設定應用程式以公開 Web api](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-143">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="eb3cc-144">驗證套件</span><span class="sxs-lookup"><span data-stu-id="eb3cc-144">Authentication package</span></span>

<span data-ttu-id="eb3cc-145">建立應用程式以使用 () 的公司或學校帳戶時 `SingleOrg` ，應用程式會自動收到 [Microsoft 驗證程式庫](/azure/active-directory/develop/msal-overview) 的套件參考 ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) 。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-145">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="eb3cc-146">此套件提供一組基本類型，可協助應用程式驗證使用者，並取得權杖以呼叫受保護的 Api。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-146">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="eb3cc-147">如果將驗證新增至應用程式，請手動將套件新增到應用程式的專案檔：</span><span class="sxs-lookup"><span data-stu-id="eb3cc-147">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="eb3cc-148">針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （ [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)）中找到符合應用程式共用架構版本的最新穩定版本套件。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-148">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="eb3cc-149">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)封裝可傳遞將 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 套件新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-149">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="eb3cc-150">驗證服務支援</span><span class="sxs-lookup"><span data-stu-id="eb3cc-150">Authentication service support</span></span>

<span data-ttu-id="eb3cc-151">驗證使用者的支援是在服務容器中註冊，並具有 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 由封裝提供的擴充方法 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-151">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="eb3cc-152">此方法會設定應用程式 Identity (IP) 與提供者互動所需的服務。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-152">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="eb3cc-153">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="eb3cc-153">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="eb3cc-154"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>方法會接受回呼來設定驗證應用程式所需的參數。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-154">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="eb3cc-155">當您註冊應用程式時，可以從 AAD 設定取得設定應用程式所需的值。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-155">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="eb3cc-156">設定是由檔案提供 `wwwroot/appsettings.json` ：</span><span class="sxs-lookup"><span data-stu-id="eb3cc-156">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="eb3cc-157">範例：</span><span class="sxs-lookup"><span data-stu-id="eb3cc-157">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="eb3cc-158">存取權杖範圍</span><span class="sxs-lookup"><span data-stu-id="eb3cc-158">Access token scopes</span></span>

<span data-ttu-id="eb3cc-159">Blazor WebAssembly範本不會自動將應用程式設定為要求安全 API 的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="eb3cc-159">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="eb3cc-160">若要布建存取權杖作為登入流程的一部分，請將範圍新增至的預設存取權杖範圍 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> ：</span><span class="sxs-lookup"><span data-stu-id="eb3cc-160">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="eb3cc-161">如需詳細資訊，請參閱下列 *其他案例* 文章的各節：</span><span class="sxs-lookup"><span data-stu-id="eb3cc-161">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="eb3cc-162">要求其他存取權杖</span><span class="sxs-lookup"><span data-stu-id="eb3cc-162">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="eb3cc-163">將權杖附加至傳出要求</span><span class="sxs-lookup"><span data-stu-id="eb3cc-163">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="eb3cc-164">登入模式</span><span class="sxs-lookup"><span data-stu-id="eb3cc-164">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="eb3cc-165">匯入檔案</span><span class="sxs-lookup"><span data-stu-id="eb3cc-165">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="eb3cc-166">索引頁面</span><span class="sxs-lookup"><span data-stu-id="eb3cc-166">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="eb3cc-167">應用程式元件</span><span class="sxs-lookup"><span data-stu-id="eb3cc-167">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="eb3cc-168">RedirectToLogin 元件</span><span class="sxs-lookup"><span data-stu-id="eb3cc-168">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="eb3cc-169">LoginDisplay 元件</span><span class="sxs-lookup"><span data-stu-id="eb3cc-169">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="eb3cc-170">驗證元件</span><span class="sxs-lookup"><span data-stu-id="eb3cc-170">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="eb3cc-171">其他資源</span><span class="sxs-lookup"><span data-stu-id="eb3cc-171">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="eb3cc-172">具有安全預設用戶端之應用程式中未經驗證或未經授權的 web API 要求</span><span class="sxs-lookup"><span data-stu-id="eb3cc-172">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="eb3cc-173">Microsoft 身分識別平台文件</span><span class="sxs-lookup"><span data-stu-id="eb3cc-173">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
