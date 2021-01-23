---
title: Blazor WebAssembly使用 Microsoft 帳戶保護 ASP.NET Core 的獨立應用程式
author: guardrex
description: 瞭解如何 Blazor WebAssembly 使用 Microsoft 帳戶保護 ASP.NET Core 的獨立應用程式。
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
uid: blazor/security/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 268debd51b6828aad0bcfe917bdf95b691ac7365
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710512"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="6baef-103">Blazor WebAssembly使用 Microsoft 帳戶保護 ASP.NET Core 的獨立應用程式</span><span class="sxs-lookup"><span data-stu-id="6baef-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="6baef-104">由 [Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6baef-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6baef-105">本文說明如何建立使用[Microsoft 帳戶搭配 Azure Active Directory (AAD) ](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)進行驗證的[獨立 Blazor WebAssembly 應用程式](xref:blazor/hosting-models#blazor-webassembly)。</span><span class="sxs-lookup"><span data-stu-id="6baef-105">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication.</span></span>

<span data-ttu-id="6baef-106">在 Azure 入口網站的 **Azure Active Directory**  >  **應用程式註冊** 區域中註冊 AAD 應用程式：</span><span class="sxs-lookup"><span data-stu-id="6baef-106">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="6baef-107">提供應用程式的 **名稱** (例如， **Blazor 獨立 AAD Microsoft 帳戶**) 。</span><span class="sxs-lookup"><span data-stu-id="6baef-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="6baef-108">在 [ **支援的帳戶類型**] 中，選取 **任何組織目錄中的帳戶**。</span><span class="sxs-lookup"><span data-stu-id="6baef-108">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="6baef-109">將 [重新 **導向 uri** ] 下拉式清單設定為 **單一頁面應用程式 (SPA)** 並提供下列重新導向 uri： `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="6baef-109">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="6baef-110">在 Kestrel 上執行應用程式的預設連接埠是 5001。</span><span class="sxs-lookup"><span data-stu-id="6baef-110">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="6baef-111">如果應用程式是在不同的 Kestrel 埠上執行，請使用應用程式的埠。</span><span class="sxs-lookup"><span data-stu-id="6baef-111">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="6baef-112">針對 IIS Express，可在 [ **調試** 程式] 面板的應用程式屬性中找到應用程式隨機產生的埠。</span><span class="sxs-lookup"><span data-stu-id="6baef-112">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="6baef-113">由於應用程式目前不存在，且 IIS Express 埠未知，因此在建立應用程式之後，請返回此步驟，並更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="6baef-113">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="6baef-114">本主題稍後會出現一個批註，提醒 IIS Express 使用者更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="6baef-114">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="6baef-115">清除 [授 **與系統** > **管理員同意 openid 和 offline_access 許可權** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="6baef-115">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="6baef-116">選取 [註冊]。</span><span class="sxs-lookup"><span data-stu-id="6baef-116">Select **Register**.</span></span>

<span data-ttu-id="6baef-117">記錄應用程式 (用戶端) 識別碼 (例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`) 。</span><span class="sxs-lookup"><span data-stu-id="6baef-117">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="6baef-118">在「**驗證** 平臺設定」的 >  > **單一頁面應用程式中， (SPA)**：</span><span class="sxs-lookup"><span data-stu-id="6baef-118">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="6baef-119">確認的重新 **導向 URI** `https://localhost:{PORT}/authentication/login-callback` 存在。</span><span class="sxs-lookup"><span data-stu-id="6baef-119">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="6baef-120">針對 **隱含授** 與，請確定 **未** 選取 **存取權杖** 和 **識別碼權杖** 的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="6baef-120">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="6baef-121">此體驗可接受應用程式的其餘預設值。</span><span class="sxs-lookup"><span data-stu-id="6baef-121">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="6baef-122">選取 [儲存] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="6baef-122">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="6baef-123">提供應用程式的 **名稱** (例如， **Blazor 獨立 AAD Microsoft 帳戶**) 。</span><span class="sxs-lookup"><span data-stu-id="6baef-123">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="6baef-124">在 [ **支援的帳戶類型**] 中，選取 **任何組織目錄中的帳戶**。</span><span class="sxs-lookup"><span data-stu-id="6baef-124">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="6baef-125">將 [重新 **導向 URI** ] 下拉式清單保持設定為 [ **Web** ]，並提供下列重新導向 uri： `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="6baef-125">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="6baef-126">在 Kestrel 上執行應用程式的預設連接埠是 5001。</span><span class="sxs-lookup"><span data-stu-id="6baef-126">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="6baef-127">如果應用程式是在不同的 Kestrel 埠上執行，請使用應用程式的埠。</span><span class="sxs-lookup"><span data-stu-id="6baef-127">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="6baef-128">針對 IIS Express，可在 [ **調試** 程式] 面板的應用程式屬性中找到應用程式隨機產生的埠。</span><span class="sxs-lookup"><span data-stu-id="6baef-128">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="6baef-129">由於應用程式目前不存在，且 IIS Express 埠未知，因此在建立應用程式之後，請返回此步驟，並更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="6baef-129">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="6baef-130">本主題稍後會出現一個批註，提醒 IIS Express 使用者更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="6baef-130">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="6baef-131">清除 [授 **與系統** > **管理員同意 openid 和 offline_access 許可權** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="6baef-131">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="6baef-132">選取 [註冊]。</span><span class="sxs-lookup"><span data-stu-id="6baef-132">Select **Register**.</span></span>

<span data-ttu-id="6baef-133">記錄應用程式 (用戶端) 識別碼 (例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`) 。</span><span class="sxs-lookup"><span data-stu-id="6baef-133">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="6baef-134">在 [ **驗證** > **平臺** 設定] > **Web**：</span><span class="sxs-lookup"><span data-stu-id="6baef-134">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="6baef-135">確認的重新 **導向 URI** `https://localhost:{PORT}/authentication/login-callback` 存在。</span><span class="sxs-lookup"><span data-stu-id="6baef-135">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="6baef-136">針對 **[隱含授** 與]，選取 **存取權杖** 和 **識別碼權杖** 的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="6baef-136">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="6baef-137">此體驗可接受應用程式的其餘預設值。</span><span class="sxs-lookup"><span data-stu-id="6baef-137">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="6baef-138">選取 [儲存] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="6baef-138">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="6baef-139">建立應用程式。</span><span class="sxs-lookup"><span data-stu-id="6baef-139">Create the app.</span></span> <span data-ttu-id="6baef-140">使用稍早所記錄的資訊來取代下列命令中的預留位置，並在命令命令介面中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="6baef-140">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common" -o {APP NAME}
```

| <span data-ttu-id="6baef-141">預留位置</span><span class="sxs-lookup"><span data-stu-id="6baef-141">Placeholder</span></span>   | <span data-ttu-id="6baef-142">Azure 入口網站名稱</span><span class="sxs-lookup"><span data-stu-id="6baef-142">Azure portal name</span></span>       | <span data-ttu-id="6baef-143">範例</span><span class="sxs-lookup"><span data-stu-id="6baef-143">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="6baef-144">應用程式 (用戶端) 識別碼</span><span class="sxs-lookup"><span data-stu-id="6baef-144">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |

<span data-ttu-id="6baef-145">使用 `-o|--output` 選項指定的輸出位置會建立專案資料夾 (如果不存在)，並成為應用程式名稱的一部分。</span><span class="sxs-lookup"><span data-stu-id="6baef-145">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="6baef-146">在 Azure 入口網站中，會針對使用預設設定在 Kestrel 伺服器上執行的應用程式，將應用程式的平臺設定重新 **導向 URI** 設定為使用埠5001。</span><span class="sxs-lookup"><span data-stu-id="6baef-146">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="6baef-147">如果應用程式是在隨機的 IIS Express 埠上執行，則可以在 [ **調試** 程式] 面板的應用程式屬性中找到應用程式的埠。</span><span class="sxs-lookup"><span data-stu-id="6baef-147">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="6baef-148">如果先前未使用應用程式的已知埠設定埠，請返回 Azure 入口網站中的應用程式註冊，然後以正確的埠更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="6baef-148">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

<span data-ttu-id="6baef-149">建立應用程式之後，您應該能夠：</span><span class="sxs-lookup"><span data-stu-id="6baef-149">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="6baef-150">使用 Microsoft 帳戶登入應用程式。</span><span class="sxs-lookup"><span data-stu-id="6baef-150">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="6baef-151">要求 Microsoft Api 的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="6baef-151">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="6baef-152">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="6baef-152">For more information, see:</span></span>
  * [<span data-ttu-id="6baef-153">存取權杖範圍</span><span class="sxs-lookup"><span data-stu-id="6baef-153">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="6baef-154">[快速入門：設定應用程式以公開 Web api](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。</span><span class="sxs-lookup"><span data-stu-id="6baef-154">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="6baef-155">驗證套件</span><span class="sxs-lookup"><span data-stu-id="6baef-155">Authentication package</span></span>

<span data-ttu-id="6baef-156">建立應用程式以使用 () 的公司或學校帳戶時 `SingleOrg` ，應用程式會自動收到 [Microsoft 驗證程式庫](/azure/active-directory/develop/msal-overview) 的套件參考 ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) 。</span><span class="sxs-lookup"><span data-stu-id="6baef-156">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="6baef-157">此套件提供一組基本類型，可協助應用程式驗證使用者，並取得權杖以呼叫受保護的 Api。</span><span class="sxs-lookup"><span data-stu-id="6baef-157">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="6baef-158">如果將驗證新增至應用程式，請手動將套件新增到應用程式的專案檔：</span><span class="sxs-lookup"><span data-stu-id="6baef-158">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="6baef-159">針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （ [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)）中找到符合應用程式共用架構版本的最新穩定版本套件。</span><span class="sxs-lookup"><span data-stu-id="6baef-159">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="6baef-160">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)封裝可傳遞將 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 套件新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="6baef-160">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="6baef-161">驗證服務支援</span><span class="sxs-lookup"><span data-stu-id="6baef-161">Authentication service support</span></span>

<span data-ttu-id="6baef-162">驗證使用者的支援是在服務容器中註冊，並具有 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 由封裝提供的擴充方法 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 。</span><span class="sxs-lookup"><span data-stu-id="6baef-162">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="6baef-163">這個方法會設定應用程式與 Identity 提供者 (IP) 互動所需的所有服務。</span><span class="sxs-lookup"><span data-stu-id="6baef-163">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="6baef-164">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="6baef-164">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="6baef-165"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>方法會接受回呼來設定驗證應用程式所需的參數。</span><span class="sxs-lookup"><span data-stu-id="6baef-165">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="6baef-166">當您註冊應用程式時，可以從 AAD 設定取得設定應用程式所需的值。</span><span class="sxs-lookup"><span data-stu-id="6baef-166">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="6baef-167">設定是由檔案提供 `wwwroot/appsettings.json` ：</span><span class="sxs-lookup"><span data-stu-id="6baef-167">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="6baef-168">範例：</span><span class="sxs-lookup"><span data-stu-id="6baef-168">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="6baef-169">存取權杖範圍</span><span class="sxs-lookup"><span data-stu-id="6baef-169">Access token scopes</span></span>

<span data-ttu-id="6baef-170">Blazor WebAssembly範本不會自動將應用程式設定為要求安全 API 的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="6baef-170">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="6baef-171">若要布建存取權杖作為登入流程的一部分，請將範圍新增至的預設存取權杖範圍 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> ：</span><span class="sxs-lookup"><span data-stu-id="6baef-171">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="6baef-172">使用下列內容指定其他範圍 `AdditionalScopesToConsent` ：</span><span class="sxs-lookup"><span data-stu-id="6baef-172">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="6baef-173">如需詳細資訊，請參閱下列 *其他案例* 文章的各節：</span><span class="sxs-lookup"><span data-stu-id="6baef-173">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="6baef-174">要求其他存取權杖</span><span class="sxs-lookup"><span data-stu-id="6baef-174">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="6baef-175">將權杖附加至傳出要求</span><span class="sxs-lookup"><span data-stu-id="6baef-175">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="6baef-176">登入模式</span><span class="sxs-lookup"><span data-stu-id="6baef-176">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="6baef-177">匯入檔案</span><span class="sxs-lookup"><span data-stu-id="6baef-177">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="6baef-178">索引頁面</span><span class="sxs-lookup"><span data-stu-id="6baef-178">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="6baef-179">應用程式元件</span><span class="sxs-lookup"><span data-stu-id="6baef-179">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="6baef-180">RedirectToLogin 元件</span><span class="sxs-lookup"><span data-stu-id="6baef-180">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="6baef-181">LoginDisplay 元件</span><span class="sxs-lookup"><span data-stu-id="6baef-181">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="6baef-182">驗證元件</span><span class="sxs-lookup"><span data-stu-id="6baef-182">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="6baef-183">其他資源</span><span class="sxs-lookup"><span data-stu-id="6baef-183">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="6baef-184">建立驗證的自訂版本 MSAL JavaScript 程式庫</span><span class="sxs-lookup"><span data-stu-id="6baef-184">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="6baef-185">具有安全預設用戶端之應用程式中未經驗證或未經授權的 web API 要求</span><span class="sxs-lookup"><span data-stu-id="6baef-185">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* [<span data-ttu-id="6baef-186">快速入門：使用 Microsoft 身分識別平台來註冊應用程式</span><span class="sxs-lookup"><span data-stu-id="6baef-186">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="6baef-187">快速入門：設定應用程式以公開 Web API</span><span class="sxs-lookup"><span data-stu-id="6baef-187">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
