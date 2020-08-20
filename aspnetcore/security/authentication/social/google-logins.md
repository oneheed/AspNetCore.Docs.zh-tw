---
title: ASP.NET Core 中的 Google 外部登入設定
author: rick-anderson
description: 本教學課程示範如何將 Google 帳戶使用者驗證整合至現有的 ASP.NET Core 應用程式。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/19/2020
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
uid: security/authentication/google-logins
ms.openlocfilehash: a7a5260a2446ac3f3be00755ef051e56080a7485
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634289"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>ASP.NET Core 中的 Google 外部登入設定

作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

本教學課程說明如何讓使用者使用在 [前一頁](xref:security/authentication/social/index)建立的 ASP.NET Core 3.0 專案以其 Google 帳戶登入。

## <a name="create-a-google-api-console-project-and-client-id"></a>建立 Google API 主控台專案和用戶端識別碼

* 安裝 [AspNetCore。 Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google)。
* 流覽至將 [Google 登入整合到您的 web 應用程式](https://developers.google.com/identity/sign-in/web/sign-in) ，並選取 [ **設定專案**]。
* 在 [ **設定 OAuth 用戶端** ] 對話方塊中，選取 [ **Web 服務器**]。
* 在 [ **授權重新導向 uri** ] 文字專案方塊中，設定重新導向 uri。 例如， `https://localhost:44312/signin-google`
* 儲存 **用戶端識別碼** 和 **用戶端密碼**。
* 部署網站時，請從 **Google 主控台**註冊新的公用 url。

## <a name="store-the-google-client-id-and-secret"></a>儲存 Google 用戶端識別碼和密碼

使用 [秘密管理員](xref:security/app-secrets)來儲存機密性設定，例如 Google 用戶端識別碼和秘密值。 針對此範例，請使用下列步驟：

1. 根據 [啟用秘密儲存](xref:security/app-secrets#enable-secret-storage)的指示，為秘密儲存體初始化專案。
1. 將機密設定儲存在具有秘密金鑰和的本機秘密存放區中 `Authentication:Google:ClientId` `Authentication:Google:ClientSecret` ：

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

您可以在 [Api 主控台](https://console.developers.google.com/apis/dashboard)中管理您的 api 認證和使用方式。

## <a name="configure-google-authentication"></a>設定 Google 驗證

將 Google 服務新增至 `Startup.ConfigureServices` ：

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>以 Google 登入

* 執行應用程式，然後按一下 [ **登入**]。 使用 Google 登入的選項隨即出現。
* 按一下 [ **google** ] 按鈕，這會重新導向至 google 進行驗證。
* 輸入您的 Google 認證之後，系統會將您重新導向回到網站。

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

如需 <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> Google 驗證所支援之設定選項的詳細資訊，請參閱 API 參考。 這可以用來要求使用者的不同資訊。

## <a name="change-the-default-callback-uri"></a>變更預設回呼 URI

URI 區段 `/signin-google` 會設定為 Google 驗證提供者的預設回呼。 您可以透過[GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions)類別的繼承[RemoteAuthenticationOptions CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)屬性，在設定 Google 驗證中介軟體時變更預設回呼 URI。

## <a name="troubleshooting"></a>疑難排解

* 如果登入無法運作，而且您未收到任何錯誤，請切換到開發模式，讓問題更容易進行偵測。
* 如果 Identity 未透過呼叫來 `services.AddIdentity` 設定 `ConfigureServices` ，則嘗試在 ArgumentException 中驗證結果 *：必須提供 ' SignInScheme ' 選項*。 本教學課程中使用的專案範本可確保這項操作已完成。
* 如果未藉由套用初始遷移來建立網站資料庫，則在 *處理要求錯誤時* ，您會收到資料庫作業失敗。 選取 [套用 **遷移** ] 來建立資料庫，然後重新整理頁面以繼續發生錯誤。

## <a name="next-steps"></a>後續步驟

* 本文說明您可以如何向 Google 進行驗證。 您可以遵循類似的方法，向 [先前頁面](xref:security/authentication/social/index)中所列的其他提供者進行驗證。
* 當您將應用程式發佈至 Azure 之後，請 `ClientSecret` 在 GOOGLE API 主控台中重設。
* `Authentication:Google:ClientId`在 Azure 入口網站中，將和設定 `Authentication:Google:ClientSecret` 為應用程式設定。 設定系統已設定為從環境變數讀取金鑰。
