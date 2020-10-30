---
title: :::no-loc(Blazor WebAssembly):::使用 Azure Active Directory B2C 保護 ASP.NET Core 託管應用程式
author: guardrex
description: '瞭解如何使用 Azure Active Directory B2C 保護 ASP.NET Core :::no-loc(Blazor WebAssembly)::: 託管應用程式。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: ff6159918dd43b56dd3fe3c61f8c3196afdd2c99
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055291"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="bec39-103">:::no-loc(Blazor WebAssembly):::使用 Azure Active Directory B2C 保護 ASP.NET Core 託管應用程式</span><span class="sxs-lookup"><span data-stu-id="bec39-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="bec39-104">由 [Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bec39-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="bec39-105">本文說明如何建立使用[Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview)進行驗證的[託管 :::no-loc(Blazor WebAssembly)::: 應用程式](xref:blazor/hosting-models#blazor-webassembly)。</span><span class="sxs-lookup"><span data-stu-id="bec39-105">This article describes how to create a [hosted :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="bec39-106">在 AAD B2C 中註冊應用程式並建立解決方案</span><span class="sxs-lookup"><span data-stu-id="bec39-106">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="bec39-107">建立租用戶</span><span class="sxs-lookup"><span data-stu-id="bec39-107">Create a tenant</span></span>

<span data-ttu-id="bec39-108">遵循 [教學課程：建立 Azure Active Directory B2C 租](/azure/active-directory-b2c/tutorial-create-tenant) 使用者建立 AAD B2C 租使用者中的指導方針。</span><span class="sxs-lookup"><span data-stu-id="bec39-108">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="bec39-109">記錄 AAD B2C 實例 (例如， `https://contoso.b2clogin.com/` 其中包含尾端的斜線) 。</span><span class="sxs-lookup"><span data-stu-id="bec39-109">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="bec39-110">實例是 Azure B2C 應用程式註冊的配置和主機，您可以從 Azure 入口網站中的 [ **應用程式註冊** ] 頁面開啟 [ **端點** ] 視窗來找到。</span><span class="sxs-lookup"><span data-stu-id="bec39-110">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="bec39-111">註冊伺服器 API 應用程式</span><span class="sxs-lookup"><span data-stu-id="bec39-111">Register a server API app</span></span>

<span data-ttu-id="bec39-112">遵循教學課程中的指導方針 [：在 Azure Active Directory B2C 中註冊應用程式](/azure/active-directory-b2c/tutorial-register-applications) 以註冊 *伺服器 API 應用* 程式的 AAD 應用程式，然後執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="bec39-112">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="bec39-113">在 **Azure Active Directory**  >  **應用程式註冊** 中，選取 [ **新增註冊** ]。</span><span class="sxs-lookup"><span data-stu-id="bec39-113">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="bec39-114">提供應用程式的 **名稱** (例如 **:::no-loc(Blazor Server)::: AAD B2C** ) 。</span><span class="sxs-lookup"><span data-stu-id="bec39-114">Provide a **Name** for the app (for example, **:::no-loc(Blazor Server)::: AAD B2C** ).</span></span>
1. <span data-ttu-id="bec39-115">針對 **支援的帳戶類型** ，請選取 [多租使用者選項： **任何識別提供者或組織目錄中的帳戶]， (以使用使用者流程驗證使用者)**</span><span class="sxs-lookup"><span data-stu-id="bec39-115">For **Supported account types** , select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="bec39-116">在此案例中， *伺服器 API 應用程式* 不需要重新 **導向 uri** ，因此請將下拉式清單保持設定為 [ **Web** ]，而不要輸入重新導向 uri。</span><span class="sxs-lookup"><span data-stu-id="bec39-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="bec39-117">確認 **Permissions**  >  已選取 [將系統 **管理員同意授與 openid] 和 [offline_access] 許可權** 。</span><span class="sxs-lookup"><span data-stu-id="bec39-117">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="bec39-118">選取 [註冊]。</span><span class="sxs-lookup"><span data-stu-id="bec39-118">Select **Register** .</span></span>

<span data-ttu-id="bec39-119">記錄下列資訊：</span><span class="sxs-lookup"><span data-stu-id="bec39-119">Record the following information:</span></span>

* <span data-ttu-id="bec39-120">*伺服器 API 應用程式* 應用程式 (用戶端) 識別碼 (例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`) </span><span class="sxs-lookup"><span data-stu-id="bec39-120">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="bec39-121">AAD 主要/發行者/租使用者網域 (例如 `contoso.onmicrosoft.com`) ：網域可作為已註冊應用程式之 Azure 入口網站的 [ **商標** ] 分頁中的 **發行者網域** 。</span><span class="sxs-lookup"><span data-stu-id="bec39-121">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="bec39-122">在中 **公開 API** ：</span><span class="sxs-lookup"><span data-stu-id="bec39-122">In **Expose an API** :</span></span>

1. <span data-ttu-id="bec39-123">選取 [新增範圍]  。</span><span class="sxs-lookup"><span data-stu-id="bec39-123">Select **Add a scope** .</span></span>
1. <span data-ttu-id="bec39-124">選取 [儲存並繼續]  。</span><span class="sxs-lookup"><span data-stu-id="bec39-124">Select **Save and continue** .</span></span>
1. <span data-ttu-id="bec39-125">提供 **範圍名稱** (例如 `API.Access`) 。</span><span class="sxs-lookup"><span data-stu-id="bec39-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="bec39-126">提供系統 **管理員同意顯示名稱** (例如 `Access API`) 。</span><span class="sxs-lookup"><span data-stu-id="bec39-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="bec39-127">提供 **管理員同意描述** (例如 `Allows the app to access server app API endpoints.`) 。</span><span class="sxs-lookup"><span data-stu-id="bec39-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="bec39-128">確認 **狀態** 設定為 [ **已啟用** ]。</span><span class="sxs-lookup"><span data-stu-id="bec39-128">Confirm that the **State** is set to **Enabled** .</span></span>
1. <span data-ttu-id="bec39-129">選取 [新增範圍]。</span><span class="sxs-lookup"><span data-stu-id="bec39-129">Select **Add scope** .</span></span>

<span data-ttu-id="bec39-130">記錄下列資訊：</span><span class="sxs-lookup"><span data-stu-id="bec39-130">Record the following information:</span></span>

* <span data-ttu-id="bec39-131">應用程式識別碼 URI (例如， `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` 、 `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` 或您提供的自訂值) </span><span class="sxs-lookup"><span data-stu-id="bec39-131">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="bec39-132">範圍名稱 (例如， `API.Access`) </span><span class="sxs-lookup"><span data-stu-id="bec39-132">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="bec39-133">註冊用戶端應用程式</span><span class="sxs-lookup"><span data-stu-id="bec39-133">Register a client app</span></span>

<span data-ttu-id="bec39-134">遵循教學課程中的指導方針 [：再次在 Azure Active Directory B2C 中註冊應用程式](/azure/active-directory-b2c/tutorial-register-applications) ，為應用程式註冊 AAD 應用程式 *`Client`* ，然後執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="bec39-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="bec39-135">在 **Azure Active Directory** > **應用程式註冊** 中，選取 [ **新增註冊** ]。</span><span class="sxs-lookup"><span data-stu-id="bec39-135">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="bec39-136">提供應用程式的 **名稱** (例如， **:::no-loc(Blazor)::: 用戶端 AAD B2C** ) 。</span><span class="sxs-lookup"><span data-stu-id="bec39-136">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Client AAD B2C** ).</span></span>
1. <span data-ttu-id="bec39-137">針對 **支援的帳戶類型** ，請選取 [多租使用者選項： **任何識別提供者或組織目錄中的帳戶]， (以使用使用者流程驗證使用者)**</span><span class="sxs-lookup"><span data-stu-id="bec39-137">For **Supported account types** , select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="bec39-138">將 [重新 **導向 uri** ] 下拉式清單設定為 **單一頁面應用程式 (SPA)** 並提供下列重新導向 uri： `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="bec39-138">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="bec39-139">在 Kestrel 上執行應用程式的預設連接埠是 5001。</span><span class="sxs-lookup"><span data-stu-id="bec39-139">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="bec39-140">如果應用程式是在不同的 Kestrel 埠上執行，請使用應用程式的埠。</span><span class="sxs-lookup"><span data-stu-id="bec39-140">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="bec39-141">針對 IIS Express，可在 [ *`Server`* **調試** 程式] 面板的應用程式屬性中找到應用程式隨機產生的埠。</span><span class="sxs-lookup"><span data-stu-id="bec39-141">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="bec39-142">由於應用程式目前不存在，且 IIS Express 埠未知，因此在建立應用程式之後，請返回此步驟，並更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="bec39-142">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="bec39-143">批註會出現在 [ [建立應用程式](#create-the-app) ] 區段中，以提醒 IIS Express 使用者更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="bec39-143">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="bec39-144">確認 **Permissions** > 已選取 [將系統 **管理員同意授與 openid] 和 [offline_access] 許可權** 。</span><span class="sxs-lookup"><span data-stu-id="bec39-144">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="bec39-145">選取 [註冊]。</span><span class="sxs-lookup"><span data-stu-id="bec39-145">Select **Register** .</span></span>

1. <span data-ttu-id="bec39-146">記錄應用程式 (用戶端) 識別碼 (例如 `4369008b-21fa-427c-abaa-9b53bf58e538`) 。</span><span class="sxs-lookup"><span data-stu-id="bec39-146">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="bec39-147">在「 **驗證** 平臺設定」的 > **Platform configurations** > **單一頁面應用程式中， (SPA)** ：</span><span class="sxs-lookup"><span data-stu-id="bec39-147">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="bec39-148">確認的重新 **導向 URI** `https://localhost:{PORT}/authentication/login-callback` 存在。</span><span class="sxs-lookup"><span data-stu-id="bec39-148">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="bec39-149">針對 **隱含授** 與，請確定 **未** 選取 **存取權杖** 和 **識別碼權杖** 的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="bec39-149">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="bec39-150">此體驗可接受應用程式的其餘預設值。</span><span class="sxs-lookup"><span data-stu-id="bec39-150">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="bec39-151">選取 [儲存] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="bec39-151">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="bec39-152">在 **Azure Active Directory** > **應用程式註冊** 中，選取 [ **新增註冊** ]。</span><span class="sxs-lookup"><span data-stu-id="bec39-152">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="bec39-153">提供應用程式的 **名稱** (例如， **:::no-loc(Blazor)::: 用戶端 AAD B2C** ) 。</span><span class="sxs-lookup"><span data-stu-id="bec39-153">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Client AAD B2C** ).</span></span>
1. <span data-ttu-id="bec39-154">針對 **支援的帳戶類型** ，請選取 [多租使用者選項： **任何識別提供者或組織目錄中的帳戶]， (以使用使用者流程驗證使用者)**</span><span class="sxs-lookup"><span data-stu-id="bec39-154">For **Supported account types** , select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="bec39-155">將 [重新 **導向 URI** ] 下拉式清單保持設定為 [ **Web** ]，並提供下列重新導向 uri： `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="bec39-155">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="bec39-156">在 Kestrel 上執行應用程式的預設連接埠是 5001。</span><span class="sxs-lookup"><span data-stu-id="bec39-156">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="bec39-157">如果應用程式是在不同的 Kestrel 埠上執行，請使用應用程式的埠。</span><span class="sxs-lookup"><span data-stu-id="bec39-157">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="bec39-158">針對 IIS Express，可在 [ *`Server`* **調試** 程式] 面板的應用程式屬性中找到應用程式隨機產生的埠。</span><span class="sxs-lookup"><span data-stu-id="bec39-158">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="bec39-159">由於應用程式目前不存在，且 IIS Express 埠未知，因此在建立應用程式之後，請返回此步驟，並更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="bec39-159">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="bec39-160">批註會出現在 [ [建立應用程式](#create-the-app) ] 區段中，以提醒 IIS Express 使用者更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="bec39-160">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="bec39-161">確認 **Permissions** > 已選取 [將系統 **管理員同意授與 openid] 和 [offline_access] 許可權** 。</span><span class="sxs-lookup"><span data-stu-id="bec39-161">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="bec39-162">選取 [註冊]。</span><span class="sxs-lookup"><span data-stu-id="bec39-162">Select **Register** .</span></span>

<span data-ttu-id="bec39-163">記錄應用程式 (用戶端) 識別碼 (例如 `4369008b-21fa-427c-abaa-9b53bf58e538`) 。</span><span class="sxs-lookup"><span data-stu-id="bec39-163">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="bec39-164">在 [ **驗證** > **平臺** 設定] > **Web** ：</span><span class="sxs-lookup"><span data-stu-id="bec39-164">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="bec39-165">確認的重新 **導向 URI** `https://localhost:{PORT}/authentication/login-callback` 存在。</span><span class="sxs-lookup"><span data-stu-id="bec39-165">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="bec39-166">針對 **[隱含授** 與]，選取 **存取權杖** 和 **識別碼權杖** 的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="bec39-166">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens** .</span></span>
1. <span data-ttu-id="bec39-167">此體驗可接受應用程式的其餘預設值。</span><span class="sxs-lookup"><span data-stu-id="bec39-167">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="bec39-168">選取 [儲存] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="bec39-168">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="bec39-169">在 **API 許可權** 中：</span><span class="sxs-lookup"><span data-stu-id="bec39-169">In **API permissions** :</span></span>

1. <span data-ttu-id="bec39-170">選取 [ **新增] 許可權** ，然後選取 [我的 **api** ]。</span><span class="sxs-lookup"><span data-stu-id="bec39-170">Select **Add a permission** followed by **My APIs** .</span></span>
1. <span data-ttu-id="bec39-171">從 [ **名稱** ] 資料行中選取 *伺服器 API 應用程式* (例如 **:::no-loc(Blazor Server)::: AAD B2C** ) 。</span><span class="sxs-lookup"><span data-stu-id="bec39-171">Select the *Server API app* from the **Name** column (for example, **:::no-loc(Blazor Server)::: AAD B2C** ).</span></span>
1. <span data-ttu-id="bec39-172">開啟 **API** 清單。</span><span class="sxs-lookup"><span data-stu-id="bec39-172">Open the **API** list.</span></span>
1. <span data-ttu-id="bec39-173">啟用對 API 的存取 (例如 `API.Access`) 。</span><span class="sxs-lookup"><span data-stu-id="bec39-173">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="bec39-174">選取 [新增權限]。</span><span class="sxs-lookup"><span data-stu-id="bec39-174">Select **Add permissions** .</span></span>
1. <span data-ttu-id="bec39-175">選取 [ **授與系統管理員同意 {租使用者名稱}** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="bec39-175">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="bec39-176">選取 [是] 以確認。</span><span class="sxs-lookup"><span data-stu-id="bec39-176">Select **Yes** to confirm.</span></span>

<span data-ttu-id="bec39-177">在 **Home**  >  **Azure AD B2C**  >  **使用者流程** ：</span><span class="sxs-lookup"><span data-stu-id="bec39-177">In **Home** > **Azure AD B2C** > **User flows** :</span></span>

[<span data-ttu-id="bec39-178">建立註冊和登入使用者流程</span><span class="sxs-lookup"><span data-stu-id="bec39-178">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="bec39-179">至少選取 [ **應用程式宣告**  >  **顯示名稱** ] 使用者屬性，以填入 `context.User.:::no-loc(Identity):::.Name` `LoginDisplay` 元件 () 中的 `Shared/LoginDisplay.razor` 。</span><span class="sxs-lookup"><span data-stu-id="bec39-179">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.:::no-loc(Identity):::.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="bec39-180">記錄為應用程式建立的註冊和登入使用者流程名稱 (例如 `B2C_1_signupsignin`) 。</span><span class="sxs-lookup"><span data-stu-id="bec39-180">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="bec39-181">建立應用程式</span><span class="sxs-lookup"><span data-stu-id="bec39-181">Create the app</span></span>

<span data-ttu-id="bec39-182">將下列命令中的預留位置取代為稍早記錄的資訊，然後在命令 shell 中執行命令：</span><span class="sxs-lookup"><span data-stu-id="bec39-182">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="bec39-183">預留位置</span><span class="sxs-lookup"><span data-stu-id="bec39-183">Placeholder</span></span>                   | <span data-ttu-id="bec39-184">Azure 入口網站名稱</span><span class="sxs-lookup"><span data-stu-id="bec39-184">Azure portal name</span></span>                                     | <span data-ttu-id="bec39-185">範例</span><span class="sxs-lookup"><span data-stu-id="bec39-185">Example</span></span>                                      |
| ----------------------------- | ----------------------------------------------------- | -------------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="bec39-186">執行個體</span><span class="sxs-lookup"><span data-stu-id="bec39-186">Instance</span></span>                                              | `https://contoso.b2clogin.com/`              |
| `{APP NAME}`                  | &mdash;                                               | `:::no-loc(Blazor):::Sample`                               |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="bec39-187">應用程式 (用戶端) 的應用程式識別碼 *`Client`*</span><span class="sxs-lookup"><span data-stu-id="bec39-187">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`       |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="bec39-188">範圍名稱</span><span class="sxs-lookup"><span data-stu-id="bec39-188">Scope name</span></span>                                            | `API.Access`                                 |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="bec39-189">*伺服器 API 應用* 程式 (用戶端) 識別碼</span><span class="sxs-lookup"><span data-stu-id="bec39-189">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`       |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="bec39-190">應用程式識別碼 URI</span><span class="sxs-lookup"><span data-stu-id="bec39-190">Application ID URI</span></span>                                    | `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="bec39-191">註冊/登入使用者流程</span><span class="sxs-lookup"><span data-stu-id="bec39-191">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                        |
| `{TENANT DOMAIN}`             | <span data-ttu-id="bec39-192">主要/發行者/租使用者網域</span><span class="sxs-lookup"><span data-stu-id="bec39-192">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                    |

<span data-ttu-id="bec39-193">使用 `-o|--output` 選項指定的輸出位置會建立專案資料夾 (如果不存在)，並成為應用程式名稱的一部分。</span><span class="sxs-lookup"><span data-stu-id="bec39-193">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="bec39-194">裝載範本所設定的範圍 :::no-loc(Blazor)::: 可能會有重複的應用程式識別碼 URI 主機。</span><span class="sxs-lookup"><span data-stu-id="bec39-194">The scope set up by the Hosted :::no-loc(Blazor)::: template might have the App ID URI host repeated.</span></span> <span data-ttu-id="bec39-195">確認 `DefaultAccessTokenScopes` 在 `Program.Main` 應用程式的 () 中為集合設定的範圍正確 `Program.cs` *`Client`* 。</span><span class="sxs-lookup"><span data-stu-id="bec39-195">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *`Client`* app.</span></span>

> [!NOTE]
> <span data-ttu-id="bec39-196">在 Azure 入口網站中， *`Client`* 會針對使用預設設定在 Kestrel 伺服器上執行的應用程式，將應用程式的平臺設定重新 **導向 URI** 設定為使用埠5001。</span><span class="sxs-lookup"><span data-stu-id="bec39-196">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="bec39-197">如果 *`Client`* 應用程式是在隨機的 IIS Express 埠上執行，則可以在 [ **調試** 程式] 面板中的 *伺服器 API 應用程式* 屬性中找到應用程式的埠。</span><span class="sxs-lookup"><span data-stu-id="bec39-197">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="bec39-198">如果先前未使用 *`Client`* 應用程式的已知埠設定埠，請返回 *`Client`* Azure 入口網站中的應用程式註冊，然後以正確的埠更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="bec39-198">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="bec39-199">*`Server`* 應用程式設定</span><span class="sxs-lookup"><span data-stu-id="bec39-199">*`Server`* app configuration</span></span>

<span data-ttu-id="bec39-200">*本節適用于解決方案的 **`Server`** 應用程式。*</span><span class="sxs-lookup"><span data-stu-id="bec39-200">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="bec39-201">驗證套件</span><span class="sxs-lookup"><span data-stu-id="bec39-201">Authentication package</span></span>

<span data-ttu-id="bec39-202">對 ASP.NET Core Web Api 的驗證和授權呼叫支援是由 [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) 套件提供：</span><span class="sxs-lookup"><span data-stu-id="bec39-202">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="bec39-203">針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （ [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI)）中找到符合應用程式共用架構版本的最新穩定版本套件。</span><span class="sxs-lookup"><span data-stu-id="bec39-203">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="bec39-204">驗證服務支援</span><span class="sxs-lookup"><span data-stu-id="bec39-204">Authentication service support</span></span>

<span data-ttu-id="bec39-205">方法會在 `AddAuthentication` 應用程式中設定驗證服務，並將 JWT 持有人處理常式設定為預設驗證方法。</span><span class="sxs-lookup"><span data-stu-id="bec39-205">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="bec39-206"><xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A>方法會設定 JWT 持有人處理常式中的特定參數，以驗證 Azure Active Directory B2C 所發出的權杖：</span><span class="sxs-lookup"><span data-stu-id="bec39-206">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="bec39-207"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> 並 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> 確定：</span><span class="sxs-lookup"><span data-stu-id="bec39-207"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="bec39-208">應用程式會嘗試剖析並驗證傳入要求的權杖。</span><span class="sxs-lookup"><span data-stu-id="bec39-208">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="bec39-209">任何嘗試在沒有適當認證的情況下存取受保護資源的要求都會失敗。</span><span class="sxs-lookup"><span data-stu-id="bec39-209">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="bec39-210">使用者 ... :::no-loc(Identity):::名字</span><span class="sxs-lookup"><span data-stu-id="bec39-210">User.:::no-loc(Identity):::.Name</span></span>

<span data-ttu-id="bec39-211">依預設， `User.:::no-loc(Identity):::.Name` 不會填入。</span><span class="sxs-lookup"><span data-stu-id="bec39-211">By default, the `User.:::no-loc(Identity):::.Name` isn't populated.</span></span>

<span data-ttu-id="bec39-212">若要將應用程式設定為接收來自宣告 `name` 類型的值，請 <xref:Microsoft.:::no-loc(Identity):::Model.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> 在中設定 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="bec39-212">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.:::no-loc(Identity):::Model.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="bec39-213">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="bec39-213">App settings</span></span>

<span data-ttu-id="bec39-214">檔案 `:::no-loc(appsettings.json):::` 包含設定用來驗證存取權杖之 JWT 持有人處理常式的選項。</span><span class="sxs-lookup"><span data-stu-id="bec39-214">The `:::no-loc(appsettings.json):::` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

<span data-ttu-id="bec39-215">範例：</span><span class="sxs-lookup"><span data-stu-id="bec39-215">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="bec39-216">WeatherForecast 控制器</span><span class="sxs-lookup"><span data-stu-id="bec39-216">WeatherForecast controller</span></span>

<span data-ttu-id="bec39-217">WeatherForecast 控制器 ( *控制器/WeatherForecastController* ) 會公開受保護的 API，並將 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 屬性套用至控制器。</span><span class="sxs-lookup"><span data-stu-id="bec39-217">The WeatherForecast controller ( *Controllers/WeatherForecastController.cs* ) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="bec39-218">請 **務必** 瞭解：</span><span class="sxs-lookup"><span data-stu-id="bec39-218">It's **important** to understand that:</span></span>

* <span data-ttu-id="bec39-219">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)此 api 控制器中的屬性是保護此 api 不會遭到未經授權存取的唯一做法。</span><span class="sxs-lookup"><span data-stu-id="bec39-219">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="bec39-220">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)應用程式中使用的屬性 :::no-loc(Blazor WebAssembly)::: 僅做為應用程式的提示，使用者應獲得授權才能讓應用程式正常運作。</span><span class="sxs-lookup"><span data-stu-id="bec39-220">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the :::no-loc(Blazor WebAssembly)::: app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="bec39-221">*`Client`* 應用程式設定</span><span class="sxs-lookup"><span data-stu-id="bec39-221">*`Client`* app configuration</span></span>

<span data-ttu-id="bec39-222">*本節適用于解決方案的 **`Client`** 應用程式。*</span><span class="sxs-lookup"><span data-stu-id="bec39-222">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="bec39-223">驗證套件</span><span class="sxs-lookup"><span data-stu-id="bec39-223">Authentication package</span></span>

<span data-ttu-id="bec39-224">建立應用程式以使用個別 B2C 帳戶 (`IndividualB2C`) 時，應用程式會自動收到 [Microsoft 驗證程式庫](/azure/active-directory/develop/msal-overview) 的套件參考 ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) 。</span><span class="sxs-lookup"><span data-stu-id="bec39-224">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="bec39-225">此套件提供一組基本類型，可協助應用程式驗證使用者，並取得權杖以呼叫受保護的 Api。</span><span class="sxs-lookup"><span data-stu-id="bec39-225">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="bec39-226">如果將驗證新增至應用程式，請手動將套件新增到應用程式的專案檔：</span><span class="sxs-lookup"><span data-stu-id="bec39-226">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="bec39-227">針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （ [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)）中找到符合應用程式共用架構版本的最新穩定版本套件。</span><span class="sxs-lookup"><span data-stu-id="bec39-227">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="bec39-228">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)封裝可傳遞將 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 套件新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="bec39-228">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="bec39-229">驗證服務支援</span><span class="sxs-lookup"><span data-stu-id="bec39-229">Authentication service support</span></span>

<span data-ttu-id="bec39-230">在對 <xref:System.Net.Http.HttpClient> 伺服器專案提出要求時，會加入包含存取權杖的實例支援。</span><span class="sxs-lookup"><span data-stu-id="bec39-230">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="bec39-231">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="bec39-231">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="bec39-232">預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如 `:::no-loc(Blazor):::Sample.ServerAPI`) 。</span><span class="sxs-lookup"><span data-stu-id="bec39-232">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `:::no-loc(Blazor):::Sample.ServerAPI`).</span></span>

<span data-ttu-id="bec39-233">驗證使用者的支援是在服務容器中註冊，並具有 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 由封裝提供的擴充方法 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 。</span><span class="sxs-lookup"><span data-stu-id="bec39-233">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="bec39-234">此方法會設定應用程式 :::no-loc(Identity)::: (IP) 與提供者互動所需的服務。</span><span class="sxs-lookup"><span data-stu-id="bec39-234">This method sets up the services required for the app to interact with the :::no-loc(Identity)::: Provider (IP).</span></span>

<span data-ttu-id="bec39-235">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="bec39-235">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="bec39-236"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>方法會接受回呼來設定驗證應用程式所需的參數。</span><span class="sxs-lookup"><span data-stu-id="bec39-236">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="bec39-237">當您註冊應用程式時，可以從 Azure 入口網站 AAD 設定取得設定應用程式所需的值。</span><span class="sxs-lookup"><span data-stu-id="bec39-237">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="bec39-238">設定是由檔案提供 `wwwroot/:::no-loc(appsettings.json):::` ：</span><span class="sxs-lookup"><span data-stu-id="bec39-238">Configuration is supplied by the `wwwroot/:::no-loc(appsettings.json):::` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="bec39-239">範例：</span><span class="sxs-lookup"><span data-stu-id="bec39-239">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="bec39-240">存取權杖範圍</span><span class="sxs-lookup"><span data-stu-id="bec39-240">Access token scopes</span></span>

<span data-ttu-id="bec39-241">預設存取權杖範圍代表存取權杖範圍的清單，這些是：</span><span class="sxs-lookup"><span data-stu-id="bec39-241">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="bec39-242">預設包含在登入要求中。</span><span class="sxs-lookup"><span data-stu-id="bec39-242">Included by default in the sign in request.</span></span>
* <span data-ttu-id="bec39-243">用來在驗證之後立即布建存取權杖。</span><span class="sxs-lookup"><span data-stu-id="bec39-243">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="bec39-244">所有範圍都必須屬於相同的應用程式（依 Azure Active Directory 規則）。</span><span class="sxs-lookup"><span data-stu-id="bec39-244">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="bec39-245">您可以視需要為其他 API 應用程式新增其他範圍：</span><span class="sxs-lookup"><span data-stu-id="bec39-245">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="bec39-246">使用下列內容指定其他範圍 `AdditionalScopesToConsent` ：</span><span class="sxs-lookup"><span data-stu-id="bec39-246">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="bec39-247">如需詳細資訊，請參閱下列 *其他案例* 文章的各節：</span><span class="sxs-lookup"><span data-stu-id="bec39-247">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="bec39-248">要求其他存取權杖</span><span class="sxs-lookup"><span data-stu-id="bec39-248">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="bec39-249">將權杖附加至傳出要求</span><span class="sxs-lookup"><span data-stu-id="bec39-249">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="bec39-250">登入模式</span><span class="sxs-lookup"><span data-stu-id="bec39-250">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="bec39-251">匯入檔案</span><span class="sxs-lookup"><span data-stu-id="bec39-251">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="bec39-252">索引頁面</span><span class="sxs-lookup"><span data-stu-id="bec39-252">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="bec39-253">應用程式元件</span><span class="sxs-lookup"><span data-stu-id="bec39-253">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="bec39-254">RedirectToLogin 元件</span><span class="sxs-lookup"><span data-stu-id="bec39-254">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="bec39-255">LoginDisplay 元件</span><span class="sxs-lookup"><span data-stu-id="bec39-255">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="bec39-256">驗證元件</span><span class="sxs-lookup"><span data-stu-id="bec39-256">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="bec39-257">FetchData 元件</span><span class="sxs-lookup"><span data-stu-id="bec39-257">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="bec39-258">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="bec39-258">Run the app</span></span>

<span data-ttu-id="bec39-259">從伺服器專案執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="bec39-259">Run the app from the Server project.</span></span> <span data-ttu-id="bec39-260">使用 Visual Studio 時，您可以：</span><span class="sxs-lookup"><span data-stu-id="bec39-260">When using Visual Studio, either:</span></span>

* <span data-ttu-id="bec39-261">將工具列中的 [ **啟始專案** ] 下拉式清單設定為 *伺服器 API 應用程式* ，然後選取 [ **執行** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="bec39-261">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="bec39-262">在 **方案總管** 中選取伺服器專案，然後選取工具列中的 [ **執行** ] 按鈕，或從 [ **調試** 程式] 功能表啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="bec39-262">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="bec39-263">其他資源</span><span class="sxs-lookup"><span data-stu-id="bec39-263">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="bec39-264">具有安全預設用戶端之應用程式中未經驗證或未經授權的 web API 要求</span><span class="sxs-lookup"><span data-stu-id="bec39-264">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* <span data-ttu-id="bec39-265">[教學課程：建立 Azure Active Directory B2C 租用戶](/azure/active-directory-b2c/tutorial-create-tenant) \(部分機器翻譯\)</span><span class="sxs-lookup"><span data-stu-id="bec39-265">[Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant)</span></span>
* [<span data-ttu-id="bec39-266">Microsoft 身分識別平台文件</span><span class="sxs-lookup"><span data-stu-id="bec39-266">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
