---
title: 使用 Azure Active Directory 保護 ASP.NET Core 的 Blazor WebAssembly 獨立應用程式
author: guardrex
description: 瞭解如何使用 Azure Active Directory 保護 ASP.NET Core 的 Blazor WebAssembly 獨立應用程式。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 557c700ffd24d4d6c85848ad360690d583e05321
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854468"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="5d223-103">使用 Azure Active Directory 保護 ASP.NET Core 的 Blazor WebAssembly 獨立應用程式</span><span class="sxs-lookup"><span data-stu-id="5d223-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="5d223-104">由 [Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5d223-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5d223-105">本文涵蓋如何建立使用[Azure Active Directory (AAD) ](https://azure.microsoft.com/services/active-directory/)進行驗證的[獨立 Blazor WebAssembly 應用程式](xref:blazor/hosting-models#blazor-webassembly)。</span><span class="sxs-lookup"><span data-stu-id="5d223-105">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="5d223-106">針對 Blazor WebAssembly 在 Visual Studio 中建立的應用程式，這些應用程式設定為支援 AAD 組織目錄中的帳戶，Visual Studio 目前未在產生專案時正確設定 Azure 入口網站應用程式註冊。</span><span class="sxs-lookup"><span data-stu-id="5d223-106">For Blazor WebAssembly apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't currently configure Azure portal app registrations correctly on project generation.</span></span> <span data-ttu-id="5d223-107">未來的 Visual Studio 版本將會解決此問題。</span><span class="sxs-lookup"><span data-stu-id="5d223-107">This will be addressed in a future release of Visual Studio.</span></span>
>
> <span data-ttu-id="5d223-108">本文說明如何使用 .NET CLI 命令建立解決方案和 Azure 應用程式入口網站註冊 `dotnet new` ，並在 Azure 入口網站中手動建立應用程式註冊。</span><span class="sxs-lookup"><span data-stu-id="5d223-108">This article shows how to create the solution and Azure app portal registration with the .NET CLI `dotnet new` command and by manually creating the app registration in the Azure portal.</span></span>
>
> <span data-ttu-id="5d223-109">如果您想要在更新 IDE 之前，使用 Visual Studio 建立解決方案和 Azure 應用程式註冊，請參閱本文 **_的每一節_** ，並確認或更新應用程式的設定，以及在 Visual Studio 建立解決方案之後，應用程式的註冊。</span><span class="sxs-lookup"><span data-stu-id="5d223-109">If you prefer to create the solution and Azure app registration with Visual Studio before the IDE is updated, refer to **_each section of this article_** and confirm or update the app's configuration and the app's registration after Visual Studio creates the solution.</span></span>

<span data-ttu-id="5d223-110">在 Azure 入口網站的 **Azure Active Directory** > **應用程式註冊** 區域中註冊 AAD 應用程式：</span><span class="sxs-lookup"><span data-stu-id="5d223-110">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="5d223-111">提供應用程式的 **名稱** (例如 **Blazor 獨立 AAD**) 。</span><span class="sxs-lookup"><span data-stu-id="5d223-111">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="5d223-112">選擇 **支援的帳戶類型**。</span><span class="sxs-lookup"><span data-stu-id="5d223-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="5d223-113">您可以 **只在此體驗中選取此組織目錄中的帳戶** 。</span><span class="sxs-lookup"><span data-stu-id="5d223-113">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="5d223-114">將 [重新 **導向 uri** ] 下拉式清單設定為 **單一頁面應用程式 (SPA)** 並提供下列重新導向 uri： `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="5d223-114">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="5d223-115">在 Kestrel 上執行應用程式的預設連接埠是 5001。</span><span class="sxs-lookup"><span data-stu-id="5d223-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="5d223-116">如果應用程式是在不同的 Kestrel 埠上執行，請使用應用程式的埠。</span><span class="sxs-lookup"><span data-stu-id="5d223-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="5d223-117">針對 IIS Express，可在 [ **調試** 程式] 面板的應用程式屬性中找到應用程式隨機產生的埠。</span><span class="sxs-lookup"><span data-stu-id="5d223-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="5d223-118">由於應用程式目前不存在，且 IIS Express 埠未知，因此在建立應用程式之後，請返回此步驟，並更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="5d223-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="5d223-119">本主題稍後會出現一個批註，提醒 IIS Express 使用者更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="5d223-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="5d223-120">清除 [授 **與系統** > **管理員同意 openid 和 offline_access 許可權** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="5d223-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="5d223-121">選取 [註冊]。</span><span class="sxs-lookup"><span data-stu-id="5d223-121">Select **Register**.</span></span>

<span data-ttu-id="5d223-122">記錄下列資訊：</span><span class="sxs-lookup"><span data-stu-id="5d223-122">Record the following information:</span></span>

* <span data-ttu-id="5d223-123">應用程式 (用戶端) 識別碼 (例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`) </span><span class="sxs-lookup"><span data-stu-id="5d223-123">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="5d223-124">目錄 (租使用者) 識別碼 (例如 `e86c78e2-8bb4-4c41-aefd-918e0565a45e`) </span><span class="sxs-lookup"><span data-stu-id="5d223-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="5d223-125">在「**驗證** 平臺設定」的 >  > **單一頁面應用程式中， (SPA)**：</span><span class="sxs-lookup"><span data-stu-id="5d223-125">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="5d223-126">確認的重新 **導向 URI** `https://localhost:{PORT}/authentication/login-callback` 存在。</span><span class="sxs-lookup"><span data-stu-id="5d223-126">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="5d223-127">針對 **隱含授** 與，請確定 **未** 選取 **存取權杖** 和 **識別碼權杖** 的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="5d223-127">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="5d223-128">此體驗可接受應用程式的其餘預設值。</span><span class="sxs-lookup"><span data-stu-id="5d223-128">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="5d223-129">選取 [儲存] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="5d223-129">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="5d223-130">在 Azure 入口網站的 **Azure Active Directory** > **應用程式註冊** 區域中註冊 AAD 應用程式：</span><span class="sxs-lookup"><span data-stu-id="5d223-130">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="5d223-131">提供應用程式的 **名稱** (例如 **Blazor 獨立 AAD**) 。</span><span class="sxs-lookup"><span data-stu-id="5d223-131">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="5d223-132">選擇 **支援的帳戶類型**。</span><span class="sxs-lookup"><span data-stu-id="5d223-132">Choose a **Supported account types**.</span></span> <span data-ttu-id="5d223-133">您可以 **只在此體驗中選取此組織目錄中的帳戶** 。</span><span class="sxs-lookup"><span data-stu-id="5d223-133">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="5d223-134">將 [重新 **導向 URI** ] 下拉式清單保持設定為 [ **Web** ]，並提供下列重新導向 uri： `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="5d223-134">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="5d223-135">在 Kestrel 上執行應用程式的預設連接埠是 5001。</span><span class="sxs-lookup"><span data-stu-id="5d223-135">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="5d223-136">如果應用程式是在不同的 Kestrel 埠上執行，請使用應用程式的埠。</span><span class="sxs-lookup"><span data-stu-id="5d223-136">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="5d223-137">針對 IIS Express，可在 [ **調試** 程式] 面板的應用程式屬性中找到應用程式隨機產生的埠。</span><span class="sxs-lookup"><span data-stu-id="5d223-137">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="5d223-138">由於應用程式目前不存在，且 IIS Express 埠未知，因此在建立應用程式之後，請返回此步驟，並更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="5d223-138">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="5d223-139">本主題稍後會出現一個批註，提醒 IIS Express 使用者更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="5d223-139">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="5d223-140">清除 [授 **與系統** > **管理員同意 openid 和 offline_access 許可權** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="5d223-140">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="5d223-141">選取 [註冊]。</span><span class="sxs-lookup"><span data-stu-id="5d223-141">Select **Register**.</span></span>

<span data-ttu-id="5d223-142">記錄下列資訊：</span><span class="sxs-lookup"><span data-stu-id="5d223-142">Record the following information:</span></span>

* <span data-ttu-id="5d223-143">應用程式 (用戶端) 識別碼 (例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`) </span><span class="sxs-lookup"><span data-stu-id="5d223-143">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="5d223-144">目錄 (租使用者) 識別碼 (例如 `e86c78e2-8bb4-4c41-aefd-918e0565a45e`) </span><span class="sxs-lookup"><span data-stu-id="5d223-144">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="5d223-145">在 [ **驗證** > **平臺** 設定] > **Web**：</span><span class="sxs-lookup"><span data-stu-id="5d223-145">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="5d223-146">確認的重新 **導向 URI** `https://localhost:{PORT}/authentication/login-callback` 存在。</span><span class="sxs-lookup"><span data-stu-id="5d223-146">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="5d223-147">針對 **[隱含授** 與]，選取 **存取權杖** 和 **識別碼權杖** 的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="5d223-147">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="5d223-148">此體驗可接受應用程式的其餘預設值。</span><span class="sxs-lookup"><span data-stu-id="5d223-148">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="5d223-149">選取 [儲存] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="5d223-149">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="5d223-150">在空的資料夾中建立應用程式。</span><span class="sxs-lookup"><span data-stu-id="5d223-150">Create the app in an empty folder.</span></span> <span data-ttu-id="5d223-151">將下列命令中的預留位置取代為稍早記錄的資訊，然後在命令 shell 中執行命令：</span><span class="sxs-lookup"><span data-stu-id="5d223-151">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="5d223-152">預留位置</span><span class="sxs-lookup"><span data-stu-id="5d223-152">Placeholder</span></span>   | <span data-ttu-id="5d223-153">Azure 入口網站名稱</span><span class="sxs-lookup"><span data-stu-id="5d223-153">Azure portal name</span></span>       | <span data-ttu-id="5d223-154">範例</span><span class="sxs-lookup"><span data-stu-id="5d223-154">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="5d223-155">應用程式 (用戶端) 識別碼</span><span class="sxs-lookup"><span data-stu-id="5d223-155">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="5d223-156">目錄 (租用戶) 識別碼</span><span class="sxs-lookup"><span data-stu-id="5d223-156">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="5d223-157">使用 `-o|--output` 選項指定的輸出位置會建立專案資料夾 (如果不存在)，並成為應用程式名稱的一部分。</span><span class="sxs-lookup"><span data-stu-id="5d223-157">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="5d223-158">在 Azure 入口網站中，會針對使用預設設定在 Kestrel 伺服器上執行的應用程式，將應用程式的平臺設定重新 **導向 URI** 設定為使用埠5001。</span><span class="sxs-lookup"><span data-stu-id="5d223-158">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="5d223-159">如果應用程式是在隨機的 IIS Express 埠上執行，則可以在 [ **調試** 程式] 面板的應用程式屬性中找到應用程式的埠。</span><span class="sxs-lookup"><span data-stu-id="5d223-159">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="5d223-160">如果先前未使用應用程式的已知埠設定埠，請返回 Azure 入口網站中的應用程式註冊，然後以正確的埠更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="5d223-160">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-AAD.md)]

::: moniker-end

<span data-ttu-id="5d223-161">建立應用程式之後，您應該能夠：</span><span class="sxs-lookup"><span data-stu-id="5d223-161">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="5d223-162">使用 AAD 使用者帳戶登入應用程式。</span><span class="sxs-lookup"><span data-stu-id="5d223-162">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="5d223-163">要求 Microsoft Api 的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="5d223-163">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="5d223-164">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="5d223-164">For more information, see:</span></span>
  * [<span data-ttu-id="5d223-165">存取權杖範圍</span><span class="sxs-lookup"><span data-stu-id="5d223-165">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="5d223-166">[快速入門：設定應用程式以公開 Web api](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。</span><span class="sxs-lookup"><span data-stu-id="5d223-166">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="5d223-167">驗證套件</span><span class="sxs-lookup"><span data-stu-id="5d223-167">Authentication package</span></span>

<span data-ttu-id="5d223-168">建立應用程式以使用 () 的公司或學校帳戶時 `SingleOrg` ，應用程式會自動收到 [Microsoft 驗證程式庫](/azure/active-directory/develop/msal-overview) 的套件參考 ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) 。</span><span class="sxs-lookup"><span data-stu-id="5d223-168">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="5d223-169">此套件提供一組基本類型，可協助應用程式驗證使用者，並取得權杖以呼叫受保護的 Api。</span><span class="sxs-lookup"><span data-stu-id="5d223-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="5d223-170">如果將驗證新增至應用程式，請手動將套件新增到應用程式的專案檔：</span><span class="sxs-lookup"><span data-stu-id="5d223-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="5d223-171">針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （ [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)）中找到符合應用程式共用架構版本的最新穩定版本套件。</span><span class="sxs-lookup"><span data-stu-id="5d223-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="5d223-172">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)封裝可傳遞將 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 套件新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="5d223-172">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="5d223-173">驗證服務支援</span><span class="sxs-lookup"><span data-stu-id="5d223-173">Authentication service support</span></span>

<span data-ttu-id="5d223-174">驗證使用者的支援是在服務容器中註冊，並具有 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 由封裝提供的擴充方法 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 。</span><span class="sxs-lookup"><span data-stu-id="5d223-174">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="5d223-175">此方法會設定應用程式 Identity (IP) 與提供者互動所需的服務。</span><span class="sxs-lookup"><span data-stu-id="5d223-175">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="5d223-176">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="5d223-176">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="5d223-177"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>方法會接受回呼來設定驗證應用程式所需的參數。</span><span class="sxs-lookup"><span data-stu-id="5d223-177">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="5d223-178">當您註冊應用程式時，可以從 AAD 設定取得設定應用程式所需的值。</span><span class="sxs-lookup"><span data-stu-id="5d223-178">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="5d223-179">設定是由檔案提供 `wwwroot/appsettings.json` ：</span><span class="sxs-lookup"><span data-stu-id="5d223-179">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="5d223-180">範例：</span><span class="sxs-lookup"><span data-stu-id="5d223-180">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="5d223-181">存取權杖範圍</span><span class="sxs-lookup"><span data-stu-id="5d223-181">Access token scopes</span></span>

<span data-ttu-id="5d223-182">Blazor WebAssembly範本不會自動將應用程式設定為要求安全 API 的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="5d223-182">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="5d223-183">若要布建存取權杖作為登入流程的一部分，請將範圍新增至的預設存取權杖範圍 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> ：</span><span class="sxs-lookup"><span data-stu-id="5d223-183">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="5d223-184">使用下列內容指定其他範圍 `AdditionalScopesToConsent` ：</span><span class="sxs-lookup"><span data-stu-id="5d223-184">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="5d223-185">如需詳細資訊，請參閱下列 *其他案例* 文章的各節：</span><span class="sxs-lookup"><span data-stu-id="5d223-185">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="5d223-186">要求其他存取權杖</span><span class="sxs-lookup"><span data-stu-id="5d223-186">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="5d223-187">將權杖附加至傳出要求</span><span class="sxs-lookup"><span data-stu-id="5d223-187">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="5d223-188">登入模式</span><span class="sxs-lookup"><span data-stu-id="5d223-188">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="5d223-189">匯入檔案</span><span class="sxs-lookup"><span data-stu-id="5d223-189">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="5d223-190">索引頁面</span><span class="sxs-lookup"><span data-stu-id="5d223-190">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="5d223-191">應用程式元件</span><span class="sxs-lookup"><span data-stu-id="5d223-191">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="5d223-192">RedirectToLogin 元件</span><span class="sxs-lookup"><span data-stu-id="5d223-192">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="5d223-193">LoginDisplay 元件</span><span class="sxs-lookup"><span data-stu-id="5d223-193">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="5d223-194">驗證元件</span><span class="sxs-lookup"><span data-stu-id="5d223-194">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="5d223-195">其他資源</span><span class="sxs-lookup"><span data-stu-id="5d223-195">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="5d223-196">具有安全預設用戶端之應用程式中未經驗證或未經授權的 web API 要求</span><span class="sxs-lookup"><span data-stu-id="5d223-196">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="5d223-197">Microsoft 身分識別平台文件</span><span class="sxs-lookup"><span data-stu-id="5d223-197">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
