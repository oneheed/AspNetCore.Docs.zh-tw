---
title: ASP.NET Core 中的 .NET 泛型主機
author: rick-anderson
description: 在 ASP.NET Core 應用程式中使用 .NET Core 泛型主機。  一般主機負責應用程式啟動和存留期管理。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: ceb766cae1f8a1735f5b578a44f0c481927e47a5
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586744"
---
# <a name="net-generic-host-in-aspnet-core"></a>ASP.NET Core 中的 .NET 泛型主機

::: moniker range=">= aspnetcore-5.0"

ASP.NET 核心範本會建立 .NET Core 泛型主機 (<xref:Microsoft.Extensions.Hosting.HostBuilder>) 。

本主題提供在 ASP.NET Core 中使用 .NET 泛型主機的相關資訊。 如需在主控台應用程式中使用 .NET 泛型主機的詳細資訊，請參閱 [.Net 泛型主機](/dotnet/core/extensions/generic-host)。

## <a name="host-definition"></a>主機定義

「主機」是封裝所有應用程式資源的物件，例如：

* 相依性插入 (DI)
* 記錄
* 組態
* `IHostedService` 實作

當主機啟動時，它會呼叫 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> <xref:Microsoft.Extensions.Hosting.IHostedService> 服務容器的託管服務集合中註冊的每個執行。 在 Web 應用程式中，其中一個 `IHostedService` 實作是一種 Web 服務，負責啟動 [HTTP 伺服器實作](xref:fundamentals/index#servers)。

在單一物件中包含所有應用程式相互依存資源的主要理由便是生命週期管理：控制應用程式的啟動及順利關機。

## <a name="set-up-a-host"></a>設定主機

主機通常由 `Program` 類別的程式碼來設定、建置並執行。 `Main` 方法：

* 呼叫 `CreateHostBuilder` 方法來建立及設定建立器物件。
* 在建立器物件上呼叫 `Build` 和 `Run` 方法。

ASP.NET Core web 範本會產生下列程式碼來建立主機：

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

下列程式碼會建立非 HTTP 工作負載，並將其 `IHostedService` 執行新增至 DI 容器。

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

針對 HTTP 工作負載，`Main` 方法相同，但 `CreateHostBuilder` 會呼叫 `ConfigureWebHostDefaults`：

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

如果應用程式使用 Entity Framework Core，請勿變更 `CreateHostBuilder` 方法的名稱或簽章。 [Entity Framework Core 工具](/ef/core/miscellaneous/cli/)預期找到 `CreateHostBuilder` 方法，其在不執行應用程式的情況下設定主機。 如需詳細資訊，請參閱[設計階段 DbContext 建立](/ef/core/miscellaneous/cli/dbcontext-creation)。

## <a name="default-builder-settings"></a>預設建立器設定

<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 方法：

* 將 [內容根目錄](xref:fundamentals/index#content-root) 設定為所傳回的路徑 <xref:System.IO.Directory.GetCurrentDirectory*> 。
* 從下列項目載入主機組態：
  * 前面加上的環境變數 `DOTNET_` 。
  * 命令列引數。
* 從下列項目載入應用程式組態：
  * *appsettings.json*.
  * *appsettings.{Environment}.json*
  * 應用程式在 `Development` 環境中執行時的[使用者密碼](xref:security/app-secrets)。
  * 環境變數。
  * 命令列引數。
* 新增下列[記錄](xref:fundamentals/logging/index)提供者：
  * 主控台
  * 偵錯
  * EventSource
  * EventLog (僅當在 Windows 上執行時)
* 環境為開發時，會啟用[範圍驗證](xref:fundamentals/dependency-injection#scope-validation)和[相依性驗證](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)。

<xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 方法：

* 從前面加上的環境變數載入主機設定 `ASPNETCORE_` 。
* 會將 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器設為網頁伺服器，並使用應用程式的主機組態提供者進行設定。 如需 Kestrel 伺服器的預設選項，請參閱 <xref:fundamentals/servers/kestrel/options>。
* 新增[主機篩選中介軟體](xref:fundamentals/servers/kestrel/host-filtering)。
* 如果等於，則新增 [轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` 。
* 啟用 IIS 整合。 如需 IIS 預設選項，請參閱 <xref:host-and-deploy/iis/index#iis-options>。

本文稍後的[＜設定所有應用程式類型＞](#settings-for-all-app-types)和[＜Web 應用程式設定＞](#settings-for-web-apps)章節，將說明如何覆寫預設的建立器設定。

## <a name="framework-provided-services"></a>架構提供的服務

下列服務會自動註冊：

* [IHostApplicationLifetime](#ihostapplicationlifetime)
* [IHostLifetime](#ihostlifetime)
* [IHostEnvironment / IWebHostEnvironment](#ihostenvironment)

如需架構所提供之服務的詳細資訊，請參閱 <xref:fundamentals/dependency-injection#framework-provided-services> 。

## <a name="ihostapplicationlifetime"></a>IHostApplicationLifetime

將 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (先前稱為 `IApplicationLifetime`) 服務插入任何類別來處理啟動後和順利關機工作。 介面上的三個屬性是用於註冊應用程式啟動和應用程式關閉事件處理程序方法的取消語彙基元。 介面也包括 `StopApplication` 方法。

下列範例是 `IHostedService` 註冊事件的實作為 `IHostApplicationLifetime` ：

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>IHostLifetime

<xref:Microsoft.Extensions.Hosting.IHostLifetime> 實作會控制主機啟動及停止的時機。 會使用最後一個註冊的實作。

`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 是預設的 `IHostLifetime` 實作。 `ConsoleLifetime`:

* 接聽<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM，並呼叫 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> 以啟動關機程式。
* 會解除封鎖 [RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等延伸模組。

## <a name="ihostenvironment"></a>IHostEnvironment

將服務插入至 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 類別，以取得下列設定的相關資訊：

* [ApplicationName](#applicationname)
* [EnvironmentName](#environmentname)
* [ContentRootPath](#contentroot)

Web apps 會執行 `IWebHostEnvironment` 介面，此介面會繼承 `IHostEnvironment` 並新增 [WebRootPath](#webroot)。

## <a name="host-configuration"></a>主機組態

主機組態用於 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 實作的屬性。

主機組態位於 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 內的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration)。 在 `ConfigureAppConfiguration` 之後，應用程式組態會取代 `HostBuilderContext.Configuration`。

若要新增主機組態，請呼叫 `IHostBuilder` 上的 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>。 `ConfigureHostConfiguration` 可以多次呼叫，其結果是累加的。 主機會使用指定索引鍵上最後設定值的任何選項。

包含前置詞 `DOTNET_` 和命令列引數的環境變數提供者 `CreateDefaultBuilder` 。 針對 Web 應用程式，會新增具有前置詞 `ASPNETCORE_` 的環境變數提供者。 讀取環境變數時，就會移除前置詞。 例如，`ASPNETCORE_ENVIRONMENT` 的環境變數值會變成 `environment` 索引鍵的主機組態值。

下列範例會建立主機組態：

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>應用程式組態

應用程式組態的建立方式是在 `IHostBuilder` 上呼叫 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>。 `ConfigureAppConfiguration` 可以多次呼叫，其結果是累加的。 應用程式會使用指定索引鍵上最後設定值的任何選項。 

由 `ConfigureAppConfiguration` 建立的組態位於 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*)，可用於後續作業並用作 DI 中的服務。 主機組態也會新增至應用程式組態。

如需詳細資訊，請參閱 [ASP.NET Core 中的組態](xref:fundamentals/configuration/index#configureappconfiguration)。

## <a name="settings-for-all-app-types"></a>所有應用程式類型的設定

本節列出適用於 HTTP 和非 HTTP 工作負載的主機設定。 根據預設，用來設定這些設定的環境變數可以具有 `DOTNET_` 或 `ASPNETCORE_` 前置詞。 如需詳細資訊，請參閱預設建立器 [設定](#default-builder-settings) 一節。

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a>ApplicationName

[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 屬性是在主機建構期間從主機組態當中設定。

**機碼**：`applicationName`  
**類型**：`string`  
**預設值**：包含應用程式進入點的元件名稱。  
**環境變數**： `<PREFIX_>APPLICATIONNAME`

若要設定此值，請使用環境變數。 

### <a name="contentroot"></a>ContentRoot

[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 屬性會決定主機從哪裡開始搜尋內容檔案。 如果路徑不存在，就無法啟動主機。

**機碼**：`contentRoot`  
**類型**：`string`  
**預設值**：應用程式元件所在的資料夾。  
**環境變數**： `<PREFIX_>CONTENTROOT`

若要設定此值，請使用環境變數或呼叫 `IHostBuilder` 上的 `UseContentRoot`：

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

如需詳細資訊，請參閱

* [基本概念：內容根目錄](xref:fundamentals/index#content-root)
* [WebRoot](#webroot)

### <a name="environmentname"></a>EnvironmentName

[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 屬性可以設為任何值。 架構定義的值包括 `Development`、`Staging` 和 `Production`。 值不區分大小寫。

**機碼**：`environment`  
**類型**：`string`  
**預設值**： `Production`  
**環境變數**： `<PREFIX_>ENVIRONMENT`

若要設定此值，請使用環境變數或呼叫 `IHostBuilder` 上的 `UseEnvironment`：

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>ShutdownTimeout

[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) 會設定 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的逾時。 預設值是五秒鐘。  在逾時期間，主機會：

* 觸發 [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)。
* 嘗試停止託管的服務，並記錄無法停止之服務的錯誤。

如果在所有的託管服務停止之前逾時期限已到期，則應用程式關閉時，會停止任何剩餘的作用中服務。 即使服務尚未完成處理也會停止。 如果服務需要更多時間才能停止，請增加逾時。

**機碼**：`shutdownTimeoutSeconds`  
**類型**：`int`  
**預設值**：5秒  
**環境變數**： `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

若要設定此值，請使用環境變數或設定 `HostOptions`。 下列範例將逾時設為 20 秒：

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a>變更時停用應用程式設定重載

依 [預設](xref:fundamentals/configuration/index#default)， *appsettings.json* 和 *appsettings。環境}。* 當檔案變更時，會重載 json。 若要停用 ASP.NET Core 5.0 或更新版本中的重載行為，請將機 `hostBuilder:reloadConfigOnChange` 碼設定為 `false` 。

**機碼**：`hostBuilder:reloadConfigOnChange`  
**類型**： `bool` (`true` 或 `1`)   
**預設值**： `true`  
**命令列引數**： `hostBuilder:reloadConfigOnChange`  
**環境變數**： `<PREFIX_>hostBuilder:reloadConfigOnChange`

> [!WARNING]
> 冒號 (`:`) 分隔符號不適用於所有平臺上的環境變數階層式索引鍵。 如需詳細資訊，請參閱 [環境變數](xref:fundamentals/configuration/index#environment-variables)。

## <a name="settings-for-web-apps"></a>Web 應用程式的設定

某些主機設定僅適用於 HTTP 工作負載。 根據預設，用來設定這些設定的環境變數可以具有 `DOTNET_` 或 `ASPNETCORE_` 前置詞。

`IWebHostBuilder` 上的擴充方法適用於這些設定。 示範如何呼叫擴充方法的程式碼範例假設 `webBuilder` 是 `IWebHostBuilder` 的執行個體，如下列範例所示：

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a>CaptureStartupErrors

當它為 `false` 時，啟動期間發生的錯誤會導致主機結束。 當它為 `true` 時，主機會擷取啟動期間的例外狀況，並嘗試啟動伺服器。

**機碼**：`captureStartupErrors`  
**類型**： `bool` (`true` 或 `1`)   
**預設值**：預設為 `false`，除非應用程式執行時在 IIS 背後有 Kestrel，此時預設值即為 `true`。  
**環境變數**： `<PREFIX_>CAPTURESTARTUPERRORS`

若要設定此值，請使用組態或呼叫 `CaptureStartupErrors`：

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>DetailedErrors

啟用時 (或當環境為 `Development` 時)，應用程式會擷取詳細錯誤。

**機碼**：`detailedErrors`  
**類型**： `bool` (`true` 或 `1`)   
**預設值**： `false`  
**環境變數**： `<PREFIX_>_DETAILEDERRORS`

若要設定此值，請使用組態或呼叫 `UseSetting`：

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>HostingStartupAssemblies

在啟動時載入的裝載啟動組件字串，以分號分隔。 雖然設定值會預設為空字串，但裝載啟動組件一律會包含應用程式的組件。 提供裝載啟動組件時，它們會新增至應用程式的組件，以便在應用程式在啟動時建置其通用服務時載入。

**機碼**：`hostingStartupAssemblies`  
**類型**：`string`  
**預設值**：空字串  
**環境變數**： `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

若要設定此值，請使用組態或呼叫 `UseSetting`：

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostingStartupExcludeAssemblies

在啟動時排除以分號分隔的裝載啟動組件字串。

**機碼**：`hostingStartupExcludeAssemblies`  
**類型**：`string`  
**預設值**：空字串  
**環境變數**： `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

若要設定此值，請使用組態或呼叫 `UseSetting`：

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a>HTTPS_Port

HTTPS 重新導向連接埠。 用於[強制 HTTPS](xref:security/enforcing-ssl)。

**機碼**：`https_port`  
**類型**：`string`  
**預設** 值：未設定預設值。  
**環境變數**： `<PREFIX_>HTTPS_PORT`

若要設定此值，請使用組態或呼叫 `UseSetting`：

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>PreferHostingUrls

指出主機是否應接聽使用設定的 Url， `IWebHostBuilder` 而不是使用執行時所設定的 url `IServer` 。

**機碼**：`preferHostingUrls`  
**類型**： `bool` (`true` 或 `1`)   
**預設值**： `true`  
**環境變數**： `<PREFIX_>_PREFERHOSTINGURLS`

若要設定此值，請使用環境變數或呼叫 `PreferHostingUrls`：

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>PreventHostingStartup

可防止自動載入裝載啟動組件，包括應用程式組件所設定的裝載啟動組件。 如需詳細資訊，請參閱<xref:fundamentals/configuration/platform-specific-configuration>。

**機碼**：`preventHostingStartup`  
**類型**： `bool` (`true` 或 `1`)   
**預設值**： `false`  
**環境變數**： `<PREFIX_>_PREVENTHOSTINGSTARTUP`

若要設定此值，請使用環境變數或呼叫 `UseSetting`：

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>StartupAssembly

要搜尋 `Startup` 類別的組件。

**機碼**：`startupAssembly`  
**類型**：`string`  
**預設值**：應用程式的組件  
**環境變數**： `<PREFIX_>STARTUPASSEMBLY`

若要設定此值，請使用環境變數或呼叫 `UseStartup`。 `UseStartup` 可以採用組件名稱 (`string`) 或類型 (`TStartup`)。 如果呼叫多個 `UseStartup` 方法，最後一個將會優先。

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>URL

以分號分隔的 IP 位址或主機位址，包含伺服器應接聽要求的連接埠和通訊協定。 例如： `http://localhost:123` 。 使用 "\*"，表示伺服器應接聽任何 IP 位址或主機名稱上的要求，並使用指定的連接埠和通訊協定 (例如，`http://*:5000`)。 通訊協定 (`http://` 或 `https://`) 必須包含在每個 URL 中。 支援的格式會依伺服器而有所不同。

**機碼**：`urls`  
**類型**：`string`  
**預設值**： `http://localhost:5000` 和 `https://localhost:5001`  
**環境變數**： `<PREFIX_>URLS`

若要設定此值，請使用環境變數或呼叫 `UseUrls`：

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Kestrel 有它自己的端點設定 API。 如需詳細資訊，請參閱<xref:fundamentals/servers/kestrel/endpoints>。

### <a name="webroot"></a>WebRoot

[IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath)屬性會決定應用程式靜態資產的相對路徑。 如果路徑不存在，則會使用無作業檔案提供者。  

**機碼**：`webroot`  
**類型**：`string`  
**預設** 值：預設值為 `wwwroot` 。 *{Content root}/wwwroot* 的路徑必須存在。  
**環境變數**： `<PREFIX_>WEBROOT`

若要設定此值，請使用環境變數或呼叫 `IWebHostBuilder` 上的 `UseWebRoot`：

```csharp
webBuilder.UseWebRoot("public");
```

如需詳細資訊，請參閱

* [基本概念： Web 根目錄](xref:fundamentals/index#web-root)
* [ContentRoot](#contentroot)

## <a name="manage-the-host-lifetime"></a>管理主機存留期

在建置的 <xref:Microsoft.Extensions.Hosting.IHost> 實作上呼叫方法來啟動和停止應用程式。 這些方法會影響所有在服務容器中註冊的 <xref:Microsoft.Extensions.Hosting.IHostedService> 實作。

### <a name="run"></a>Run

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 會執行應用程式並封鎖呼叫執行緒，直到主機關閉為止。

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 會執行應用程式，並傳回觸發取消語彙基元或關機時所完成的 <xref:System.Threading.Tasks.Task>。

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>啟用主控台支援、建立並啟動主機，以及等候<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM 關閉。

### <a name="start"></a>Start

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 會同步啟動主機。

### <a name="startasync"></a>StartAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 會啟動主機，並傳回觸發取消語彙基元或關機時所完成的 <xref:System.Threading.Tasks.Task>。 

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> 在 `StartAsync` 開始時呼叫，並等到完成後再繼續進行。 這可用來將啟動延遲到外部事件發出訊號為止。

### <a name="stopasync"></a>StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 會嘗試在提供的逾時內停止主機。

### <a name="waitforshutdown"></a>WaitForShutdown

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>封鎖呼叫的執行緒，直到 IHostLifetime 觸發關機（例如 via <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM）。

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 會傳回透過指定語彙基元觸發關機時所完成的 <xref:System.Threading.Tasks.Task>，並呼叫 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。

### <a name="external-control"></a>外部控制

主機存留期直接控制可以使用可從外部呼叫的方法來達成：

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

ASP.NET 核心範本會建立 .NET Core 泛型主機 (<xref:Microsoft.Extensions.Hosting.HostBuilder>) 。

本主題提供在 ASP.NET Core 中使用 .NET 泛型主機的相關資訊。 如需在主控台應用程式中使用 .NET 泛型主機的詳細資訊，請參閱 [.Net 泛型主機](/dotnet/core/extensions/generic-host)。

## <a name="host-definition"></a>主機定義

「主機」是封裝所有應用程式資源的物件，例如：

* 相依性插入 (DI)
* 記錄
* 組態
* `IHostedService` 實作

當主機啟動時，它會呼叫 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> <xref:Microsoft.Extensions.Hosting.IHostedService> 服務容器的託管服務集合中註冊的每個執行。 在 Web 應用程式中，其中一個 `IHostedService` 實作是一種 Web 服務，負責啟動 [HTTP 伺服器實作](xref:fundamentals/index#servers)。

在單一物件中包含所有應用程式相互依存資源的主要理由便是生命週期管理：控制應用程式的啟動及順利關機。

## <a name="set-up-a-host"></a>設定主機

主機通常由 `Program` 類別的程式碼來設定、建置並執行。 `Main` 方法：

* 呼叫 `CreateHostBuilder` 方法來建立及設定建立器物件。
* 在建立器物件上呼叫 `Build` 和 `Run` 方法。

ASP.NET Core web 範本會產生下列程式碼來建立泛型主機：

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

下列程式碼會使用非 HTTP 工作負載來建立泛型主機。 `IHostedService`執行會新增至 DI 容器：

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

針對 HTTP 工作負載，`Main` 方法相同，但 `CreateHostBuilder` 會呼叫 `ConfigureWebHostDefaults`：

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

上述程式碼是由 ASP.NET Core 範本所產生。

如果應用程式使用 Entity Framework Core，請勿變更 `CreateHostBuilder` 方法的名稱或簽章。 [Entity Framework Core 工具](/ef/core/miscellaneous/cli/)預期找到 `CreateHostBuilder` 方法，其在不執行應用程式的情況下設定主機。 如需詳細資訊，請參閱[設計階段 DbContext 建立](/ef/core/miscellaneous/cli/dbcontext-creation)。

## <a name="default-builder-settings"></a>預設建立器設定

<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 方法：

* 將 [內容根目錄](xref:fundamentals/index#content-root) 設定為所傳回的路徑 <xref:System.IO.Directory.GetCurrentDirectory%2A> 。
* 從下列項目載入主機組態：
  * 前面加上的環境變數 `DOTNET_` 。
  * 命令列引數。
* 從下列項目載入應用程式組態：
  * *appsettings.json*.
  * *appsettings.{Environment}.json*
  * 應用程式在 `Development` 環境中執行時的[使用者密碼](xref:security/app-secrets)。
  * 環境變數。
  * 命令列引數。
* 新增下列[記錄](xref:fundamentals/logging/index)提供者：
  * 主控台
  * 偵錯
  * EventSource
  * EventLog (僅當在 Windows 上執行時)
* 環境為開發時，會啟用[範圍驗證](xref:fundamentals/dependency-injection#scope-validation)和[相依性驗證](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)。

`ConfigureWebHostDefaults` 方法：

* 從前面加上的環境變數載入主機設定 `ASPNETCORE_` 。
* 會將 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器設為網頁伺服器，並使用應用程式的主機組態提供者進行設定。 如需 Kestrel 伺服器的預設選項，請參閱 <xref:fundamentals/servers/kestrel#kestrel-options>。
* 新增[主機篩選中介軟體](xref:fundamentals/servers/kestrel#host-filtering)。
* 如果等於，則新增 [轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` 。
* 啟用 IIS 整合。 如需 IIS 預設選項，請參閱 <xref:host-and-deploy/iis/index#iis-options>。

本文稍後的[＜設定所有應用程式類型＞](#settings-for-all-app-types)和[＜Web 應用程式設定＞](#settings-for-web-apps)章節，將說明如何覆寫預設的建立器設定。

## <a name="framework-provided-services"></a>架構提供的服務

下列服務會自動註冊：

* [IHostApplicationLifetime](#ihostapplicationlifetime)
* [IHostLifetime](#ihostlifetime)
* [IHostEnvironment / IWebHostEnvironment](#ihostenvironment)

如需架構所提供之服務的詳細資訊，請參閱 <xref:fundamentals/dependency-injection#framework-provided-services> 。

## <a name="ihostapplicationlifetime"></a>IHostApplicationLifetime

將 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (先前稱為 `IApplicationLifetime`) 服務插入任何類別來處理啟動後和順利關機工作。 介面上的三個屬性是用於註冊應用程式啟動和應用程式關閉事件處理程序方法的取消語彙基元。 介面也包括 `StopApplication` 方法。

下列範例是 `IHostedService` 註冊事件的實作為 `IHostApplicationLifetime` ：

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>IHostLifetime

<xref:Microsoft.Extensions.Hosting.IHostLifetime> 實作會控制主機啟動及停止的時機。 會使用最後一個註冊的實作。

`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 是預設的 `IHostLifetime` 實作。 `ConsoleLifetime`:

* 接聽<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM，並呼叫 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> 以啟動關機程式。
* 會解除封鎖 [RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等延伸模組。

## <a name="ihostenvironment"></a>IHostEnvironment

將服務插入至 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 類別，以取得下列設定的相關資訊：

* [ApplicationName](#applicationname)
* [EnvironmentName](#environmentname)
* [ContentRootPath](#contentroot)

Web apps 會執行 `IWebHostEnvironment` 介面，此介面會繼承 `IHostEnvironment` 並新增 [WebRootPath](#webroot)。

## <a name="host-configuration"></a>主機組態

主機組態用於 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 實作的屬性。

主機組態位於 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> 內的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration)。 在 `ConfigureAppConfiguration` 之後，應用程式組態會取代 `HostBuilderContext.Configuration`。

若要新增主機組態，請呼叫 `IHostBuilder` 上的 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A>。 `ConfigureHostConfiguration` 可以多次呼叫，其結果是累加的。 主機會使用指定索引鍵上最後設定值的任何選項。

包含前置詞 `DOTNET_` 和命令列引數的環境變數提供者 `CreateDefaultBuilder` 。 針對 Web 應用程式，會新增具有前置詞 `ASPNETCORE_` 的環境變數提供者。 讀取環境變數時，就會移除前置詞。 例如，`ASPNETCORE_ENVIRONMENT` 的環境變數值會變成 `environment` 索引鍵的主機組態值。

下列範例會建立主機組態：

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>應用程式組態

應用程式組態的建立方式是在 `IHostBuilder` 上呼叫 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>。 `ConfigureAppConfiguration` 可以多次呼叫，其結果是累加的。 應用程式會使用指定索引鍵上最後設定值的任何選項。 

由 `ConfigureAppConfiguration` 建立的組態位於 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*)，可用於後續作業並用作 DI 中的服務。 主機組態也會新增至應用程式組態。

如需詳細資訊，請參閱 [ASP.NET Core 中的組態](xref:fundamentals/configuration/index#configureappconfiguration)。

## <a name="settings-for-all-app-types"></a>所有應用程式類型的設定

本節列出適用於 HTTP 和非 HTTP 工作負載的主機設定。 根據預設，用來設定這些設定的環境變數可以具有 `DOTNET_` 或 `ASPNETCORE_` 前置詞。

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a>ApplicationName

[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 屬性是在主機建構期間從主機組態當中設定。

**機碼**：`applicationName`  
**類型**：`string`  
**預設值**：包含應用程式進入點的元件名稱。  
**環境變數**： `<PREFIX_>APPLICATIONNAME`

若要設定此值，請使用環境變數。 

### <a name="contentroot"></a>ContentRoot

[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 屬性會決定主機從哪裡開始搜尋內容檔案。 如果路徑不存在，就無法啟動主機。

**機碼**：`contentRoot`  
**類型**：`string`  
**預設值**：應用程式元件所在的資料夾。  
**環境變數**： `<PREFIX_>CONTENTROOT`

若要設定此值，請使用環境變數或呼叫 `IHostBuilder` 上的 `UseContentRoot`：

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

如需詳細資訊，請參閱

* [基本概念：內容根目錄](xref:fundamentals/index#content-root)
* [WebRoot](#webroot)

### <a name="environmentname"></a>EnvironmentName

[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 屬性可以設為任何值。 架構定義的值包括 `Development`、`Staging` 和 `Production`。 值不區分大小寫。

**機碼**：`environment`  
**類型**：`string`  
**預設值**： `Production`  
**環境變數**： `<PREFIX_>ENVIRONMENT`

若要設定此值，請使用環境變數或呼叫 `IHostBuilder` 上的 `UseEnvironment`：

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>ShutdownTimeout

[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) 會設定 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的逾時。 預設值是五秒鐘。  在逾時期間，主機會：

* 觸發 [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)。
* 嘗試停止託管的服務，並記錄無法停止之服務的錯誤。

如果在所有的託管服務停止之前逾時期限已到期，則應用程式關閉時，會停止任何剩餘的作用中服務。 即使服務尚未完成處理也會停止。 如果服務需要更多時間才能停止，請增加逾時。

**機碼**：`shutdownTimeoutSeconds`  
**類型**：`int`  
**預設值**：5秒  
**環境變數**： `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

若要設定此值，請使用環境變數或設定 `HostOptions`。 下列範例將逾時設為 20 秒：

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a>Web 應用程式的設定

某些主機設定僅適用於 HTTP 工作負載。 根據預設，用來設定這些設定的環境變數可以具有 `DOTNET_` 或 `ASPNETCORE_` 前置詞。

`IWebHostBuilder` 上的擴充方法適用於這些設定。 示範如何呼叫擴充方法的程式碼範例假設 `webBuilder` 是 `IWebHostBuilder` 的執行個體，如下列範例所示：

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a>CaptureStartupErrors

當它為 `false` 時，啟動期間發生的錯誤會導致主機結束。 當它為 `true` 時，主機會擷取啟動期間的例外狀況，並嘗試啟動伺服器。

**機碼**：`captureStartupErrors`  
**類型**： `bool` (`true` 或 `1`)   
**預設值**：預設為 `false`，除非應用程式執行時在 IIS 背後有 Kestrel，此時預設值即為 `true`。  
**環境變數**： `<PREFIX_>CAPTURESTARTUPERRORS`

若要設定此值，請使用組態或呼叫 `CaptureStartupErrors`：

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>DetailedErrors

啟用時 (或當環境為 `Development` 時)，應用程式會擷取詳細錯誤。

**機碼**：`detailedErrors`  
**類型**： `bool` (`true` 或 `1`)   
**預設值**： `false`  
**環境變數**： `<PREFIX_>_DETAILEDERRORS`

若要設定此值，請使用組態或呼叫 `UseSetting`：

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>HostingStartupAssemblies

在啟動時載入的裝載啟動組件字串，以分號分隔。 雖然設定值會預設為空字串，但裝載啟動組件一律會包含應用程式的組件。 提供裝載啟動組件時，它們會新增至應用程式的組件，以便在應用程式在啟動時建置其通用服務時載入。

**機碼**：`hostingStartupAssemblies`  
**類型**：`string`  
**預設值**：空字串  
**環境變數**： `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

若要設定此值，請使用組態或呼叫 `UseSetting`：

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostingStartupExcludeAssemblies

在啟動時排除以分號分隔的裝載啟動組件字串。

**機碼**：`hostingStartupExcludeAssemblies`  
**類型**：`string`  
**預設值**：空字串  
**環境變數**： `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

若要設定此值，請使用組態或呼叫 `UseSetting`：

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a>HTTPS_Port

HTTPS 重新導向連接埠。 用於[強制 HTTPS](xref:security/enforcing-ssl)。

**機碼**：`https_port`  
**類型**：`string`  
**預設** 值：未設定預設值。  
**環境變數**： `<PREFIX_>HTTPS_PORT`

若要設定此值，請使用組態或呼叫 `UseSetting`：

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>PreferHostingUrls

指出主機是否應接聽使用設定的 Url， `IWebHostBuilder` 而不是使用執行時所設定的 url `IServer` 。

**機碼**：`preferHostingUrls`  
**類型**： `bool` (`true` 或 `1`)   
**預設值**： `true`  
**環境變數**： `<PREFIX_>_PREFERHOSTINGURLS`

若要設定此值，請使用環境變數或呼叫 `PreferHostingUrls`：

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>PreventHostingStartup

可防止自動載入裝載啟動組件，包括應用程式組件所設定的裝載啟動組件。 如需詳細資訊，請參閱<xref:fundamentals/configuration/platform-specific-configuration>。

**機碼**：`preventHostingStartup`  
**類型**： `bool` (`true` 或 `1`)   
**預設值**： `false`  
**環境變數**： `<PREFIX_>_PREVENTHOSTINGSTARTUP`

若要設定此值，請使用環境變數或呼叫 `UseSetting`：

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>StartupAssembly

要搜尋 `Startup` 類別的組件。

**機碼**：`startupAssembly`  
**類型**：`string`  
**預設值**：應用程式的組件  
**環境變數**： `<PREFIX_>STARTUPASSEMBLY`

若要設定此值，請使用環境變數或呼叫 `UseStartup`。 `UseStartup` 可以採用組件名稱 (`string`) 或類型 (`TStartup`)。 如果呼叫多個 `UseStartup` 方法，最後一個將會優先。

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>URL

以分號分隔的 IP 位址或主機位址，包含伺服器應接聽要求的連接埠和通訊協定。 例如： `http://localhost:123` 。 使用 "\*"，表示伺服器應接聽任何 IP 位址或主機名稱上的要求，並使用指定的連接埠和通訊協定 (例如，`http://*:5000`)。 通訊協定 (`http://` 或 `https://`) 必須包含在每個 URL 中。 支援的格式會依伺服器而有所不同。

**機碼**：`urls`  
**類型**：`string`  
**預設值**： `http://localhost:5000` 和 `https://localhost:5001`  
**環境變數**： `<PREFIX_>URLS`

若要設定此值，請使用環境變數或呼叫 `UseUrls`：

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Kestrel 有它自己的端點設定 API。 如需詳細資訊，請參閱<xref:fundamentals/servers/kestrel#endpoint-configuration>。

### <a name="webroot"></a>WebRoot

[IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath)屬性會決定應用程式靜態資產的相對路徑。 如果路徑不存在，則會使用無作業檔案提供者。  

**機碼**：`webroot`  
**類型**：`string`  
**預設** 值：預設值為 `wwwroot` 。 *{Content root}/wwwroot* 的路徑必須存在。  
**環境變數**： `<PREFIX_>WEBROOT`

若要設定此值，請使用環境變數或呼叫 `IWebHostBuilder` 上的 `UseWebRoot`：

```csharp
webBuilder.UseWebRoot("public");
```

如需詳細資訊，請參閱

* [基本概念： Web 根目錄](xref:fundamentals/index#web-root)
* [ContentRoot](#contentroot)

## <a name="manage-the-host-lifetime"></a>管理主機存留期

在建置的 <xref:Microsoft.Extensions.Hosting.IHost> 實作上呼叫方法來啟動和停止應用程式。 這些方法會影響所有在服務容器中註冊的 <xref:Microsoft.Extensions.Hosting.IHostedService> 實作。

### <a name="run"></a>執行

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 會執行應用程式並封鎖呼叫執行緒，直到主機關閉為止。

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 會執行應用程式，並傳回觸發取消語彙基元或關機時所完成的 <xref:System.Threading.Tasks.Task>。

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>啟用主控台支援、建立並啟動主機，以及等候<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM 關閉。

### <a name="start"></a>開始

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 會同步啟動主機。

### <a name="startasync"></a>StartAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 會啟動主機，並傳回觸發取消語彙基元或關機時所完成的 <xref:System.Threading.Tasks.Task>。 

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> 在 `StartAsync` 開始時呼叫，並等到完成後再繼續進行。 這可用來將啟動延遲到外部事件發出訊號為止。

### <a name="stopasync"></a>StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 會嘗試在提供的逾時內停止主機。

### <a name="waitforshutdown"></a>WaitForShutdown

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>封鎖呼叫的執行緒，直到 IHostLifetime 觸發關機（例如 via <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM）。

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 會傳回透過指定語彙基元觸發關機時所完成的 <xref:System.Threading.Tasks.Task>，並呼叫 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。

### <a name="external-control"></a>外部控制

主機存留期直接控制可以使用可從外部呼叫的方法來達成：

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 應用程式會設定並啟動主機。 主機負責應用程式啟動和存留期管理。

本文所涵蓋的 ASP.NET Core 泛型主機 (<xref:Microsoft.Extensions.Hosting.HostBuilder>)，可用來不會處理 HTTP 要求的應用程式。

泛型主機的用途是將 HTTP 管線從 Web 主機 API 分離，以允許更廣泛的主機陣列案例。 傳訊、背景工作及其他以泛型主機為基礎的非 HTTP 工作負載都受益於跨領域的功能，例如設定、相依性插入 (DI) 和記錄。

泛型主機是 ASP.NET Core 2.1 的新功能，不適用於虛擬主機案例。 針對虛擬主機案例，請使用 [Web 主機](xref:fundamentals/host/web-host)。 泛型主機將在未來版本中取代 Web 主機，並成為 HTTP 和非 HTTP 案例中的主要主機 API。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/) ([如何下載](xref:index#how-to-download-a-sample)) 

在 [Visual Studio Code](https://code.visualstudio.com/) 中執行範例應用程式時，請使用「外部或整合式終端機」。 請勿在 `internalConsole` 中執行範例。

若要在 Visual Studio Code 中設定主控台：

1. 開啟 *.vscode/launch.json* 檔案。
1. 在 [.NET Core Launch (console)] \(.NET Core 啟動 (主控台)\) 設定中，尋找 **console** 項目。 將此值設定為 `externalTerminal` 或 `integratedTerminal`。

## <a name="introduction"></a>簡介

可使用位於 <xref:Microsoft.Extensions.Hosting> 命名空間的泛型主機程式庫，且該程式庫由 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) 套件提供。 [Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 或更新版本) 包含 `Microsoft.Extensions.Hosting` 套件。

<xref:Microsoft.Extensions.Hosting.IHostedService> 是程式碼執行的進入點。 每個 <xref:Microsoft.Extensions.Hosting.IHostedService> 實作會依 [ConfigureServices 中的服務註冊](#configureservices)順序執行。 當主機啟動時，會在每個 <xref:Microsoft.Extensions.Hosting.IHostedService> 上呼叫 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>；當主機順利關閉時，則會依反向註冊順序呼叫 <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*>。

## <a name="set-up-a-host"></a>設定主機

<xref:Microsoft.Extensions.Hosting.IHostBuilder> 是程式庫和應用程式用來初始化、建置及執行主機的主要元件：

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a>選項

<xref:Microsoft.Extensions.Hosting.IHost> 的 <xref:Microsoft.Extensions.Hosting.HostOptions> 設定選項。

### <a name="shutdown-timeout"></a>關機逾時

<xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> 會為 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 設定逾時。 預設值是五秒鐘。

中的下列選項 `Program.Main` 設定會將預設的五秒關機超時時間增加為20秒：

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a>預設服務

下列服務會在主機初始化期間註冊：

* [環境](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>) 
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* [Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>) 
* <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)
* <xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)
* <xref:Microsoft.Extensions.Hosting.IHost>
* [選項](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>) 
* [記錄](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>) 

## <a name="host-configuration"></a>主機組態

主機組態的建立方式：

* 呼叫 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 上的擴充方法，來設定[內容根目錄](#content-root)及[環境](#environment)。
* 在 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 中從組態提供者讀取組態。

### <a name="extension-methods"></a>擴充方法

### <a name="application-key-name"></a>應用程式索引鍵 (名稱)

[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) 屬性是在主機建構期間從主機設定當中設定。 若要明確設定該值，請使用 [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)：

**機碼**：`applicationName`  
**類型**：`string`  
**預設**：包含應用程式進入點的組件名稱。  
**設定使用**： `HostBuilderContext.HostingEnvironment.ApplicationName`  
**環境變數**： `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` 是 [選擇性的，而且是使用者定義的](#configurehostconfiguration)) 

### <a name="content-root"></a>內容根目錄

此設定可決定主機開始搜尋內容檔案的位置。

**機碼**：`contentRoot`  
**類型**：`string`  
**預設值**：預設為應用程式組件所在的資料夾。  
**設定使用**： `UseContentRoot`  
**環境變數**： `<PREFIX_>CONTENTROOT` (`<PREFIX_>` 是 [選擇性的，而且是使用者定義的](#configurehostconfiguration)) 

如果路徑不存在，就無法啟動主機。

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

如需詳細資訊，請參閱 [基本概念：內容根目錄](xref:fundamentals/index#content-root)。

### <a name="environment"></a>環境

設定應用程式的 [環境](xref:fundamentals/environments)。

**機碼**：`environment`  
**類型**：`string`  
**預設值**： `Production`  
**設定使用**： `UseEnvironment`  
**環境變數**： `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` 是 [選擇性的，而且是使用者定義的](#configurehostconfiguration)) 

環境可以設定為任何值。 架構定義的值包括 `Development`、`Staging` 和 `Production`。 值不區分大小寫。

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a>ConfigureHostConfiguration

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 會使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 來建立主機的 <xref:Microsoft.Extensions.Configuration.IConfiguration>。 主機組態會用來將 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 初始化，使其在應用程式建置流程中可供使用。

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 可以多次呼叫，其結果是累加的。 主機會使用指定索引鍵上最後設定值的任何選項。

預設不會包含任何提供者。 您必須明確地指定應用程式在 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 之中需要哪個組態提供者，包括：

* 檔案組態 (例如，來自 *hostsettings.json* 的檔案)。
* 環境變數組態。
* 命令列引數組態。
* 任何其他必要的組態提供者。

使用之後呼叫[檔案組態提供者](xref:fundamentals/configuration/index#file-configuration-provider)的 `SetBasePath`，並透過指定應用程式基底路徑，就可使用主機的檔案設定。 範例應用程式會使用 JSON 檔案，*hostsettings.json*，並呼叫 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 取用檔案的主機組態設定。

若要新增主機的[環境變數組態](xref:fundamentals/configuration/index#environment-variables-configuration-provider)，請在主機建立器上呼叫 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>。 `AddEnvironmentVariables` 可接受選擇性的使用者定義前置詞。 範例應用程式會使用前置詞 `PREFIX_`。 讀取環境變數時，就會移除前置詞。 在設定範例應用程式的主機時，`PREFIX_ENVIRONMENT` 的環境變數值會變成 `environment` 索引鍵的主機組態值。

在開發期間使用 [Visual Studio](https://visualstudio.microsoft.com) 或以 `dotnet run` 執行應用程式時，可能會在 *Properties/launchSettings.json* 檔案中設定環境變數。 在 [Visual Studio Code](https://code.visualstudio.com/) 中，可以在開發期間於 *.vscode/launch.json* 檔案中設定環境變數。 如需詳細資訊，請參閱<xref:fundamentals/environments>。

[命令列組態](xref:fundamentals/configuration/index#command-line-configuration-provider)可透過呼叫 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 新增。 命令列組態會在最後新增，以便命令列引數覆寫由先前組態提供者提供的組態。

*hostsettings.json*：

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

您可以使用 [applicationName](#application-key-name) 和 [contentRoot](#content-root) 索引鍵來提供其他組態。

使用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 的 `HostBuilder` 組態範例：

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

應用程式組態的建立方式是在 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 實作上呼叫 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>。 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 會使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 來建立應用程式的 <xref:Microsoft.Extensions.Configuration.IConfiguration>。 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 可以多次呼叫，其結果是累加的。 應用程式會使用指定索引鍵上最後設定值的任何選項。 您可以從後續作業的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 及 <xref:Microsoft.Extensions.Hosting.IHost.Services*> 中存取 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 所建立的組態。

應用程式組態會自動接收由 [ConfigureHostConfiguration](#configurehostconfiguration) 提供的主機組態。

使用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 的應用程式組態範例：

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

*appsettings.json*:

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

*appsettings.Development.json*：

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

*appsettings.Production.json*：

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

若要將設定檔移至輸出目錄，請在專案檔中將設定檔指定為 [MSBuild 專案項目](/visualstudio/msbuild/common-msbuild-project-items)。 範例應用程式使用下列 `<Content>` 項目，移動其 JSON 應用程式設定檔和 *hostsettings.json*：

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> 組態擴充方法 (例如 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 和 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>) 需要其他的 NuGet 套件，例如 [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) \(英文\) 和[Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables) \(英文\)。 除非應用程式使用 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app)，否則，除了核心 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) \(英文\) 套件，還必須將這些套件新增至專案。 如需詳細資訊，請參閱<xref:fundamentals/configuration/index>。

## <a name="configureservices"></a>ConfigureServices

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> 會將服務新增至應用程式的[相依性插入](xref:fundamentals/dependency-injection)容器。 <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> 可以多次呼叫，其結果是累加的。

託管服務是具有背景工作邏輯的類別，可實作 <xref:Microsoft.Extensions.Hosting.IHostedService> 介面。 如需詳細資訊，請參閱<xref:fundamentals/host/hosted-services>。

[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/)使用 `AddHostedService` 擴充方法，將存留期事件 `LifetimeEventsHostedService` 和計時背景工作 `TimedHostedService` 等服務新增至應用程式：

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a>ConfigureLogging

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> 會新增委派以設定提供的 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>。 <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> 可以多次呼叫，其結果是累加的。

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a>UseConsoleLifetime

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>接聽<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM，並呼叫 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 以啟動關機程式。 <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> 解除封鎖 [RunAsync](#runasync) 和 [>waitforshutdownasync](#waitforshutdownasync)等擴充功能。 `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 會預先註冊為預設存留期實作。 系統會使用最後一個註冊的存留期。

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a>容器設定

為支援插入其他容器，主機可以接受 <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>。 提供處理站不是 DI 容器註冊的一部分，而是用來建立實體 DI 容器的主機內建功能。 [UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) 會覆寫用來建立應用程式服務提供者的預設處理站。

自訂容器組態由 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 方法管理。 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 提供在基礎主機 API 上設定容器的強型別體驗。 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 可以多次呼叫，其結果是累加的。

建立應用程式的服務容器：

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

提供服務容器處理站：

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

使用處理站，並設定應用程式的自訂服務容器：

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a>擴充性

主機擴充性是透過 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 上的擴充方法執行。 下列範例使用 <xref:fundamentals/host/hosted-services> 中示範的 [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) 範例顯示擴充方法如何擴充 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 實作。

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

應用程式會建立 `UseHostedService` 擴充方法以註冊在 `T` 中傳遞的裝載服務：

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a>管理主機

<xref:Microsoft.Extensions.Hosting.IHost> 實作負責啟動及停止服務容器中已註冊的 <xref:Microsoft.Extensions.Hosting.IHostedService> 實作。

### <a name="run"></a>執行

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 會執行應用程式並封鎖呼叫執行緒，直到主機關閉為止：

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 會執行應用程式，並傳回觸發取消語彙基元或關機時所完成的 <xref:System.Threading.Tasks.Task>：

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>啟用主控台支援、建立並啟動主機，以及等候<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM 關閉。

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a>Start 和 StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 會同步啟動主機。

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 會嘗試在提供的逾時內停止主機。

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a>StartAsync 和 StopAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 會啟動應用程式。

<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 會停止應用程式。

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a>WaitForShutdown

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>會透過來觸發 <xref:Microsoft.Extensions.Hosting.IHostLifetime> ，例如 `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (接聽<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT 或 SIGTERM) 。 <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 會呼叫 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 會傳回透過指定語彙基元觸發關機時所完成的 <xref:System.Threading.Tasks.Task>，並呼叫 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a>外部控制

主機的外部控制可以使用可從外部呼叫的方法來達成：

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> 在 <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 開始時呼叫，並等到完成後再繼續進行。 這可用來將啟動延遲到外部事件發出訊號為止。

## <a name="ihostingenvironment-interface"></a>IHostingEnvironment 介面

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 提供應用程式主控環境的相關資訊。 使用[建構函式插入](xref:fundamentals/dependency-injection)以取得 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>，才能使用其屬性和擴充方法：

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

如需詳細資訊，請參閱<xref:fundamentals/environments>。

## <a name="iapplicationlifetime-interface"></a>IApplicationLifetime 介面

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 允許啟動後和關機活動，包括順利關機要求。 在介面上的三個屬性是用來註冊定義啟動和關閉事件之 <xref:System.Action> 方法的取消語彙基元。

| 取消權杖 | 觸發時機&#8230; |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | 已完全啟動主機。 |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | 主機正在完成正常關機程序。 應該處理所有要求。 關機封鎖，直到完成此事件。 |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | 主機正在執行正常關機程序。 可能仍在處理要求。 關機封鎖，直到完成此事件。 |

建構函式將 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 服務插入任何類別。 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/)使用建構函式插入 `LifetimeEventsHostedService` 類別 (<xref:Microsoft.Extensions.Hosting.IHostedService> 實作) 來註冊事件。

*LifetimeEventsHostedService.cs*：

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 會要求應用程式終止。 當呼叫類別的 `Shutdown` 方法時，下列類別使用 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 來順利關閉應用程式：

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a>其他資源

* <xref:fundamentals/host/hosted-services>
