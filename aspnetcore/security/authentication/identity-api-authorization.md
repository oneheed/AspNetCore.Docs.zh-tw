---
title: ASP.NET Core 上的單一頁面應用程式驗證簡介
author: javiercn
description: 搭配使用 Identity 于 ASP.NET Core 應用程式內的單一頁面應用程式。
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
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
uid: security/authentication/identity/spa
ms.openlocfilehash: 8acc34c88bf62b3da1b920acc7318c94435c100e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051976"
---
# <a name="authentication-and-authorization-for-spas"></a>Spa 的驗證和授權

ASP.NET Core 3.1 和更新版本的範本會在單一頁面應用程式中提供驗證， (使用 API 授權支援的 Spa) 。 ASP.NET Core Identity針對驗證和儲存使用者，會結合[ Identity 伺服器](https://identityserver.io/)來執行 OpenID Connect。

驗證參數已新增至「 **角度** 」和「 **回應** 」專案範本，類似于 Web 應用程式中的驗證參數 **(模型-視圖控制器)** (MVC) 和 **web 應用程式** (Razor 頁面) 專案範本。 允許的參數值為 [ **無** ] 和 [ **個人** ]。 **React.js 和 Redux** 專案範本目前不支援驗證參數。

## <a name="create-an-app-with-api-authorization-support"></a>建立具有 API 授權支援的應用程式

使用者驗證和授權可以搭配使用角度和回應 Spa。 開啟命令 shell，然後執行下列命令：

**角度** ：

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

**回應** ：

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

上述命令會使用包含 SPA 的 *ClientApp* 目錄來建立 ASP.NET Core 應用程式。

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>應用程式 ASP.NET Core 元件的一般描述

下列各節說明在包含驗證支援時，專案的新增專案：

### <a name="startup-class"></a>啟始類別

下列程式碼範例依賴 [AspNetCore. ApiAuthorization。 IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet 套件。 這些範例會使用 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 和擴充方法來設定 API 驗證和授權 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> 。 使用回應或角 SPA 專案範本進行驗證的專案，會包含此封裝的參考。

`Startup`類別具有下列新增專案：

* 在 `Startup.ConfigureServices` 方法內：
  * Identity 使用預設 UI：

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * Identity具有其他 `AddApiAuthorization` helper 方法的伺服器，可在伺服器上設定一些預設 ASP.NET Core 慣例 Identity ：

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * 使用額外的 helper 方法進行驗證，以設定 `AddIdentityServerJwt` 應用程式驗證服務器所產生的 JWT 權杖 Identity ：

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* 在 `Startup.Configure` 方法內：
  * 負責驗證要求認證和在要求內容上設定使用者的驗證中介軟體：

    ```csharp
    app.UseAuthentication();
    ```

  * Identity公開 OpenID Connect 端點的伺服器中介軟體：

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

此 helper 方法會將 Identity 伺服器設定為使用我們支援的設定。 Identity伺服器是一種功能強大且可擴充的架構，可處理應用程式安全性的考慮。 同時也會在最常見的情況下公開不必要的複雜性。 因此，系統會為您提供一組慣例和設定選項，這些選項會被視為良好的起點。 一旦您的驗證需要變更，伺服器的完整功能 Identity 仍然可以自訂驗證以符合您的需求。

### <a name="addno-locidentityserverjwt"></a>新增 Identity ServerJwt

此 helper 方法會設定應用程式的原則配置作為預設驗證處理常式。 原則設定為可讓您 Identity 處理所有路由至 Identity URL 空間 "/" 中之子路徑的要求 Identity 。 會 `JwtBearerHandler` 處理所有其他要求。 此外，此方法會 `<<ApplicationName>>API` 使用 Identity 預設範圍的伺服器來註冊 API 資源 `<<ApplicationName>>API` ，並設定 JWT 持有人權杖中介軟體，以驗證 Identity 伺服器針對應用程式所發出的權杖。

### <a name="weatherforecastcontroller"></a>WeatherForecastController

在 *Controllers\WeatherForecastController.cs* 檔案中，請注意套用 `[Authorize]` 至類別的屬性，指出使用者必須根據預設原則來存取資源，才能獲得授權。 預設授權原則會設定為使用預設的驗證配置，此配置是由上述的原則配置所設定 `AddIdentityServerJwt` ，讓 `JwtBearerHandler` 這類 helper 方法設定為應用程式要求的預設處理常式。

### <a name="applicationdbcontext"></a>[ApplicationdbcoNtext

在 *Data\ApplicationDbCoNtext.cs* 檔案中，請注意在 `DbContext` 中使用相同的， Identity 因為它會 `ApiAuthorizationDbContext` 從) 延伸 (更衍生的類別 `IdentityDbContext` ，以包含伺服器的架構 Identity 。

若要取得資料庫架構的完整控制權，請從其中一個可用類別繼承， Identity `DbContext` 並設定內容以包含 Identity 架構，方法是 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` 在方法上呼叫 `OnModelCreating` 。

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

在 *Controllers\OidcConfigurationController.cs* 檔案中，請注意布建來提供用戶端需要使用之 OIDC 參數的端點。

### appsettings.json

在專案根目錄的檔案中 *appsettings.json* ，有一個新的 `IdentityServer` 區段描述已設定的用戶端清單。 在下列範例中，有一個用戶端。 用戶端名稱會對應到應用程式名稱，並依照慣例對應至 OAuth `ClientId` 參數。 設定檔會指出正在設定的應用程式類型。 它是在內部用來推動可簡化伺服器設定流程的慣例。 有數個可用的設定檔，如 [應用程式佈建檔](#application-profiles) 一節中所述。

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appsettings.Development.js開啟

在專案根目錄的 *appsettings.Development.js* 檔案中，有一個 `IdentityServer` 區段描述用來簽署權杖的金鑰。 部署至生產環境時，必須連同應用程式一起布建和部署金鑰，如「 [部署至生產](#deploy-to-production) 」一節中所述。

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>角度應用程式的一般描述

角度範本中的驗證和 API 授權支援位於其本身在 *ClientApp\src\api-authorization* 目錄中的角度模組。 模組是由下列元素所組成：

* 3個元件：
  * *login* ：處理應用程式的登入流程。
  * *登出。 component* ：處理應用程式的登出流程。
  * *登入-功能表.* t：顯示下列其中一組連結的 widget：
    * 使用者設定檔管理和登出連結（當使用者通過驗證時）。
    * 當使用者未通過驗證時，註冊並登入連結。
* `AuthorizeGuard`可以新增至路由的路由防護，並且需要先驗證使用者，才能造訪路由。
* HTTP 攔截器 `AuthorizeInterceptor` ，會在使用者通過驗證時，將存取權杖附加至以 API 為目標的傳出 HTTP 要求。
* 一種服務 `AuthorizeService` ，可處理驗證程式的較低層級詳細資料，並將已驗證使用者的相關資訊公開給其餘的應用程式以供取用。
* 用來定義與應用程式驗證部分相關聯之路由的角度模組。 它會公開登入功能表元件、攔截器、防護和服務，以從應用程式的其餘部分取用。

## <a name="general-description-of-the-react-app"></a>回應應用程式的一般描述

回應範本中的驗證和 API 授權支援位於 *ClientApp\src\components\api-authorization* 目錄中。 它是由下列元素所組成：

* 4個元件：
  * *Login.js* ：處理應用程式的登入流程。
  * *Logout.js* ：處理應用程式的登出流程。
  * *LoginMenu.js* ：顯示下列其中一組連結的 widget：
    * 使用者設定檔管理和登出連結（當使用者通過驗證時）。
    * 當使用者未通過驗證時，註冊並登入連結。
  * *AuthorizeRoute.js* ：需要先驗證使用者才能轉譯參數中所指出之元件的路由元件 `Component` 。
* 已匯出 `authService` 之類別的實例 `AuthorizeService` ，可處理驗證進程的較低層級詳細資料，並將已驗證使用者的相關資訊公開給其餘的應用程式以供取用。

現在您已瞭解解決方案的主要元件，您可以進一步瞭解應用程式的個別案例。

## <a name="require-authorization-on-a-new-api"></a>在新的 API 上要求授權

根據預設，系統會設定為輕鬆地要求新 Api 的授權。 若要這樣做，請建立新的控制器，並將 `[Authorize]` 屬性新增至控制器類別或控制器內的任何動作。

## <a name="customize-the-api-authentication-handler"></a>自訂 API 驗證處理常式

若要自訂 API 的 JWT 處理常式的設定，請設定其 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> 實例：

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

API 的 JWT 處理常式會引發事件，讓您可以使用來控制驗證程式 `JwtBearerEvents` 。 若要提供 API 授權支援，請 `AddIdentityServerJwt` 註冊其本身的事件處理常式。

若要自訂事件的處理，請視需要使用額外的邏輯來包裝現有的事件處理常式。 例如：

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

在上述程式碼中， `OnTokenValidated` 事件處理常式會取代為自訂的執行。 此實作為：

1. 呼叫 API 授權支援所提供的原始實作為。
1. 執行自己的自訂邏輯。

## <a name="protect-a-client-side-route-angular"></a>保護用戶端路由 (角度) 

保護用戶端路由的方式是將授權防護新增至設定路由時要執行的防護清單。 例如，您可以在 `fetch-data` 主要應用程式角度模組中查看路由的設定方式：

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

請務必注意，保護路由並不會保護實際的端點 (但仍需要套用 `[Authorize]` 屬性) 但它只會防止使用者在未經過驗證時流覽至指定的用戶端路由。

## <a name="authenticate-api-requests-angular"></a> (角) 驗證 API 要求

使用應用程式所定義的 HTTP 用戶端攔截器，對與應用程式一起裝載的 Api 進行驗證要求。

## <a name="protect-a-client-side-route-react"></a>保護用戶端路由 (回應) 

使用 `AuthorizeRoute` 元件而非純文字元件來保護用戶端路由 `Route` 。 例如，請注意如何在 `fetch-data` 元件中設定路由 `App` ：

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

保護路由：

* 不會保護實際的端點 (但仍需要將 `[Authorize]` 屬性套用至該端點) 。
* 只有當使用者未經過驗證時，才會防止使用者流覽至指定的用戶端路由。

## <a name="authenticate-api-requests-react"></a>驗證 API 要求 (回應) 

藉由先從匯入實例來驗證具有回應的要求 `authService` `AuthorizeService` 。 從取出存取權杖 `authService` ，並附加至要求，如下所示。 在回應元件中，這項工作通常是在 `componentDidMount` 生命週期方法中完成，或做為某些使用者互動的結果。

### <a name="import-the-authservice-into-your-component"></a>將 authService 匯入您的元件

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>取得存取權杖並將其附加至回應

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a>部署到生產

若要將應用程式部署至生產環境，必須布建下列資源：

* 用來儲存 Identity 使用者帳戶和伺服器授與的資料庫 Identity 。
* 用於簽署權杖的生產憑證。
  * 此憑證沒有特定需求;它可以是自我簽署的憑證，或透過 CA 授權單位布建的憑證。
  * 您可以透過 PowerShell 或 OpenSSL 等標準工具來產生它。
  * 您可以將它安裝到目的電腦上的憑證存放區中，或部署為具有強式密碼的 *.pfx* 檔案。

### <a name="example-deploy-to-a-non-azure-web-hosting-provider"></a>範例：部署至非 Azure web 主控提供者

在您的 web 主控面板中，建立或載入您的憑證。 然後，在應用程式的檔案中 *appsettings.json* ，修改 `IdentityServer` 區段以包含金鑰詳細資料。 例如：

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "WebHosting",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

在上述範例中：

* `StoreName` 代表用來儲存憑證的憑證存放區名稱。 在此情況下，它會指向 web 主控存放區。
* `StoreLocation` 代表在此案例中從 (載入憑證的位置 `CurrentUser`) 。
* `Name` 對應于憑證的辨別主體。

### <a name="example-deploy-to-azure-app-service"></a>範例：部署至 Azure App Service

本節說明如何使用儲存在憑證存放區中的憑證，將應用程式部署至 Azure App Service。 若要修改應用程式以從憑證存放區載入憑證，當您在稍後的步驟中設定 Azure 入口網站中的應用程式時，需要標準層服務方案或更高的版本。

在應用程式的檔案中 *appsettings.json* ，修改 `IdentityServer` 區段以包含金鑰詳細資料：

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* 存放區名稱代表儲存憑證的憑證存放區名稱。 在此情況下，它會指向個人使用者存放區。
* 存放區位置代表從 (或) 載入憑證的 `CurrentUser` 位置 `LocalMachine` 。
* 憑證上的名稱屬性與憑證的辨別主體相對應。

若要部署至 Azure App Service，請遵循將 [應用程式部署至 Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)中的步驟，其中說明如何建立必要的 Azure 資源，並將應用程式部署至生產環境。

遵循上述指示之後，應用程式會部署至 Azure，但還無法運作。 應用程式所使用的憑證必須在 Azure 入口網站中設定。 找出憑證的憑證指紋，並依照 [載入您的憑證](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)中所述的步驟進行。

雖然這些步驟提及 SSL，但 Azure 入口網站中有一個 **私用憑證** 區段，您可以在其中上傳佈建的憑證以與應用程式搭配使用。

在 Azure 入口網站中設定應用程式和應用程式的設定之後，請在入口網站中重新開機應用程式。

## <a name="other-configuration-options"></a>其他設定選項

Identity使用一組慣例、預設值和增強功能來支援 API 授權，以簡化 spa 的體驗。 不用說， Identity 如果 ASP.NET Core 的整合不涵蓋您的案例，伺服器的完整威力就可以在幕後使用。 ASP.NET Core 支援著重于「第一方」應用程式，而所有應用程式都是由我們的組織建立和部署。 因此，不會針對同意或同盟等專案提供支援。 在這些情況下，請使用 Identity Server 並遵循其檔。

### <a name="application-profiles"></a>應用程式佈建檔

應用程式佈建檔是針對進一步定義其參數的應用程式預先定義的設定。 目前支援下列設定檔：

* `IdentityServerSPA`：表示 Identity 以單一單位的形式裝載于伺服器的 SPA。
  * `redirect_uri`預設為 `/authentication/login-callback` 。
  * `post_logout_redirect_uri`預設為 `/authentication/logout-callback` 。
  * 這組範圍包括 `openid` 、 `profile` 和針對應用程式中的 api 所定義的每個範圍。
  * 一組允許的 OIDC 回應類型為 `id_token token` 個別或個別 (`id_token` ， `token`) 。
  * 允許的回應模式為 `fragment` 。
* `SPA`：代表未裝載于伺服器的 SPA Identity 。
  * 這組範圍包括 `openid` 、 `profile` 和針對應用程式中的 api 所定義的每個範圍。
  * 一組允許的 OIDC 回應類型為 `id_token token` 個別或個別 (`id_token` ， `token`) 。
  * 允許的回應模式為 `fragment` 。
* `IdentityServerJwt`：表示與伺服器一起主控的 API Identity 。
  * 應用程式會設定為具有預設為應用程式名稱的單一範圍。
* `API`：代表未裝載在伺服器上的 API Identity 。
  * 應用程式會設定為具有預設為應用程式名稱的單一範圍。

### <a name="configuration-through-appsettings"></a>透過 AppSettings 設定

將應用程式新增至或的清單，以透過設定系統設定這些應用程式 `Clients` `Resources` 。

設定每個用戶端 `redirect_uri` 和 `post_logout_redirect_uri` 屬性，如下列範例所示：

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

設定資源時，您可以設定資源的範圍，如下所示：

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a>透過程式碼設定

您也可以透過程式碼 `AddApiAuthorization` ，使用會採取動作來設定選項的多載，來設定用戶端和資源。

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a>其他資源

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
