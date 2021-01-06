---
title: Blazor WebAssembly使用 Azure Active Directory B2C 保護 ASP.NET Core 託管應用程式
author: guardrex
description: 瞭解如何使用 Azure Active Directory B2C 保護 ASP.NET Core Blazor WebAssembly 託管應用程式。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/26/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: dac2203d63b2d924ee6ae4f7012e9c33739e6213
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97792075"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="41412-103">Blazor WebAssembly使用 Azure Active Directory B2C 保護 ASP.NET Core 託管應用程式</span><span class="sxs-lookup"><span data-stu-id="41412-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="41412-104">由 [Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="41412-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="41412-105">本文說明如何建立使用[Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview)進行驗證的[託管 Blazor WebAssembly 應用程式](xref:blazor/hosting-models#blazor-webassembly)。</span><span class="sxs-lookup"><span data-stu-id="41412-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="41412-106">在 AAD B2C 中註冊應用程式並建立解決方案</span><span class="sxs-lookup"><span data-stu-id="41412-106">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="41412-107">建立租用戶</span><span class="sxs-lookup"><span data-stu-id="41412-107">Create a tenant</span></span>

<span data-ttu-id="41412-108">遵循 [教學課程：建立 Azure Active Directory B2C 租](/azure/active-directory-b2c/tutorial-create-tenant) 使用者建立 AAD B2C 租使用者中的指導方針。</span><span class="sxs-lookup"><span data-stu-id="41412-108">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span> <span data-ttu-id="41412-109">建立或識別要使用的租使用者之後，請立即返回此文章。</span><span class="sxs-lookup"><span data-stu-id="41412-109">Return to this article immediately after creating or identifying a tenant to use.</span></span>

<span data-ttu-id="41412-110">記錄 AAD B2C 實例 (例如， `https://contoso.b2clogin.com/` 其中包含尾端的斜線) 。</span><span class="sxs-lookup"><span data-stu-id="41412-110">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="41412-111">實例是 Azure B2C 應用程式註冊的配置和主機，您可以從 Azure 入口網站中的 [**應用程式註冊**] 頁面開啟 [**端點**] 視窗來找到。</span><span class="sxs-lookup"><span data-stu-id="41412-111">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="41412-112">註冊伺服器 API 應用程式</span><span class="sxs-lookup"><span data-stu-id="41412-112">Register a server API app</span></span>

<span data-ttu-id="41412-113">註冊 *伺服器 API 應用程式* 的 AAD B2C 應用程式：</span><span class="sxs-lookup"><span data-stu-id="41412-113">Register an AAD B2C app for the *Server API app*:</span></span>

1. <span data-ttu-id="41412-114">在 **Azure Active Directory**  >  **應用程式註冊** 中，選取 [**新增註冊**]。</span><span class="sxs-lookup"><span data-stu-id="41412-114">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="41412-115">提供應用程式的 **名稱** (例如 **Blazor Server AAD B2C**) 。</span><span class="sxs-lookup"><span data-stu-id="41412-115">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="41412-116">針對 **支援的帳戶類型**，請選取 [多租使用者選項： **任何識別提供者或組織目錄中的帳戶]， (以使用使用者流程驗證使用者)**</span><span class="sxs-lookup"><span data-stu-id="41412-116">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="41412-117">在此案例中， *伺服器 API 應用程式* 不需要重新 **導向 uri** ，因此請將下拉式清單保持設定為 [ **Web** ]，而不要輸入重新導向 uri。</span><span class="sxs-lookup"><span data-stu-id="41412-117">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="41412-118">確認  >  已選取 [將系統 **管理員同意授與 openid] 和 [offline_access] 許可權**。</span><span class="sxs-lookup"><span data-stu-id="41412-118">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="41412-119">選取 [註冊]。</span><span class="sxs-lookup"><span data-stu-id="41412-119">Select **Register**.</span></span>

<span data-ttu-id="41412-120">記錄下列資訊：</span><span class="sxs-lookup"><span data-stu-id="41412-120">Record the following information:</span></span>

* <span data-ttu-id="41412-121">*伺服器 API 應用程式* 應用程式 (用戶端) 識別碼 (例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`) </span><span class="sxs-lookup"><span data-stu-id="41412-121">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="41412-122">AAD 主要/發行者/租使用者網域 (例如 `contoso.onmicrosoft.com`) ：網域可作為已註冊應用程式之 Azure 入口網站的 [**商標**] 分頁中的 **發行者網域**。</span><span class="sxs-lookup"><span data-stu-id="41412-122">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="41412-123">在中 **公開 API**：</span><span class="sxs-lookup"><span data-stu-id="41412-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="41412-124">選取 [新增範圍]  。</span><span class="sxs-lookup"><span data-stu-id="41412-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="41412-125">選取 [儲存並繼續]  。</span><span class="sxs-lookup"><span data-stu-id="41412-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="41412-126">提供 **範圍名稱** (例如 `API.Access`) 。</span><span class="sxs-lookup"><span data-stu-id="41412-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="41412-127">提供系統 **管理員同意顯示名稱** (例如 `Access API`) 。</span><span class="sxs-lookup"><span data-stu-id="41412-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="41412-128">提供 **管理員同意描述** (例如 `Allows the app to access server app API endpoints.`) 。</span><span class="sxs-lookup"><span data-stu-id="41412-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="41412-129">確認 **狀態** 設定為 [ **已啟用**]。</span><span class="sxs-lookup"><span data-stu-id="41412-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="41412-130">選取 [新增範圍]。</span><span class="sxs-lookup"><span data-stu-id="41412-130">Select **Add scope**.</span></span>

<span data-ttu-id="41412-131">記錄下列資訊：</span><span class="sxs-lookup"><span data-stu-id="41412-131">Record the following information:</span></span>

* <span data-ttu-id="41412-132">應用程式識別碼 URI (例如， `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` 、 `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` 或您提供的自訂值) </span><span class="sxs-lookup"><span data-stu-id="41412-132">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="41412-133">範圍名稱 (例如， `API.Access`) </span><span class="sxs-lookup"><span data-stu-id="41412-133">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="41412-134">註冊用戶端應用程式</span><span class="sxs-lookup"><span data-stu-id="41412-134">Register a client app</span></span>

<span data-ttu-id="41412-135">註冊 *用戶端應用程式* 的 AAD B2C 應用程式：</span><span class="sxs-lookup"><span data-stu-id="41412-135">Register an AAD B2C app for the *Client app*:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="41412-136">在 **Azure Active Directory** > **應用程式註冊** 中，選取 [ **新增註冊**]。</span><span class="sxs-lookup"><span data-stu-id="41412-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="41412-137">提供應用程式的 **名稱** (例如， **Blazor 用戶端 AAD B2C**) 。</span><span class="sxs-lookup"><span data-stu-id="41412-137">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="41412-138">針對 **支援的帳戶類型**，請選取 [多租使用者選項： **任何識別提供者或組織目錄中的帳戶]， (以使用使用者流程驗證使用者)**</span><span class="sxs-lookup"><span data-stu-id="41412-138">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="41412-139">將 [重新 **導向 uri** ] 下拉式清單設定為 **單一頁面應用程式 (SPA)** 並提供下列重新導向 uri： `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="41412-139">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="41412-140">在 Kestrel 上執行應用程式的預設連接埠是 5001。</span><span class="sxs-lookup"><span data-stu-id="41412-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="41412-141">如果應用程式是在不同的 Kestrel 埠上執行，請使用應用程式的埠。</span><span class="sxs-lookup"><span data-stu-id="41412-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="41412-142">針對 IIS Express，可在 [ *`Server`* **調試** 程式] 面板的應用程式屬性中找到應用程式隨機產生的埠。</span><span class="sxs-lookup"><span data-stu-id="41412-142">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="41412-143">由於應用程式目前不存在，且 IIS Express 埠未知，因此在建立應用程式之後，請返回此步驟，並更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="41412-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="41412-144">批註會出現在 [ [建立應用程式](#create-the-app) ] 區段中，以提醒 IIS Express 使用者更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="41412-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="41412-145">確認 > 已選取 [將系統 **管理員同意授與 openid] 和 [offline_access] 許可權**。</span><span class="sxs-lookup"><span data-stu-id="41412-145">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="41412-146">選取 [註冊]。</span><span class="sxs-lookup"><span data-stu-id="41412-146">Select **Register**.</span></span>

1. <span data-ttu-id="41412-147">記錄應用程式 (用戶端) 識別碼 (例如 `4369008b-21fa-427c-abaa-9b53bf58e538`) 。</span><span class="sxs-lookup"><span data-stu-id="41412-147">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="41412-148">在「**驗證** 平臺設定」的 >  > **單一頁面應用程式中， (SPA)**：</span><span class="sxs-lookup"><span data-stu-id="41412-148">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="41412-149">確認的重新 **導向 URI** `https://localhost:{PORT}/authentication/login-callback` 存在。</span><span class="sxs-lookup"><span data-stu-id="41412-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="41412-150">針對 **隱含授** 與，請確定 **未** 選取 **存取權杖** 和 **識別碼權杖** 的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="41412-150">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="41412-151">此體驗可接受應用程式的其餘預設值。</span><span class="sxs-lookup"><span data-stu-id="41412-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="41412-152">選取 [儲存] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="41412-152">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="41412-153">在 **Azure Active Directory** > **應用程式註冊** 中，選取 [ **新增註冊**]。</span><span class="sxs-lookup"><span data-stu-id="41412-153">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="41412-154">提供應用程式的 **名稱** (例如， **Blazor 用戶端 AAD B2C**) 。</span><span class="sxs-lookup"><span data-stu-id="41412-154">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="41412-155">針對 **支援的帳戶類型**，請選取 [多租使用者選項： **任何識別提供者或組織目錄中的帳戶]， (以使用使用者流程驗證使用者)**</span><span class="sxs-lookup"><span data-stu-id="41412-155">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="41412-156">將 [重新 **導向 URI** ] 下拉式清單保持設定為 [ **Web** ]，並提供下列重新導向 uri： `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="41412-156">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="41412-157">在 Kestrel 上執行應用程式的預設連接埠是 5001。</span><span class="sxs-lookup"><span data-stu-id="41412-157">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="41412-158">如果應用程式是在不同的 Kestrel 埠上執行，請使用應用程式的埠。</span><span class="sxs-lookup"><span data-stu-id="41412-158">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="41412-159">針對 IIS Express，可在 [ *`Server`* **調試** 程式] 面板的應用程式屬性中找到應用程式隨機產生的埠。</span><span class="sxs-lookup"><span data-stu-id="41412-159">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="41412-160">由於應用程式目前不存在，且 IIS Express 埠未知，因此在建立應用程式之後，請返回此步驟，並更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="41412-160">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="41412-161">批註會出現在 [ [建立應用程式](#create-the-app) ] 區段中，以提醒 IIS Express 使用者更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="41412-161">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="41412-162">確認 > 已選取 [將系統 **管理員同意授與 openid] 和 [offline_access] 許可權**。</span><span class="sxs-lookup"><span data-stu-id="41412-162">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="41412-163">選取 [註冊]。</span><span class="sxs-lookup"><span data-stu-id="41412-163">Select **Register**.</span></span>

<span data-ttu-id="41412-164">記錄應用程式 (用戶端) 識別碼 (例如 `4369008b-21fa-427c-abaa-9b53bf58e538`) 。</span><span class="sxs-lookup"><span data-stu-id="41412-164">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="41412-165">在 [ **驗證** > **平臺** 設定] > **Web**：</span><span class="sxs-lookup"><span data-stu-id="41412-165">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="41412-166">確認的重新 **導向 URI** `https://localhost:{PORT}/authentication/login-callback` 存在。</span><span class="sxs-lookup"><span data-stu-id="41412-166">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="41412-167">針對 **[隱含授** 與]，選取 **存取權杖** 和 **識別碼權杖** 的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="41412-167">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="41412-168">此體驗可接受應用程式的其餘預設值。</span><span class="sxs-lookup"><span data-stu-id="41412-168">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="41412-169">選取 [儲存] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="41412-169">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="41412-170">在 **API 許可權** 中：</span><span class="sxs-lookup"><span data-stu-id="41412-170">In **API permissions**:</span></span>

1. <span data-ttu-id="41412-171">選取 [ **新增] 許可權** ，然後選取 [我的 **api**]。</span><span class="sxs-lookup"><span data-stu-id="41412-171">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="41412-172">從 [**名稱**] 資料行中選取 *伺服器 API 應用程式* (例如 **Blazor Server AAD B2C**) 。</span><span class="sxs-lookup"><span data-stu-id="41412-172">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="41412-173">開啟 **API** 清單。</span><span class="sxs-lookup"><span data-stu-id="41412-173">Open the **API** list.</span></span>
1. <span data-ttu-id="41412-174">啟用對 API 的存取 (例如 `API.Access`) 。</span><span class="sxs-lookup"><span data-stu-id="41412-174">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="41412-175">選取 [新增權限]。</span><span class="sxs-lookup"><span data-stu-id="41412-175">Select **Add permissions**.</span></span>
1. <span data-ttu-id="41412-176">選取 [ **授與系統管理員同意 {租使用者名稱}** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="41412-176">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="41412-177">選取 [是]  加以確認。</span><span class="sxs-lookup"><span data-stu-id="41412-177">Select **Yes** to confirm.</span></span>

<span data-ttu-id="41412-178">在 **Home**  >  **Azure AD B2C**  >  **使用者流程**：</span><span class="sxs-lookup"><span data-stu-id="41412-178">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="41412-179">建立註冊和登入使用者流程</span><span class="sxs-lookup"><span data-stu-id="41412-179">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="41412-180">至少選取 [**應用程式宣告**  >  **顯示名稱**] 使用者屬性，以填入 `context.User.Identity.Name` `LoginDisplay` 元件 () 中的 `Shared/LoginDisplay.razor` 。</span><span class="sxs-lookup"><span data-stu-id="41412-180">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="41412-181">記錄為應用程式建立的註冊和登入使用者流程名稱 (例如 `B2C_1_signupsignin`) 。</span><span class="sxs-lookup"><span data-stu-id="41412-181">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="41412-182">建立應用程式</span><span class="sxs-lookup"><span data-stu-id="41412-182">Create the app</span></span>

<span data-ttu-id="41412-183">將下列命令中的預留位置取代為稍早記錄的資訊，然後在命令 shell 中執行命令：</span><span class="sxs-lookup"><span data-stu-id="41412-183">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="41412-184">預留位置</span><span class="sxs-lookup"><span data-stu-id="41412-184">Placeholder</span></span>                   | <span data-ttu-id="41412-185">Azure 入口網站名稱</span><span class="sxs-lookup"><span data-stu-id="41412-185">Azure portal name</span></span>                                     | <span data-ttu-id="41412-186">範例</span><span class="sxs-lookup"><span data-stu-id="41412-186">Example</span></span>                                        |
| ----------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="41412-187">執行個體</span><span class="sxs-lookup"><span data-stu-id="41412-187">Instance</span></span>                                              | `https://contoso.b2clogin.com/`                |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="41412-188">應用程式 (用戶端) 的應用程式識別碼 *`Client`*</span><span class="sxs-lookup"><span data-stu-id="41412-188">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="41412-189">範圍名稱</span><span class="sxs-lookup"><span data-stu-id="41412-189">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="41412-190">*伺服器 API 應用* 程式 (用戶端) 識別碼</span><span class="sxs-lookup"><span data-stu-id="41412-190">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="41412-191">應用程式識別碼 URI&dagger;</span><span class="sxs-lookup"><span data-stu-id="41412-191">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="41412-192">註冊/登入使用者流程</span><span class="sxs-lookup"><span data-stu-id="41412-192">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                          |
| `{TENANT DOMAIN}`             | <span data-ttu-id="41412-193">主要/發行者/租使用者網域</span><span class="sxs-lookup"><span data-stu-id="41412-193">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |

<span data-ttu-id="41412-194">&dagger;Blazor WebAssembly範本會自動將配置新增 `api://` 至命令中傳遞的應用程式識別碼 URI 引數 `dotnet new` 。</span><span class="sxs-lookup"><span data-stu-id="41412-194">&dagger;The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="41412-195">提供預留位置的應用程式識別碼 URI， `{SERVER API APP ID URI}` 而且如果配置為 `api://` ，請從引數中移除配置 (`api://`) ，如同上表中的範例值所示。</span><span class="sxs-lookup"><span data-stu-id="41412-195">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="41412-196">如果應用程式識別碼 URI 是自訂值，或有一些其他配置 (例如，如果 `https://` 不受信任的發行者網域類似 `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`) ，您必須手動更新預設範圍 URI，並在 `api://` *`Client`* 範本建立應用程式之後移除配置。</span><span class="sxs-lookup"><span data-stu-id="41412-196">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="41412-197">如需詳細資訊，請參閱 [存取權杖範圍](#access-token-scopes) 一節中的附注。</span><span class="sxs-lookup"><span data-stu-id="41412-197">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="41412-198">此 Blazor WebAssembly 範本可能會在未來的 ASP.NET Core 版本中變更，以解決這些案例。</span><span class="sxs-lookup"><span data-stu-id="41412-198">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="41412-199">如需詳細資訊，請參閱 [WASM template (hosted 的應用程式識別碼 URI 的雙配置 Blazor 、單一組織)  (dotnet/aspnetcore #27417) ](https://github.com/dotnet/aspnetcore/issues/27417)。</span><span class="sxs-lookup"><span data-stu-id="41412-199">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="41412-200">使用 `-o|--output` 選項指定的輸出位置會建立專案資料夾 (如果不存在)，並成為應用程式名稱的一部分。</span><span class="sxs-lookup"><span data-stu-id="41412-200">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="41412-201">裝載範本所設定的範圍 Blazor 可能會有重複的應用程式識別碼 URI 主機。</span><span class="sxs-lookup"><span data-stu-id="41412-201">The scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="41412-202">確認 `DefaultAccessTokenScopes` 在 `Program.Main` 應用程式的 () 中為集合設定的範圍正確 `Program.cs` *`Client`* 。</span><span class="sxs-lookup"><span data-stu-id="41412-202">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *`Client`* app.</span></span>

> [!NOTE]
> <span data-ttu-id="41412-203">在 Azure 入口網站中， *`Client`* 會針對使用預設設定在 Kestrel 伺服器上執行的應用程式，將應用程式的平臺設定重新 **導向 URI** 設定為使用埠5001。</span><span class="sxs-lookup"><span data-stu-id="41412-203">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="41412-204">如果 *`Client`* 應用程式是在隨機的 IIS Express 埠上執行，則可以在 [**調試** 程式] 面板中的 *伺服器 API 應用程式* 屬性中找到應用程式的埠。</span><span class="sxs-lookup"><span data-stu-id="41412-204">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="41412-205">如果先前未使用 *`Client`* 應用程式的已知埠設定埠，請返回 *`Client`* Azure 入口網站中的應用程式註冊，然後以正確的埠更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="41412-205">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="41412-206">*`Server`* 應用程式設定</span><span class="sxs-lookup"><span data-stu-id="41412-206">*`Server`* app configuration</span></span>

<span data-ttu-id="41412-207">*本節適用于解決方案的 **`Server`** 應用程式。*</span><span class="sxs-lookup"><span data-stu-id="41412-207">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="41412-208">驗證套件</span><span class="sxs-lookup"><span data-stu-id="41412-208">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="41412-209">使用 Microsoft 平臺對 ASP.NET Core Web Api 進行驗證和授權呼叫的支援 Identity 是由下列套件提供：</span><span class="sxs-lookup"><span data-stu-id="41412-209">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="41412-210">針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （NuGet.org）中找到符合應用程式共用架構版本的最新穩定版本套件。</span><span class="sxs-lookup"><span data-stu-id="41412-210">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="41412-211">對 ASP.NET Core Web Api 的驗證和授權呼叫支援是由 [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) 套件提供：</span><span class="sxs-lookup"><span data-stu-id="41412-211">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="41412-212">針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （ [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI)）中找到符合應用程式共用架構版本的最新穩定版本套件。</span><span class="sxs-lookup"><span data-stu-id="41412-212">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="41412-213">驗證服務支援</span><span class="sxs-lookup"><span data-stu-id="41412-213">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="41412-214">方法會在 `AddAuthentication` 應用程式中設定驗證服務，並將 JWT 持有人處理常式設定為預設驗證方法。</span><span class="sxs-lookup"><span data-stu-id="41412-214">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="41412-215">方法會設定 <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> 服務，以使用 Microsoft Platform v2.0 來保護 WEB API Identity 。</span><span class="sxs-lookup"><span data-stu-id="41412-215">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="41412-216">此方法需要 `AzureAdB2C` 應用程式設定中的區段，並具有初始化驗證選項所需的設定。</span><span class="sxs-lookup"><span data-stu-id="41412-216">This method expects an `AzureAdB2C` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAdB2C"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="41412-217">方法會在 `AddAuthentication` 應用程式中設定驗證服務，並將 JWT 持有人處理常式設定為預設驗證方法。</span><span class="sxs-lookup"><span data-stu-id="41412-217">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="41412-218"><xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A>方法會設定 JWT 持有人處理常式中的特定參數，以驗證 Azure Active Directory B2C 所發出的權杖：</span><span class="sxs-lookup"><span data-stu-id="41412-218">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

::: moniker-end

<span data-ttu-id="41412-219"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> 並 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> 確定：</span><span class="sxs-lookup"><span data-stu-id="41412-219"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="41412-220">應用程式會嘗試剖析並驗證傳入要求的權杖。</span><span class="sxs-lookup"><span data-stu-id="41412-220">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="41412-221">任何嘗試在沒有適當認證的情況下存取受保護資源的要求都會失敗。</span><span class="sxs-lookup"><span data-stu-id="41412-221">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="41412-222">使用者 ... Identity名字</span><span class="sxs-lookup"><span data-stu-id="41412-222">User.Identity.Name</span></span>

<span data-ttu-id="41412-223">依預設， `User.Identity.Name` 不會填入。</span><span class="sxs-lookup"><span data-stu-id="41412-223">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="41412-224">若要將應用程式設定為接收來自宣告 `name` 類型的值：</span><span class="sxs-lookup"><span data-stu-id="41412-224">To configure the app to receive the value from the `name` claim type:</span></span>

* <span data-ttu-id="41412-225">將命名空間加入 <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> 至 `Startup.cs` ：</span><span class="sxs-lookup"><span data-stu-id="41412-225">Add a namespace for <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> to `Startup.cs`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Authentication.JwtBearer;
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="41412-226"><xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> 在中設定 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="41412-226">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      JwtBearerDefaults.AuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="41412-227"><xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> 在中設定 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="41412-227">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

### <a name="app-settings"></a><span data-ttu-id="41412-228">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="41412-228">App settings</span></span>

<span data-ttu-id="41412-229">檔案 `appsettings.json` 包含設定用來驗證存取權杖之 JWT 持有人處理常式的選項。</span><span class="sxs-lookup"><span data-stu-id="41412-229">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="41412-230">範例：</span><span class="sxs-lookup"><span data-stu-id="41412-230">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="41412-231">WeatherForecast 控制器</span><span class="sxs-lookup"><span data-stu-id="41412-231">WeatherForecast controller</span></span>

<span data-ttu-id="41412-232">WeatherForecast 控制器 (*控制器/WeatherForecastController*) 會公開受保護的 API，並將 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 屬性套用至控制器。</span><span class="sxs-lookup"><span data-stu-id="41412-232">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="41412-233">請 **務必** 瞭解：</span><span class="sxs-lookup"><span data-stu-id="41412-233">It's **important** to understand that:</span></span>

* <span data-ttu-id="41412-234">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)此 api 控制器中的屬性是保護此 api 不會遭到未經授權存取的唯一做法。</span><span class="sxs-lookup"><span data-stu-id="41412-234">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="41412-235">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)應用程式中使用的屬性 Blazor WebAssembly 僅做為應用程式的提示，使用者應獲得授權才能讓應用程式正常運作。</span><span class="sxs-lookup"><span data-stu-id="41412-235">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="41412-236">*`Client`* 應用程式設定</span><span class="sxs-lookup"><span data-stu-id="41412-236">*`Client`* app configuration</span></span>

<span data-ttu-id="41412-237">*本節適用于解決方案的 **`Client`** 應用程式。*</span><span class="sxs-lookup"><span data-stu-id="41412-237">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="41412-238">驗證套件</span><span class="sxs-lookup"><span data-stu-id="41412-238">Authentication package</span></span>

<span data-ttu-id="41412-239">建立應用程式以使用個別 B2C 帳戶 (`IndividualB2C`) 時，應用程式會自動收到 [Microsoft 驗證程式庫](/azure/active-directory/develop/msal-overview) 的套件參考 ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) 。</span><span class="sxs-lookup"><span data-stu-id="41412-239">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="41412-240">此套件提供一組基本類型，可協助應用程式驗證使用者，並取得權杖以呼叫受保護的 Api。</span><span class="sxs-lookup"><span data-stu-id="41412-240">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="41412-241">如果將驗證新增至應用程式，請手動將套件新增到應用程式的專案檔：</span><span class="sxs-lookup"><span data-stu-id="41412-241">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="41412-242">針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （ [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)）中找到符合應用程式共用架構版本的最新穩定版本套件。</span><span class="sxs-lookup"><span data-stu-id="41412-242">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="41412-243">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)封裝可傳遞將 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 套件新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="41412-243">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="41412-244">驗證服務支援</span><span class="sxs-lookup"><span data-stu-id="41412-244">Authentication service support</span></span>

<span data-ttu-id="41412-245">在對 <xref:System.Net.Http.HttpClient> 伺服器專案提出要求時，會加入包含存取權杖的實例支援。</span><span class="sxs-lookup"><span data-stu-id="41412-245">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="41412-246">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="41412-246">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="41412-247">預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如 `BlazorSample.ServerAPI`) 。</span><span class="sxs-lookup"><span data-stu-id="41412-247">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="41412-248">驗證使用者的支援是在服務容器中註冊，並具有 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 由封裝提供的擴充方法 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 。</span><span class="sxs-lookup"><span data-stu-id="41412-248">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="41412-249">此方法會設定應用程式 Identity (IP) 與提供者互動所需的服務。</span><span class="sxs-lookup"><span data-stu-id="41412-249">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="41412-250">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="41412-250">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="41412-251"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>方法會接受回呼來設定驗證應用程式所需的參數。</span><span class="sxs-lookup"><span data-stu-id="41412-251">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="41412-252">當您註冊應用程式時，可以從 Azure 入口網站 AAD 設定取得設定應用程式所需的值。</span><span class="sxs-lookup"><span data-stu-id="41412-252">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="41412-253">設定是由檔案提供 `wwwroot/appsettings.json` ：</span><span class="sxs-lookup"><span data-stu-id="41412-253">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="41412-254">範例：</span><span class="sxs-lookup"><span data-stu-id="41412-254">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="41412-255">存取權杖範圍</span><span class="sxs-lookup"><span data-stu-id="41412-255">Access token scopes</span></span>

<span data-ttu-id="41412-256">預設存取權杖範圍代表存取權杖範圍的清單，這些是：</span><span class="sxs-lookup"><span data-stu-id="41412-256">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="41412-257">預設包含在登入要求中。</span><span class="sxs-lookup"><span data-stu-id="41412-257">Included by default in the sign in request.</span></span>
* <span data-ttu-id="41412-258">用來在驗證之後立即布建存取權杖。</span><span class="sxs-lookup"><span data-stu-id="41412-258">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="41412-259">所有範圍都必須屬於相同的應用程式（依 Azure Active Directory 規則）。</span><span class="sxs-lookup"><span data-stu-id="41412-259">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="41412-260">您可以視需要為其他 API 應用程式新增其他範圍：</span><span class="sxs-lookup"><span data-stu-id="41412-260">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="41412-261">Blazor WebAssembly範本會自動將配置新增 `api://` 至命令中傳遞的應用程式識別碼 URI 引數 `dotnet new` 。</span><span class="sxs-lookup"><span data-stu-id="41412-261">The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="41412-262">從專案範本產生應用程式時 Blazor ，請確認預設存取權杖範圍的值使用您在 Azure 入口網站中提供的正確自訂應用程式識別碼 URI 值，或使用下列 **其中一** 種格式的值：</span><span class="sxs-lookup"><span data-stu-id="41412-262">When generating an app from the Blazor project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="41412-263">**信任** 目錄的發行者網域時，預設的存取權杖範圍通常會是類似下列範例的值，其中 `API.Access` 是預設的範圍名稱：</span><span class="sxs-lookup"><span data-stu-id="41412-263">When the publisher domain of the directory is **trusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="41412-264">檢查雙精度浮配置的值 (`api://api://...`) 。</span><span class="sxs-lookup"><span data-stu-id="41412-264">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="41412-265">如果有雙重配置，請 `api://` 從值中移除第一個配置。</span><span class="sxs-lookup"><span data-stu-id="41412-265">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="41412-266">當目錄的發行者網域不 **受信任** 時，預設存取權杖範圍通常會是類似下列範例的值，其中 `API.Access` 是預設的範圍名稱：</span><span class="sxs-lookup"><span data-stu-id="41412-266">When the publisher domain of the directory is **untrusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="41412-267">檢查額外 `api://` 配置 () 的值 `api://https://contoso.onmicrosoft.com/...` 。</span><span class="sxs-lookup"><span data-stu-id="41412-267">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="41412-268">如果有額外的 `api://` 配置，請 `api://` 從值中移除配置。</span><span class="sxs-lookup"><span data-stu-id="41412-268">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="41412-269">此 Blazor WebAssembly 範本可能會在未來的 ASP.NET Core 版本中變更，以解決這些案例。</span><span class="sxs-lookup"><span data-stu-id="41412-269">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="41412-270">如需詳細資訊，請參閱 [WASM template (hosted 的應用程式識別碼 URI 的雙配置 Blazor 、單一組織)  (dotnet/aspnetcore #27417) ](https://github.com/dotnet/aspnetcore/issues/27417)。</span><span class="sxs-lookup"><span data-stu-id="41412-270">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="41412-271">使用下列內容指定其他範圍 `AdditionalScopesToConsent` ：</span><span class="sxs-lookup"><span data-stu-id="41412-271">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="41412-272">如需詳細資訊，請參閱下列 *其他案例* 文章的各節：</span><span class="sxs-lookup"><span data-stu-id="41412-272">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="41412-273">要求其他存取權杖</span><span class="sxs-lookup"><span data-stu-id="41412-273">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="41412-274">將權杖附加至傳出要求</span><span class="sxs-lookup"><span data-stu-id="41412-274">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="41412-275">登入模式</span><span class="sxs-lookup"><span data-stu-id="41412-275">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="41412-276">匯入檔案</span><span class="sxs-lookup"><span data-stu-id="41412-276">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="41412-277">索引頁面</span><span class="sxs-lookup"><span data-stu-id="41412-277">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="41412-278">應用程式元件</span><span class="sxs-lookup"><span data-stu-id="41412-278">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="41412-279">RedirectToLogin 元件</span><span class="sxs-lookup"><span data-stu-id="41412-279">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="41412-280">LoginDisplay 元件</span><span class="sxs-lookup"><span data-stu-id="41412-280">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="41412-281">驗證元件</span><span class="sxs-lookup"><span data-stu-id="41412-281">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="41412-282">FetchData 元件</span><span class="sxs-lookup"><span data-stu-id="41412-282">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="41412-283">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="41412-283">Run the app</span></span>

<span data-ttu-id="41412-284">從伺服器專案執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="41412-284">Run the app from the Server project.</span></span> <span data-ttu-id="41412-285">使用 Visual Studio 時，您可以：</span><span class="sxs-lookup"><span data-stu-id="41412-285">When using Visual Studio, either:</span></span>

* <span data-ttu-id="41412-286">將工具列中的 [ **啟始專案** ] 下拉式清單設定為 *伺服器 API 應用程式* ，然後選取 [ **執行** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="41412-286">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="41412-287">在 **方案總管** 中選取伺服器專案，然後選取工具列中的 [ **執行** ] 按鈕，或從 [ **調試** 程式] 功能表啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="41412-287">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/blazor/includes/security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="41412-288">其他資源</span><span class="sxs-lookup"><span data-stu-id="41412-288">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="41412-289">具有安全預設用戶端之應用程式中未經驗證或未經授權的 web API 要求</span><span class="sxs-lookup"><span data-stu-id="41412-289">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* <span data-ttu-id="41412-290">[教學課程：建立 Azure Active Directory B2C 租用戶](/azure/active-directory-b2c/tutorial-create-tenant) \(部分機器翻譯\)</span><span class="sxs-lookup"><span data-stu-id="41412-290">[Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant)</span></span>
* [<span data-ttu-id="41412-291">教學課程：在 Azure Active Directory B2C 中註冊應用程式</span><span class="sxs-lookup"><span data-stu-id="41412-291">Tutorial: Register an application in Azure Active Directory B2C</span></span>](/azure/active-directory-b2c/tutorial-register-applications)
* [<span data-ttu-id="41412-292">Microsoft 身分識別平台文件</span><span class="sxs-lookup"><span data-stu-id="41412-292">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
