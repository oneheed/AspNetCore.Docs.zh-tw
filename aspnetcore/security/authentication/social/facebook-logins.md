---
title: ASP.NET Core 中的 Facebook 外部登入設定
author: rick-anderson
description: 教學課程中的程式碼範例示範如何將 Facebook 帳戶使用者驗證整合至現有的 ASP.NET Core 應用程式。
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
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
uid: security/authentication/facebook-logins
ms.openlocfilehash: ce0e7ad30c137562b74dc9fe5c53235e3599e575
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634354"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>ASP.NET Core 中的 Facebook 外部登入設定

作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

本教學課程中的程式碼範例會示範如何使用在 [前一頁](xref:security/authentication/social/index)建立的範例 ASP.NET Core 3.0 專案，讓使用者能夠使用其 Facebook 帳戶登入。 首先，我們會遵循 [官方步驟](https://developers.facebook.com)來建立 Facebook 應用程式識別碼。

## <a name="create-the-app-in-facebook"></a>在 Facebook 中建立應用程式

* 將 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet 套件新增至專案。

* 流覽至 [Facebook 開發人員應用程式](https://developers.facebook.com/apps/) 頁面並登入。 如果您還沒有 Facebook 帳戶，請使用登入頁面上的 [ **註冊 Facebook** ] 連結建立一個。  擁有 Facebook 帳戶之後，請遵循指示註冊為 Facebook 開發人員。

* 從 **我的應用程式** ] 功能表中，選取 [ **建立應用程式** ] 以建立新的應用程式識別碼。

   ![在 Microsoft Edge 中開啟適用于開發人員入口網站的 Facebook](index/_static/FBMyApps.png)

* 填寫表單，然後按一下 [ **建立應用程式識別碼** ] 按鈕。

  ![建立新的應用程式識別碼表單](index/_static/FBNewAppId.png)

* 在新的應用程式卡片上，選取 [新增 **產品**]。  在**Facebook 登**入卡上，按一下 [**設定**] 

  ![產品安裝頁面](index/_static/FBProductSetup.png)

* **快速入門**wizard 會以**選擇平臺**作為第一個頁面來啟動。 按一下左下方功能表中的 [ **FaceBook 登**入 **設定** ] 連結，立即略過嚮導：

  ![略過快速入門](index/_static/FBSkipQuickStart.png)

* 您會看到 [ **用戶端 OAuth 設定** ] 頁面：

  ![用戶端 OAuth 設定頁面](index/_static/FBOAuthSetup.png)

* 在 [**有效的 OAuth 重新導向 uri** ] 欄位中，輸入 */signin-facebook*附加的開發 URI (例如： `https://localhost:44320/signin-facebook`) 。 本教學課程稍後設定的 Facebook 驗證會自動處理 */signin-facebook* route 的要求，以實行 OAuth 流程。

> [!NOTE]
> URI */signin-facebook* 會設定為 facebook 驗證提供者的預設回呼。 您可以在設定 Facebook 驗證中介軟體時，透過[FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)類別的繼承[RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)屬性來變更預設回呼 URI。

* 按一下 **[儲存變更]** 。

* 按一下**Settings**  >  左側導覽列中的 [設定**基本**] 連結。

  在此頁面上，記下您的 `App ID` 和 `App Secret` 。 您將在下一節中將兩者新增至 ASP.NET Core 應用程式：

* 部署網站時，您需要重新流覽 **Facebook 登** 入設定頁面，並註冊新的公用 URI。

## <a name="store-the-facebook-app-id-and-secret"></a>儲存 Facebook 應用程式識別碼和密碼

使用 [秘密管理員](xref:security/app-secrets)儲存 Facebook 應用程式識別碼和秘密值等機密設定。 針對此範例，請使用下列步驟：

1. 根據 [啟用秘密儲存](xref:security/app-secrets#enable-secret-storage)的指示，為秘密儲存體初始化專案。
1. 將機密設定儲存在具有秘密金鑰和的本機秘密存放區中 `Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret` ：

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>設定 Facebook 驗證

在 Startup.cs 檔案的方法中新增 Facebook 服務 `ConfigureServices` ： *Startup.cs*

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>使用 Facebook 登入

* 執行應用程式並選取 [ **登入**]。 
* 在 [ **使用其他服務登入**] 底下，選取 [Facebook]。
* 系統會將您重新導向至 **Facebook** 進行驗證。
* 輸入您的 Facebook 認證。
* 系統會將您重新導向回到您的網站，您可以在其中設定電子郵件。

您現在已使用 Facebook 認證登入：

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>回應取消授權外部登入

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> 當使用者未核准要求的授權要求時，可以提供使用者代理程式的重新導向路徑。

下列程式碼會將設定 `AccessDeniedPath` 為 `"/AccessDeniedPathInfo"` ：

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

我們建議 `AccessDeniedPath` 頁面包含下列資訊：

*  已取消遠端驗證。
* 此應用程式需要驗證。
* 若要再次嘗試登入，請選取 [登入] 連結。

### <a name="test-accessdeniedpath"></a>測試 AccessDeniedPath

* 流覽至 [facebook.com](https://www.facebook.com/)
* 如果您已登入，則必須登出。
* 執行應用程式並選取 Facebook 登入。
* 選取 [ **不是現在**]。 系統會將您重新導向至指定的 `AccessDeniedPath` 頁面。

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

如需 Facebook 驗證所支援之設定選項的詳細資訊，請參閱 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API 參考。 設定選項可以用來：

* 要求使用者的不同資訊。
* 新增查詢字串引數以自訂登入體驗。

## <a name="troubleshooting"></a>疑難排解

* **僅 ASP.NET Core 2.x：** 如果 Identity 未透過呼叫來 `services.AddIdentity` 設定 `ConfigureServices` ，嘗試驗證將會導致 *ArgumentException：必須提供 ' SignInScheme ' 選項*。 本教學課程中使用的專案範本可確保這項操作已完成。
* 如果未藉由套用初始遷移來建立網站資料庫，則在 *處理要求錯誤時* ，您會收到資料庫作業失敗。 請按一下 [套用 **遷移** ] 來建立資料庫，並重新整理以繼續發生錯誤。

## <a name="next-steps"></a>後續步驟

* 本文說明您可以如何使用 Facebook 進行驗證。 您可以遵循類似的方法，向 [先前頁面](xref:security/authentication/social/index)中所列的其他提供者進行驗證。

* 當您將網站發佈至 Azure web 應用程式之後，您應該 `AppSecret` 在 Facebook 開發人員入口網站中重設。

* `Authentication:Facebook:AppId`在 Azure 入口網站中，將和設定 `Authentication:Facebook:AppSecret` 為應用程式設定。 設定系統已設定為從環境變數讀取金鑰。
