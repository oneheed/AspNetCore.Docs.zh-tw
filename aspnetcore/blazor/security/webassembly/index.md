---
title: 安全 ASP.NET Core Blazor WebAssembly
author: guardrex
description: 瞭解如何將 Blazor WebAssemlby 應用程式保護為單一頁面應用程式， (spa) 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/16/2020
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
uid: blazor/security/webassembly/index
ms.openlocfilehash: e1af8f1de61edd934505a44e75ea07e0f09a67b5
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90592952"
---
# <a name="secure-aspnet-core-no-locblazor-webassembly"></a>安全 ASP.NET Core Blazor WebAssembly

[Javier Calvarro Nelson](https://github.com/javiercn)

Blazor WebAssembly 應用程式的保護方式與單一頁面應用程式 (Spa) 相同。 有幾種方法可以驗證使用者的 Spa，但最常見且最完整的方法是使用以 [OAuth 2.0 通訊協定](https://oauth.net/)為基礎的執行，例如 [OpenID Connect (OIDC) ](https://openid.net/connect/)。

## <a name="authentication-library"></a>驗證程式庫

Blazor WebAssembly 支援透過程式庫使用 OIDC 來驗證和授權應用程式 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 。 此程式庫提供一組基本類型，可針對 ASP.NET Core 後端進行順暢的驗證。 此程式庫會 ASP.NET Core Identity 與以[ Identity 伺服器](https://identityserver.io/)為基礎的 API 授權支援整合。 此程式庫可以針對支援 OIDC 的任何協力廠商 Identity 提供者進行驗證 (IP) ，這稱為 OpenID 提供者 (OP) 。

中的驗證支援 Blazor WebAssembly 是建立在程式庫的上層 `oidc-client.js` ，用來處理基礎驗證通訊協定的詳細資料。

有其他驗證 Spa 的選項存在，例如使用 SameSite cookie s。 但是，的工程設計 Blazor WebAssembly 會在 OAuth 和 OIDC 上進行結算，作為應用程式中驗證的最佳選項 Blazor WebAssembly 。 以 JSON Web 權杖為基礎的[權杖型驗證](xref:security/anti-request-forgery#token-based-authentication) [ (jwt) ](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)基於功能和安全性理由選擇了 [以超過[ cookie 基礎的驗證](xref:security/anti-request-forgery#cookie-based-authentication)]：

* 使用以權杖為基礎的通訊協定提供較小的攻擊面區，因為權杖不會在所有要求中傳送。
* 伺服器端點不需要保護 [跨網站偽造要求 (CSRF) ](xref:security/anti-request-forgery) ，因為權杖是明確傳送的。 這可讓您將 Blazor WebAssembly 應用程式與 MVC 或 Razor pages 應用程式一起裝載。
* 權杖的許可權比 cookie s 窄。 例如，除非明確執行這類功能，否則無法使用權杖來管理使用者帳戶或變更使用者的密碼。
* 權杖有短暫的存留期（預設為一小時），這會限制攻擊時間範圍。 您也可以隨時撤銷權杖。
* 獨立的 Jwt 供應專案可保證用戶端和伺服器有關驗證程式。 例如，用戶端的方法是偵測並驗證其所接收的權杖是否合法，並在指定的驗證程式中發出。 如果協力廠商嘗試在驗證程式的過程中切換權杖，用戶端可以偵測到切換的權杖，並避免使用它。
* OAuth 和 OIDC 的權杖不依賴使用者代理程式正確運作，以確保應用程式的安全。
* 以權杖為基礎的通訊協定（例如 OAuth 和 OIDC）可讓您使用相同的安全性特性集來驗證和授權託管和獨立應用程式。

## <a name="authentication-process-with-oidc"></a>使用 OIDC 進行驗證流程

此連結 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 庫提供數個基本專案，以使用 OIDC 來執行驗證和授權。 大致來說，驗證的運作方式如下：

* 當匿名使用者選取登入按鈕或要求套用屬性的頁面時 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) ，系統會將使用者重新導向至應用程式的登入頁面， (`/authentication/login`) 。
* 在 [登入] 頁面中，驗證程式庫會準備重新導向至授權端點。 授權端點位於 Blazor WebAssembly 應用程式之外，而且可以裝載于不同的來源。 端點負責判斷使用者是否已通過驗證，並在回應中發出一或多個權杖。 驗證程式庫會提供登入回呼，以接收驗證回應。
  * 如果使用者未經過驗證，系統會將使用者重新導向至基礎驗證系統，這通常是 ASP.NET Core Identity 。
  * 如果使用者已通過驗證，授權端點會產生適當的權杖，並將瀏覽器重新導向回登入回呼端點 (`/authentication/login-callback`) 。
* 當 Blazor WebAssembly 應用程式載入登入回呼端點 (`/authentication/login-callback`) 時，會處理驗證回應。
  * 如果驗證程式順利完成，就會驗證使用者，並選擇性地將其傳回給使用者要求的原始受保護 URL。
  * 如果驗證程式因任何原因而失敗，則會將使用者傳送至登入失敗頁面 (`/authentication/login-failed`) ，並顯示錯誤。

## <a name="authentication-component"></a>`Authentication` 元件

`Authentication`元件 (`Pages/Authentication.razor`) 會處理遠端驗證作業，並允許應用程式：

* 設定驗證狀態的應用程式路由。
* 設定驗證狀態的 UI 內容。
* 管理驗證狀態。

驗證動作（例如註冊或登入使用者）會傳遞至 Blazor 架構的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> 元件，以保存並控制驗證作業之間的狀態。

如需詳細資訊和範例，請參閱 <xref:blazor/security/webassembly/additional-scenarios>。

## <a name="authorization"></a>授權

在 Blazor WebAssembly 應用程式中，可以略過授權檢查，因為使用者可以修改所有用戶端程式代碼。 這同樣也適用於所有的用戶端應用程式技術，包括 JavaScript SPA 架構或任何作業系統的原生應用程式。

**請一律在由您用戶端應用程式所存取之任何 API 端點內的伺服器上執行授權檢查。**

## <a name="require-authorization-for-the-entire-app"></a>需要整個應用程式的授權

使用下列其中一種方法，將 ([API 檔](xref:System.Web.Mvc.AuthorizeAttribute)) 的[ `[Authorize]` 屬性](xref:blazor/security/index#authorize-attribute)套用至應用程式的每個 Razor 元件：

* 使用檔案中的指示詞 [`@attribute`](xref:mvc/views/razor#attribute) `_Imports.razor` ：

  ```razor
  @using Microsoft.AspNetCore.Authorization
  @attribute [Authorize]
  ```

* 將屬性新增至 Razor 資料夾中的每個元件 `Pages` 。

> [!NOTE]
> <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType>不支援使用將設定為 <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> 原則**not** 。

## <a name="refresh-tokens"></a>重新整理權杖

重新整理權杖無法在應用程式中保護用戶端 Blazor WebAssembly 。 因此，不應將重新整理權杖傳送至應用程式以供直接使用。

重新整理權杖可由裝載解決方案中的伺服器端應用程式維護及使用， Blazor WebAssembly 以存取協力廠商 api。 如需詳細資訊，請參閱<xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>。

## <a name="establish-claims-for-users"></a>為使用者建立宣告

應用程式通常需要以伺服器的 web API 呼叫為基礎的使用者宣告。 例如，宣告經常用來在應用程式中 [建立授權](xref:blazor/security/index#authorization) 。 在這些情況下，應用程式會要求存取權杖以存取服務，並使用權杖來取得宣告的使用者資料。 如需範例，請參閱下列資源：

* [其他案例：自訂使用者](xref:blazor/security/webassembly/additional-scenarios#customize-the-user)
* <xref:blazor/security/webassembly/aad-groups-roles>

## <a name="implementation-guidance"></a>實作指引

本 *總覽* 底下的文章提供在 Blazor WebAssembly 應用程式中針對特定提供者驗證使用者的相關資訊。

獨立 Blazor WebAssembly 應用程式：

* [OIDC 提供者和 WebAssembly Authentication Library 的一般指引](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [Microsoft 帳戶](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Azure Active Directory (AAD)](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

託管 Blazor WebAssembly 應用程式：

* [Azure Active Directory (AAD)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [Identity 伺服器](xref:blazor/security/webassembly/hosted-with-identity-server)

如需設定的進一步指引，請參閱 <xref:blazor/security/webassembly/additional-scenarios> 。
