---
title: ASP.NET Core 中的 SMS 雙因素驗證
author: rick-anderson
description: 瞭解如何使用 ASP.NET Core 應用程式 (2FA) 設定雙因素驗證。
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
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
uid: security/authentication/2fa
ms.openlocfilehash: 1f77f3f4b7e9dd558e9869992e2f1f4d185e5b10
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586848"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a><span data-ttu-id="5380e-103">ASP.NET Core 中的 SMS 雙因素驗證</span><span class="sxs-lookup"><span data-stu-id="5380e-103">Two-factor authentication with SMS in ASP.NET Core</span></span>

<span data-ttu-id="5380e-104">由 [Rick Anderson](https://twitter.com/RickAndMSFT) 和 [瑞士開發人員](https://github.com/Swiss-Devs)</span><span class="sxs-lookup"><span data-stu-id="5380e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Swiss-Devs](https://github.com/Swiss-Devs)</span></span>

>[!WARNING]
> <span data-ttu-id="5380e-105">雙因素驗證 (2FA) 驗證器應用程式，使用以時間為基礎的單次密碼演算法 (TOTP) ，是2FA 的產業建議方法。</span><span class="sxs-lookup"><span data-stu-id="5380e-105">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="5380e-106">2FA 使用 TOTP 是 SMS 2FA 的首選。</span><span class="sxs-lookup"><span data-stu-id="5380e-106">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="5380e-107">如需詳細資訊，請參閱針對 ASP.NET Core 2.0 和更新版本的 [ASP.NET Core 中的 TOTP 驗證器應用程式啟用 QR 代碼產生](xref:security/authentication/identity-enable-qrcodes) 。</span><span class="sxs-lookup"><span data-stu-id="5380e-107">For more information, see [Enable QR Code generation for TOTP authenticator apps in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) for ASP.NET Core 2.0 and later.</span></span>

<span data-ttu-id="5380e-108">本教學課程說明如何使用 SMS (2FA) 設定雙因素驗證。</span><span class="sxs-lookup"><span data-stu-id="5380e-108">This tutorial shows how to set up two-factor authentication (2FA) using SMS.</span></span> <span data-ttu-id="5380e-109">提供 [twilio](https://www.twilio.com/) 和 [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/)的指示，但您可以使用任何其他 SMS 提供者。</span><span class="sxs-lookup"><span data-stu-id="5380e-109">Instructions are given for [twilio](https://www.twilio.com/) and [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), but you can use any other SMS provider.</span></span> <span data-ttu-id="5380e-110">在開始本教學課程之前，建議您先完成 [帳戶確認和密碼修復](xref:security/authentication/accconfirm) 。</span><span class="sxs-lookup"><span data-stu-id="5380e-110">We recommend you complete [Account Confirmation and Password Recovery](xref:security/authentication/accconfirm) before starting this tutorial.</span></span>

<span data-ttu-id="5380e-111">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/2fa/sample/Web2FA)。</span><span class="sxs-lookup"><span data-stu-id="5380e-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/2fa/sample/Web2FA).</span></span> <span data-ttu-id="5380e-112">[如何下載](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="5380e-112">[How to download](xref:index#how-to-download-a-sample).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="5380e-113">建立新的 ASP.NET Core 專案</span><span class="sxs-lookup"><span data-stu-id="5380e-113">Create a new ASP.NET Core project</span></span>

<span data-ttu-id="5380e-114">建立以個別使用者帳戶命名的新 ASP.NET Core web 應用程式 `Web2FA` 。</span><span class="sxs-lookup"><span data-stu-id="5380e-114">Create a new ASP.NET Core web app named `Web2FA` with individual user accounts.</span></span> <span data-ttu-id="5380e-115">請依照中的指示 <xref:security/enforcing-ssl> 進行設定，並要求 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="5380e-115">Follow the instructions in <xref:security/enforcing-ssl> to set up and require HTTPS.</span></span>

### <a name="create-an-sms-account"></a><span data-ttu-id="5380e-116">建立 SMS 帳戶</span><span class="sxs-lookup"><span data-stu-id="5380e-116">Create an SMS account</span></span>

<span data-ttu-id="5380e-117">建立 SMS 帳戶，例如，從 [twilio](https://www.twilio.com/) 或 [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/)。</span><span class="sxs-lookup"><span data-stu-id="5380e-117">Create an SMS account, for example, from [twilio](https://www.twilio.com/) or [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span></span> <span data-ttu-id="5380e-118">記錄 twilio 的驗證認證 (： accountSid 和 authToken，適用于 ASPSMS： Userkey 和密碼) 。</span><span class="sxs-lookup"><span data-stu-id="5380e-118">Record the authentication credentials (for twilio: accountSid and authToken, for ASPSMS: Userkey and Password).</span></span>

#### <a name="figuring-out-sms-provider-credentials"></a><span data-ttu-id="5380e-119">找出 SMS 提供者認證</span><span class="sxs-lookup"><span data-stu-id="5380e-119">Figuring out SMS Provider credentials</span></span>

<span data-ttu-id="5380e-120">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="5380e-120">**Twilio:**</span></span>

<span data-ttu-id="5380e-121">從 Twilio 帳戶的 [儀表板] 索引標籤中，複製 **帳戶 SID** 和 **驗證權杖**。</span><span class="sxs-lookup"><span data-stu-id="5380e-121">From the Dashboard tab of your Twilio account, copy the **Account SID** and **Auth token**.</span></span>

<span data-ttu-id="5380e-122">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="5380e-122">**ASPSMS:**</span></span>

<span data-ttu-id="5380e-123">從您的帳戶設定中，流覽至 **Userkey** ，並將其與您的 **密碼** 一起複製。</span><span class="sxs-lookup"><span data-stu-id="5380e-123">From your account settings, navigate to **Userkey** and copy it together with your **Password**.</span></span>

<span data-ttu-id="5380e-124">稍後我們將在中使用秘密管理員工具將這些值儲存在金鑰 `SMSAccountIdentification` 和中 `SMSAccountPassword` 。</span><span class="sxs-lookup"><span data-stu-id="5380e-124">We will later store these values in with the secret-manager tool within the keys `SMSAccountIdentification` and `SMSAccountPassword`.</span></span>

#### <a name="specifying-senderid--originator"></a><span data-ttu-id="5380e-125">指定 SenderID/建立者</span><span class="sxs-lookup"><span data-stu-id="5380e-125">Specifying SenderID / Originator</span></span>

<span data-ttu-id="5380e-126">**Twilio：** 在 [數位] 索引標籤中，複製您的 Twilio **電話號碼**。</span><span class="sxs-lookup"><span data-stu-id="5380e-126">**Twilio:** From the Numbers tab, copy your Twilio **phone number**.</span></span>

<span data-ttu-id="5380e-127">**ASPSMS：** 在 [解除鎖定的寄件者] 功能表中，解除鎖定一或多個發件 (人，或選擇不受所有網路) 支援的英數位元。</span><span class="sxs-lookup"><span data-stu-id="5380e-127">**ASPSMS:** Within the Unlock Originators Menu, unlock one or more Originators or choose an alphanumeric Originator (Not supported by all networks).</span></span>

<span data-ttu-id="5380e-128">稍後我們將使用金鑰中的秘密管理員工具來儲存此值 `SMSAccountFrom` 。</span><span class="sxs-lookup"><span data-stu-id="5380e-128">We will later store this value with the secret-manager tool within the key `SMSAccountFrom`.</span></span>

### <a name="provide-credentials-for-the-sms-service"></a><span data-ttu-id="5380e-129">提供 SMS 服務的認證</span><span class="sxs-lookup"><span data-stu-id="5380e-129">Provide credentials for the SMS service</span></span>

<span data-ttu-id="5380e-130">我們將使用 [選項模式](xref:fundamentals/configuration/options) 來存取使用者帳戶和金鑰設定。</span><span class="sxs-lookup"><span data-stu-id="5380e-130">We'll use the [Options pattern](xref:fundamentals/configuration/options) to access the user account and key settings.</span></span>

* <span data-ttu-id="5380e-131">建立類別來提取安全的 SMS 金鑰。</span><span class="sxs-lookup"><span data-stu-id="5380e-131">Create a class to fetch the secure SMS key.</span></span> <span data-ttu-id="5380e-132">在此範例中， `SMSoptions` 會在 *Services/SMSoptions .cs* 檔案中建立類別。</span><span class="sxs-lookup"><span data-stu-id="5380e-132">For this sample, the `SMSoptions` class is created in the *Services/SMSoptions.cs* file.</span></span>

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

<span data-ttu-id="5380e-133">`SMSAccountIdentification` `SMSAccountPassword` `SMSAccountFrom` 使用[secret manager 工具](xref:security/app-secrets)設定和。</span><span class="sxs-lookup"><span data-stu-id="5380e-133">Set the `SMSAccountIdentification`, `SMSAccountPassword` and `SMSAccountFrom` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="5380e-134">例如：</span><span class="sxs-lookup"><span data-stu-id="5380e-134">For example:</span></span>

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* <span data-ttu-id="5380e-135">新增 SMS 提供者的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="5380e-135">Add the NuGet package for the SMS provider.</span></span> <span data-ttu-id="5380e-136">從套件管理員主控台 (PMC) 執行：</span><span class="sxs-lookup"><span data-stu-id="5380e-136">From the Package Manager Console (PMC) run:</span></span>

<span data-ttu-id="5380e-137">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="5380e-137">**Twilio:**</span></span>

`Install-Package Twilio`

<span data-ttu-id="5380e-138">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="5380e-138">**ASPSMS:**</span></span>

`Install-Package ASPSMS`

* <span data-ttu-id="5380e-139">在 *Services/MessageServices .cs* 檔案中新增程式碼，以啟用 SMS。</span><span class="sxs-lookup"><span data-stu-id="5380e-139">Add code in the *Services/MessageServices.cs* file to enable SMS.</span></span> <span data-ttu-id="5380e-140">請使用 Twilio 或 ASPSMS 區段：</span><span class="sxs-lookup"><span data-stu-id="5380e-140">Use either the Twilio or the ASPSMS section:</span></span>

<span data-ttu-id="5380e-141">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="5380e-141">**Twilio:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

<span data-ttu-id="5380e-142">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="5380e-142">**ASPSMS:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a><span data-ttu-id="5380e-143">設定要使用的啟動 `SMSoptions`</span><span class="sxs-lookup"><span data-stu-id="5380e-143">Configure startup to use `SMSoptions`</span></span>

<span data-ttu-id="5380e-144">將 `SMSoptions` Startup.cs 中的方法新增至服務容器 `ConfigureServices` ： </span><span class="sxs-lookup"><span data-stu-id="5380e-144">Add `SMSoptions` to the service container in the `ConfigureServices` method in the *Startup.cs*:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a><span data-ttu-id="5380e-145">啟用雙因素驗證</span><span class="sxs-lookup"><span data-stu-id="5380e-145">Enable two-factor authentication</span></span>

<span data-ttu-id="5380e-146">開啟 [ *Views]/[管理]/[索引*] 視圖檔 Razor ，並移除批註字元 (因此) 不會將標記標記為批註。</span><span class="sxs-lookup"><span data-stu-id="5380e-146">Open the *Views/Manage/Index.cshtml* Razor view file and remove the comment characters (so no markup is commented out).</span></span>

## <a name="log-in-with-two-factor-authentication"></a><span data-ttu-id="5380e-147">使用雙因素驗證登入</span><span class="sxs-lookup"><span data-stu-id="5380e-147">Log in with two-factor authentication</span></span>

* <span data-ttu-id="5380e-148">執行應用程式並註冊新的使用者</span><span class="sxs-lookup"><span data-stu-id="5380e-148">Run the app and register a new user</span></span>

![在 Microsoft Edge 中開啟的 Web 應用程式註冊 view](2fa/_static/login2fa1.png)

* <span data-ttu-id="5380e-150">點一下您的使用者名稱，它會 `Index` 在 [管理控制器] 中啟用動作方法。</span><span class="sxs-lookup"><span data-stu-id="5380e-150">Tap on your user name, which activates the `Index` action method in Manage controller.</span></span> <span data-ttu-id="5380e-151">然後，按一下 [電話號碼 **新增** ] 連結。</span><span class="sxs-lookup"><span data-stu-id="5380e-151">Then tap the phone number **Add** link.</span></span>

![管理 view-請按 [新增] 連結](2fa/_static/login2fa2.png)

* <span data-ttu-id="5380e-153">新增將接收驗證碼的電話號碼，然後按一下 [ **傳送驗證碼**]。</span><span class="sxs-lookup"><span data-stu-id="5380e-153">Add a phone number that will receive the verification code, and tap **Send verification code**.</span></span>

![[新增電話號碼] 頁面](2fa/_static/login2fa3.png)

* <span data-ttu-id="5380e-155">您將會收到包含驗證碼的文字訊息。</span><span class="sxs-lookup"><span data-stu-id="5380e-155">You will get a text message with the verification code.</span></span> <span data-ttu-id="5380e-156">輸入它，然後按一下 [**提交**]</span><span class="sxs-lookup"><span data-stu-id="5380e-156">Enter it and tap **Submit**</span></span>

![驗證電話號碼頁面](2fa/_static/login2fa4.png)

<span data-ttu-id="5380e-158">如果您沒有收到文字訊息，請參閱 twilio 記錄頁面。</span><span class="sxs-lookup"><span data-stu-id="5380e-158">If you don't get a text message, see twilio log page.</span></span>

* <span data-ttu-id="5380e-159">[管理] 視圖會顯示您的電話號碼已成功新增。</span><span class="sxs-lookup"><span data-stu-id="5380e-159">The Manage view shows your phone number was added successfully.</span></span>

![已成功新增管理檢視-電話號碼](2fa/_static/login2fa5.png)

* <span data-ttu-id="5380e-161">按一下 [ **啟用** ] 以啟用雙因素驗證。</span><span class="sxs-lookup"><span data-stu-id="5380e-161">Tap **Enable** to enable two-factor authentication.</span></span>

![管理 view-啟用雙因素驗證](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a><span data-ttu-id="5380e-163">測試雙因素驗證</span><span class="sxs-lookup"><span data-stu-id="5380e-163">Test two-factor authentication</span></span>

* <span data-ttu-id="5380e-164">登出。</span><span class="sxs-lookup"><span data-stu-id="5380e-164">Log off.</span></span>

* <span data-ttu-id="5380e-165">登入。</span><span class="sxs-lookup"><span data-stu-id="5380e-165">Log in.</span></span>

* <span data-ttu-id="5380e-166">使用者帳戶已啟用雙因素驗證，因此您必須提供第二個驗證因素。</span><span class="sxs-lookup"><span data-stu-id="5380e-166">The user account has enabled two-factor authentication, so you have to provide the second factor of authentication .</span></span> <span data-ttu-id="5380e-167">在本教學課程中，您已啟用電話驗證。</span><span class="sxs-lookup"><span data-stu-id="5380e-167">In this tutorial you have enabled phone verification.</span></span> <span data-ttu-id="5380e-168">內建範本也可讓您將電子郵件設定為第二個因素。</span><span class="sxs-lookup"><span data-stu-id="5380e-168">The built in templates also allow you to set up email as the second factor.</span></span> <span data-ttu-id="5380e-169">您可以針對驗證（例如 QR 代碼）設定額外的第二個因素。</span><span class="sxs-lookup"><span data-stu-id="5380e-169">You can set up additional second factors for authentication such as QR codes.</span></span> <span data-ttu-id="5380e-170">點擊 [ **提交**]。</span><span class="sxs-lookup"><span data-stu-id="5380e-170">Tap **Submit**.</span></span>

![傳送驗證碼視圖](2fa/_static/login2fa7.png)

* <span data-ttu-id="5380e-172">輸入您在 SMS 訊息中取得的代碼。</span><span class="sxs-lookup"><span data-stu-id="5380e-172">Enter the code you get in the SMS message.</span></span>

* <span data-ttu-id="5380e-173">按一下 [ **記住此瀏覽器** ] 核取方塊，將會豁免您在使用相同的裝置和瀏覽器時，不需要使用2FA 來登入。</span><span class="sxs-lookup"><span data-stu-id="5380e-173">Clicking on the **Remember this browser** check box will exempt you from needing to use 2FA to log on when using the same device and browser.</span></span> <span data-ttu-id="5380e-174">啟用2FA，然後按一下 [ **記住此瀏覽器** ]，只要使用者沒有您的裝置存取權，就能為您提供強大的2FA 保護，讓您免于惡意使用者嘗試存取您的帳戶。</span><span class="sxs-lookup"><span data-stu-id="5380e-174">Enabling 2FA and clicking on **Remember this browser** will provide you with strong 2FA protection from malicious users trying to access your account, as long as they don't have access to your device.</span></span> <span data-ttu-id="5380e-175">您可以在任何您經常使用的私人裝置上執行此動作。</span><span class="sxs-lookup"><span data-stu-id="5380e-175">You can do this on any private device you regularly use.</span></span> <span data-ttu-id="5380e-176">藉由設定  **記住此瀏覽器**，您可以從不常使用的裝置獲得2FA 的額外安全性，並讓您在自己的裝置上不需要經歷2FA 的便利性。</span><span class="sxs-lookup"><span data-stu-id="5380e-176">By setting  **Remember this browser**, you get the added security of 2FA from devices you don't regularly use, and you get the convenience on not having to go through 2FA on your own devices.</span></span>

![確認 view](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a><span data-ttu-id="5380e-178">防止暴力密碼破解攻擊的帳戶鎖定</span><span class="sxs-lookup"><span data-stu-id="5380e-178">Account lockout for protecting against brute force attacks</span></span>

<span data-ttu-id="5380e-179">建議使用2FA 來鎖定帳戶。</span><span class="sxs-lookup"><span data-stu-id="5380e-179">Account lockout is recommended with 2FA.</span></span> <span data-ttu-id="5380e-180">一旦使用者透過本機帳戶或社交帳戶登入，就會儲存2FA 每次失敗的嘗試。</span><span class="sxs-lookup"><span data-stu-id="5380e-180">Once a user signs in through a local account or social account, each failed attempt at 2FA is stored.</span></span> <span data-ttu-id="5380e-181">如果達到失敗的存取嘗試次數上限，使用者會被鎖定 (預設：5分鐘後鎖定失敗的存取嘗試次數) 。</span><span class="sxs-lookup"><span data-stu-id="5380e-181">If the maximum failed access attempts is reached, the user is locked out (default: 5 minute lockout after 5 failed access attempts).</span></span> <span data-ttu-id="5380e-182">成功的驗證會重設失敗的存取嘗試次數，並重設時鐘。</span><span class="sxs-lookup"><span data-stu-id="5380e-182">A successful authentication resets the failed access attempts count and resets the clock.</span></span> <span data-ttu-id="5380e-183">失敗的存取嘗試次數上限和鎖定時間可使用 [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) 和 [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan)設定。</span><span class="sxs-lookup"><span data-stu-id="5380e-183">The maximum failed access attempts and lockout time can be set with [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) and [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span></span> <span data-ttu-id="5380e-184">以下會在10次失敗的存取嘗試次數之後，將帳戶鎖定設定為10分鐘：</span><span class="sxs-lookup"><span data-stu-id="5380e-184">The following configures account lockout for 10 minutes after 10 failed access attempts:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

<span data-ttu-id="5380e-185">確認 [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) 設定 `lockoutOnFailure` 為 `true` ：</span><span class="sxs-lookup"><span data-stu-id="5380e-185">Confirm that [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) sets `lockoutOnFailure` to `true`:</span></span>

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
