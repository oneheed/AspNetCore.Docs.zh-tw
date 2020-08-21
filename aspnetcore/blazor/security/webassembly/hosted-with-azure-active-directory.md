---
title: Blazor WebAssembly使用 Azure Active Directory 保護 ASP.NET Core 託管應用程式
author: guardrex
description: 瞭解如何使用 Azure Active Directory 保護 ASP.NET Core Blazor WebAssembly 託管應用程式。
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 12a2509998bb9b4d56e250518b2db91f73dd0e67
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712411"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="89da7-103">Blazor WebAssembly使用 Azure Active Directory 保護 ASP.NET Core 託管應用程式</span><span class="sxs-lookup"><span data-stu-id="89da7-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="89da7-104">由 [Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="89da7-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="89da7-105">本文說明如何建立使用[Azure Active Directory (AAD) ](https://azure.microsoft.com/services/active-directory/)進行驗證的[託管 Blazor WebAssembly 應用程式](xref:blazor/hosting-models#blazor-webassembly)。</span><span class="sxs-lookup"><span data-stu-id="89da7-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="89da7-106">在 AAD 中註冊應用程式並建立解決方案</span><span class="sxs-lookup"><span data-stu-id="89da7-106">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="89da7-107">建立租用戶</span><span class="sxs-lookup"><span data-stu-id="89da7-107">Create a tenant</span></span>

<span data-ttu-id="89da7-108">遵循快速入門中的指導方針 [：設定租](/azure/active-directory/develop/quickstart-create-new-tenant) 使用者以在 AAD 中建立租使用者。</span><span class="sxs-lookup"><span data-stu-id="89da7-108">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="89da7-109">註冊伺服器 API 應用程式</span><span class="sxs-lookup"><span data-stu-id="89da7-109">Register a server API app</span></span>

<span data-ttu-id="89da7-110">遵循快速入門中的指導方針 [：使用 Microsoft 身分識別平臺註冊應用程式](/azure/active-directory/develop/quickstart-register-app) ，以及後續的 Azure AAD 主題以註冊 *伺服器 API 應用* 程式的 AAD 應用程式，然後執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="89da7-110">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="89da7-111">在**Azure Active Directory**  >  **應用程式註冊**中，選取 [**新增註冊**]。</span><span class="sxs-lookup"><span data-stu-id="89da7-111">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="89da7-112">提供應用程式的**名稱** (例如， \*\* Blazor Server AAD\*\*) 。</span><span class="sxs-lookup"><span data-stu-id="89da7-112">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="89da7-113">選擇 **支援的帳戶類型**。</span><span class="sxs-lookup"><span data-stu-id="89da7-113">Choose a **Supported account types**.</span></span> <span data-ttu-id="89da7-114">您可以選取 **此組織目錄中的帳戶，只** (此體驗的單一租使用者) 。</span><span class="sxs-lookup"><span data-stu-id="89da7-114">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="89da7-115">在此案例中， *伺服器 API 應用程式* 不需要重新 **導向 uri** ，因此請將下拉式清單保持設定為 [ **Web** ]，而不要輸入重新導向 uri。</span><span class="sxs-lookup"><span data-stu-id="89da7-115">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="89da7-116">停用 **[授與系統**  >  **管理員同意 openid 和 offline_access 許可權**] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="89da7-116">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="89da7-117">選取 [註冊]。</span><span class="sxs-lookup"><span data-stu-id="89da7-117">Select **Register**.</span></span>

<span data-ttu-id="89da7-118">記錄下列資訊：</span><span class="sxs-lookup"><span data-stu-id="89da7-118">Record the following information:</span></span>

* <span data-ttu-id="89da7-119">*伺服器 API 應用程式* 應用程式 (用戶端) 識別碼 (例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`) </span><span class="sxs-lookup"><span data-stu-id="89da7-119">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="89da7-120">目錄 (租使用者) 識別碼 (例如 `e86c78e2-8bb4-4c41-aefd-918e0565a45e`) </span><span class="sxs-lookup"><span data-stu-id="89da7-120">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="89da7-121">AAD 主要/發行者/租使用者網域 (例如 `contoso.onmicrosoft.com`) ：網域可作為已註冊應用程式之 Azure 入口網站的 [**商標**] 分頁中的**發行者網域**。</span><span class="sxs-lookup"><span data-stu-id="89da7-121">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="89da7-122">在 [ **API 許可權**] 中，移除**Microsoft Graph**的  >  **使用者。讀取**許可權，因為應用程式不需要登入或使用者設定檔存取權。</span><span class="sxs-lookup"><span data-stu-id="89da7-122">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="89da7-123">在中 **公開 API**：</span><span class="sxs-lookup"><span data-stu-id="89da7-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="89da7-124">選取 [新增範圍]  。</span><span class="sxs-lookup"><span data-stu-id="89da7-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="89da7-125">選取 [儲存並繼續]  。</span><span class="sxs-lookup"><span data-stu-id="89da7-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="89da7-126">提供 **範圍名稱** (例如 `API.Access`) 。</span><span class="sxs-lookup"><span data-stu-id="89da7-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="89da7-127">提供系統 **管理員同意顯示名稱** (例如 `Access API`) 。</span><span class="sxs-lookup"><span data-stu-id="89da7-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="89da7-128">提供 **管理員同意描述** (例如 `Allows the app to access server app API endpoints.`) 。</span><span class="sxs-lookup"><span data-stu-id="89da7-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="89da7-129">確認 **狀態** 設定為 [ **已啟用**]。</span><span class="sxs-lookup"><span data-stu-id="89da7-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="89da7-130">選取 [新增範圍]。</span><span class="sxs-lookup"><span data-stu-id="89da7-130">Select **Add scope**.</span></span>

<span data-ttu-id="89da7-131">記錄下列資訊：</span><span class="sxs-lookup"><span data-stu-id="89da7-131">Record the following information:</span></span>

* <span data-ttu-id="89da7-132">應用程式識別碼 URI (例如， `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` 、 `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` 或您提供的自訂值) </span><span class="sxs-lookup"><span data-stu-id="89da7-132">App ID URI (for example, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="89da7-133">範圍名稱 (例如， `API.Access`) </span><span class="sxs-lookup"><span data-stu-id="89da7-133">Scope name (for example, `API.Access`)</span></span>

<span data-ttu-id="89da7-134">應用程式識別碼 URI 可能需要用戶端應用程式中的特殊設定，如本主題稍後的 [存取權杖範圍](#access-token-scopes) 一節中所述。</span><span class="sxs-lookup"><span data-stu-id="89da7-134">The App ID URI might require a special configuration in the client app, which is described in the [Access token scopes](#access-token-scopes) section later in this topic.</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="89da7-135">註冊用戶端應用程式</span><span class="sxs-lookup"><span data-stu-id="89da7-135">Register a client app</span></span>

<span data-ttu-id="89da7-136">遵循快速入門中的指導方針 [：使用 Microsoft 身分識別平臺註冊應用程式](/azure/active-directory/develop/quickstart-register-app) ，以及後續的 Azure AAD 主題以註冊 *用戶端應用* 程式的 AAD 應用程式，然後執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="89da7-136">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="89da7-137">在**Azure Active Directory**  >  **應用程式註冊**中，選取 [**新增註冊**]。</span><span class="sxs-lookup"><span data-stu-id="89da7-137">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="89da7-138">提供應用程式的**名稱** (例如， \*\* Blazor 用戶端 AAD\*\*) 。</span><span class="sxs-lookup"><span data-stu-id="89da7-138">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="89da7-139">選擇 **支援的帳戶類型**。</span><span class="sxs-lookup"><span data-stu-id="89da7-139">Choose a **Supported account types**.</span></span> <span data-ttu-id="89da7-140">您可以選取 **此組織目錄中的帳戶，只** (此體驗的單一租使用者) 。</span><span class="sxs-lookup"><span data-stu-id="89da7-140">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="89da7-141">將 [重新 **導向 URI** ] 下拉式清單保持設定為 [ **Web** ]，並提供下列重新導向 uri： `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="89da7-141">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="89da7-142">在 Kestrel 上執行之應用程式的預設埠是5001。</span><span class="sxs-lookup"><span data-stu-id="89da7-142">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="89da7-143">如果應用程式是在不同的 Kestrel 埠上執行，請使用應用程式的埠。</span><span class="sxs-lookup"><span data-stu-id="89da7-143">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="89da7-144">針對 IIS Express，可在 [ **調試** 程式] 面板的伺服器應用程式屬性中找到應用程式隨機產生的埠。</span><span class="sxs-lookup"><span data-stu-id="89da7-144">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="89da7-145">由於應用程式目前不存在，且 IIS Express 埠未知，因此在建立應用程式之後，請返回此步驟，並更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="89da7-145">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="89da7-146">批註會出現在 [ [建立應用程式](#create-the-app) ] 區段中，以提醒 IIS Express 使用者更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="89da7-146">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="89da7-147">停用 **[授與系統**  >  **管理員同意 openid 和 offline_access 許可權**] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="89da7-147">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="89da7-148">選取 [註冊]。</span><span class="sxs-lookup"><span data-stu-id="89da7-148">Select **Register**.</span></span>

<span data-ttu-id="89da7-149">記錄 *用戶端應用* 程式應用程式 (用戶端) 識別碼 (例如 `4369008b-21fa-427c-abaa-9b53bf58e538`) 。</span><span class="sxs-lookup"><span data-stu-id="89da7-149">Record the *Client app* Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="89da7-150">在 [**驗證**  >  **平臺**設定]  >  **Web**：</span><span class="sxs-lookup"><span data-stu-id="89da7-150">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="89da7-151">確認的重新 **導向 URI** `https://localhost:{PORT}/authentication/login-callback` 存在。</span><span class="sxs-lookup"><span data-stu-id="89da7-151">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="89da7-152">針對 **[隱含授**與]，選取 **存取權杖** 和 **識別碼權杖**的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="89da7-152">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="89da7-153">此體驗可接受應用程式的其餘預設值。</span><span class="sxs-lookup"><span data-stu-id="89da7-153">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="89da7-154">選取 [儲存] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="89da7-154">Select the **Save** button.</span></span>

<span data-ttu-id="89da7-155">在 **API 許可權**中：</span><span class="sxs-lookup"><span data-stu-id="89da7-155">In **API permissions**:</span></span>

1. <span data-ttu-id="89da7-156">確認應用程式已**Microsoft Graph**  >  **使用者。讀取**許可權。</span><span class="sxs-lookup"><span data-stu-id="89da7-156">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="89da7-157">選取 [ **新增] 許可權** ，然後選取 [我的 **api**]。</span><span class="sxs-lookup"><span data-stu-id="89da7-157">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="89da7-158">從 [**名稱**] 資料行中選取*伺服器 API 應用程式* (例如， \*\* Blazor Server AAD\*\*) 。</span><span class="sxs-lookup"><span data-stu-id="89da7-158">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="89da7-159">開啟 **API** 清單。</span><span class="sxs-lookup"><span data-stu-id="89da7-159">Open the **API** list.</span></span>
1. <span data-ttu-id="89da7-160">啟用對 API 的存取 (例如 `API.Access`) 。</span><span class="sxs-lookup"><span data-stu-id="89da7-160">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="89da7-161">選取 [新增權限]。</span><span class="sxs-lookup"><span data-stu-id="89da7-161">Select **Add permissions**.</span></span>
1. <span data-ttu-id="89da7-162">選取 [ **授與系統管理員同意 {租使用者名稱}** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="89da7-162">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="89da7-163">選取 [是] 以確認。</span><span class="sxs-lookup"><span data-stu-id="89da7-163">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="89da7-164">建立應用程式</span><span class="sxs-lookup"><span data-stu-id="89da7-164">Create the app</span></span>

<span data-ttu-id="89da7-165">在空的資料夾中，將下列命令中的預留位置取代為先前記錄的資訊，然後在命令 shell 中執行命令：</span><span class="sxs-lookup"><span data-stu-id="89da7-165">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="89da7-166">預留位置</span><span class="sxs-lookup"><span data-stu-id="89da7-166">Placeholder</span></span>                  | <span data-ttu-id="89da7-167">Azure 入口網站名稱</span><span class="sxs-lookup"><span data-stu-id="89da7-167">Azure portal name</span></span>                                     | <span data-ttu-id="89da7-168">範例</span><span class="sxs-lookup"><span data-stu-id="89da7-168">Example</span></span>                                |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="89da7-169">*用戶端*應用程式 (用戶端) 識別碼的應用程式</span><span class="sxs-lookup"><span data-stu-id="89da7-169">Application (client) ID for the *Client app*</span></span>          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="89da7-170">領域名稱</span><span class="sxs-lookup"><span data-stu-id="89da7-170">Scope name</span></span>                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="89da7-171">*伺服器 API 應用*程式 (用戶端) 識別碼</span><span class="sxs-lookup"><span data-stu-id="89da7-171">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="89da7-172">應用程式識別碼 URI ([請參閱附注](#access-token-scopes)) </span><span class="sxs-lookup"><span data-stu-id="89da7-172">Application ID URI ([see note](#access-token-scopes))</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | <span data-ttu-id="89da7-173">主要/發行者/租使用者網域</span><span class="sxs-lookup"><span data-stu-id="89da7-173">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`              |
| `{TENANT ID}`                | <span data-ttu-id="89da7-174">目錄 (租用戶) 識別碼</span><span class="sxs-lookup"><span data-stu-id="89da7-174">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="89da7-175">使用選項指定的輸出位置會 `-o|--output` 建立專案資料夾（如果不存在），而且會成為應用程式名稱的一部分。</span><span class="sxs-lookup"><span data-stu-id="89da7-175">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="89da7-176">將應用程式識別碼 URI 傳遞給 `app-id-uri` 選項，但請注意，用戶端應用程式中可能需要進行設定變更，如 [存取權杖範圍](#access-token-scopes) 一節中所述。</span><span class="sxs-lookup"><span data-stu-id="89da7-176">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

> [!NOTE]
> <span data-ttu-id="89da7-177">在 Azure 入口網站中，*用戶端應用程式的\*\*\*驗證*\*  >  **平臺**  >  設定**Web**重新  >  **導向 URI**是針對使用預設設定在 Kestrel 伺服器上執行的應用程式所設定的埠5001。</span><span class="sxs-lookup"><span data-stu-id="89da7-177">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="89da7-178">如果*用戶端應用程式*是在隨機的 IIS Express 埠上執行，則可以在 [**調試**程式] 面板中的*伺服器 API 應用程式*屬性中找到應用程式的埠。</span><span class="sxs-lookup"><span data-stu-id="89da7-178">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="89da7-179">如果先前未使用 *用戶端應用程式的* 已知埠設定埠，請返回 Azure 入口網站中的 *用戶端應用程式* 註冊，然後以正確的埠更新重新導向 URI。</span><span class="sxs-lookup"><span data-stu-id="89da7-179">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="89da7-180">伺服器應用程式設定</span><span class="sxs-lookup"><span data-stu-id="89da7-180">Server app configuration</span></span>

<span data-ttu-id="89da7-181">*本節適用于解決方案的 **`Server`** 應用程式。*</span><span class="sxs-lookup"><span data-stu-id="89da7-181">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="89da7-182">驗證套件</span><span class="sxs-lookup"><span data-stu-id="89da7-182">Authentication package</span></span>

<span data-ttu-id="89da7-183">對 ASP.NET Core Web Api 的驗證和授權呼叫支援是由 [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) 套件提供：</span><span class="sxs-lookup"><span data-stu-id="89da7-183">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="89da7-184">針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （ [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI)）中找到符合應用程式共用架構版本的最新穩定版本套件。</span><span class="sxs-lookup"><span data-stu-id="89da7-184">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="89da7-185">驗證服務支援</span><span class="sxs-lookup"><span data-stu-id="89da7-185">Authentication service support</span></span>

<span data-ttu-id="89da7-186">方法會在 `AddAuthentication` 應用程式中設定驗證服務，並將 JWT 持有人處理常式設定為預設驗證方法。</span><span class="sxs-lookup"><span data-stu-id="89da7-186">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="89da7-187"><xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A>方法會設定 JWT 持有人處理常式中的特定參數，以驗證 Azure Active Directory 所發出的權杖：</span><span class="sxs-lookup"><span data-stu-id="89da7-187">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="89da7-188"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> 並 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> 確定：</span><span class="sxs-lookup"><span data-stu-id="89da7-188"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="89da7-189">應用程式會嘗試剖析並驗證傳入要求的權杖。</span><span class="sxs-lookup"><span data-stu-id="89da7-189">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="89da7-190">任何嘗試在沒有適當認證的情況下存取受保護資源的要求都會失敗。</span><span class="sxs-lookup"><span data-stu-id="89da7-190">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="89da7-191">使用者 ... Identity名字</span><span class="sxs-lookup"><span data-stu-id="89da7-191">User.Identity.Name</span></span>

<span data-ttu-id="89da7-192">根據預設，伺服器應用程式 API 會 `User.Identity.Name` 以宣告類型的值填入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` (例如 `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`) 。</span><span class="sxs-lookup"><span data-stu-id="89da7-192">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="89da7-193">若要將應用程式設定為接收來自宣告 `name` 類型的值，請 <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> 在中設定 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="89da7-193">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="89da7-194">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="89da7-194">App settings</span></span>

<span data-ttu-id="89da7-195">檔案 `appsettings.json` 包含設定用來驗證存取權杖之 JWT 持有人處理常式的選項：</span><span class="sxs-lookup"><span data-stu-id="89da7-195">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="89da7-196">範例：</span><span class="sxs-lookup"><span data-stu-id="89da7-196">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="89da7-197">WeatherForecast 控制器</span><span class="sxs-lookup"><span data-stu-id="89da7-197">WeatherForecast controller</span></span>

<span data-ttu-id="89da7-198">WeatherForecast 控制器 (*控制器/WeatherForecastController*) 會公開受保護的 API，並將 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 屬性套用至控制器。</span><span class="sxs-lookup"><span data-stu-id="89da7-198">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="89da7-199">請 **務必** 瞭解：</span><span class="sxs-lookup"><span data-stu-id="89da7-199">It's **important** to understand that:</span></span>

* <span data-ttu-id="89da7-200">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)此 api 控制器中的屬性是保護此 api 不會遭到未經授權存取的唯一做法。</span><span class="sxs-lookup"><span data-stu-id="89da7-200">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="89da7-201">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)應用程式中使用的屬性 Blazor WebAssembly 僅做為應用程式的提示，使用者應獲得授權才能讓應用程式正常運作。</span><span class="sxs-lookup"><span data-stu-id="89da7-201">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="89da7-202">用戶端應用程式設定</span><span class="sxs-lookup"><span data-stu-id="89da7-202">Client app configuration</span></span>

<span data-ttu-id="89da7-203">*本節適用于解決方案的 **`Client`** 應用程式。*</span><span class="sxs-lookup"><span data-stu-id="89da7-203">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="89da7-204">驗證套件</span><span class="sxs-lookup"><span data-stu-id="89da7-204">Authentication package</span></span>

<span data-ttu-id="89da7-205">建立應用程式以使用 () 的公司或學校帳戶時 `SingleOrg` ，應用程式會自動收到 [Microsoft 驗證程式庫](/azure/active-directory/develop/msal-overview) 的套件參考 ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) 。</span><span class="sxs-lookup"><span data-stu-id="89da7-205">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="89da7-206">此套件提供一組基本類型，可協助應用程式驗證使用者，並取得權杖以呼叫受保護的 Api。</span><span class="sxs-lookup"><span data-stu-id="89da7-206">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="89da7-207">如果將驗證新增至應用程式，請手動將套件新增到應用程式的專案檔：</span><span class="sxs-lookup"><span data-stu-id="89da7-207">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="89da7-208">針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （ [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)）中找到符合應用程式共用架構版本的最新穩定版本套件。</span><span class="sxs-lookup"><span data-stu-id="89da7-208">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="89da7-209">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)封裝可傳遞將 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 套件新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="89da7-209">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="89da7-210">驗證服務支援</span><span class="sxs-lookup"><span data-stu-id="89da7-210">Authentication service support</span></span>

<span data-ttu-id="89da7-211">在對 <xref:System.Net.Http.HttpClient> 伺服器專案提出要求時，會加入包含存取權杖的實例支援。</span><span class="sxs-lookup"><span data-stu-id="89da7-211">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="89da7-212">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="89da7-212">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="89da7-213">預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如 `BlazorSample.ServerAPI`) 。</span><span class="sxs-lookup"><span data-stu-id="89da7-213">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="89da7-214">驗證使用者的支援是在服務容器中註冊，並具有 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 由封裝提供的擴充方法 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 。</span><span class="sxs-lookup"><span data-stu-id="89da7-214">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="89da7-215">此方法會設定應用程式 Identity (IP) 與提供者互動所需的服務。</span><span class="sxs-lookup"><span data-stu-id="89da7-215">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="89da7-216">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="89da7-216">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="89da7-217"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>方法會接受回呼來設定驗證應用程式所需的參數。</span><span class="sxs-lookup"><span data-stu-id="89da7-217">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="89da7-218">當您註冊應用程式時，可以從 Azure 入口網站 AAD 設定取得設定應用程式所需的值。</span><span class="sxs-lookup"><span data-stu-id="89da7-218">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="89da7-219">設定是由檔案提供 `wwwroot/appsettings.json` ：</span><span class="sxs-lookup"><span data-stu-id="89da7-219">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="89da7-220">範例：</span><span class="sxs-lookup"><span data-stu-id="89da7-220">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="89da7-221">存取權杖範圍</span><span class="sxs-lookup"><span data-stu-id="89da7-221">Access token scopes</span></span>

<span data-ttu-id="89da7-222">預設存取權杖範圍代表存取權杖範圍的清單，這些是：</span><span class="sxs-lookup"><span data-stu-id="89da7-222">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="89da7-223">預設包含在登入要求中。</span><span class="sxs-lookup"><span data-stu-id="89da7-223">Included by default in the sign in request.</span></span>
* <span data-ttu-id="89da7-224">用來在驗證之後立即布建存取權杖。</span><span class="sxs-lookup"><span data-stu-id="89da7-224">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="89da7-225">所有範圍都必須屬於相同的應用程式（依 Azure Active Directory 規則）。</span><span class="sxs-lookup"><span data-stu-id="89da7-225">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="89da7-226">您可以視需要為其他 API 應用程式新增其他範圍：</span><span class="sxs-lookup"><span data-stu-id="89da7-226">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="89da7-227">如需詳細資訊，請參閱下列 *其他案例* 文章的各節：</span><span class="sxs-lookup"><span data-stu-id="89da7-227">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="89da7-228">要求其他存取權杖</span><span class="sxs-lookup"><span data-stu-id="89da7-228">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="89da7-229">將權杖附加至傳出要求</span><span class="sxs-lookup"><span data-stu-id="89da7-229">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="89da7-230">登入模式</span><span class="sxs-lookup"><span data-stu-id="89da7-230">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="89da7-231">匯入檔案</span><span class="sxs-lookup"><span data-stu-id="89da7-231">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="89da7-232">索引頁面</span><span class="sxs-lookup"><span data-stu-id="89da7-232">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="89da7-233">應用程式元件</span><span class="sxs-lookup"><span data-stu-id="89da7-233">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="89da7-234">RedirectToLogin 元件</span><span class="sxs-lookup"><span data-stu-id="89da7-234">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="89da7-235">LoginDisplay 元件</span><span class="sxs-lookup"><span data-stu-id="89da7-235">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="89da7-236">驗證元件</span><span class="sxs-lookup"><span data-stu-id="89da7-236">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="89da7-237">FetchData 元件</span><span class="sxs-lookup"><span data-stu-id="89da7-237">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="89da7-238">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="89da7-238">Run the app</span></span>

<span data-ttu-id="89da7-239">從伺服器專案執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="89da7-239">Run the app from the Server project.</span></span> <span data-ttu-id="89da7-240">使用 Visual Studio 時，您可以：</span><span class="sxs-lookup"><span data-stu-id="89da7-240">When using Visual Studio, either:</span></span>

* <span data-ttu-id="89da7-241">將工具列中的 [ **啟始專案** ] 下拉式清單設定為 *伺服器 API 應用程式* ，然後選取 [ **執行** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="89da7-241">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="89da7-242">在 **方案總管** 中選取伺服器專案，然後選取工具列中的 [ **執行** ] 按鈕，或從 [ **調試** 程式] 功能表啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="89da7-242">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="89da7-243">其他資源</span><span class="sxs-lookup"><span data-stu-id="89da7-243">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="89da7-244">具有安全預設用戶端之應用程式中未經驗證或未經授權的 web API 要求</span><span class="sxs-lookup"><span data-stu-id="89da7-244">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="89da7-245">Microsoft 身分識別平台文件</span><span class="sxs-lookup"><span data-stu-id="89da7-245">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
