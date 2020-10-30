---
title: 使用 ASP.NET Core 的 Twitter 外部登錄設定
author: rick-anderson
description: 本教學課程示範如何將 Twitter 帳戶使用者驗證整合至現有的 ASP.NET Core 應用程式。
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
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
uid: security/authentication/twitter-logins
ms.openlocfilehash: 47926d12ac5f922f2937df164d38ff6eb63cacf1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053276"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>使用 ASP.NET Core 的 Twitter 外部登錄設定

作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

此範例示範如何使用在[前一頁](xref:security/authentication/social/index)建立的範例 ASP.NET Core 3.0 專案，讓使用者能夠以[其 Twitter 帳戶登入](https://dev.twitter.com/web/sign-in/desktop-browser)。

## <a name="create-the-app-in-twitter"></a>在 Twitter 中建立應用程式

* 將 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) 新增至專案中，並將其加入至專案。

* 流覽 [https://apps.twitter.com/](https://apps.twitter.com/) 並登入。 如果您還沒有 Twitter 帳戶，請使用 [ **[立即註冊](https://twitter.com/signup)** ] 連結來建立一個帳戶。

* 選取 [ **建立應用程式** ]。 填寫應用程式 **名稱** 、 **應用程式描述** 和公用 **網站** URI (這可以是暫時性的，直到您註冊功能變數名稱) ：

* 核取 [ **啟用 Twitter 登入** ] 旁的核取方塊

* AspNetCore。Identity 預設會要求使用者擁有電子郵件地址。 移至 [ **許可權** ] 索引標籤，按一下 [ **編輯** ] 按鈕，然後核取 [ **使用者要求電子郵件地址** ] 旁的核取方塊。

* 輸入您的開發 URI，並 `/signin-twitter` 將其附加至 **回呼 url** 欄位 (例如： `https://webapp128.azurewebsites.net/signin-twitter`) 。 稍後在此範例中設定的 Twitter 驗證配置會自動處理路由上的要求 `/signin-twitter` ，以實行 OAuth 流程。

  > [!NOTE]
  > URI 區段 `/signin-twitter` 會設定為 Twitter 驗證提供者的預設回呼。 您可以在透過[TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)類別的繼承[RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)屬性設定 Twitter 驗證中介軟體時，變更預設回呼 URI。

* 填寫表單的其餘部分，然後選取 [ **建立** ]。 隨即顯示新的應用程式詳細資料：

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>儲存 Twitter 取用者 API 金鑰和秘密

使用 [秘密管理員](xref:security/app-secrets)來儲存 Twitter 取用者 API 金鑰和秘密等機密設定。 針對此範例，請使用下列步驟：

1. 根據 [啟用秘密儲存](xref:security/app-secrets#enable-secret-storage)的指示，為秘密儲存體初始化專案。
1. 使用秘密金鑰，將敏感性設定儲存在本機秘密存放區 `Authentication:Twitter:ConsumerKey` 中 `Authentication:Twitter:ConsumerSecret` ：

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

建立新的 Twitter 應用程式之後，可以在 [ **金鑰和存取權杖** ] 索引標籤上找到這些權杖：

## <a name="configure-twitter-authentication"></a>設定 Twitter 驗證

在 Startup.cs 檔案的方法中新增 Twitter 服務 `ConfigureServices` ： *Startup.cs*

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

如需 Twitter 驗證所支援之設定選項的詳細資訊，請參閱 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API 參考。 這可以用來要求使用者的不同資訊。

## <a name="sign-in-with-twitter"></a>使用 Twitter 登入

執行應用程式並選取 [ **登入** ]。 使用 Twitter 登入的選項隨即出現：

按一下 **twitter** 重新導向至 twitter 以進行驗證：

輸入您的 Twitter 認證之後，系統會將您重新導向回到可設定電子郵件的網站。

您現在已使用 Twitter 認證登入：

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>疑難排解

* **僅 ASP.NET Core 2.x：** 如果 Identity 未透過呼叫來 `services.AddIdentity` 設定 `ConfigureServices` ，嘗試驗證將會導致 *ArgumentException：必須提供 ' SignInScheme ' 選項* 。 此範例中使用的專案範本可確保完成此操作。
* 如果未藉由套用初始遷移來建立網站資料庫，則在處理要求錯誤時，您將會收到 *資料庫操作失敗* 。 請按一下 [套用 **遷移** ] 來建立資料庫，並重新整理以繼續發生錯誤。

## <a name="next-steps"></a>後續步驟

* 本文說明如何使用 Twitter 進行驗證。 您可以遵循類似的方法，向 [先前頁面](xref:security/authentication/social/index)中所列的其他提供者進行驗證。

* 當您將網站發佈至 Azure web 應用程式之後，您應該 `ConsumerSecret` 在 Twitter 開發人員入口網站中重設。

* `Authentication:Twitter:ConsumerKey`在 Azure 入口網站中，將和設定 `Authentication:Twitter:ConsumerSecret` 為應用程式設定。 設定系統已設定為從環境變數讀取金鑰。
