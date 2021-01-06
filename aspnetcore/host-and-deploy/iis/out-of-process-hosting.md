---
title: 使用 IIS 和 ASP.NET Core 的跨進程裝載
author: rick-anderson
description: 瞭解 IIS 和 ASP.NET Core 模組的跨進程裝載。
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 78ead27bd1373237d1c0a48655d73a41a0a72279
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060413"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a>使用 IIS 和 ASP.NET Core 的跨進程裝載 

由於 ASP.NET Core apps 會在與 IIS 背景工作進程不同的進程中執行，因此 ASP.NET Core 模組會處理進程管理。 此模組會在第一個要求到達時啟動 ASP.NET Core 應用程式的處理序，並在應用程式關閉或損毀時將它重新啟動。 此行為基本上與執行同處理序，並由 [Windows 處理序啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 所管理的應用程式相同。

下圖說明 IIS、ASP.NET Core 模組和跨處理序裝載應用程式之間的關聯性：

![非同處理序代管內的 ASP.NET Core 模組案例](index/_static/ancm-outofprocess.png)

1. 要求會從 Web 到達核心模式的 HTTP.sys 驅動程式。
1. 驅動程式會在網站設定的埠上將要求路由傳送至 IIS。 設定的埠通常是 80 (HTTP) 或 443 (HTTPS) 。
1. 模組會將要求轉送到應用程式的隨機埠上的 Kestrel。 隨機埠不是80或443。

<!-- make this a bullet list -->
ASP.NET Core 模組會在啟動時透過環境變數指定埠。 延伸模組會設定 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> 要接聽的伺服器 `http://localhost:{PORT}` 。 將會執行額外檢查，不是源自模組的要求都會遭到拒絕。 模組不支援 HTTPS 轉送。 即使 IIS 透過 HTTPS 接收，要求還是會透過 HTTP 轉送。

在 Kestrel 收取來自模組的要求之後，會將要求轉送到 ASP.NET Core 中介軟體管線。 中介軟體管線會處理要求，並將其作為 `HttpContext` 執行個體傳遞至應用程式的邏輯。 IIS Integration 新增的中介軟體會更新配置、遠端 IP 和帳戶路徑基底，以將要求轉送至 Kestrel。 應用程式的回應會傳回 IIS，然後將其轉送回起始要求的 HTTP 用戶端。

如需 ASP.NET Core 模組組態指南，請參閱 <xref:host-and-deploy/aspnet-core-module>。

如需代管的詳細資訊，請參閱[在 ASP.NET Core 中代管](xref:fundamentals/index#host)。

## <a name="application-configuration"></a>應用程式設定

### <a name="enable-the-iisintegration-components"></a>啟用 IISIntegration 元件

在 () 中建立主機時 `CreateHostBuilder` `Program.cs` ，請呼叫 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 來啟用 IIS 整合：

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

如需 `CreateDefaultBuilder` 的詳細資訊，請參閱 <xref:fundamentals/host/generic-host#default-builder-settings>。


**跨處理序裝載模型**

若要設定 IIS 選項，請在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> 中加入 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服務設定。 下列範例會防止應用程式填入 `HttpContext.Connection.ClientCertificate`：

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| 選項                         | 預設 | 設定 |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | 若為 `true`，[IIS 整合中介軟體](#enable-the-iisintegration-components)會設定由 [Windows 驗證](xref:security/authentication/windowsauth)所驗證的 `HttpContext.User`。 如果為 `false`，則驗證中介軟體僅針對 `HttpContext.User` 提供身分識別，並在游 `AuthenticationScheme` 提出明確要求時回應挑戰。 必須在 IIS 中啟用 Windows 驗證以讓 `AutomaticAuthentication` 作用。 如需詳細資訊，請參閱 [Windows 驗證](xref:security/authentication/windowsauth)主題。 |
| `AuthenticationDisplayName`    | `null`  | 設定使用者在登入頁面上看到的顯示名稱。 |
| `ForwardClientCertificate`     | `true`  | 如果為 `true` 且 `MS-ASPNETCORE-CLIENTCERT` 要求標頭已存在，則會填入 `HttpContext.Connection.ClientCertificate`。 |


### <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy 伺服器和負載平衡器案例

[IIS 整合中介軟體](#enable-the-iisintegration-components)和 ASP.NET Core 模組會設定為轉寄：

*  (HTTP/HTTPS) 的配置。
* 發出要求的遠端 IP 位址。

[IIS 整合中介軟體](#enable-the-iisintegration-components)會設定轉送的標頭中介軟體。

其他 Proxy 伺服器和負載平衡器後方託管的應用程式可能需要其他設定。 如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。


### <a name="out-of-process-hosting-model"></a>跨處理序裝載模型

若要設定跨進程裝載的應用程式，請將專案檔中的屬性值設定 `<AspNetCoreHostingModel>` 為， `OutOfProcess` (`.csproj`) ：

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

同進程裝載是使用設定 `InProcess` ，這是預設值。

的值 `<AspNetCoreHostingModel>` 不區分大小寫，因此 `inprocess` 和 `outofprocess` 都是有效的值。

使用 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器而不是 IIS HTTP 伺服器 (`IISHttpServer`)。

針對跨進程， [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) 呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> ：

* 設定伺服器在 ASP.NET Core 模組後方執行時應該接聽的連接埠和基底路徑。
* 設定主機以擷取啟動錯誤。

### <a name="process-name"></a>程序名稱

`Process.GetCurrentProcess().ProcessName` 會報告 `w3wp`/`iisexpress` (同處理序) 或 `dotnet` (跨處理序)。

許多如 Windows 驗證等原生模組仍在使用中。 若要深入了解搭配 ASP.NET Core 模組的使用中 IIS 模組，請參閱<xref:host-and-deploy/iis/modules>。

ASP.NET Core 模組也可以：

* 設定背景工作處理序的環境變數。
* 將 stdout 輸出記錄到檔案儲存區，以針對啟動問題進行疑難排解。
* 轉送 Windows 驗證權杖。
