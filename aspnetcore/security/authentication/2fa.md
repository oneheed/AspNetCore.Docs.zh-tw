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
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a>ASP.NET Core 中的 SMS 雙因素驗證

由 [Rick Anderson](https://twitter.com/RickAndMSFT) 和 [瑞士開發人員](https://github.com/Swiss-Devs)

>[!WARNING]
> 雙因素驗證 (2FA) 驗證器應用程式，使用以時間為基礎的單次密碼演算法 (TOTP) ，是2FA 的產業建議方法。 2FA 使用 TOTP 是 SMS 2FA 的首選。 如需詳細資訊，請參閱針對 ASP.NET Core 2.0 和更新版本的 [ASP.NET Core 中的 TOTP 驗證器應用程式啟用 QR 代碼產生](xref:security/authentication/identity-enable-qrcodes) 。

本教學課程說明如何使用 SMS (2FA) 設定雙因素驗證。 提供 [twilio](https://www.twilio.com/) 和 [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/)的指示，但您可以使用任何其他 SMS 提供者。 在開始本教學課程之前，建議您先完成 [帳戶確認和密碼修復](xref:security/authentication/accconfirm) 。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/2fa/sample/Web2FA)。 [如何下載](xref:index#how-to-download-a-sample)。

## <a name="create-a-new-aspnet-core-project"></a>建立新的 ASP.NET Core 專案

建立以個別使用者帳戶命名的新 ASP.NET Core web 應用程式 `Web2FA` 。 請依照中的指示 <xref:security/enforcing-ssl> 進行設定，並要求 HTTPS。

### <a name="create-an-sms-account"></a>建立 SMS 帳戶

建立 SMS 帳戶，例如，從 [twilio](https://www.twilio.com/) 或 [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/)。 記錄 twilio 的驗證認證 (： accountSid 和 authToken，適用于 ASPSMS： Userkey 和密碼) 。

#### <a name="figuring-out-sms-provider-credentials"></a>找出 SMS 提供者認證

**Twilio**

從 Twilio 帳戶的 [儀表板] 索引標籤中，複製 **帳戶 SID** 和 **驗證權杖**。

**ASPSMS:**

從您的帳戶設定中，流覽至 **Userkey** ，並將其與您的 **密碼** 一起複製。

稍後我們將在中使用秘密管理員工具將這些值儲存在金鑰 `SMSAccountIdentification` 和中 `SMSAccountPassword` 。

#### <a name="specifying-senderid--originator"></a>指定 SenderID/建立者

**Twilio：** 在 [數位] 索引標籤中，複製您的 Twilio **電話號碼**。

**ASPSMS：** 在 [解除鎖定的寄件者] 功能表中，解除鎖定一或多個發件 (人，或選擇不受所有網路) 支援的英數位元。

稍後我們將使用金鑰中的秘密管理員工具來儲存此值 `SMSAccountFrom` 。

### <a name="provide-credentials-for-the-sms-service"></a>提供 SMS 服務的認證

我們將使用 [選項模式](xref:fundamentals/configuration/options) 來存取使用者帳戶和金鑰設定。

* 建立類別來提取安全的 SMS 金鑰。 在此範例中， `SMSoptions` 會在 *Services/SMSoptions .cs* 檔案中建立類別。

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

`SMSAccountIdentification` `SMSAccountPassword` `SMSAccountFrom` 使用[secret manager 工具](xref:security/app-secrets)設定和。 例如：

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* 新增 SMS 提供者的 NuGet 套件。 從套件管理員主控台 (PMC) 執行：

**Twilio**

`Install-Package Twilio`

**ASPSMS:**

`Install-Package ASPSMS`

* 在 *Services/MessageServices .cs* 檔案中新增程式碼，以啟用 SMS。 請使用 Twilio 或 ASPSMS 區段：

**Twilio**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

**ASPSMS:**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a>設定要使用的啟動 `SMSoptions`

將 `SMSoptions` Startup.cs 中的方法新增至服務容器 `ConfigureServices` ： 

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a>啟用雙因素驗證

開啟 [ *Views]/[管理]/[索引*] 視圖檔 Razor ，並移除批註字元 (因此) 不會將標記標記為批註。

## <a name="log-in-with-two-factor-authentication"></a>使用雙因素驗證登入

* 執行應用程式並註冊新的使用者

![在 Microsoft Edge 中開啟的 Web 應用程式註冊 view](2fa/_static/login2fa1.png)

* 點一下您的使用者名稱，它會 `Index` 在 [管理控制器] 中啟用動作方法。 然後，按一下 [電話號碼 **新增** ] 連結。

![管理 view-請按 [新增] 連結](2fa/_static/login2fa2.png)

* 新增將接收驗證碼的電話號碼，然後按一下 [ **傳送驗證碼**]。

![[新增電話號碼] 頁面](2fa/_static/login2fa3.png)

* 您將會收到包含驗證碼的文字訊息。 輸入它，然後按一下 [**提交**]

![驗證電話號碼頁面](2fa/_static/login2fa4.png)

如果您沒有收到文字訊息，請參閱 twilio 記錄頁面。

* [管理] 視圖會顯示您的電話號碼已成功新增。

![已成功新增管理檢視-電話號碼](2fa/_static/login2fa5.png)

* 按一下 [ **啟用** ] 以啟用雙因素驗證。

![管理 view-啟用雙因素驗證](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a>測試雙因素驗證

* 登出。

* 登入。

* 使用者帳戶已啟用雙因素驗證，因此您必須提供第二個驗證因素。 在本教學課程中，您已啟用電話驗證。 內建範本也可讓您將電子郵件設定為第二個因素。 您可以針對驗證（例如 QR 代碼）設定額外的第二個因素。 點擊 [ **提交**]。

![傳送驗證碼視圖](2fa/_static/login2fa7.png)

* 輸入您在 SMS 訊息中取得的代碼。

* 按一下 [ **記住此瀏覽器** ] 核取方塊，將會豁免您在使用相同的裝置和瀏覽器時，不需要使用2FA 來登入。 啟用2FA，然後按一下 [ **記住此瀏覽器** ]，只要使用者沒有您的裝置存取權，就能為您提供強大的2FA 保護，讓您免于惡意使用者嘗試存取您的帳戶。 您可以在任何您經常使用的私人裝置上執行此動作。 藉由設定  **記住此瀏覽器**，您可以從不常使用的裝置獲得2FA 的額外安全性，並讓您在自己的裝置上不需要經歷2FA 的便利性。

![確認 view](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a>防止暴力密碼破解攻擊的帳戶鎖定

建議使用2FA 來鎖定帳戶。 一旦使用者透過本機帳戶或社交帳戶登入，就會儲存2FA 每次失敗的嘗試。 如果達到失敗的存取嘗試次數上限，使用者會被鎖定 (預設：5分鐘後鎖定失敗的存取嘗試次數) 。 成功的驗證會重設失敗的存取嘗試次數，並重設時鐘。 失敗的存取嘗試次數上限和鎖定時間可使用 [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) 和 [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan)設定。 以下會在10次失敗的存取嘗試次數之後，將帳戶鎖定設定為10分鐘：

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

確認 [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) 設定 `lockoutOnFailure` 為 `true` ：

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
