---
title: Blazor WebAssembly使用 Microsoft 帳戶保護 ASP.NET Core 的獨立應用程式
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: a073e1d9e7e9dc1956ad9df5e4e9cf60573be726
ms.sourcegitcommit: dfea24471f4f3d7904faa92fe60c000853bddc3b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504641"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>Blazor WebAssembly使用 Microsoft 帳戶保護 ASP.NET Core 的獨立應用程式

由 [Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)

若要建立使用[Microsoft 帳戶搭配 Azure Active Directory (AAD) ](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)進行驗證的[獨立 Blazor WebAssembly 應用程式](xref:blazor/hosting-models#blazor-webassembly)：

[建立 AAD 租使用者和 web 應用程式](/azure/active-directory/develop/v2-overview)

在 Azure 入口網站的**Azure Active Directory**  >  **應用程式註冊**區域中註冊 AAD 應用程式：

1. 提供應用程式的**名稱** (例如， ** Blazor 獨立 AAD Microsoft 帳戶**) 。
1. 在 [ **支援的帳戶類型**] 中，選取 **任何組織目錄中的帳戶**。
1. 將 [重新 **導向 URI** ] 下拉式清單保持設定為 [ **Web** ]，並提供下列重新導向 uri： `https://localhost:{PORT}/authentication/login-callback` 。 在 Kestrel 上執行之應用程式的預設埠是5001。 如果應用程式是在不同的 Kestrel 埠上執行，請使用應用程式的埠。 針對 IIS Express，可在 [ **調試** 程式] 面板的應用程式屬性中找到應用程式隨機產生的埠。 由於應用程式目前不存在，且 IIS Express 埠未知，因此在建立應用程式之後，請返回此步驟，並更新重新導向 URI。 本主題稍後會出現一個批註，提醒 IIS Express 使用者更新重新導向 URI。
1. 停用 **[授與系統**  >  **管理員同意 openid 和 offline_access 許可權**] 核取方塊。
1. 選取 [註冊]。

記錄應用程式 (用戶端) 識別碼 (例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`) 。

在 [**驗證**  >  **平臺**設定]  >  **Web**：

1. 確認的重新 **導向 URI** `https://localhost:{PORT}/authentication/login-callback` 存在。
1. 針對 **[隱含授**與]，選取 **存取權杖** 和 **識別碼權杖**的核取方塊。
1. 此體驗可接受應用程式的其餘預設值。
1. 選取 [儲存] 按鈕。

建立應用程式。 使用稍早所記錄的資訊來取代下列命令中的預留位置，並在命令命令介面中執行下列命令：

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common" -o {APP NAME}
```

| 預留位置   | Azure 入口網站名稱       | 範例                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | 應用程式 (用戶端) 識別碼 | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |

使用選項指定的輸出位置會 `-o|--output` 建立專案資料夾（如果不存在），而且會成為應用程式名稱的一部分。

> [!NOTE]
> 在 Azure 入口網站中，會針對**Authentication**  >  **Platform configurations**  >  **Web**  >  使用預設設定在 Kestrel 伺服器上執行的應用程式，將應用程式的驗證平臺設定 Web 重新**導向 URI**設定為使用埠5001。
>
> 如果應用程式是在隨機的 IIS Express 埠上執行，則可以在 [ **調試** 程式] 面板的應用程式屬性中找到應用程式的埠。
>
> 如果先前未使用應用程式的已知埠設定埠，請返回 Azure 入口網站中的應用程式註冊，然後以正確的埠更新重新導向 URI。

建立應用程式之後，您應該能夠：

* 使用 Microsoft 帳戶登入應用程式。
* 要求 Microsoft Api 的存取權杖。 如需詳細資訊，請參閱
  * [存取權杖範圍](#access-token-scopes)
  * [快速入門：設定應用程式以公開 Web api](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。

## <a name="authentication-package"></a>驗證套件

建立應用程式以使用 () 的公司或學校帳戶時 `SingleOrg` ，應用程式會自動收到 [Microsoft 驗證程式庫](/azure/active-directory/develop/msal-overview) 的套件參考 ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) 。 此套件提供一組基本類型，可協助應用程式驗證使用者，並取得權杖以呼叫受保護的 Api。

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
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>方法會接受回呼來設定驗證應用程式所需的參數。 當您註冊應用程式時，可以從 AAD 設定取得設定應用程式所需的值。

設定是由檔案提供 `wwwroot/appsettings.json` ：

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

範例：

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
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

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

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

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>其他資源

* <xref:blazor/security/webassembly/additional-scenarios>
* [具有安全預設用戶端之應用程式中未經驗證或未經授權的 web API 要求](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* [快速入門：使用 Microsoft 身分識別平台來註冊應用程式](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [快速入門：設定應用程式以公開 Web API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
