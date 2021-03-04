---
title: ASP.NET Core 中的 Google 外部登入設定
author: rick-anderson
description: 本教學課程示範如何將 Google 帳戶使用者驗證整合至現有的 ASP.NET Core 應用程式。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/18/2021
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
uid: security/authentication/google-logins
ms.openlocfilehash: 181ce87e8085839e0fcc0d77010c588ef7a290b1
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110127"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="4a17d-103">ASP.NET Core 中的 Google 外部登入設定</span><span class="sxs-lookup"><span data-stu-id="4a17d-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="4a17d-104">作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4a17d-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4a17d-105">本教學課程說明如何讓使用者使用在 [前一頁](xref:security/authentication/social/index)建立的 ASP.NET Core 3.0 專案以其 Google 帳戶登入。</span><span class="sxs-lookup"><span data-stu-id="4a17d-105">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="4a17d-106">建立 Google API 主控台專案和用戶端識別碼</span><span class="sxs-lookup"><span data-stu-id="4a17d-106">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="4a17d-107">將 [AspNetCore 的 Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) NuGet 套件新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="4a17d-107">Add the [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) NuGet package to the app.</span></span>
* <span data-ttu-id="4a17d-108">遵循將 [google Sign-In 整合到您的 web 應用程式](https://developers.google.com/identity/sign-in/web/sign-in) 中的指引， (Google 檔) 。</span><span class="sxs-lookup"><span data-stu-id="4a17d-108">Follow the guidance in [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/sign-in) (Google documentation).</span></span>
* <span data-ttu-id="4a17d-109">在 [Google 主控台](https://console.developers.google.com/apis/credentials)的 [**認證**] 頁面中，選取 [**建立認證**  >  **OAuth 用戶端識別碼**]。</span><span class="sxs-lookup"><span data-stu-id="4a17d-109">In the **Credentials** page of the [Google console](https://console.developers.google.com/apis/credentials), select **CREATE CREDENTIALS** > **OAuth client ID**.</span></span>
* <span data-ttu-id="4a17d-110">在 [ **應用程式類型** ] 對話方塊中，選取 [ **Web 應用程式**]。</span><span class="sxs-lookup"><span data-stu-id="4a17d-110">In the **Application type** dialog, select **Web application**.</span></span> <span data-ttu-id="4a17d-111">提供應用程式的 **名稱** 。</span><span class="sxs-lookup"><span data-stu-id="4a17d-111">Provide a **Name** for the app.</span></span>
* <span data-ttu-id="4a17d-112">在 [ **授權重新導向 uri** ] 區段中，選取 [ **新增 uri** ] 來設定重新導向 uri。</span><span class="sxs-lookup"><span data-stu-id="4a17d-112">In the **Authorized redirect URIs** section, select **ADD URI** to set the redirect URI.</span></span> <span data-ttu-id="4a17d-113">重新導向 URI 範例： `https://localhost:{PORT}/signin-google` ，其中 `{PORT}` 預留位置為應用程式的埠。</span><span class="sxs-lookup"><span data-stu-id="4a17d-113">Example redirect URI: `https://localhost:{PORT}/signin-google`, where the `{PORT}` placeholder is the app's port.</span></span>
* <span data-ttu-id="4a17d-114">選取 [ **建立** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="4a17d-114">Select the **CREATE** button.</span></span>
* <span data-ttu-id="4a17d-115">儲存 **用戶端識別碼** 和 **用戶端密碼** ，以用於應用程式的設定。</span><span class="sxs-lookup"><span data-stu-id="4a17d-115">Save the **Client ID** and **Client Secret** for use in the app's configuration.</span></span>
* <span data-ttu-id="4a17d-116">部署網站時，您可以：</span><span class="sxs-lookup"><span data-stu-id="4a17d-116">When deploying the site, either:</span></span>
  * <span data-ttu-id="4a17d-117">將 **Google 主控台** 中的應用程式重新導向 uri 更新為應用程式部署的重新導向 uri。</span><span class="sxs-lookup"><span data-stu-id="4a17d-117">Update the app's redirect URI in the **Google Console** to the app's deployed redirect URI.</span></span>
  * <span data-ttu-id="4a17d-118">使用實際執行的重新導向 URI，在 **Google 主控台** 中為生產應用程式建立新的 google API 註冊。</span><span class="sxs-lookup"><span data-stu-id="4a17d-118">Create a new Google API registration in the **Google Console** for the production app with its production redirect URI.</span></span>

## <a name="store-the-google-client-id-and-secret"></a><span data-ttu-id="4a17d-119">儲存 Google 用戶端識別碼和密碼</span><span class="sxs-lookup"><span data-stu-id="4a17d-119">Store the Google client ID and secret</span></span>

<span data-ttu-id="4a17d-120">使用 [秘密管理員](xref:security/app-secrets)來儲存機密性設定，例如 Google 用戶端識別碼和秘密值。</span><span class="sxs-lookup"><span data-stu-id="4a17d-120">Store sensitive settings such as the Google client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="4a17d-121">針對此範例，請使用下列步驟：</span><span class="sxs-lookup"><span data-stu-id="4a17d-121">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="4a17d-122">根據 [啟用秘密儲存](xref:security/app-secrets#enable-secret-storage)的指示，為秘密儲存體初始化專案。</span><span class="sxs-lookup"><span data-stu-id="4a17d-122">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="4a17d-123">將機密設定儲存在具有秘密金鑰和的本機秘密存放區中 `Authentication:Google:ClientId` `Authentication:Google:ClientSecret` ：</span><span class="sxs-lookup"><span data-stu-id="4a17d-123">Store the sensitive settings in the local secret store with the secret keys `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="4a17d-124">您可以在 [Api 主控台](https://console.developers.google.com/apis/dashboard)中管理您的 api 認證和使用方式。</span><span class="sxs-lookup"><span data-stu-id="4a17d-124">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="4a17d-125">設定 Google 驗證</span><span class="sxs-lookup"><span data-stu-id="4a17d-125">Configure Google authentication</span></span>

<span data-ttu-id="4a17d-126">將 Google 服務新增至 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="4a17d-126">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="4a17d-127">以 Google 登入</span><span class="sxs-lookup"><span data-stu-id="4a17d-127">Sign in with Google</span></span>

* <span data-ttu-id="4a17d-128">執行應用程式，然後按一下 [ **登入**]。</span><span class="sxs-lookup"><span data-stu-id="4a17d-128">Run the app and click **Log in**.</span></span> <span data-ttu-id="4a17d-129">使用 Google 登入的選項隨即出現。</span><span class="sxs-lookup"><span data-stu-id="4a17d-129">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="4a17d-130">按一下 [ **google** ] 按鈕，這會重新導向至 google 進行驗證。</span><span class="sxs-lookup"><span data-stu-id="4a17d-130">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="4a17d-131">輸入您的 Google 認證之後，系統會將您重新導向回到網站。</span><span class="sxs-lookup"><span data-stu-id="4a17d-131">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="4a17d-132">如需 <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> Google 驗證所支援之設定選項的詳細資訊，請參閱 API 參考。</span><span class="sxs-lookup"><span data-stu-id="4a17d-132">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="4a17d-133">這可以用來要求使用者的不同資訊。</span><span class="sxs-lookup"><span data-stu-id="4a17d-133">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="4a17d-134">變更預設回呼 URI</span><span class="sxs-lookup"><span data-stu-id="4a17d-134">Change the default callback URI</span></span>

<span data-ttu-id="4a17d-135">URI 區段 `/signin-google` 會設定為 Google 驗證提供者的預設回呼。</span><span class="sxs-lookup"><span data-stu-id="4a17d-135">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="4a17d-136">您可以透過類別的繼承) 屬性來設定 Google 驗證中介軟體時，變更預設回呼 URI <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CallbackPath?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> 。</span><span class="sxs-lookup"><span data-stu-id="4a17d-136">You can change the default callback URI while configuring the Google authentication middleware via the inherited <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CallbackPath?displayProperty=nameWithType>) property of the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="4a17d-137">疑難排解</span><span class="sxs-lookup"><span data-stu-id="4a17d-137">Troubleshooting</span></span>

* <span data-ttu-id="4a17d-138">如果登入無法運作，而且您未收到任何錯誤，請切換到開發模式，讓問題更容易進行偵測。</span><span class="sxs-lookup"><span data-stu-id="4a17d-138">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="4a17d-139">如果 Identity 未透過呼叫來 `services.AddIdentity` 設定 `ConfigureServices` ，則嘗試在 ArgumentException 中驗證結果 *：必須提供 ' SignInScheme ' 選項*。</span><span class="sxs-lookup"><span data-stu-id="4a17d-139">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="4a17d-140">本教學課程中使用的專案範本可確保這項操作已完成。</span><span class="sxs-lookup"><span data-stu-id="4a17d-140">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="4a17d-141">如果未藉由套用初始遷移來建立網站資料庫，則在 *處理要求錯誤時* ，您會收到資料庫作業失敗。</span><span class="sxs-lookup"><span data-stu-id="4a17d-141">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="4a17d-142">選取 [套用 **遷移** ] 來建立資料庫，然後重新整理頁面以繼續發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="4a17d-142">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4a17d-143">下一步</span><span class="sxs-lookup"><span data-stu-id="4a17d-143">Next steps</span></span>

* <span data-ttu-id="4a17d-144">本文說明您可以如何向 Google 進行驗證。</span><span class="sxs-lookup"><span data-stu-id="4a17d-144">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="4a17d-145">您可以遵循類似的方法，向 [先前頁面](xref:security/authentication/social/index)中所列的其他提供者進行驗證。</span><span class="sxs-lookup"><span data-stu-id="4a17d-145">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="4a17d-146">當您將應用程式發佈至 Azure 之後，請 `ClientSecret` 在 GOOGLE API 主控台中重設。</span><span class="sxs-lookup"><span data-stu-id="4a17d-146">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="4a17d-147">`Authentication:Google:ClientId` `Authentication:Google:ClientSecret` 在 Azure 入口網站中，將和設定為應用程式設定。</span><span class="sxs-lookup"><span data-stu-id="4a17d-147">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="4a17d-148">設定系統已設定為從環境變數讀取金鑰。</span><span class="sxs-lookup"><span data-stu-id="4a17d-148">The configuration system is set up to read keys from environment variables.</span></span>
