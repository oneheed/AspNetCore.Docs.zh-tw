---
title: 使用 ASP.NET Core 中的 WS-Federation 來驗證使用者
author: chlowell
description: 本教學課程示範如何使用 ASP.NET Core 應用程式中的 WS-Federation。
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
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
uid: security/authentication/ws-federation
ms.openlocfilehash: ed78923a2bdd1ed683a72c0a6f34337a38350035
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053367"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a>使用 ASP.NET Core 中的 WS-Federation 來驗證使用者

本教學課程示範如何讓使用者使用 WS-Federation authentication 提供者登入，例如 Active Directory 同盟服務 (ADFS) 或 [Azure Active Directory](/azure/active-directory/) (AAD) 。 它會使用 [Facebook、Google 和外部提供者驗證](xref:security/authentication/social/index)中所述的 ASP.NET Core 範例應用程式。

對於 ASP.NET Core 應用程式，WS-Federation 支援是由 [WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)提供。 此元件會從 [Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) 移植，並共用許多元件的機制。 不過，元件有幾個重要的差異。

依預設，新的中介軟體：

* 不允許未經要求的登入。 WS-Federation 通訊協定的這項功能很容易遭受 XSRF 攻擊。 不過，您可以使用選項來啟用它 `AllowUnsolicitedLogins` 。
* 不會檢查每個表單張貼是否有登入訊息。 只 `CallbackPath` 會檢查登入的要求。 `CallbackPath` 預設為， `/signin-wsfed` 但可透過[WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions)類別的繼承[RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)屬性來變更。 您可以啟用 [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) 選項，與其他驗證提供者共用此路徑。

## <a name="register-the-app-with-active-directory"></a>使用 Active Directory 註冊應用程式

### <a name="active-directory-federation-services"></a>Active Directory Federation Services

* 從 ADFS 管理主控台開啟伺服器的 [ **新增信賴憑證者信任] 嚮導** ：

![新增信賴憑證者信任嚮導：歡迎使用](ws-federation/_static/AdfsAddTrust.png)

* 選擇手動輸入資料：

![新增信賴憑證者信任 Wizard：選取資料來源](ws-federation/_static/AdfsSelectDataSource.png)

* 輸入信賴憑證者的顯示名稱。 此名稱對 ASP.NET Core 應用程式而言並不重要。

* [AspNetCore WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) 缺少權杖加密的支援，因此請勿設定權杖加密憑證：

![新增信賴憑證者信任 Wizard：設定憑證](ws-federation/_static/AdfsConfigureCert.png)

* 使用應用程式的 URL 啟用 WS-Federation 被動通訊協定的支援。 確認應用程式的埠正確：

![新增信賴憑證者信任 Wizard：設定 URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> 這必須是 HTTPS URL。 IIS Express 可以在開發期間裝載應用程式時提供自我簽署的憑證。 Kestrel 需要手動憑證設定。 如需詳細資訊，請參閱 [Kestrel 檔](xref:fundamentals/servers/kestrel) 。

* 按一下 [ **下一步** ]，再按嚮導的其餘部分，並在結尾處 **關閉** 。

* ASP.NET Core Identity 需要 **名稱識別碼** 宣告。 從 [編輯宣告 **規則** ] 對話方塊新增一個：

![編輯宣告規則](ws-federation/_static/EditClaimRules.png)

* 在 [ **新增轉換宣告規則] 嚮導** 中，保留選取 [ **傳送 LDAP 屬性作為宣告** 範本] 的預設值，然後按 **[下一步]** 。 新增規則，將 **SAM 帳戶名稱** LDAP 屬性對應到 **名稱識別碼** 輸出宣告：

![新增轉換宣告規則 Wizard：設定宣告規則](ws-federation/_static/AddTransformClaimRule.png)

* 按一下 **Finish**  >  [ **編輯宣告規則** ] 視窗中的 [完成 **]** 。

### <a name="azure-active-directory"></a>Azure Active Directory

* 流覽至 AAD 租使用者的 [應用程式註冊] 分頁。 按一下 [ **新增應用程式註冊** ：

![Azure Active Directory：應用程式註冊](ws-federation/_static/AadNewAppRegistration.png)

* 輸入應用程式註冊的名稱。 這對 ASP.NET Core 應用程式而言並不重要。
* 輸入應用程式接聽的 URL 作為登 **入 url** ：

![Azure Active Directory：建立應用程式註冊](ws-federation/_static/AadCreateAppRegistration.png)

* 按一下 [ **端點** ]，並記下 **同盟元資料檔案** URL。 這是 WS-Federation 中介軟體 `MetadataAddress` ：

![Azure Active Directory：端點](ws-federation/_static/AadFederationMetadataDocument.png)

* 流覽至新的應用程式註冊。 按一下 [ **公開 API** ]。 按一下 [應用程式識別碼 URI **設定**  >  **儲存** ]。 記下  **應用程式識別碼 URI** 。 這是 WS-Federation 中介軟體 `Wtrealm` ：

![Azure Active Directory：應用程式註冊屬性](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-no-locaspnet-core-identity"></a>使用 WS-Federation 但不使用 ASP.NET Core Identity

您可以不使用 WS-Federation 中介軟體 Identity 。 例如：
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-no-locaspnet-core-identity"></a>將 WS-Federation 新增為的外部登入提供者 ASP.NET Core Identity

* 將 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) 的相依性新增至專案。
* 將 WS-Federation 新增至 `Startup.ConfigureServices` ：

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a>使用 WS-Federation 登入

流覽至應用程式，然後按一下導覽標頭中的 [ **登入** ] 連結。 有一個選項可使用 WsFederation： [登入] 頁面登入 ![](ws-federation/_static/WsFederationButton.png)

使用 ADFS 作為提供者時，按鈕會重新導向至 ADFS 登入頁面： ![ adfs 登入頁面](ws-federation/_static/AdfsLoginPage.png)

使用 Azure Active Directory 作為提供者時，按鈕會重新導向至 AAD 登入頁面： ![ aad 登入頁面](ws-federation/_static/AadSignIn.png)

成功登入新的使用者會重新導向至應用程式的使用者註冊頁面： ![ 註冊頁面](ws-federation/_static/Register.png)
