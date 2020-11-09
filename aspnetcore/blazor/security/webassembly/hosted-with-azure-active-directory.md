---
title: Blazor WebAssembly使用 Azure Active Directory 保護 ASP.NET Core 託管應用程式
author: guardrex
description: '瞭解如何使用 Azure Active Directory 保護 ASP.NET Core Blazor WebAssembly 託管應用程式。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 11/02/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 17f96be762ece8c59577445eb2ae630a8ee3b3dd
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234474"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="6160b-103">Blazor WebAssembly使用 Azure Active Directory 保護 ASP.NET Core 託管應用程式</span><span class="sxs-lookup"><span data-stu-id="6160b-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="6160b-104">由 [Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6160b-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6160b-105">本文說明如何建立使用[Azure Active Directory (AAD) ](https://azure.microsoft.com/services/active-directory/)進行驗證的[託管 Blazor WebAssembly 應用程式](xref:blazor/hosting-models#blazor-webassembly)。</span><span class="sxs-lookup"><span data-stu-id="6160b-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="6160b-106">針對 Blazor WebAssembly 在 Visual Studio 中建立的應用程式，這些應用程式設定為支援 AAD 組織目錄中的帳戶，Visual Studio 不會在產生專案時正確設定應用程式。</span><span class="sxs-lookup"><span data-stu-id="6160b-106">For Blazor WebAssembly apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't configure the app correctly on project generation.</span></span> <span data-ttu-id="6160b-107">未來的 Visual Studio 版本將會解決此問題。</span><span class="sxs-lookup"><span data-stu-id="6160b-107">This will be addressed in a future release of Visual Studio.</span></span> <span data-ttu-id="6160b-108">本文說明如何使用 .NET Core CLI 的命令來建立應用程式 `dotnet new` 。</span><span class="sxs-lookup"><span data-stu-id="6160b-108">This article shows how to create the app with the .NET Core CLI's `dotnet new` command.</span></span> <span data-ttu-id="6160b-109">如果您想要在 IDE 更新 ASP.NET Core 5.0 中的最新範本之前使用 Visual Studio 建立應用程式 Blazor ，請參閱本文的每一節，然後在 Visual Studio 建立應用程式之後，確認或更新應用程式的設定。</span><span class="sxs-lookup"><span data-stu-id="6160b-109">If you prefer to create the app with Visual Studio before the IDE is updated for the latest Blazor templates in ASP.NET Core 5.0, refer to each section of this article and confirm or update the app's configuration after Visual Studio creates the app.</span></span>

::: moniker-end

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="6160b-110">在 AAD 中註冊應用程式並建立解決方案</span><span class="sxs-lookup"><span data-stu-id="6160b-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="6160b-111">建立租用戶</span><span class="sxs-lookup"><span data-stu-id="6160b-111">Create a tenant</span></span>

<span data-ttu-id="6160b-112">遵循快速入門中的指導方針 [：設定租](/azure/active-directory/develop/quickstart-create-new-tenant) 使用者以在 AAD 中建立租使用者。</span><span class="sxs-lookup"><span data-stu-id="6160b-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="6160b-113">註冊伺服器 API 應用程式</span><span class="sxs-lookup"><span data-stu-id="6160b-113">Register a server API app</span></span>

<span data-ttu-id="6160b-114">遵循快速入門中的指導方針 [：使用 Microsoft 身分識別平臺註冊應用程式](/azure/active-directory/develop/quickstart-register-app) ，以及後續的 Azure AAD 主題以註冊 *伺服器 API 應用* 程式的 AAD 應用程式，然後執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="6160b-114">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="6160b-115">在 **Azure Active Directory**  >  **應用程式註冊** 中，選取 [ **新增註冊** ]。</span><span class="sxs-lookup"><span data-stu-id="6160b-115">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="6160b-116">提供應用程式的 **名稱** (例如， **Blazor Server AAD** ) 。</span><span class="sxs-lookup"><span data-stu-id="6160b-116">Provide a **Name** for the app (for example, **Blazor Server AAD** ).</span></span>
1. <span data-ttu-id="6160b-117">選擇 **支援的帳戶類型** 。</span><span class="sxs-lookup"><span data-stu-id="6160b-117">Choose a **Supported account types** .</span></span> <span data-ttu-id="6160b-118">您可以選取 **此組織目錄中的帳戶，只** (此體驗的單一租使用者) 。</span><span class="sxs-lookup"><span data-stu-id="6160b-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="6160b-119">在此案例中， *伺服器 API 應用程式* 不需要重新 **導向 uri** ，因此請將下拉式清單保持設定為 [ **Web** ]，而不要輸入重新導向 uri。</span><span class="sxs-lookup"><span data-stu-id="6160b-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="6160b-120">清除 [授 **與系統**  >  **管理員同意 openid 和 offline_access 許可權** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="6160b-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="6160b-121">選取 [註冊]。</span><span class="sxs-lookup"><span data-stu-id="6160b-121">Select **Register** .</span></span>

<span data-ttu-id="6160b-122">記錄下列資訊：</span><span class="sxs-lookup"><span data-stu-id="6160b-122">Record the following information:</span></span>

* <span data-ttu-id="6160b-123">*伺服器 API 應用程式* 應用程式 (用戶端) 識別碼 (例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`) </span><span class="sxs-lookup"><span data-stu-id="6160b-123">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="6160b-124">目錄 (租使用者) 識別碼 (例如 `e86c78e2-8bb4-4c41-aefd-918e0565a45e`) </span><span class="sxs-lookup"><span data-stu-id="6160b-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="6160b-125">AAD 主要/發行者/租使用者網域 (例如 `contoso.onmicrosoft.com`) ：網域可作為已註冊應用程式之 Azure 入口網站的 [ **商標** ] 分頁中的 **發行者網域** 。</span><span class="sxs-lookup"><span data-stu-id="6160b-125">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="6160b-126">在 [ **API 許可權** ] 中，移除 **Microsoft Graph** 的  >  **使用者。讀取** 許可權，因為應用程式不需要登入或使用者設定檔存取權。</span><span class="sxs-lookup"><span data-stu-id="6160b-126">In **API permissions** , remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="6160b-127">在中 **公開 API** ：</span><span class="sxs-lookup"><span data-stu-id="6160b-127">In **Expose an API** :</span></span>

1. <span data-ttu-id="6160b-128">選取 [新增範圍]  。</span><span class="sxs-lookup"><span data-stu-id="6160b-128">Select **Add a scope** .</span></span>
1. <span data-ttu-id="6160b-129">選取 [儲存並繼續]  。</span><span class="sxs-lookup"><span data-stu-id="6160b-129">Select **Save and continue** .</span></span>
1. <span data-ttu-id="6160b-130">提供 **範圍名稱** (例如 `API.Access`) 。</span><span class="sxs-lookup"><span data-stu-id="6160b-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="6160b-131">提供系統 **管理員同意顯示名稱** (例如 `Access API`) 。</span><span class="sxs-lookup"><span data-stu-id="6160b-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="6160b-132">提供 **管理員同意描述** (例如 `Allows the app to access server app API endpoints.`) 。</span><span class="sxs-lookup"><span data-stu-id="6160b-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="6160b-133">確認 **狀態** 設定為 [ **已啟用** ]。</span><span class="sxs-lookup"><span data-stu-id="6160b-133">Confirm that the **State** is set to **Enabled** .</span></span>
1. <span data-ttu-id="6160b-134">選取 [新增範圍]。</span><span class="sxs-lookup"><span data-stu-id="6160b-134">Select **Add scope** .</span></span>

<span data-ttu-id="6160b-135">記錄下列資訊：</span><span class="sxs-lookup"><span data-stu-id="6160b-135">Record the following information:</span></span>

* <span data-ttu-id="6160b-136">應用程式識別碼 URI (例如， `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` 、 `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` 或您提供的自訂值) </span><span class="sxs-lookup"><span data-stu-id="6160b-136">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provide)</span></span>
* <span data-ttu-id="6160b-137">範圍名稱 (例如， `API.Access`) </span><span class="sxs-lookup"><span data-stu-id="6160b-137">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="6160b-138">註冊用戶端應用程式</span><span class="sxs-lookup"><span data-stu-id="6160b-138">Register a client app</span></span>

<span data-ttu-id="6160b-139">遵循快速入門中的指導方針 [：使用 Microsoft 身分識別平臺註冊應用程式](/azure/active-directory/develop/quickstart-register-app) ，並使用後續的 Azure AAD 主題來為應用程式註冊 AAD 應用程式 *`Client`* ，然後執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="6160b-139">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="6160b-140">在 **Azure Active Directory** > **應用程式註冊** 中，選取 [ **新增註冊** ]。</span><span class="sxs-lookup"><span data-stu-id="6160b-140">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="6160b-141">提供應用程式的 **名稱** (例如， **Blazor 用戶端 AAD** ) 。</span><span class="sxs-lookup"><span data-stu-id="6160b-141">Provide a **Name** for the app (for example, **Blazor Client AAD** ).</span></span>
1. <span data-ttu-id="6160b-142">選擇 **支援的帳戶類型** 。</span><span class="sxs-lookup"><span data-stu-id="6160b-142">Choose a **Supported account types** .</span></span> <span data-ttu-id="6160b-143">您可以選取 **此組織目錄中的帳戶，只** (此體驗的單一租使用者) 。</span><span class="sxs-lookup"><span data-stu-id="6160b-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="6160b-144">將 [重新 **導向 uri** ] 下拉式清單設定為 **單一頁面應用程式 (SPA)** 並提供下列重新導向 uri： `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="6160b-144">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="6160b-145">在 Kestrel 上執行應用程式的預設連接埠是 5001。</span><span class="sxs-lookup"><span data-stu-id="6160b-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="6160b-146">如果應用程式是在不同的 Kestrel 埠上執行，請使用應用程式的埠。</span><span class="sxs-lookup"><span data-stu-id="6160b-146">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="6160b-147">針對 IIS Express，可在 [ *`Server`* **調試** 程式] 面板的應用程式屬性中找到應用程式隨機產生的埠。</span><span class="sxs-lookup"><span data-stu-id="6160b-147">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="6160b-148">由於應用程式目前不存在，且 IIS Express 埠未知，因此在建立應用程式之後，請返回此步驟，並更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="6160b-148">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="6160b-149">批註會出現在 [ [建立應用程式](#create-the-app) ] 區段中，以提醒 IIS Express 使用者更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="6160b-149">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="6160b-150">清除 [授 **與系統** > **管理員同意 openid 和 offline_access 許可權** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="6160b-150">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="6160b-151">選取 [註冊]。</span><span class="sxs-lookup"><span data-stu-id="6160b-151">Select **Register** .</span></span>

<span data-ttu-id="6160b-152">記錄 *`Client`* 應用程式應用程式 (用戶端) 識別碼 (例如 `4369008b-21fa-427c-abaa-9b53bf58e538`) 。</span><span class="sxs-lookup"><span data-stu-id="6160b-152">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="6160b-153">在「 **驗證** 平臺設定」的 > **Platform configurations** > **單一頁面應用程式中， (SPA)** ：</span><span class="sxs-lookup"><span data-stu-id="6160b-153">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="6160b-154">確認的重新 **導向 URI** `https://localhost:{PORT}/authentication/login-callback` 存在。</span><span class="sxs-lookup"><span data-stu-id="6160b-154">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="6160b-155">針對 **隱含授** 與，請確定 **未** 選取 **存取權杖** 和 **識別碼權杖** 的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="6160b-155">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="6160b-156">此體驗可接受應用程式的其餘預設值。</span><span class="sxs-lookup"><span data-stu-id="6160b-156">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="6160b-157">選取 [儲存] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="6160b-157">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="6160b-158">在 **Azure Active Directory** > **應用程式註冊** 中，選取 [ **新增註冊** ]。</span><span class="sxs-lookup"><span data-stu-id="6160b-158">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="6160b-159">提供應用程式的 **名稱** (例如， **Blazor 用戶端 AAD** ) 。</span><span class="sxs-lookup"><span data-stu-id="6160b-159">Provide a **Name** for the app (for example, **Blazor Client AAD** ).</span></span>
1. <span data-ttu-id="6160b-160">選擇 **支援的帳戶類型** 。</span><span class="sxs-lookup"><span data-stu-id="6160b-160">Choose a **Supported account types** .</span></span> <span data-ttu-id="6160b-161">您可以選取 **此組織目錄中的帳戶，只** (此體驗的單一租使用者) 。</span><span class="sxs-lookup"><span data-stu-id="6160b-161">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="6160b-162">將 [重新 **導向 URI** ] 下拉式清單保持設定為 [ **Web** ]，並提供下列重新導向 uri： `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="6160b-162">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="6160b-163">在 Kestrel 上執行應用程式的預設連接埠是 5001。</span><span class="sxs-lookup"><span data-stu-id="6160b-163">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="6160b-164">如果應用程式是在不同的 Kestrel 埠上執行，請使用應用程式的埠。</span><span class="sxs-lookup"><span data-stu-id="6160b-164">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="6160b-165">針對 IIS Express，可在 [ *`Server`* **調試** 程式] 面板的應用程式屬性中找到應用程式隨機產生的埠。</span><span class="sxs-lookup"><span data-stu-id="6160b-165">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="6160b-166">由於應用程式目前不存在，且 IIS Express 埠未知，因此在建立應用程式之後，請返回此步驟，並更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="6160b-166">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="6160b-167">批註會出現在 [ [建立應用程式](#create-the-app) ] 區段中，以提醒 IIS Express 使用者更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="6160b-167">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="6160b-168">清除 [授 **與系統** > **管理員同意 openid 和 offline_access 許可權** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="6160b-168">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="6160b-169">選取 [註冊]。</span><span class="sxs-lookup"><span data-stu-id="6160b-169">Select **Register** .</span></span>

<span data-ttu-id="6160b-170">記錄 *`Client`* 應用程式應用程式 (用戶端) 識別碼 (例如 `4369008b-21fa-427c-abaa-9b53bf58e538`) 。</span><span class="sxs-lookup"><span data-stu-id="6160b-170">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="6160b-171">在 [ **驗證** > **平臺** 設定] > **Web** ：</span><span class="sxs-lookup"><span data-stu-id="6160b-171">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="6160b-172">確認的重新 **導向 URI** `https://localhost:{PORT}/authentication/login-callback` 存在。</span><span class="sxs-lookup"><span data-stu-id="6160b-172">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="6160b-173">針對 **[隱含授** 與]，選取 **存取權杖** 和 **識別碼權杖** 的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="6160b-173">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens** .</span></span>
1. <span data-ttu-id="6160b-174">此體驗可接受應用程式的其餘預設值。</span><span class="sxs-lookup"><span data-stu-id="6160b-174">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="6160b-175">選取 [儲存] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="6160b-175">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="6160b-176">在 **API 許可權** 中：</span><span class="sxs-lookup"><span data-stu-id="6160b-176">In **API permissions** :</span></span>

1. <span data-ttu-id="6160b-177">確認應用程式已 **Microsoft Graph**  >  **使用者。讀取** 許可權。</span><span class="sxs-lookup"><span data-stu-id="6160b-177">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="6160b-178">選取 [ **新增] 許可權** ，然後選取 [我的 **api** ]。</span><span class="sxs-lookup"><span data-stu-id="6160b-178">Select **Add a permission** followed by **My APIs** .</span></span>
1. <span data-ttu-id="6160b-179">從 [ **名稱** ] 資料行中選取 *伺服器 API 應用程式* (例如， **Blazor Server AAD** ) 。</span><span class="sxs-lookup"><span data-stu-id="6160b-179">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD** ).</span></span>
1. <span data-ttu-id="6160b-180">開啟 **API** 清單。</span><span class="sxs-lookup"><span data-stu-id="6160b-180">Open the **API** list.</span></span>
1. <span data-ttu-id="6160b-181">啟用對 API 的存取 (例如 `API.Access`) 。</span><span class="sxs-lookup"><span data-stu-id="6160b-181">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="6160b-182">選取 [新增權限]。</span><span class="sxs-lookup"><span data-stu-id="6160b-182">Select **Add permissions** .</span></span>
1. <span data-ttu-id="6160b-183">選取 [ **授與系統管理員同意 {租使用者名稱}** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="6160b-183">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="6160b-184">選取 [是] 以確認。</span><span class="sxs-lookup"><span data-stu-id="6160b-184">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="6160b-185">建立應用程式</span><span class="sxs-lookup"><span data-stu-id="6160b-185">Create the app</span></span>

<span data-ttu-id="6160b-186">在空的資料夾中，將下列命令中的預留位置取代為先前記錄的資訊，然後在命令 shell 中執行命令：</span><span class="sxs-lookup"><span data-stu-id="6160b-186">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="6160b-187">預留位置</span><span class="sxs-lookup"><span data-stu-id="6160b-187">Placeholder</span></span>                  | <span data-ttu-id="6160b-188">Azure 入口網站名稱</span><span class="sxs-lookup"><span data-stu-id="6160b-188">Azure portal name</span></span>                                     | <span data-ttu-id="6160b-189">範例</span><span class="sxs-lookup"><span data-stu-id="6160b-189">Example</span></span>                                        |
| ---------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="6160b-190">應用程式 (用戶端) 的應用程式識別碼 *`Client`*</span><span class="sxs-lookup"><span data-stu-id="6160b-190">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="6160b-191">範圍名稱</span><span class="sxs-lookup"><span data-stu-id="6160b-191">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="6160b-192">*伺服器 API 應用* 程式 (用戶端) 識別碼</span><span class="sxs-lookup"><span data-stu-id="6160b-192">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="6160b-193">應用程式識別碼 URI&dagger;</span><span class="sxs-lookup"><span data-stu-id="6160b-193">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{TENANT DOMAIN}`            | <span data-ttu-id="6160b-194">主要/發行者/租使用者網域</span><span class="sxs-lookup"><span data-stu-id="6160b-194">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |
| `{TENANT ID}`                | <span data-ttu-id="6160b-195">目錄 (租用戶) 識別碼</span><span class="sxs-lookup"><span data-stu-id="6160b-195">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`         |

<span data-ttu-id="6160b-196">&dagger;Blazor WebAssembly範本會自動將配置新增 `api://` 至命令中傳遞的應用程式識別碼 URI 引數 `dotnet new` 。</span><span class="sxs-lookup"><span data-stu-id="6160b-196">&dagger;The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="6160b-197">提供預留位置的應用程式識別碼 URI， `{SERVER API APP ID URI}` 而且如果配置為 `api://` ，請從引數中移除配置 (`api://`) ，如同上表中的範例值所示。</span><span class="sxs-lookup"><span data-stu-id="6160b-197">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="6160b-198">如果應用程式識別碼 URI 是自訂值，或有一些其他配置 (例如，如果 `https://` 不受信任的發行者網域類似 `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`) ，您必須手動更新預設範圍 URI，並在 `api://` *`Client`* 範本建立應用程式之後移除配置。</span><span class="sxs-lookup"><span data-stu-id="6160b-198">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="6160b-199">如需詳細資訊，請參閱 [存取權杖範圍](#access-token-scopes) 一節中的附注。</span><span class="sxs-lookup"><span data-stu-id="6160b-199">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="6160b-200">此 Blazor WebAssembly 範本可能會在未來的 ASP.NET Core 版本中變更，以解決這些案例。</span><span class="sxs-lookup"><span data-stu-id="6160b-200">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="6160b-201">如需詳細資訊，請參閱 [WASM template (hosted 的應用程式識別碼 URI 的雙配置 Blazor 、單一組織)  (dotnet/aspnetcore #27417) ](https://github.com/dotnet/aspnetcore/issues/27417)。</span><span class="sxs-lookup"><span data-stu-id="6160b-201">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="6160b-202">使用 `-o|--output` 選項指定的輸出位置會建立專案資料夾 (如果不存在)，並成為應用程式名稱的一部分。</span><span class="sxs-lookup"><span data-stu-id="6160b-202">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="6160b-203">使用具有未驗證發行者網域的 Azure 租使用者時，可能需要進行設定變更，如 [應用程式設定](#app-settings) 一節中所述。</span><span class="sxs-lookup"><span data-stu-id="6160b-203">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [App settings](#app-settings) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="6160b-204">使用具有未驗證發行者網域的 Azure 租使用者時，可能需要進行設定變更，如 [存取權杖範圍](#access-token-scopes) 一節中所述。</span><span class="sxs-lookup"><span data-stu-id="6160b-204">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="6160b-205">在 Azure 入口網站中， *`Client`* 會針對使用預設設定在 Kestrel 伺服器上執行的應用程式，將應用程式的平臺設定重新 **導向 URI** 設定為使用埠5001。</span><span class="sxs-lookup"><span data-stu-id="6160b-205">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="6160b-206">如果 *`Client`* 應用程式是在隨機的 IIS Express 埠上執行，則可以在 [ **調試** 程式] 面板中的 *伺服器 API 應用程式* 屬性中找到應用程式的埠。</span><span class="sxs-lookup"><span data-stu-id="6160b-206">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="6160b-207">如果先前未使用 *`Client`* 應用程式的已知埠設定埠，請返回 *`Client`* Azure 入口網站中的應用程式註冊，然後以正確的埠更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="6160b-207">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="6160b-208">*`Server`* 應用程式設定</span><span class="sxs-lookup"><span data-stu-id="6160b-208">*`Server`* app configuration</span></span>

<span data-ttu-id="6160b-209">*本節適用于解決方案的 **`Server`** 應用程式。*</span><span class="sxs-lookup"><span data-stu-id="6160b-209">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="6160b-210">驗證套件</span><span class="sxs-lookup"><span data-stu-id="6160b-210">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6160b-211">使用 Microsoft 平臺對 ASP.NET Core Web Api 進行驗證和授權呼叫的支援 Identity 是由下列套件提供：</span><span class="sxs-lookup"><span data-stu-id="6160b-211">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="6160b-212">針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （NuGet.org）中找到符合應用程式共用架構版本的最新穩定版本套件。</span><span class="sxs-lookup"><span data-stu-id="6160b-212">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="6160b-213">對 ASP.NET Core Web Api 的驗證和授權呼叫支援是由 [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) 套件提供：</span><span class="sxs-lookup"><span data-stu-id="6160b-213">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="6160b-214">針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （ [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI)）中找到符合應用程式共用架構版本的最新穩定版本套件。</span><span class="sxs-lookup"><span data-stu-id="6160b-214">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="6160b-215">驗證服務支援</span><span class="sxs-lookup"><span data-stu-id="6160b-215">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6160b-216">方法會在 `AddAuthentication` 應用程式中設定驗證服務，並將 JWT 持有人處理常式設定為預設驗證方法。</span><span class="sxs-lookup"><span data-stu-id="6160b-216">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="6160b-217">方法會設定 <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> 服務，以使用 Microsoft Platform v2.0 來保護 WEB API Identity 。</span><span class="sxs-lookup"><span data-stu-id="6160b-217">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="6160b-218">此方法需要 `AzureAd` 應用程式設定中的區段，並具有初始化驗證選項所需的設定。</span><span class="sxs-lookup"><span data-stu-id="6160b-218">This method expects an `AzureAd` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="6160b-219">方法會在 `AddAuthentication` 應用程式中設定驗證服務，並將 JWT 持有人處理常式設定為預設驗證方法。</span><span class="sxs-lookup"><span data-stu-id="6160b-219">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="6160b-220"><xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A>方法會設定 JWT 持有人處理常式中的特定參數，以驗證 Azure Active Directory 所發出的權杖：</span><span class="sxs-lookup"><span data-stu-id="6160b-220">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<span data-ttu-id="6160b-221"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> 並 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> 確定：</span><span class="sxs-lookup"><span data-stu-id="6160b-221"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="6160b-222">應用程式會嘗試剖析並驗證傳入要求的權杖。</span><span class="sxs-lookup"><span data-stu-id="6160b-222">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="6160b-223">任何嘗試在沒有適當認證的情況下存取受保護資源的要求都會失敗。</span><span class="sxs-lookup"><span data-stu-id="6160b-223">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="6160b-224">使用者 ... Identity名字</span><span class="sxs-lookup"><span data-stu-id="6160b-224">User.Identity.Name</span></span>

<span data-ttu-id="6160b-225">根據預設， *`Server`* 應用程式 API 會 `User.Identity.Name` 以宣告類型的值填入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` (例如 `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`) 。</span><span class="sxs-lookup"><span data-stu-id="6160b-225">By default, the *`Server`* app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="6160b-226">若要將應用程式設定為接收來自宣告 `name` 類型的值，請 <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> 在中設定 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="6160b-226">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="6160b-227">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="6160b-227">App settings</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6160b-228">檔案 `appsettings.json` 包含設定用來驗證存取權杖之 JWT 持有人處理常式的選項：</span><span class="sxs-lookup"><span data-stu-id="6160b-228">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "CallbackPath": "/signin-oidc"
  }
}
```

<span data-ttu-id="6160b-229">範例：</span><span class="sxs-lookup"><span data-stu-id="6160b-229">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "CallbackPath": "/signin-oidc"
  }
}
```

[!INCLUDE[](~/includes/blazor-security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="6160b-230">檔案 `appsettings.json` 包含設定用來驗證存取權杖之 JWT 持有人處理常式的選項：</span><span class="sxs-lookup"><span data-stu-id="6160b-230">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="6160b-231">範例：</span><span class="sxs-lookup"><span data-stu-id="6160b-231">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

::: moniker-end

### <a name="weatherforecast-controller"></a><span data-ttu-id="6160b-232">WeatherForecast 控制器</span><span class="sxs-lookup"><span data-stu-id="6160b-232">WeatherForecast controller</span></span>

<span data-ttu-id="6160b-233">WeatherForecast 控制器 ( *控制器/WeatherForecastController* ) 會公開受保護的 API，並將 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 屬性套用至控制器。</span><span class="sxs-lookup"><span data-stu-id="6160b-233">The WeatherForecast controller ( *Controllers/WeatherForecastController.cs* ) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="6160b-234">請 **務必** 瞭解：</span><span class="sxs-lookup"><span data-stu-id="6160b-234">It's **important** to understand that:</span></span>

* <span data-ttu-id="6160b-235">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)此 api 控制器中的屬性是保護此 api 不會遭到未經授權存取的唯一做法。</span><span class="sxs-lookup"><span data-stu-id="6160b-235">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="6160b-236">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)應用程式中使用的屬性 Blazor WebAssembly 僅做為應用程式的提示，使用者應獲得授權才能讓應用程式正常運作。</span><span class="sxs-lookup"><span data-stu-id="6160b-236">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="6160b-237">*`Client`* 應用程式設定</span><span class="sxs-lookup"><span data-stu-id="6160b-237">*`Client`* app configuration</span></span>

<span data-ttu-id="6160b-238">*本節適用于解決方案的 **`Client`** 應用程式。*</span><span class="sxs-lookup"><span data-stu-id="6160b-238">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="6160b-239">驗證套件</span><span class="sxs-lookup"><span data-stu-id="6160b-239">Authentication package</span></span>

<span data-ttu-id="6160b-240">建立應用程式以使用 () 的公司或學校帳戶時 `SingleOrg` ，應用程式會自動收到 [Microsoft 驗證程式庫](/azure/active-directory/develop/msal-overview) 的套件參考 ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) 。</span><span class="sxs-lookup"><span data-stu-id="6160b-240">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="6160b-241">此套件提供一組基本類型，可協助應用程式驗證使用者，並取得權杖以呼叫受保護的 Api。</span><span class="sxs-lookup"><span data-stu-id="6160b-241">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="6160b-242">如果將驗證新增至應用程式，請手動將套件新增到應用程式的專案檔：</span><span class="sxs-lookup"><span data-stu-id="6160b-242">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="6160b-243">針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （ [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)）中找到符合應用程式共用架構版本的最新穩定版本套件。</span><span class="sxs-lookup"><span data-stu-id="6160b-243">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="6160b-244">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)封裝可傳遞將 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 套件新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="6160b-244">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="6160b-245">驗證服務支援</span><span class="sxs-lookup"><span data-stu-id="6160b-245">Authentication service support</span></span>

<span data-ttu-id="6160b-246">在對 <xref:System.Net.Http.HttpClient> 伺服器專案提出要求時，會加入包含存取權杖的實例支援。</span><span class="sxs-lookup"><span data-stu-id="6160b-246">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="6160b-247">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="6160b-247">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="6160b-248">預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如 `BlazorSample.ServerAPI`) 。</span><span class="sxs-lookup"><span data-stu-id="6160b-248">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="6160b-249">驗證使用者的支援是在服務容器中註冊，並具有 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 由封裝提供的擴充方法 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 。</span><span class="sxs-lookup"><span data-stu-id="6160b-249">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="6160b-250">此方法會設定應用程式 Identity (IP) 與提供者互動所需的服務。</span><span class="sxs-lookup"><span data-stu-id="6160b-250">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="6160b-251">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="6160b-251">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="6160b-252"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>方法會接受回呼來設定驗證應用程式所需的參數。</span><span class="sxs-lookup"><span data-stu-id="6160b-252">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="6160b-253">當您註冊應用程式時，可以從 Azure 入口網站 AAD 設定取得設定應用程式所需的值。</span><span class="sxs-lookup"><span data-stu-id="6160b-253">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="6160b-254">設定是由檔案提供 `wwwroot/appsettings.json` ：</span><span class="sxs-lookup"><span data-stu-id="6160b-254">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="6160b-255">範例：</span><span class="sxs-lookup"><span data-stu-id="6160b-255">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="6160b-256">存取權杖範圍</span><span class="sxs-lookup"><span data-stu-id="6160b-256">Access token scopes</span></span>

<span data-ttu-id="6160b-257">預設存取權杖範圍代表存取權杖範圍的清單，這些是：</span><span class="sxs-lookup"><span data-stu-id="6160b-257">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="6160b-258">預設包含在登入要求中。</span><span class="sxs-lookup"><span data-stu-id="6160b-258">Included by default in the sign in request.</span></span>
* <span data-ttu-id="6160b-259">用來在驗證之後立即布建存取權杖。</span><span class="sxs-lookup"><span data-stu-id="6160b-259">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="6160b-260">所有範圍都必須屬於相同的應用程式（依 Azure Active Directory 規則）。</span><span class="sxs-lookup"><span data-stu-id="6160b-260">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="6160b-261">您可以視需要為其他 API 應用程式新增其他範圍：</span><span class="sxs-lookup"><span data-stu-id="6160b-261">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="6160b-262">Blazor WebAssembly範本會自動將配置新增 `api://` 至命令中傳遞的應用程式識別碼 URI 引數 `dotnet new` 。</span><span class="sxs-lookup"><span data-stu-id="6160b-262">The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="6160b-263">從專案範本產生應用程式時 Blazor ，請確認預設存取權杖範圍的值使用您在 Azure 入口網站中提供的正確自訂應用程式識別碼 URI 值，或使用下列 **其中一** 種格式的值：</span><span class="sxs-lookup"><span data-stu-id="6160b-263">When generating an app from the Blazor project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="6160b-264">**信任** 目錄的發行者網域時，預設的存取權杖範圍通常會是類似下列範例的值，其中 `API.Access` 是預設的範圍名稱：</span><span class="sxs-lookup"><span data-stu-id="6160b-264">When the publisher domain of the directory is **trusted** , the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="6160b-265">檢查雙精度浮配置的值 (`api://api://...`) 。</span><span class="sxs-lookup"><span data-stu-id="6160b-265">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="6160b-266">如果有雙重配置，請 `api://` 從值中移除第一個配置。</span><span class="sxs-lookup"><span data-stu-id="6160b-266">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="6160b-267">當目錄的發行者網域不 **受信任** 時，預設存取權杖範圍通常會是類似下列範例的值，其中 `API.Access` 是預設的範圍名稱：</span><span class="sxs-lookup"><span data-stu-id="6160b-267">When the publisher domain of the directory is **untrusted** , the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="6160b-268">檢查額外 `api://` 配置 () 的值 `api://https://contoso.onmicrosoft.com/...` 。</span><span class="sxs-lookup"><span data-stu-id="6160b-268">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="6160b-269">如果有額外的 `api://` 配置，請 `api://` 從值中移除配置。</span><span class="sxs-lookup"><span data-stu-id="6160b-269">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="6160b-270">此 Blazor WebAssembly 範本可能會在未來的 ASP.NET Core 版本中變更，以解決這些案例。</span><span class="sxs-lookup"><span data-stu-id="6160b-270">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="6160b-271">如需詳細資訊，請參閱 [WASM template (hosted 的應用程式識別碼 URI 的雙配置 Blazor 、單一組織)  (dotnet/aspnetcore #27417) ](https://github.com/dotnet/aspnetcore/issues/27417)。</span><span class="sxs-lookup"><span data-stu-id="6160b-271">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="6160b-272">使用下列內容指定其他範圍 `AdditionalScopesToConsent` ：</span><span class="sxs-lookup"><span data-stu-id="6160b-272">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="6160b-273">如需詳細資訊，請參閱下列 *其他案例* 文章的各節：</span><span class="sxs-lookup"><span data-stu-id="6160b-273">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="6160b-274">要求其他存取權杖</span><span class="sxs-lookup"><span data-stu-id="6160b-274">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="6160b-275">將權杖附加至傳出要求</span><span class="sxs-lookup"><span data-stu-id="6160b-275">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="6160b-276">登入模式</span><span class="sxs-lookup"><span data-stu-id="6160b-276">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="6160b-277">匯入檔案</span><span class="sxs-lookup"><span data-stu-id="6160b-277">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="6160b-278">索引頁面</span><span class="sxs-lookup"><span data-stu-id="6160b-278">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="6160b-279">應用程式元件</span><span class="sxs-lookup"><span data-stu-id="6160b-279">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="6160b-280">RedirectToLogin 元件</span><span class="sxs-lookup"><span data-stu-id="6160b-280">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="6160b-281">LoginDisplay 元件</span><span class="sxs-lookup"><span data-stu-id="6160b-281">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="6160b-282">驗證元件</span><span class="sxs-lookup"><span data-stu-id="6160b-282">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="6160b-283">FetchData 元件</span><span class="sxs-lookup"><span data-stu-id="6160b-283">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="6160b-284">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="6160b-284">Run the app</span></span>

<span data-ttu-id="6160b-285">從伺服器專案執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="6160b-285">Run the app from the Server project.</span></span> <span data-ttu-id="6160b-286">使用 Visual Studio 時，您可以：</span><span class="sxs-lookup"><span data-stu-id="6160b-286">When using Visual Studio, either:</span></span>

* <span data-ttu-id="6160b-287">將工具列中的 [ **啟始專案** ] 下拉式清單設定為 *伺服器 API 應用程式* ，然後選取 [ **執行** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="6160b-287">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="6160b-288">在 **方案總管** 中選取伺服器專案，然後選取工具列中的 [ **執行** ] 按鈕，或從 [ **調試** 程式] 功能表啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="6160b-288">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="6160b-289">其他資源</span><span class="sxs-lookup"><span data-stu-id="6160b-289">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="6160b-290">具有安全預設用戶端之應用程式中未經驗證或未經授權的 web API 要求</span><span class="sxs-lookup"><span data-stu-id="6160b-290">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="6160b-291">Microsoft 身分識別平台文件</span><span class="sxs-lookup"><span data-stu-id="6160b-291">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
