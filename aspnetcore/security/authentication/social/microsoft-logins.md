---
title: 使用 ASP.NET Core 的 Microsoft 帳戶外部登入設定
author: rick-anderson
description: 此範例示範如何將 Microsoft 帳戶使用者驗證整合至現有的 ASP.NET Core 應用程式。
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
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
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 3430d842b6a4f7da30370977f72e6f132e28bb7f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634250"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="45377-103">使用 ASP.NET Core 的 Microsoft 帳戶外部登入設定</span><span class="sxs-lookup"><span data-stu-id="45377-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="45377-104">作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="45377-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="45377-105">這個範例會示範如何讓使用者使用在 [前一頁](xref:security/authentication/social/index)建立的 ASP.NET Core 3.0 專案來登入他們的工作、學校或個人 Microsoft 帳戶。</span><span class="sxs-lookup"><span data-stu-id="45377-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="45377-106">在 Microsoft 開發人員入口網站中建立應用程式</span><span class="sxs-lookup"><span data-stu-id="45377-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="45377-107">將 [AspNetCore MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet 套件新增至專案。</span><span class="sxs-lookup"><span data-stu-id="45377-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="45377-108">流覽至 [ [Azure 入口網站應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908) ] 頁面，並建立或登入 Microsoft 帳戶：</span><span class="sxs-lookup"><span data-stu-id="45377-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="45377-109">如果您沒有 Microsoft 帳戶，請選取 [ **建立一個**]。</span><span class="sxs-lookup"><span data-stu-id="45377-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="45377-110">登入之後，系統會將您重新導向至 **應用程式註冊** 頁面：</span><span class="sxs-lookup"><span data-stu-id="45377-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="45377-111">選取 **新的註冊**</span><span class="sxs-lookup"><span data-stu-id="45377-111">Select **New registration**</span></span>
* <span data-ttu-id="45377-112">輸入 [名稱]。</span><span class="sxs-lookup"><span data-stu-id="45377-112">Enter a **Name**.</span></span>
* <span data-ttu-id="45377-113">選取 **支援的帳戶類型**的選項。</span><span class="sxs-lookup"><span data-stu-id="45377-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* <span data-ttu-id="45377-114">在 [重新 **導向 URI**] 下，輸入您附加的開發 URL `/signin-microsoft` 。</span><span class="sxs-lookup"><span data-stu-id="45377-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="45377-115">例如： `https://localhost:5001/signin-microsoft` 。</span><span class="sxs-lookup"><span data-stu-id="45377-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="45377-116">本範例稍後設定的 Microsoft 驗證配置會自動處理在 `/signin-microsoft` 路由執行的要求，以實行 OAuth 流程。</span><span class="sxs-lookup"><span data-stu-id="45377-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="45377-117">選取 [註冊]</span><span class="sxs-lookup"><span data-stu-id="45377-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="45377-118">建立用戶端密碼</span><span class="sxs-lookup"><span data-stu-id="45377-118">Create client secret</span></span>

* <span data-ttu-id="45377-119">在左側窗格中，選取 [憑證及祕密]。</span><span class="sxs-lookup"><span data-stu-id="45377-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="45377-120">在 [**用戶端密碼**] 下，選取 [**新增用戶端密碼**]</span><span class="sxs-lookup"><span data-stu-id="45377-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="45377-121">新增用戶端密碼的描述。</span><span class="sxs-lookup"><span data-stu-id="45377-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="45377-122">選取 [新增] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="45377-122">Select the **Add** button.</span></span>

* <span data-ttu-id="45377-123">在 [ **用戶端密碼**] 下，複製用戶端密碼的值。</span><span class="sxs-lookup"><span data-stu-id="45377-123">Under **Client secrets**, copy the value of the client secret.</span></span>

<span data-ttu-id="45377-124">URI 區段 `/signin-microsoft` 會設定為 Microsoft 驗證提供者的預設回呼。</span><span class="sxs-lookup"><span data-stu-id="45377-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="45377-125">您可以在設定 Microsoft 驗證中介軟體時，透過[MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)類別的繼承[RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)屬性來變更預設回呼 URI。</span><span class="sxs-lookup"><span data-stu-id="45377-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="45377-126">儲存 Microsoft 用戶端識別碼和密碼</span><span class="sxs-lookup"><span data-stu-id="45377-126">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="45377-127">使用 [秘密管理員](xref:security/app-secrets)來儲存機密設定，例如 Microsoft 用戶端識別碼和秘密值。</span><span class="sxs-lookup"><span data-stu-id="45377-127">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="45377-128">針對此範例，請使用下列步驟：</span><span class="sxs-lookup"><span data-stu-id="45377-128">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="45377-129">根據 [啟用秘密儲存](xref:security/app-secrets#enable-secret-storage)的指示，為秘密儲存體初始化專案。</span><span class="sxs-lookup"><span data-stu-id="45377-129">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="45377-130">將機密設定儲存在具有秘密金鑰和的本機秘密存放區中 `Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret` ：</span><span class="sxs-lookup"><span data-stu-id="45377-130">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="45377-131">設定 Microsoft 帳戶驗證</span><span class="sxs-lookup"><span data-stu-id="45377-131">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="45377-132">將 Microsoft 帳戶服務新增至 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="45377-132">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="45377-133">如需有關 Microsoft 帳戶驗證所支援之設定選項的詳細資訊，請參閱 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API 參考。</span><span class="sxs-lookup"><span data-stu-id="45377-133">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="45377-134">這可以用來要求使用者的不同資訊。</span><span class="sxs-lookup"><span data-stu-id="45377-134">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="45377-135">使用 Microsoft 帳戶登入帳戶</span><span class="sxs-lookup"><span data-stu-id="45377-135">Sign in with Microsoft Account</span></span>

<span data-ttu-id="45377-136">執行應用程式，然後按一下 [ **登入**]。</span><span class="sxs-lookup"><span data-stu-id="45377-136">Run the app and click **Log in**.</span></span> <span data-ttu-id="45377-137">使用 Microsoft 登入的選項隨即出現。</span><span class="sxs-lookup"><span data-stu-id="45377-137">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="45377-138">當您按一下 [Microsoft] 時，系統會將您重新導向至 Microsoft 進行驗證。</span><span class="sxs-lookup"><span data-stu-id="45377-138">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="45377-139">使用您的 Microsoft 帳戶登入之後，系統會提示您讓應用程式存取您的資訊：</span><span class="sxs-lookup"><span data-stu-id="45377-139">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="45377-140">請按一下 **[是]** ，系統會將您重新導向回到可設定電子郵件的網站。</span><span class="sxs-lookup"><span data-stu-id="45377-140">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="45377-141">您現在已使用您的 Microsoft 認證登入：</span><span class="sxs-lookup"><span data-stu-id="45377-141">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="45377-142">疑難排解</span><span class="sxs-lookup"><span data-stu-id="45377-142">Troubleshooting</span></span>

* <span data-ttu-id="45377-143">如果 Microsoft 帳戶提供者將您重新導向至 [登入錯誤] 頁面，請注意 Uri 中的 (主題標籤) 的錯誤標題和描述查詢字串參數 `#` 。</span><span class="sxs-lookup"><span data-stu-id="45377-143">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="45377-144">雖然錯誤訊息似乎指出 Microsoft 驗證有問題，但最常見的原因是您的應用程式 Uri 不符合為**Web**平臺指定的任何重新**導向 uri** 。</span><span class="sxs-lookup"><span data-stu-id="45377-144">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="45377-145">如果 Identity 未透過呼叫來 `services.AddIdentity` 設定 `ConfigureServices` ，嘗試驗證將會導致 *ArgumentException：必須提供 ' SignInScheme ' 選項*。</span><span class="sxs-lookup"><span data-stu-id="45377-145">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="45377-146">此範例中使用的專案範本可確保完成此操作。</span><span class="sxs-lookup"><span data-stu-id="45377-146">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="45377-147">如果未藉由套用初始遷移來建立網站資料庫，則在處理要求錯誤時，您將會收到 *資料庫操作失敗* 。</span><span class="sxs-lookup"><span data-stu-id="45377-147">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="45377-148">請按一下 [套用 **遷移** ] 來建立資料庫，並重新整理以繼續發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="45377-148">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="45377-149">後續步驟</span><span class="sxs-lookup"><span data-stu-id="45377-149">Next steps</span></span>

* <span data-ttu-id="45377-150">本文說明您可以如何向 Microsoft 進行驗證。</span><span class="sxs-lookup"><span data-stu-id="45377-150">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="45377-151">您可以遵循類似的方法，向 [先前頁面](xref:security/authentication/social/index)中所列的其他提供者進行驗證。</span><span class="sxs-lookup"><span data-stu-id="45377-151">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="45377-152">當您將網站發佈至 Azure web 應用程式之後，請在 Microsoft 開發人員入口網站中建立新的用戶端密碼。</span><span class="sxs-lookup"><span data-stu-id="45377-152">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="45377-153">`Authentication:Microsoft:ClientId`在 Azure 入口網站中，將和設定 `Authentication:Microsoft:ClientSecret` 為應用程式設定。</span><span class="sxs-lookup"><span data-stu-id="45377-153">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="45377-154">設定系統已設定為從環境變數讀取金鑰。</span><span class="sxs-lookup"><span data-stu-id="45377-154">The configuration system is set up to read keys from environment variables.</span></span>
