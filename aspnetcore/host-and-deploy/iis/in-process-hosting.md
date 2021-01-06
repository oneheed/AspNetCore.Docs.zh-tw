---
title: 使用 IIS 和 ASP.NET Core 的同進程裝載
author: rick-anderson
description: 瞭解 IIS 和 ASP.NET Core 模組的同進程裝載。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: 47dc6f65f398ecce45c563c359dfde6e17d1dc1b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058476"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a>使用 IIS 和 ASP.NET Core 的同進程裝載 

同進程裝載會在與 IIS 背景工作進程相同的進程中執行 ASP.NET Core 應用程式。 因為要求未透過回送介面卡 (將連出網路流量傳回同一部電腦的網路介面) 進行 proxy 處理，所以同處理序裝載會提供優於跨處理序裝載的效能。

下圖說明 IIS、ASP.NET Core 模組和同處理序裝載應用程式之間的關聯性：

![同處理序代管內的 ASP.NET Core 模組案例](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a>啟用同進程裝載

自 ASP.NET Core 3.0 起，所有部署至 IIS 的應用程式預設都會啟用同進程裝載。

若要明確設定同進程裝載的應用程式，請將專案檔中的屬性值設定 `<AspNetCoreHostingModel>` 為， `InProcess` (`.csproj`) ：

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a>一般架構

要求的一般流程如下所示：

1. 要求會從 Web 到達核心模式的 HTTP.sys 驅動程式。
1. 驅動程式會在網站設定的連接埠上將原生要求路由至 IIS，此連接埠通常是 80 (HTTP) 或 443 (HTTPS)。
1. ASP.NET Core 模組會接收原生要求，並將它傳遞給 IIS HTTP 伺服器 (`IISHttpServer`) 。 IIS HTTP 伺服器是 IIS 的同處理序伺服程式實作，可將要求從原生轉換為受控。

IIS HTTP 伺服器處理要求之後：

1. 要求會傳送至 ASP.NET Core 中介軟體管線。
1. 中介軟體管線會處理要求，並將其作為 `HttpContext` 執行個體傳遞至應用程式的邏輯。
1. 應用程式的回應會透過 IIS HTTP 伺服器傳回 IIS。
1. IIS 會將回應傳送到起始該要求的用戶端。

`CreateDefaultBuilder` 藉 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 由呼叫方法來加入實例， <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> 以啟動 [CoreCLR](/dotnet/standard/glossary#coreclr) ，並將應用程式裝載在 IIS 工作者進程內 (`w3wp.exe` 或 `iisexpress.exe`) 。 效能測試指出，相較於將 .NET Core 應用程式裝載於處理序外，並將要求 Proxy 處理至 [Kestrel](xref:fundamentals/servers/kestrel)，裝載於處理序內可提供明顯更高的要求輸送量。

發佈為單一檔案可執行檔的應用程式無法由同處理序裝載模型載入。

## <a name="application-configuration"></a>應用程式設定

若要設定 IIS 選項，請在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> 中加入 <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 的服務設定。 下列範例會停用 AutomaticAuthentication：

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| 選項 | 預設 | 設定 |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | 若為 `true`，IIS 伺服器會設定由 [Windows 驗證](xref:security/authentication/windowsauth)所驗證的 `HttpContext.User`。 若為 `false`，則伺服器僅會對 `HttpContext.User` 提供身分識別，並在 `AuthenticationScheme` 明確要求時回應挑戰。 必須在 IIS 中啟用 Windows 驗證以讓 `AutomaticAuthentication` 作用。 如需詳細資訊，請參閱 [Windows 驗證](xref:security/authentication/windowsauth)。 |
| `AuthenticationDisplayName` | `null` | 設定使用者在登入頁面上看到的顯示名稱。 |
| `AllowSynchronousIO` | `false` | 和是否允許同步 i/o `HttpContext.Request` `HttpContext.Response` 。 |
| `MaxRequestBodySize` | `30000000` | 取得或設定 `HttpRequest` 的要求本文大小上限。 請注意，IIS 本身具有限制 `maxAllowedContentLength`，此限制將在 `IISServerOptions` 中設定 `MaxRequestBodySize` 時處理。 變更 `MaxRequestBodySize` 將不會影響 `maxAllowedContentLength`。 若要增加 `maxAllowedContentLength` ，請在中加入專案， `web.config` 以將設定 `maxAllowedContentLength` 為較高的值。 如需更多詳細資料，請參閱[組態](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)。 |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a>同進程和跨進程裝載之間的差異

同處理序裝載時具有下列特性：

* 使用 IIS HTTP 伺服器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器。 針對同進程， [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) 呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> ：

  * 註冊 `IISHttpServer`。
  * 設定伺服器在 ASP.NET Core 模組後方執行時應該接聽的連接埠和基底路徑。
  * 設定主機以擷取啟動錯誤。

* [ `requestTimeout` 屬性](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element)不適用於同進程裝載。

* 不支援在應用程式之間共用應用程式集區。 每個應用程式使用一個應用程式集區。

* 應用程式的架構 (位元) 和已安裝的執行階段 (x64 或 x86) 必須符合應用程式集區的架構。 例如，針對32位 (x86) 發佈的應用程式必須為其 IIS 應用程式集區啟用32位。 如需詳細資訊，請參閱 [建立 IIS 網站](xref:tutorials/publish-to-iis#create-the-iis-site) 一節。

* 偵測到用戶端中斷連線。 [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A)當用戶端中斷連線時，解除標記就會取消。

* 裝載同處理序時，不會內部呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> 來將使用者初始化。 因此，預設會在未啟動每個驗證之後，使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 實作來轉換宣告。 使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 實作來轉換宣告時，請呼叫 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> 來新增入驗證服務：

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
* 不支援[ (單一檔案) 部署的 Web 套件](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)。
