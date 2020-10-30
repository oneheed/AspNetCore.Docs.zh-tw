---
title: 使用 Azure Active Directory B2C 保護 ASP.NET Core 的 Blazor WebAssembly 獨立應用程式
author: guardrex
description: 瞭解如何使用 Azure Active Directory B2C 保護 ASP.NET Core 的 Blazor WebAssembly 獨立應用程式。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 423c7ad9f00a991c634f24a170c52d6dcb035ee1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055148"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 保護 ASP.NET Core 的 Blazor WebAssembly 獨立應用程式

由 [Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)

若要建立使用[Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview)進行驗證的[獨立 Blazor WebAssembly 應用程式](xref:blazor/hosting-models#blazor-webassembly)：

依照下列主題中的指導方針，建立租使用者，並在 Azure 入口網站中註冊 web 應用程式：

[建立 AAD B2C 租使用者](/azure/active-directory-b2c/tutorial-create-tenant)

記錄下列資訊：

* AAD B2C 實例 (例如， `https://contoso.b2clogin.com/` 其中包含尾端的斜線) ：實例是 AZURE B2C 應用程式註冊的配置和主機，您可以從 Azure 入口網站的 **應用程式註冊** 頁面開啟 [ **端點** ] 視窗來找到此實例。
* AAD B2C 主要/發行者/租使用者網域 (例如 `contoso.onmicrosoft.com`) ：網域可作為已註冊應用程式之 Azure 入口網站的 [ **商標** ] 分頁中的 **發行者網域** 。

遵循教學課程中的指導方針 [：再次在 Azure Active Directory B2C 中註冊應用程式](/azure/active-directory-b2c/tutorial-register-applications) ，為應用程式註冊 AAD 應用程式 *`Client`* ，然後執行下列動作：

::: moniker range=">= aspnetcore-5.0"

1. 在 **Azure Active Directory** > **應用程式註冊** 中，選取 [ **新增註冊** ]。
1. 提供應用程式的 **名稱** (例如 **Blazor 獨立 AAD B2C** ) 。
1. 針對 **支援的帳戶類型** ，請選取 [多租使用者] 選項： **任何組織目錄中的帳戶或任何身分識別提供者。用於驗證具有 Azure AD B2C 的使用者。**
1. 將 [重新 **導向 uri** ] 下拉式清單設定為 **單一頁面應用程式 (SPA)** 並提供下列重新導向 uri： `https://localhost:{PORT}/authentication/login-callback` 。 在 Kestrel 上執行應用程式的預設連接埠是 5001。 如果應用程式是在不同的 Kestrel 埠上執行，請使用應用程式的埠。 針對 IIS Express，可在 [ **調試** 程式] 面板的應用程式屬性中找到應用程式隨機產生的埠。 由於應用程式目前不存在，且 IIS Express 埠未知，因此在建立應用程式之後，請返回此步驟，並更新重新導向 URI。 本主題稍後會出現一個批註，提醒 IIS Express 使用者更新重新導向 URI。
1. 確認 **Permissions** > 已選取 [將系統 **管理員同意授與 openid] 和 [offline_access] 許可權** 。
1. 選取 [註冊]。

記錄應用程式 (用戶端) 識別碼 (例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`) 。

在「 **驗證** 平臺設定」的 > **Platform configurations** > **單一頁面應用程式中， (SPA)** ：

1. 確認的重新 **導向 URI** `https://localhost:{PORT}/authentication/login-callback` 存在。
1. 針對 **隱含授** 與，請確定 **未** 選取 **存取權杖** 和 **識別碼權杖** 的核取方塊。
1. 此體驗可接受應用程式的其餘預設值。
1. 選取 [儲存] 按鈕。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. 在 **Azure Active Directory** > **應用程式註冊** 中，選取 [ **新增註冊** ]。
1. 提供應用程式的 **名稱** (例如 **Blazor 獨立 AAD B2C** ) 。
1. 針對 **支援的帳戶類型** ，請選取 [多租使用者] 選項： **任何組織目錄中的帳戶或任何身分識別提供者。用於驗證具有 Azure AD B2C 的使用者。**
1. 將 [重新 **導向 URI** ] 下拉式清單保持設定為 [ **Web** ]，並提供下列重新導向 uri： `https://localhost:{PORT}/authentication/login-callback` 。 在 Kestrel 上執行應用程式的預設連接埠是 5001。 如果應用程式是在不同的 Kestrel 埠上執行，請使用應用程式的埠。 針對 IIS Express，可在 [ **調試** 程式] 面板的應用程式屬性中找到應用程式隨機產生的埠。 由於應用程式目前不存在，且 IIS Express 埠未知，因此在建立應用程式之後，請返回此步驟，並更新重新導向 URI。 本主題稍後會出現一個批註，提醒 IIS Express 使用者更新重新導向 URI。
1. 確認 **Permissions** > 已選取 [將系統 **管理員同意授與 openid] 和 [offline_access] 許可權** 。
1. 選取 [註冊]。

記錄應用程式 (用戶端) 識別碼 (例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`) 。

在 [ **驗證** > **平臺** 設定] > **Web** ：

1. 確認的重新 **導向 URI** `https://localhost:{PORT}/authentication/login-callback` 存在。
1. 針對 **[隱含授** 與]，選取 **存取權杖** 和 **識別碼權杖** 的核取方塊。
1. 此體驗可接受應用程式的其餘預設值。
1. 選取 [儲存] 按鈕。

::: moniker-end

在 **Home**  >  **Azure AD B2C**  >  **使用者流程** ：

[建立註冊和登入使用者流程](/azure/active-directory-b2c/tutorial-create-user-flows)

至少選取 [ **應用程式宣告**  >  **顯示名稱** ] 使用者屬性，以填入 `context.User.Identity.Name` `LoginDisplay` 元件 () 中的 `Shared/LoginDisplay.razor` 。

記錄為應用程式建立的註冊和登入使用者流程名稱 (例如 `B2C_1_signupsignin`) 。

在空的資料夾中，將下列命令中的預留位置取代為先前記錄的資訊，然後在命令 shell 中執行命令：

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| 預留位置                   | Azure 入口網站名稱               | 範例                                |
| ----------------------------- | ------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | 執行個體                        | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                         | `BlazorSample`                         |
| `{CLIENT ID}`                 | 應用程式 (用戶端) 識別碼         | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | 註冊/登入使用者流程       | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | 主要/發行者/租使用者網域 | `contoso.onmicrosoft.com`              |

使用 `-o|--output` 選項指定的輸出位置會建立專案資料夾 (如果不存在)，並成為應用程式名稱的一部分。

> [!NOTE]
> 在 Azure 入口網站中，會針對使用預設設定在 Kestrel 伺服器上執行的應用程式，將應用程式的平臺設定重新 **導向 URI** 設定為使用埠5001。
>
> 如果應用程式是在隨機的 IIS Express 埠上執行，則可以在 [ **調試** 程式] 面板的應用程式屬性中找到應用程式的埠。
>
> 如果先前未使用應用程式的已知埠設定埠，請返回 Azure 入口網站中的應用程式註冊，然後以正確的埠更新重新導向 URI。

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

建立應用程式之後，您應該能夠：

* 使用 AAD 使用者帳戶登入應用程式。
* 要求 Microsoft Api 的存取權杖。 如需詳細資訊，請參閱
  * [存取權杖範圍](#access-token-scopes)
  * [快速入門：設定應用程式以公開 Web api](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。

## <a name="authentication-package"></a>驗證套件

建立應用程式以使用個別 B2C 帳戶 (`IndividualB2C`) 時，應用程式會自動收到 [Microsoft 驗證程式庫](/azure/active-directory/develop/msal-overview) 的套件參考 ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) 。 此套件提供一組基本類型，可協助應用程式驗證使用者，並取得權杖以呼叫受保護的 Api。

如果將驗證新增至應用程式，請手動將套件新增到應用程式的專案檔：

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （ [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)）中找到符合應用程式共用架構版本的最新穩定版本套件。

[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)封裝可傳遞將 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 套件新增至應用程式。

## <a name="authentication-service-support"></a>驗證服務支援

驗證使用者的支援是在服務容器中註冊，並具有 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 由封裝提供的擴充方法 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 。 這個方法會設定應用程式與 Identity 提供者 (IP) 互動所需的所有服務。

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>方法會接受回呼來設定驗證應用程式所需的參數。 當您註冊應用程式時，可以從 AAD 設定取得設定應用程式所需的值。

設定是由檔案提供 `wwwroot/appsettings.json` ：

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

範例：

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a>存取權杖範圍

Blazor WebAssembly範本不會自動將應用程式設定為要求安全 API 的存取權杖。 若要布建存取權杖作為登入流程的一部分，請將範圍新增至的預設存取權杖範圍 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> ：

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

使用下列內容指定其他範圍 `AdditionalScopesToConsent` ：

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

如需詳細資訊，請參閱下列 *其他案例* 文章的各節：

* [要求其他存取權杖](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [將權杖附加至傳出要求](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a>登入模式

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a>匯入檔案

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>索引頁面

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>應用程式元件

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin 元件

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay 元件

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>驗證元件

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>其他資源

* <xref:blazor/security/webassembly/additional-scenarios>
* [具有安全預設用戶端之應用程式中未經驗證或未經授權的 web API 要求](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [教學課程：建立 Azure Active Directory B2C 租用戶](/azure/active-directory-b2c/tutorial-create-tenant) \(部分機器翻譯\)
* [Microsoft 身分識別平台文件](/azure/active-directory/develop/)
