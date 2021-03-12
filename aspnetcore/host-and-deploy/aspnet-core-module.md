---
title: ASP.NET Core 模組
author: rick-anderson
description: 深入瞭解使用 IIS 裝載 ASP.NET Core 應用程式的 ASP.NET 核心模組。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 8574bc06b7c27fbb8caf834754188dba67bcb4e3
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605691"
---
# <a name="aspnet-core-module"></a>ASP.NET Core 模組

由 [Tom Dykstra](https://github.com/tdykstra)、 [rick Strahl](https://github.com/RickStrahl)、 [Chris Ross](https://github.com/Tratcher)、 [Rick Anderson](https://twitter.com/RickAndMSFT)、 [>sourabh Shirhatti](https://twitter.com/sshirhatti)和 [Justin Kotalik](https://github.com/jkotalik)

::: moniker range=">= aspnetcore-5.0"

ASP.NET Core 模組是一種原生 IIS 模組，可插入 IIS 管線，讓 ASP.NET Core 應用程式能與 IIS 搭配運作。 使用 IIS 執行 ASP.NET Core 應用程式，方法是： 

* 將 ASP.NET Core 應用程式裝載在 IIS 背景工作進程內 (`w3wp.exe`) ，稱為同 [進程裝載模型](xref:host-and-deploy/iis/in-process-hosting)。
* 將 web 要求轉送到執行 Kestrel 伺服器的後端 ASP.NET Core 應用程式，稱為 [跨進程裝載模型](xref:host-and-deploy/iis/out-of-process-hosting)。

每個裝載模型之間都有取捨。 依預設，會使用同進程裝載模型，因為效能和診斷效能較佳。

## <a name="install-aspnet-core-module"></a>安裝 ASP.NET Core 模組

使用下列連結下載安裝程式：

[目前的 .NET Core 裝載套件組合安裝程式 (直接下載)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

如需有關如何安裝 ASP.NET Core 模組或安裝不同模組版本的詳細資訊，請參閱 [安裝 .Net Core 裝載](xref:host-and-deploy/iis/hosting-bundle)套件組合。

如需將 ASP.NET Core 應用程式發佈至 IIS 伺服器的教學課程體驗，請參閱<xref:tutorials/publish-to-iis>。

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

ASP.NET Core 模組是一種原生 IIS 模組，可外掛至 IIS 管線以便：

* 在 IIS 工作者處理序 (`w3wp.exe`) 中裝載 ASP.NET Core 應用程式 (稱為[同處理序裝載模型](#in-process-hosting-model))。
* 將 Web 要求轉送到執行 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)的後端 ASP.NET Core 應用程式 (稱為[跨處理序裝載模型](#out-of-process-hosting-model))。

支援的 Windows 版本：

* Windows 7 或更新版本
* Windows Server 2012 R2 或更新版本

同處理序裝載時，模組會使用 IIS 的同處理序伺服程式實作，稱為 IIS HTTP 伺服器 (`IISHttpServer`)。

跨處理序裝載時，該模組只適用於 Kestrel。 模組無法搭配 [HTTP.sys](xref:fundamentals/servers/httpsys)運作。

## <a name="hosting-models"></a>裝載模型

### <a name="in-process-hosting-model"></a>同處理序裝載模型

ASP.NET Core 應用程式預設為同進程裝載模型。

同處理序裝載時具有下列特性：

* 使用 IIS HTTP 伺服器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器。 針對同處理序，[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 會呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 來：

  * 註冊 `IISHttpServer`。
  * 設定伺服器在 ASP.NET Core 模組後方執行時應該接聽的連接埠和基底路徑。
  * 設定主機以擷取啟動錯誤。

* [requestTimeout 屬性](#attributes-of-the-aspnetcore-element)不適用於同處理序裝載。

* 不支援在應用程式之間共用應用程式集區。 每個應用程式使用一個應用程式集區。

* 使用[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy)或手動將檔案放[ `app_offline.htm` 入部署](xref:host-and-deploy/iis/index#locked-deployment-files)時，如果有開啟的連線，應用程式可能不會立即關機。 例如，WebSocket 連線可能會延遲應用程式關機。

* 應用程式的架構 (位元) 和已安裝的執行階段 (x64 或 x86) 必須符合應用程式集區的架構。

* 偵測到用戶端中斷連線。 [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*)當用戶端中斷連線時，解除標記就會取消。

* 在 ASP.NET Core 2.2.1 或更早版本中，會傳回 <xref:System.IO.Directory.GetCurrentDirectory*> IIS 所啟動之進程的背景工作目錄，而不是應用程式的目錄 (例如 `C:\Windows\System32\inetsrv` `w3wp.exe`) 。

  如需設定應用程式目前的目錄的範例程式碼，請參閱[ `CurrentDirectoryHelpers` 類別](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs)。 呼叫 `SetCurrentDirectory` 方法。 後續呼叫 <xref:System.IO.Directory.GetCurrentDirectory*> 會提供應用程式的目錄。

* 裝載同處理序時，不會內部呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 來將使用者初始化。 因此，預設會在未啟動每個驗證之後，使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 實作來轉換宣告。 使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 實作來轉換宣告時，請呼叫 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 來新增入驗證服務：

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

針對跨進程， [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) 呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> ：

* 設定伺服器在 ASP.NET Core 模組後方執行時應該接聽的連接埠和基底路徑。
* 設定主機以擷取啟動錯誤。

### <a name="hosting-model-changes"></a>裝載模型變更

如果檔案 `hostingModel` 中的設定有所變更 `web.config` ([在 `web.config` ](#configuration-with-webconfig)) 的設定中說明，則模組會回收 IIS 的工作者進程。

針對 IIS Express，模組不會回收工作者處理序，但會改為觸發目前 IIS Express 處理序的正常關閉。 應用程式的下一個要求會繁衍新的 IIS Express 處理序。

### <a name="process-name"></a>程序名稱

`Process.GetCurrentProcess().ProcessName` 會報告 `w3wp`/`iisexpress` (同處理序) 或 `dotnet` (跨處理序)。

許多如 Windows 驗證等原生模組仍在使用中。 若要深入了解搭配 ASP.NET Core 模組的使用中 IIS 模組，請參閱<xref:host-and-deploy/iis/modules>。

ASP.NET Core 模組也可以：

* 設定背景工作處理序的環境變數。
* 將 stdout 輸出記錄到檔案儲存區，以針對啟動問題進行疑難排解。
* 轉送 Windows 驗證權杖。

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>如何安裝和使用 ASP.NET Core 模組

如需如何安裝 ASP.NET Core 模組的指示，請參閱[安裝 .NET Core 裝載套件組合](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。

## <a name="configuration-with-webconfig"></a>使用 web.config 進行設定

設定 ASP.NET Core 模組時，是使用網站 *web.config* 檔案中 `system.webServer` 節點的 `aspNetCore` 區段來設定。

下列檔案 `web.config` 是針對與 [framework 相依的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) 所發佈，並設定 ASP.NET 核心模組來處理網站要求：

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

以下 *web.config* 是針對 [自封式部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)發佈的檔案：

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<xref:System.Configuration.SectionInformation.InheritInChildApplications*>屬性設定為，表示在專案 `false` 內指定的設定不會 [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 由位於應用程式子目錄中的應用程式所繼承。

將應用程式部署至 [Azure App Service](https://azure.microsoft.com/services/app-service/) 時，`stdoutLogFile` 路徑會設定為 `\\?\%home%\LogFiles\stdout`。 路徑會將 stdout 記錄檔儲存到 `LogFiles` 資料夾，這是服務自動建立的位置。

如需有關 IIS 子應用程式設定的詳細資訊，請參閱 <xref:host-and-deploy/iis/index#sub-applications>。

### <a name="attributes-of-the-aspnetcore-element"></a>aspNetCore 元素的屬性

| 屬性 | 描述 | 預設 |
| --------- | ----------- | :-----: |
| `arguments` | <p>選擇性字串屬性。</p><p>**processPath** 中所指定可執行檔的引數。</p> | |
| `disableStartUpErrorPage` | <p>選擇性的 Boolean 屬性。</p><p>如果為 true，就會抑制 [502.5 - 處理序失敗] 頁面，而優先顯示 *web.config* 中設定的 502 狀態碼頁面。</p> | `false` |
| `forwardWindowsAuthToken` | <p>選擇性的 Boolean 屬性。</p><p>若為 true，則會將權杖轉送至以 `%ASPNETCORE_PORT%` 每個要求的標頭接聽的子進程 `'MS-ASPNETCORE-WINAUTHTOKEN'` 。 該處理序需負責依據要求呼叫此權杖上的 CloseHandle。</p> | `true` |
| `hostingModel` | <p>選擇性字串屬性。</p><p>將裝載模型指定為同進程 (`InProcess` / `inprocess`) 或跨進程的 (`OutOfProcess` / `outofprocess`) 。</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>選擇性的整數屬性。</p><p>指定 **processPath** 設定中所指定處理序執行個體每個應用程式可上調的數目。</p><p>&dagger;針對同處理序裝載，此值會限制為 `1`。</p><p>不建議使用 `processesPerApplication` 設定。 此屬性將在未來版本中移除。</p> | 預設： `1`<br>最小值：`1`<br>最大值：`100`&dagger; |
| `processPath` | <p>必要的字串屬性。</p><p>啟動接聽 HTTP 要求之處理序的可執行檔路徑。 支援相對路徑。 如果路徑的開頭為 `.`，該路徑即被視為網站根目錄的相對路徑。</p> | |
| `rapidFailsPerMinute` | <p>選擇性的整數屬性。</p><p>指定允許 **processPath** 中所指定處理序每分鐘當機的次數。 如果超出此限制，模組就會在該分鐘的剩餘時間內停止啟動處理序。</p><p>不支援同處理序裝載。</p> | 預設： `10`<br>最小值：`0`<br>最大值︰`100` |
| `requestTimeout` | <p>選擇性的時間範圍屬性。</p><p>針對在 %ASPNETCORE_PORT% 進行接聽的處理序，指定 ASP.NET Core 模組等候回應的持續時間。</p><p>在 ASP.NET Core 2.1 或更新版本隨附的 ASP.NET Core 模組版本中，是以小時、分鐘及秒為單位來指定 `requestTimeout`。</p><p>不適用於同處理序裝載。 針對同處理序裝載，該模組會等待應用程式處理要求。</p><p>字串之分鐘和秒數的有效值介於 0-59。 在分鐘或秒數的值中使用 **60** 將會導致「500 - 內部伺服器錯誤」。</p> | 預設： `00:02:00`<br>最小值：`00:00:00`<br>最大值︰`360:00:00` |
| `shutdownTimeLimit` | <p>選擇性的整數屬性。</p><p>當偵測到 *app_offline.htm* 檔案時，模組等候可執行檔正常關閉的持續時間 (以秒為單位)。</p> | 預設： `10`<br>最小值：`0`<br>最大值︰`600` |
| `startupTimeLimit` | <p>選擇性的整數屬性。</p><p>針對可執行檔啟動在連接埠進行接聽的處理序，模組等候的持續時間 (以秒為單位)。 如果超出此時間限制，模組就會終止處理序。</p><p>裝載同 *進程* 時： **進程不會重新開機** ， **也不會使用** **rapidFailsPerMinute** 設定。</p><p>裝載 *跨進程* 時：此模組會在收到新的要求時嘗試重新開機進程，並在後續的連入要求上繼續嘗試重新開機進程，除非應用程式在最後一次的輪流分鐘內無法啟動 **rapidFailsPerMinute** 次數。</p><p>0 (零) 值 **不會** 視為無限逾時。</p> | 預設： `120`<br>最小值：`0`<br>最大值︰`3600` |
| `stdoutLogEnabled` | <p>選擇性的 Boolean 屬性。</p><p>如果為 true，就會將 **processPath** 中所指定處理序的 **stdout** 和 **stderr** 重新導向到 **stdoutLogFile** 中所指定的檔案。</p> | `false` |
| `stdoutLogFile` | <p>選擇性字串屬性。</p><p>指定記錄來自 **processPath** 中所指定處理序之 **stdout** 和 **stderr** 的相對或絕對檔案路徑。 相對路徑是相對於網站的根目錄。 所有開頭為 `.` 的路徑都是網站根目錄的相對路徑，而所有其他路徑則視為絕對路徑。 建立記錄檔後，模組會建立路徑中提供的所有資料夾。 使用底線分隔符號，時間戳記、處理序識別碼和副檔名 (`.log`) 會新增至 **>stdoutlogfile** 路徑的最後一個區段。 如果 `.\logs\stdout` 以值的形式提供，就會將 stdout 記錄檔的範例儲存在 `stdout_20180205194132_1934.log` `logs` 2/5/2018 （19:41:32），並在處理序識別碼為1934時儲存在資料夾中。</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>設定環境變數

您可以在 `processPath` 屬性中為處理序指定環境變數。 請使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素來指定環境變數。 本節中所設定環境變數的優先順序會高於系統環境變數。

下列範例會在中設定兩個環境變數 `web.config` 。 `ASPNETCORE_ENVIRONMENT` 會將應用程式的環境設定為 `Development`。 開發人員可以在檔案中暫時設定此值 `web.config` ，以在偵錯工具例外狀況時強制載入 [開發人員例外狀況頁面](xref:fundamentals/error-handling) 。 `CONFIG_DIR` 是一個使用者定義的環境變數範例，其中開發人員已撰寫程式碼，會在啟動時讀取值來構成用以載入應用程式設定檔的路徑。

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> 直接在中設定環境的替代方式， `web.config` 就是在 `<EnvironmentName>` [發行設定檔 (`.pubxml`) ](xref:host-and-deploy/visual-studio-publish-profiles) 或專案檔中包含屬性。 這種方法 `web.config` 會設定發行專案時的環境：
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> 請只有在未受信任網路 (例如網際網路) 無法存取的暫存和測試伺服器上，才將 `ASPNETCORE_ENVIRONMENT` 環境變數設定為 `Development`。

## `app_offline.htm`

如果 `app_offline.htm` 在應用程式的根目錄中偵測到名稱為的檔案，ASP.NET Core 模組會嘗試以正常方式關閉應用程式，並停止處理傳入的要求。 如果在經過 `shutdownTimeLimit` 中所定義的秒數之後，應用程式仍然在執行，ASP.NET Core Module 就會終止執行中的處理序。

當檔案 `app_offline.htm` 存在時，ASP.NET Core 模組會傳回該檔案的內容，以回應要求 `app_offline.htm` 。 移除檔案時 `app_offline.htm` ，下一個要求會啟動應用程式。

使用跨處理序裝載模型時，若未開啟連線，應用程式可能無法立即關閉。 例如，WebSocket 連線可能會延遲應用程式關機。

## <a name="start-up-error-page"></a>啟動錯誤頁面

同處理序及跨處理序裝載無法啟動應用程式時，兩者皆會產生自訂錯誤頁面。

若 ASP.NET Core 模組找不到同處理序或跨處理序要求處理常式時，就會顯示 [500.0 - 同處理序/跨處理序處理常式載入失敗] 狀態碼頁面。

對於同處理序裝載，若 ASP.NET Core 模組無法啟動應用程式，就會顯示 [500.30 - 啟動失敗] 狀態碼頁面。

對於跨處理序裝載，若 ASP.NET Core 模組無法啟動後端處理序，或後端處理序啟動但無法在所設定的連接埠上進行接聽，就會顯示 [502.5 - 處理序失敗] 狀態碼頁面。

若要避免此頁面產生並還原至預設的 IIS 5xx 狀態碼頁面，請使用 `disableStartUpErrorPage` 屬性。 如需有關設定自訂錯誤訊息的詳細資訊，請參閱 [HTTP 錯誤`<httpErrors>`](/iis/configuration/system.webServer/httpErrors/)。

## <a name="log-creation-and-redirection"></a>記錄檔建立和重新導向

如果已設定 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 屬性，ASP.NET Core 模組就會將 stdout 和 stderr 主控台輸出重新導向到磁碟。 建立記錄檔後，模組會建立 `stdoutLogFile` 路徑中的所有資料夾。 應用程式集區必須具有記錄檔寫入位置的寫入權限 (請使用 `IIS AppPool\<app_pool_name>` 來提供寫入權限)。

除非發生處理序回收/重新啟動，否則不會輪替記錄檔。 主機服務提供者必須負責限制記錄檔所使用的磁碟空間。

只有在 IIS 上裝載時，或使用 [Visual Studio 的 iis 開發階段支援](xref:host-and-deploy/iis/development-time-iis-support)時，才會使用 stdout 記錄檔來疑難排解應用程式啟動問題，而不是在本機進行偵錯工具並使用 iis Express 執行應用程式時進行疑難排解。

請勿將 stdout 記錄檔用來進行一般應用程式記錄。 針對 ASP.NET Core 應用程式中的例行性記錄，請使用會限制記錄檔大小並輪替記錄檔的記錄程式庫。 如需詳細資訊，請參閱[協力廠商記錄提供者](xref:fundamentals/logging/index#third-party-logging-providers)。

建立記錄檔時，系統會自動新增時間戳記和副檔名。 記錄檔名稱的組成方式是將時間戳記、處理序識別碼和副檔名 () 附加 `.log` 至路徑的最後一個區段 `stdoutLogFile` (通常 `stdout`) 以底線分隔。 如果 `stdoutLogFile` 路徑以結尾 `stdout` ，則在2/5/2018 （19:42:32）上建立的應用1934程式記錄檔名稱會是 `stdout_20180205194132_1934.log` 。

若 `stdoutLogEnabled` 為 false，會擷取在應用程式啟動時發生的錯誤，並發出最大 30KB 的事件記錄檔。 啟動之後，就會捨棄其他的記錄檔。

下列範例專案會 `aspNetCore` 在相對路徑設定 stdout 記錄 `.\log\` 。 請確認 AppPool 使用者身分識別具備所提供路徑的寫入權限。

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

針對 Azure App Service 部署發行應用程式時，Web SDK 會將 `stdoutLogFile` 值設定為 `\\?\%home%\LogFiles\stdout` 。 `%home`環境變數是針對 Azure App Service 所裝載的應用程式預先定義。

若要建立記錄篩選規則，請參閱 ASP.NET 核心記錄檔[集的設定和](xref:fundamentals/logging/index#log-filtering)[記錄篩選](xref:fundamentals/logging/index#log-filtering)區段。

如需路徑格式的詳細資訊，請參閱 [Windows 系統上的檔案路徑格式](/dotnet/standard/io/file-path-formats)。

## <a name="enhanced-diagnostic-logs"></a>增強型診斷記錄

ASP.NET Core 模組是可設定的，以提供增強型診斷記錄。 將專案加入 `<handlerSettings>` 至 `<aspNetCore>` 中的元素 `web.config` 。 將 `debugLevel` 設定為 `TRACE` 會公開精確性更高的診斷資訊：

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

`logs`當記錄檔建立時，模組會建立上述範例) 中路徑 (的任何資料夾。 應用程式集區必須具有寫入記錄檔之位置的寫入權限 (使用 `IIS AppPool\{APP POOL NAME}` ，其中預留位置 `{APP POOL NAME}` 為應用程式集區名稱，以提供寫入權限) 。

偵錯層級 (`debugLevel`) 值可以同時包含層級和位置。

層級 (順序從最不詳細到最詳細)：

* ERROR
* WARNING
* INFO
* TRACE

位置 (允許多個位置)：

* 主控台
* EVENTLOG
* FILE

也可以透過環境變數提供處理常式設定：

* `ASPNETCORE_MODULE_DEBUG_FILE`： Debug 記錄檔的路徑。  (預設： `aspnetcore-debug.log`) 
* `ASPNETCORE_MODULE_DEBUG`： Debug 層級設定。

> [!WARNING]
> 在部署中保持啟用偵錯記錄的時間，**不要** 超過針對問題進行排解疑難所需的時間。 記錄的大小不受限制。 保持啟用偵錯記錄可能會耗盡可用磁碟空間，並讓伺服器或應用程式服務當機。

如需檔案中專案的範例，請參閱 [web.config](#configuration-with-webconfig) 的設定 `aspNetCore` `web.config` 。

## <a name="modify-the-stack-size"></a>修改堆疊大小

*只有在使用同進程裝載模型時才適用。*

使用中的 `stackSize` 設定（以位元組為單位）設定受管理的堆疊大小 `web.config` 。 預設大小為1048576個位元組 (1 MB) 。

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Proxy 組態使用 HTTP 通訊協定和配對權杖

僅適用於跨處理序裝載。

在 ASP.NET Core 模組與 Kestrel 之間建立的 Proxy 會使用 HTTP 通訊協定。 沒有從伺服器外的位置竊聽模組與 Kestrel 之間流量的風險。

配對權杖用來保證 Kestrel 所接收的要求已由 IIS 代理，而且不是來自其他來源。 模組會建立配對權杖，並將其設定成環境變數 (`ASPNETCORE_TOKEN`)。 配對權杖也會設定成每個代理要求的標頭 (`MS-ASPNETCORE-TOKEN`)。 IIS 中介軟體會檢查其收到的每個要求，以確認配對權杖的標頭值符合環境變數值。 如果權杖值不相符，將記錄並拒絕要求。 使用者無法從伺服器外的位置存取配對權杖環境變數，以及模組與 Kestrel 之間的流量。 在不知道配對權杖值的情況下，攻擊者無法略過 IIS 中介軟體的檢查送出要求。

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>具有 IIS 共用設定的 ASP.NET Core 模組

ASP.NET Core 模組安裝程式會以 **TrustedInstaller** 帳戶的權限執行。 由於本機系統帳戶對於 IIS 共用設定所使用的共用路徑沒有 modify 許可權，因此，安裝程式在嘗試于共用上的檔案中設定模組設定時，會擲回「拒絕存取」錯誤 `applicationHost.config` 。

在與 IIS 安裝相同的電腦上使用 IIS 共用設定時，請執行 ASP.NET Core 裝載套件組合安裝程式並將 `OPT_NO_SHARED_CONFIG_CHECK` 參數設為 `1`：

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

若共用設定的路徑位於與 IIS 安裝不同的電腦上，請遵循下列步驟：

1. 停用「IIS 共用設定」。
1. 執行安裝程式。
1. 將更新的檔案匯出 `applicationHost.config` 至共用。
1. 重新啟用「IIS 共用設定」。

## <a name="module-version-and-hosting-bundle-installer-logs"></a>模組版本和裝載套件組合安裝程式記錄檔

判斷已安裝的 ASP.NET Core 模組版本：

1. 在主控系統上，流覽至 `%windir%\System32\inetsrv` 。
1. 找出檔案 `aspnetcore.dll` 。
1. 在該檔案上按一下滑鼠右鍵，然後從關聯式功能表中選取 [內容]。
1. 選取 [ **詳細資料** ] 索引標籤。檔案 **版本** 和 **產品版本** 代表已安裝的模組版本。

模組的裝載套件組合安裝程式記錄檔位於 `C:\Users\%UserName%\AppData\Local\Temp` 。 檔案命名為 `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`。

## <a name="module-schema-and-configuration-file-locations"></a>模組、結構描述及設定檔位置

### <a name="module"></a>模組

**IIS (x86/amd64)：**

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

**IIS Express (x86/amd64)：**

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a>結構描述

**IIS**

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

**IIS Express**

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a>組態

**IIS**

* `%windir%\System32\inetsrv\config\applicationHost.config`

**IIS Express**

* Visualstudio： `{APPLICATION ROOT}\.vs\config\applicationHost.config`

* *iisexpress.exe* Cli： `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`

您可以藉由在檔案中搜尋來找到檔案 `aspnetcore` `applicationHost.config` 。

::: moniker-end

::: moniker range="= aspnetcore-2.2"

ASP.NET Core 模組是一種原生 IIS 模組，可外掛至 IIS 管線以便：

* 在 IIS 工作者處理序 (`w3wp.exe`) 中裝載 ASP.NET Core 應用程式 (稱為[同處理序裝載模型](#in-process-hosting-model))。
* 將 Web 要求轉送到執行 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)的後端 ASP.NET Core 應用程式 (稱為[跨處理序裝載模型](#out-of-process-hosting-model))。

支援的 Windows 版本：

* Windows 7 或更新版本
* Windows Server 2008 R2 或更新版本

同處理序裝載時，模組會使用 IIS 的同處理序伺服程式實作，稱為 IIS HTTP 伺服器 (`IISHttpServer`)。

跨處理序裝載時，該模組只適用於 Kestrel。 模組無法搭配 [HTTP.sys](xref:fundamentals/servers/httpsys)運作。

## <a name="hosting-models"></a>裝載模型

### <a name="in-process-hosting-model"></a>同處理序裝載模型

若要設定同處理序裝載的應用程式，請將 `<AspNetCoreHostingModel>` 屬性新增至應用程式的專案檔，其值為 `InProcess` (跨處理序裝載是使用 `OutOfProcess` 設定)：

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

以 .NET Framework 為目標的 ASP.NET Core 應用程式不支援處理序內裝載模型。

的值 `<AspNetCoreHostingModel>` 不區分大小寫，因此 `inprocess` 和 `outofprocess` 都是有效的值。

如果檔案中沒有 `<AspNetCoreHostingModel>` 屬性，預設值為 `OutOfProcess`。

同處理序裝載時具有下列特性：

* 使用 IIS HTTP 伺服器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器。 針對同處理序，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 會呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 來：

  * 註冊 `IISHttpServer`。
  * 設定伺服器在 ASP.NET Core 模組後方執行時應該接聽的連接埠和基底路徑。
  * 設定主機以擷取啟動錯誤。

* [requestTimeout 屬性](#attributes-of-the-aspnetcore-element)不適用於同處理序裝載。

* 不支援在應用程式之間共用應用程式集區。 每個應用程式使用一個應用程式集區。

* 使用 [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) 或以手動方式[將 app_offline.htm 檔案放入部署](xref:host-and-deploy/iis/index#locked-deployment-files)時，若未開啟連線，應用程式可能無法立即關閉。 例如，WebSocket 連線可能會延遲應用程式關閉。

* 應用程式的架構 (位元) 和已安裝的執行階段 (x64 或 x86) 必須符合應用程式集區的架構。

* 偵測到用戶端中斷連線。 用戶端中斷連線時，會取消 [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 取消權杖。

* 在 ASP.NET Core 2.2.1 或更早版本中，<xref:System.IO.Directory.GetCurrentDirectory*> 會傳回 IIS 所啟動之處理序的背景工作目錄，而非應用程式的目錄 (例如 *w3wp.exe* 為 *C:\Windows\System32\inetsrv*)。

  如需設定應用程式目前所在目錄的範例程式碼，請參閱 [CurrentDirectoryHelpers 類別](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)。 呼叫 `SetCurrentDirectory` 方法。 後續呼叫 <xref:System.IO.Directory.GetCurrentDirectory*> 會提供應用程式的目錄。

* 裝載同處理序時，不會內部呼叫 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 來將使用者初始化。 因此，預設會在未啟動每個驗證之後，使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 實作來轉換宣告。 使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 實作來轉換宣告時，請呼叫 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 來新增入驗證服務：

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

### <a name="out-of-process-hosting-model"></a>跨處理序裝載模型

若要設定跨處理序裝載的應用程式，請在專案檔中使用下列任一方法：

* 請勿指定 `<AspNetCoreHostingModel>` 屬性。 如果檔案中沒有 `<AspNetCoreHostingModel>` 屬性，預設值為 `OutOfProcess`。
* 將 `<AspNetCoreHostingModel>` 屬性的值設定為 `OutOfProcess` (同處理序裝載是使用 `InProcess` 設定)：

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

值不區分大小寫，因此 `inprocess` 和 `outofprocess` 都是有效的值。

使用 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器而不是 IIS HTTP 伺服器 (`IISHttpServer`)。

針對跨處理序，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 會呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 來：

* 設定伺服器在 ASP.NET Core 模組後方執行時應該接聽的連接埠和基底路徑。
* 設定主機以擷取啟動錯誤。

### <a name="hosting-model-changes"></a>裝載模型變更

如果 `hostingModel` 設定在 *web.config* 檔案中已有所變更 (如 [使用 web.config 進行設定](#configuration-with-webconfig)一節中所說明)，模組會回收 IIS 的工作者處理序。

針對 IIS Express，模組不會回收工作者處理序，但會改為觸發目前 IIS Express 處理序的正常關閉。 應用程式的下一個要求會繁衍新的 IIS Express 處理序。

### <a name="process-name"></a>程序名稱

`Process.GetCurrentProcess().ProcessName` 會報告 `w3wp`/`iisexpress` (同處理序) 或 `dotnet` (跨處理序)。

許多如 Windows 驗證等原生模組仍在使用中。 若要深入了解搭配 ASP.NET Core 模組的使用中 IIS 模組，請參閱<xref:host-and-deploy/iis/modules>。

ASP.NET Core 模組也可以：

* 設定背景工作處理序的環境變數。
* 將 stdout 輸出記錄到檔案儲存區，以針對啟動問題進行疑難排解。
* 轉送 Windows 驗證權杖。

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>如何安裝和使用 ASP.NET Core 模組

如需如何安裝 ASP.NET Core 模組的指示，請參閱[安裝 .NET Core 裝載套件組合](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。

## <a name="configuration-with-webconfig"></a>使用 web.config 進行設定

設定 ASP.NET Core 模組時，是使用網站 *web.config* 檔案中 `system.webServer` 節點的 `aspNetCore` 區段來設定。

以下 *web.config* 檔案是針對 [架構相依部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)發佈的檔案，會設定 ASP.NET Core 模組來處理網站要求：

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

以下 *web.config* 是針對 [自封式部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)發佈的檔案：

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<xref:System.Configuration.SectionInformation.InheritInChildApplications*>屬性設定為，表示在專案 `false` 內指定的設定不會 [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 由位於應用程式子目錄中的應用程式所繼承。

將應用程式部署至 [Azure App Service](https://azure.microsoft.com/services/app-service/) 時，`stdoutLogFile` 路徑會設定為 `\\?\%home%\LogFiles\stdout`。 此路徑會將 stdout 記錄檔儲存至 [LogFiles] 資料夾，這是服務自動建立的位置。

如需有關 IIS 子應用程式設定的詳細資訊，請參閱 <xref:host-and-deploy/iis/index#sub-applications>。

### <a name="attributes-of-the-aspnetcore-element"></a>aspNetCore 元素的屬性

| 屬性 | 描述 | 預設 |
| --------- | ----------- | :-----: |
| `arguments` | <p>選擇性字串屬性。</p><p>中指定之可執行檔的引數 `processPath` 。</p> | |
| `disableStartUpErrorPage` | <p>選擇性的 Boolean 屬性。</p><p>如果為 true，就會抑制 [502.5 - 處理序失敗] 頁面，而優先顯示 *web.config* 中設定的 502 狀態碼頁面。</p> | `false` |
| `forwardWindowsAuthToken` | <p>選擇性的 Boolean 屬性。</p><p>如果為 true，就會依據要求將權杖以標頭 'MS-ASPNETCORE-WINAUTHTOKEN' 形式轉送至在 %ASPNETCORE_PORT% 進行接聽的子處理序。 該處理序需負責依據要求呼叫此權杖上的 CloseHandle。</p> | `true` |
| `hostingModel` | <p>選擇性字串屬性。</p><p>將裝載模型指定為同進程 (`InProcess` / `inprocess`) 或跨進程的 (`OutOfProcess` / `outofprocess`) 。</p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p>選擇性的整數屬性。</p><p>指定 `processPath` 每個應用程式可以啟動的設定中指定之進程的實例數目。</p><p>&dagger;針對同處理序裝載，此值會限制為 `1`。</p><p>不建議使用 `processesPerApplication` 設定。 此屬性將在未來版本中移除。</p> | 預設： `1`<br>最小值：`1`<br>最大值：`100`&dagger; |
| `processPath` | <p>必要的字串屬性。</p><p>啟動接聽 HTTP 要求之處理序的可執行檔路徑。 支援相對路徑。 如果路徑的開頭為 `.`，該路徑即被視為網站根目錄的相對路徑。</p> | |
| `rapidFailsPerMinute` | <p>選擇性的整數屬性。</p><p>指定中指定的進程 `processPath` 每分鐘損毀的次數。 如果超出此限制，模組就會在該分鐘的剩餘時間內停止啟動處理序。</p><p>不支援同處理序裝載。</p> | 預設： `10`<br>最小值：`0`<br>最大值︰`100` |
| `requestTimeout` | <p>選擇性的時間範圍屬性。</p><p>針對在 %ASPNETCORE_PORT% 進行接聽的處理序，指定 ASP.NET Core 模組等候回應的持續時間。</p><p>在 ASP.NET Core 2.1 或更新版本隨附的 ASP.NET Core 模組版本中，是以小時、分鐘及秒為單位來指定 `requestTimeout`。</p><p>不適用於同處理序裝載。 針對同處理序裝載，該模組會等待應用程式處理要求。</p><p>字串之分鐘和秒數的有效值介於 0-59。 在分鐘或秒數的值中使用 **60** 將會導致「500 - 內部伺服器錯誤」。</p> | 預設： `00:02:00`<br>最小值：`00:00:00`<br>最大值︰`360:00:00` |
| `shutdownTimeLimit` | <p>選擇性的整數屬性。</p><p>當偵測到檔案時，模組等候可執行檔正常關機的持續時間（以秒為單位） `app_offline.htm` 。</p> | 預設： `10`<br>最小值：`0`<br>最大值︰`600` |
| `startupTimeLimit` | <p>選擇性的整數屬性。</p><p>針對可執行檔啟動在連接埠進行接聽的處理序，模組等候的持續時間 (以秒為單位)。 如果超出此時間限制，模組就會終止處理序。</p><p>裝載同 *進程* 時： **進程不會重新開機** ， **也不會使用** 此 `rapidFailsPerMinute` 設定。</p><p>裝載 *跨進程* 時：此模組會在收到新的要求時嘗試重新開機進程，並在後續的連入要求上繼續嘗試重新開機進程，除非應用程式在最後一次的輪流分鐘內無法啟動次數 `rapidFailsPerMinute` 。</p><p>0 (零) 值 **不會** 視為無限逾時。</p> | 預設： `120`<br>最小值：`0`<br>最大值︰`3600` |
| `stdoutLogEnabled` | <p>選擇性的 Boolean 屬性。</p><p>若為 true，則中指定之進程的 **stdout** 和 **stderr** `processPath` 會重新導向至 **>stdoutlogfile** 中指定的檔案。</p> | `false` |
| `stdoutLogFile` | <p>選擇性字串屬性。</p><p>指定 `stdout` `stderr` 記錄中所指定進程的相對或絕對檔案路徑 `processPath` 。 相對路徑是相對於網站的根目錄。 所有開頭為 `.` 的路徑都是網站根目錄的相對路徑，而所有其他路徑則視為絕對路徑。 建立記錄檔後，模組會建立路徑中提供的所有資料夾。 使用底線分隔符號，會將時間戳記、處理序識別碼和副檔名 (`.log`) 新增至路徑的最後一個區段 `stdoutLogFile` 。 如果 `.\logs\stdout` 以值的形式提供，就會將 stdout 記錄檔的範例儲存在 `stdout_20180205194132_1934.log` `logs` 2/5/2018 （19:41:32），並在處理序識別碼為1934時儲存在資料夾中。</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>設定環境變數

您可以在 `processPath` 屬性中為處理序指定環境變數。 請使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素來指定環境變數。 本節中所設定環境變數的優先順序會高於系統環境變數。

下列範例會設定兩個環境變數。 `ASPNETCORE_ENVIRONMENT` 會將應用程式的環境設定為 `Development`。 開發人員可以在檔案中暫時設定此值 `web.config` ，以在偵錯工具例外狀況時強制載入 [開發人員例外狀況頁面](xref:fundamentals/error-handling) 。 `CONFIG_DIR` 是一個使用者定義的環境變數範例，其中開發人員已撰寫程式碼，會在啟動時讀取值來構成用以載入應用程式設定檔的路徑。

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> 直接在中設定環境的替代方式， `web.config` 就是在 `<EnvironmentName>` [發行設定檔 ( .pubxml) ](xref:host-and-deploy/visual-studio-publish-profiles) 或專案檔中包含屬性。 這種方法 `web.config` 會設定發行專案時的環境：
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> 請只有在未受信任網路 (例如網際網路) 無法存取的暫存和測試伺服器上，才將 `ASPNETCORE_ENVIRONMENT` 環境變數設定為 `Development`。

## <a name="app_offlinehtm"></a>app_offline.htm

如果 `app_offline.htm` 在應用程式的根目錄中偵測到名稱為的檔案，ASP.NET Core 模組會嘗試以正常方式關閉應用程式，並停止處理傳入的要求。 如果在經過 `shutdownTimeLimit` 中所定義的秒數之後，應用程式仍然在執行，ASP.NET Core Module 就會終止執行中的處理序。

當檔案 `app_offline.htm` 存在時，ASP.NET Core 模組會傳回該檔案的內容，以回應要求 `app_offline.htm` 。 移除檔案時 `app_offline.htm` ，下一個要求會啟動應用程式。

使用跨處理序裝載模型時，若未開啟連線，應用程式可能無法立即關閉。 例如，WebSocket 連線可能會延遲應用程式關閉。

## <a name="start-up-error-page"></a>啟動錯誤頁面

同處理序及跨處理序裝載無法啟動應用程式時，兩者皆會產生自訂錯誤頁面。

若 ASP.NET Core 模組找不到同處理序或跨處理序要求處理常式時，就會顯示 [500.0 - 同處理序/跨處理序處理常式載入失敗] 狀態碼頁面。

對於同處理序裝載，若 ASP.NET Core 模組無法啟動應用程式，就會顯示 [500.30 - 啟動失敗] 狀態碼頁面。

對於跨處理序裝載，若 ASP.NET Core 模組無法啟動後端處理序，或後端處理序啟動但無法在所設定的連接埠上進行接聽，就會顯示 [502.5 - 處理序失敗] 狀態碼頁面。

若要避免此頁面產生並還原至預設的 IIS 5xx 狀態碼頁面，請使用 `disableStartUpErrorPage` 屬性。 如需有關設定自訂錯誤訊息的詳細資訊，請參閱 [HTTP 錯誤\<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。

## <a name="log-creation-and-redirection"></a>記錄檔建立和重新導向

如果已設定 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 屬性，ASP.NET Core 模組就會將 stdout 和 stderr 主控台輸出重新導向到磁碟。 建立記錄檔後，模組會建立 `stdoutLogFile` 路徑中的所有資料夾。 應用程式集區必須具有寫入記錄檔之位置的寫入權限 (用 `IIS AppPool\{APP POOL NAME}` 來提供寫入權限，其中預留位置 `{APP POOL NAME}` 是) 的應用程式集區名稱。

除非發生處理序回收/重新啟動，否則不會輪替記錄檔。 主機服務提供者必須負責限制記錄檔所使用的磁碟空間。

只有在 IIS 上裝載時，或使用 [Visual Studio 的 iis 開發階段支援](xref:host-and-deploy/iis/development-time-iis-support)時，才會使用 stdout 記錄檔來疑難排解應用程式啟動問題，而不是在本機進行偵錯工具並使用 iis Express 執行應用程式時進行疑難排解。

請勿將 stdout 記錄檔用來進行一般應用程式記錄。 針對 ASP.NET Core 應用程式中的例行性記錄，請使用會限制記錄檔大小並輪替記錄檔的記錄程式庫。 如需詳細資訊，請參閱[協力廠商記錄提供者](xref:fundamentals/logging/index#third-party-logging-providers)。

建立記錄檔時，系統會自動新增時間戳記和副檔名。 記錄檔名稱的組成方式是將時間戳記、處理序識別碼和副檔名 () 附加 `.log` 至路徑的最後一個區段 `stdoutLogFile` (通常 `stdout`) 以底線分隔。 如果 `stdoutLogFile` 路徑以結尾 `stdout` ，則在2/5/2018 （19:42:32）上建立的應用1934程式記錄檔名稱會是 `stdout_20180205194132_1934.log` 。

若 `stdoutLogEnabled` 為 false，會擷取在應用程式啟動時發生的錯誤，並發出最大 30KB 的事件記錄檔。 啟動之後，就會捨棄其他的記錄檔。

下列範例專案會 `aspNetCore` 在相對路徑設定 stdout 記錄 `.\log\` 。 確認應用程式集區使用者身分識別有權寫入提供的路徑。

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

針對 Azure App Service 部署發行應用程式時，Web SDK 會將 `stdoutLogFile` 值設定為 `\\?\%home%\LogFiles\stdout` 。 `%home`環境變數是針對 Azure App Service 所裝載的應用程式預先定義。

如需路徑格式的詳細資訊，請參閱 [Windows 系統上的檔案路徑格式](/dotnet/standard/io/file-path-formats)。

## <a name="enhanced-diagnostic-logs"></a>增強型診斷記錄

ASP.NET Core 模組是可設定的，以提供增強型診斷記錄。 將專案加入 `<handlerSettings>` 至 `<aspNetCore>` 中的元素 `web.config` 。 將 `debugLevel` 設定為 `TRACE` 會公開精確性更高的診斷資訊：

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

在先前範例中提供給值 (的路徑中的資料夾 `<handlerSetting>` `logs` ，) 不會自動由模組建立，且應該在部署中預先存在。 應用程式集區必須具有寫入記錄檔之位置的寫入權限 (用 `IIS AppPool\{APP POOL NAME}` 來提供寫入權限，其中預留位置 `{APP POOL NAME}` 是) 的應用程式集區名稱。

偵錯層級 (`debugLevel`) 值可以同時包含層級和位置。

層級 (順序從最不詳細到最詳細)：

* ERROR
* WARNING
* INFO
* TRACE

位置 (允許多個位置)：

* 主控台
* EVENTLOG
* FILE

也可以透過環境變數提供處理常式設定：

* `ASPNETCORE_MODULE_DEBUG_FILE`： Debug 記錄檔的路徑。  (預設： `aspnetcore-debug.log`) 
* `ASPNETCORE_MODULE_DEBUG`： Debug 層級設定。

> [!WARNING]
> 在部署中保持啟用偵錯記錄的時間，**不要** 超過針對問題進行排解疑難所需的時間。 記錄的大小不受限制。 保持啟用偵錯記錄可能會耗盡可用磁碟空間，並讓伺服器或應用程式服務當機。

如需檔案中專案的範例，請參閱 [web.config](#configuration-with-webconfig) 的設定 `aspNetCore` `web.config` 。

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Proxy 組態使用 HTTP 通訊協定和配對權杖

僅適用於跨處理序裝載。

在 ASP.NET Core 模組與 Kestrel 之間建立的 Proxy 會使用 HTTP 通訊協定。 沒有從伺服器外的位置竊聽模組與 Kestrel 之間流量的風險。

配對權杖用來保證 Kestrel 所接收的要求已由 IIS 代理，而且不是來自其他來源。 模組會建立配對權杖，並將其設定成環境變數 (`ASPNETCORE_TOKEN`)。 配對權杖也會設定成每個代理要求的標頭 (`MS-ASPNETCORE-TOKEN`)。 IIS 中介軟體會檢查其收到的每個要求，以確認配對權杖的標頭值符合環境變數值。 如果權杖值不相符，將記錄並拒絕要求。 使用者無法從伺服器外的位置存取配對權杖環境變數，以及模組與 Kestrel 之間的流量。 在不知道配對權杖值的情況下，攻擊者無法略過 IIS 中介軟體的檢查送出要求。

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>具有 IIS 共用設定的 ASP.NET Core 模組

ASP.NET Core 模組安裝程式會以帳戶的許可權執行 `TrustedInstaller` 。 由於本機系統帳戶對於 IIS 共用設定所使用的共用路徑沒有 modify 許可權，因此，安裝程式在嘗試于共用上的檔案中設定模組設定時，會擲回「拒絕存取」錯誤 `applicationHost.config` 。

在與 IIS 安裝相同的電腦上使用 IIS 共用設定時，請執行 ASP.NET Core 裝載套件組合安裝程式並將 `OPT_NO_SHARED_CONFIG_CHECK` 參數設為 `1`：

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

若共用設定的路徑位於與 IIS 安裝不同的電腦上，請遵循下列步驟：

1. 停用「IIS 共用設定」。
1. 執行安裝程式。
1. 將更新的檔案匯出 `applicationHost.config` 至共用。
1. 重新啟用「IIS 共用設定」。

## <a name="module-version-and-hosting-bundle-installer-logs"></a>模組版本和裝載套件組合安裝程式記錄檔

判斷已安裝的 ASP.NET Core 模組版本：

1. 在主控系統上，流覽至 `%windir%\System32\inetsrv` 。
1. 找出檔案 `aspnetcore.dll` 。
1. 在該檔案上按一下滑鼠右鍵，然後從關聯式功能表中選取 [內容]。
1. 選取 [ **詳細資料** ] 索引標籤。檔案 **版本** 和 **產品版本** 代表已安裝的模組版本。

模組的裝載套件組合安裝程式記錄檔位於 `C:\\Users\\%UserName%\\AppData\\Local\\Temp` 。 檔案的名稱 `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log` ，其中預留位置 `{TIMESTAMP}` 是時間戳記。

## <a name="module-schema-and-configuration-file-locations"></a>模組、結構描述及設定檔位置

### <a name="module"></a>模組

**IIS (x86/amd64)**：

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

**IIS Express (x86/amd64)**：

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a>結構描述

**IIS**

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

**IIS Express**

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a>組態

**IIS**

* `%windir%\System32\inetsrv\config\applicationHost.config`

**IIS Express**

* Visualstudio： `{APPLICATION ROOT}\.vs\config\applicationHost.config`

* *iisexpress.exe* Cli： `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`

您可以藉由在檔案中搜尋來找到檔案 `aspnetcore` `applicationHost.config` 。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

ASP.NET Core 模組是一種原生 IIS 模組，可外掛至 IIS 管線，將 Web 要求重新轉送到後端的 ASP.NET Core 應用程式。

支援的 Windows 版本：

* Windows 7 或更新版本
* Windows Server 2008 R2 或更新版本

該模組只適用於 Kestrel。 該模組與 [HTTP.sys](xref:fundamentals/servers/httpsys) 不相容。

因為處理序中執行的 ASP.NET Core 應用程式會與 IIS 背景工作處理序分開，所以此模組也會執行處理序管理。 此模組會在第一個要求到達時啟動 ASP.NET Core 應用程式的處理序，並在應用程式損毀時將它重新啟動。 此行為基本上與在 IIS 中執行同處理序，並由 [Windows 處理器啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 所管理的 ASP.NET 4.x 應用程式相同。

下圖說明 IIS、ASP.NET Core 模組和應用程式之間的關聯性：

![ASP.NET Core 模組](iis/index/_static/ancm-outofprocess.png)

要求會從 Web 到達核心模式的 HTTP.sys 驅動程式。 驅動程式會在網站設定的通訊埠上將要求路由至 IIS，此通訊埠通常是 80 (HTTP) 或 443 (HTTPS)。 此模組會在應用程式的隨機通訊埠上將要求轉送至 Kestrel，而且不會是通訊埠 80 或 443。

模組會在啟動時透過環境變數指定埠，而 [IIS 整合中介軟體](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 會設定要接聽的伺服器 `http://localhost:{port}` 。 將會執行額外檢查，不是源自模組的要求都會遭到拒絕。 此模組不支援 HTTPS 轉送，因此即使由 IIS 透過 HTTPS 接收，要求還是會透過 HTTP 轉送。

Kestrel 收取來自模組的要求之後，要求會被推送至 ASP.NET Core 中介軟體管線。 中介軟體管線會處理要求，並將其作為 `HttpContext` 執行個體傳遞至應用程式的邏輯。 IIS Integration 新增的中介軟體會更新配置、遠端 IP 和帳戶路徑基底，以將要求轉送至 Kestrel。 應用程式的回應會傳回 IIS，而 IIS 會將其推送回起始要求的 HTTP 用戶端。

許多如 Windows 驗證等原生模組仍在使用中。 若要深入了解搭配 ASP.NET Core 模組的使用中 IIS 模組，請參閱<xref:host-and-deploy/iis/modules>。

ASP.NET Core 模組也可以：

* 設定背景工作處理序的環境變數。
* 將 stdout 輸出記錄到檔案儲存區，以針對啟動問題進行疑難排解。
* 轉送 Windows 驗證權杖。

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>如何安裝和使用 ASP.NET Core 模組

如需如何安裝 ASP.NET Core 模組的指示，請參閱[安裝 .NET Core 裝載套件組合](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。

## <a name="configuration-with-webconfig"></a>使用 web.config 進行設定

設定 ASP.NET Core 模組時，是使用網站 *web.config* 檔案中 `system.webServer` 節點的 `aspNetCore` 區段來設定。

以下 *web.config* 檔案是針對 [架構相依部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)發佈的檔案，會設定 ASP.NET Core 模組來處理網站要求：

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

以下 *web.config* 是針對 [自封式部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)發佈的檔案：

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

將應用程式部署至 [Azure App Service](https://azure.microsoft.com/services/app-service/) 時，`stdoutLogFile` 路徑會設定為 `\\?\%home%\LogFiles\stdout`。 此路徑會將 stdout 記錄檔儲存至 [LogFiles] 資料夾，這是服務自動建立的位置。

如需有關 IIS 子應用程式設定的詳細資訊，請參閱 <xref:host-and-deploy/iis/index#sub-applications>。

### <a name="attributes-of-the-aspnetcore-element"></a>aspNetCore 元素的屬性

| 屬性 | 描述 | 預設 |
| --------- | ----------- | :-----: |
| `arguments` | <p>選擇性字串屬性。</p><p>**processPath** 中所指定可執行檔的引數。</p>| |
| `disableStartUpErrorPage` | <p>選擇性的 Boolean 屬性。</p><p>如果為 true，就會抑制 [502.5 - 處理序失敗] 頁面，而優先顯示 *web.config* 中設定的 502 狀態碼頁面。</p> | `false` |
| `forwardWindowsAuthToken` | <p>選擇性的 Boolean 屬性。</p><p>如果為 true，就會依據要求將權杖以標頭 'MS-ASPNETCORE-WINAUTHTOKEN' 形式轉送至在 %ASPNETCORE_PORT% 進行接聽的子處理序。 該處理序需負責依據要求呼叫此權杖上的 CloseHandle。</p> | `true` |
| `processesPerApplication` | <p>選擇性的整數屬性。</p><p>指定 **processPath** 設定中所指定處理序執行個體每個應用程式可上調的數目。</p><p>不建議使用 `processesPerApplication` 設定。 此屬性將在未來版本中移除。</p> | 預設： `1`<br>最小值：`1`<br>最大值︰`100` |
| `processPath` | <p>必要的字串屬性。</p><p>啟動接聽 HTTP 要求之處理序的可執行檔路徑。 支援相對路徑。 如果路徑的開頭為 `.`，該路徑即被視為網站根目錄的相對路徑。</p> | |
| `rapidFailsPerMinute` | <p>選擇性的整數屬性。</p><p>指定允許 **processPath** 中所指定處理序每分鐘當機的次數。 如果超出此限制，模組就會在該分鐘的剩餘時間內停止啟動處理序。</p> | 預設： `10`<br>最小值：`0`<br>最大值︰`100` |
| `requestTimeout` | <p>選擇性的時間範圍屬性。</p><p>針對在 %ASPNETCORE_PORT% 進行接聽的處理序，指定 ASP.NET Core 模組等候回應的持續時間。</p><p>在 ASP.NET Core 2.1 或更新版本隨附的 ASP.NET Core 模組版本中，是以小時、分鐘及秒為單位來指定 `requestTimeout`。</p> | 預設： `00:02:00`<br>最小值：`00:00:00`<br>最大值︰`360:00:00` |
| `shutdownTimeLimit` | <p>選擇性的整數屬性。</p><p>當偵測到 *app_offline.htm* 檔案時，模組等候可執行檔正常關閉的持續時間 (以秒為單位)。</p> | 預設： `10`<br>最小值：`0`<br>最大值︰`600` |
| `startupTimeLimit` | <p>選擇性的整數屬性。</p><p>針對可執行檔啟動在連接埠進行接聽的處理序，模組等候的持續時間 (以秒為單位)。 如果超出此時間限制，模組就會終止處理序。 模組會在收到新要求時，嘗試重新啟動處理序，然後在後續的連入要求上繼續嘗試重新啟動處理序，除非應用程式在上一次循環的分鐘內無法啟動的次數達到 **rapidFailsPerMinute** 所指定的次數。</p><p>0 (零) 值 **不會** 視為無限逾時。</p> | 預設： `120`<br>最小值：`0`<br>最大值︰`3600` |
| `stdoutLogEnabled` | <p>選擇性的 Boolean 屬性。</p><p>如果為 true，就會將 **processPath** 中所指定處理序的 **stdout** 和 **stderr** 重新導向到 **stdoutLogFile** 中所指定的檔案。</p> | `false` |
| `stdoutLogFile` | <p>選擇性字串屬性。</p><p>指定記錄來自 **processPath** 中所指定處理序之 **stdout** 和 **stderr** 的相對或絕對檔案路徑。 相對路徑是相對於網站的根目錄。 所有開頭為 `.` 的路徑都是網站根目錄的相對路徑，而所有其他路徑則視為絕對路徑。 路徑中提供的所有資料夾都必須存在，模組才能建立記錄檔。 使用底線分隔符號，時間戳記、處理序識別碼及副檔名 (*.log*) 會新增至 **stdoutLogFile** 路徑的最後一個區段。 如果提供 `.\logs\stdout` 作為值，在 2018 年 2 月 5 日的 19:41:32 以處理序識別碼 1934 進行儲存時，範例 stdout 記錄檔就會以 *stdout_20180205194132_1934.log* 的形式儲存在 [logs] 資料夾中。</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>設定環境變數

您可以在 `processPath` 屬性中為處理序指定環境變數。 請使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素來指定環境變數。

> [!WARNING]
> 此節中所設定的環境變數與使用相同名稱設定的系統環境變數相衝突。 若同時在 *web.config* 檔案與 Windows 中的系統層級設定環境變數，來自 *web.config* 檔案的值會成為附加到系統環境變數值 (例如，`ASPNETCORE_ENVIRONMENT: Development;Development`)，這會造成應用程式無法啟動。

下列範例會設定兩個環境變數。 `ASPNETCORE_ENVIRONMENT` 會將應用程式的環境設定為 `Development`。 開發人員可以在 *web.config* 檔案中暫時設定這個值，以在進行應用程式例外狀況偵錯時，強制 [開發人員例外狀況頁面](xref:fundamentals/error-handling)載入。 `CONFIG_DIR` 是一個使用者定義的環境變數範例，其中開發人員已撰寫程式碼，會在啟動時讀取值來構成用以載入應用程式設定檔的路徑。

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> 請只有在未受信任網路 (例如網際網路) 無法存取的暫存和測試伺服器上，才將 `ASPNETCORE_ENVIRONMENT` 環境變數設定為 `Development`。

## <a name="app_offlinehtm"></a>app_offline.htm

如果在應用程式根目錄中偵測到名稱為 *app_offline.htm* 的檔案，ASP.NET Core Module 會嘗試正常關閉應用程式並停止處理連入的要求。 如果在經過 `shutdownTimeLimit` 中所定義的秒數之後，應用程式仍然在執行，ASP.NET Core Module 就會終止執行中的處理序。

當 *app_offline.htm* 檔案存在時，ASP.NET Core 模組會藉由傳回 *app_offline.htm* 檔案的內容來回應要求。 已移除 *app_offline.htm* 檔案時，下一個要求則會啟動應用程式。

## <a name="start-up-error-page"></a>啟動錯誤頁面

如果 ASP.NET Core 模組無法啟動後端處理序，或後端處理序啟動但無法在所設定的連接埠上進行接聽，就會顯示 [502.5 - 處理序失敗] 狀態碼頁面。 若要抑制此頁面並還原至預設的 IIS 502 狀態碼頁面，請使用 `disableStartUpErrorPage` 屬性。 如需有關設定自訂錯誤訊息的詳細資訊，請參閱 [HTTP 錯誤\<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。

## <a name="log-creation-and-redirection"></a>記錄檔建立和重新導向

如果已設定 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 屬性，ASP.NET Core 模組就會將 stdout 和 stderr 主控台輸出重新導向到磁碟。 建立記錄檔後，模組會建立 `stdoutLogFile` 路徑中的所有資料夾。 應用程式集區必須具有記錄檔寫入位置的寫入權限 (請使用 `IIS AppPool\<app_pool_name>` 來提供寫入權限)。

除非發生處理序回收/重新啟動，否則不會輪替記錄檔。 主機服務提供者必須負責限制記錄檔所使用的磁碟空間。

只有在 IIS 上裝載時，或使用 [Visual Studio 的 iis 開發階段支援](xref:host-and-deploy/iis/development-time-iis-support)時，才會使用 stdout 記錄檔來疑難排解應用程式啟動問題，而不是在本機進行偵錯工具並使用 iis Express 執行應用程式時進行疑難排解。

請勿將 stdout 記錄檔用來進行一般應用程式記錄。 針對 ASP.NET Core 應用程式中的例行性記錄，請使用會限制記錄檔大小並輪替記錄檔的記錄程式庫。 如需詳細資訊，請參閱[協力廠商記錄提供者](xref:fundamentals/logging/index#third-party-logging-providers)。

建立記錄檔時，系統會自動新增時間戳記和副檔名。 記錄檔名稱會藉由將時間戳記、處理序識別碼及副檔名 (*.log*) 以底線分隔並附加至 `stdoutLogFile` 路徑的最後一個區段 (通常是 *stdout*) 來組成。 如果 `stdoutLogFile` 路徑的結尾是 *stdout*，則在 2018 年 2 月 5 日 19:42:32 建立且 PID 為 1934 的應用程式記錄檔檔案名稱會是 *stdout_20180205194132_1934.log*。

下列範例專案會 `aspNetCore` 在相對路徑設定 stdout 記錄 `.\log\` 。 請確認 AppPool 使用者身分識別具備所提供路徑的寫入權限。

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

針對 Azure App Service 部署發行應用程式時，Web SDK 會將 `stdoutLogFile` 值設定為 `\\?\%home%\LogFiles\stdout` 。 `%home`環境變數是針對 Azure App Service 所裝載的應用程式預先定義。

若要建立記錄篩選規則，請參閱 ASP.NET 核心記錄檔[集的設定和](xref:fundamentals/logging/index#log-filtering)[記錄篩選](xref:fundamentals/logging/index#log-filtering)區段。

如需路徑格式的詳細資訊，請參閱 [Windows 系統上的檔案路徑格式](/dotnet/standard/io/file-path-formats)。

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Proxy 組態使用 HTTP 通訊協定和配對權杖

在 ASP.NET Core 模組與 Kestrel 之間建立的 Proxy 會使用 HTTP 通訊協定。 沒有從伺服器外的位置竊聽模組與 Kestrel 之間流量的風險。

配對權杖用來保證 Kestrel 所接收的要求已由 IIS 代理，而且不是來自其他來源。 模組會建立配對權杖，並將其設定成環境變數 (`ASPNETCORE_TOKEN`)。 配對權杖也會設定成每個代理要求的標頭 (`MS-ASPNETCORE-TOKEN`)。 IIS 中介軟體會檢查其收到的每個要求，以確認配對權杖的標頭值符合環境變數值。 如果權杖值不相符，將記錄並拒絕要求。 使用者無法從伺服器外的位置存取配對權杖環境變數，以及模組與 Kestrel 之間的流量。 在不知道配對權杖值的情況下，攻擊者無法略過 IIS 中介軟體的檢查送出要求。

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>具有 IIS 共用設定的 ASP.NET Core 模組

ASP.NET Core 模組安裝程式會以 **TrustedInstaller** 帳戶的權限執行。 由於本機系統帳戶並未具備 IIS 共用設定所使用的共用路徑修改權限，因此，安裝程式在嘗試於共用上的 *applicationHost.config* 檔案中進行模組設定時，會擲回拒絕存取的錯誤。

使用「IIS 共用設定」時，請依照下列步驟進行操作：

1. 停用「IIS 共用設定」。
1. 執行安裝程式。
1. 將已更新的 *applicationHost.config* 檔案匯出到共用。
1. 重新啟用「IIS 共用設定」。

## <a name="module-version-and-hosting-bundle-installer-logs"></a>模組版本和裝載套件組合安裝程式記錄檔

判斷已安裝的 ASP.NET Core 模組版本：

1. 在主控系統上，瀏覽至 *%windir%\System32\inetsrv*。
1. 找出 *aspnetcore.dll* 檔案。
1. 在該檔案上按一下滑鼠右鍵，然後從關聯式功能表中選取 [內容]。
1. 選取 [ **詳細資料** ] 索引標籤。檔案 **版本** 和 **產品版本** 代表已安裝的模組版本。

模組的裝載套件組合安裝程式記錄檔位於 *C： \\ Users \\ % UserName% \\ AppData \\ Local \\ Temp*。檔案的名稱 *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64 .log*。

## <a name="module-schema-and-configuration-file-locations"></a>模組、結構描述及設定檔位置

### <a name="module"></a>模組

**IIS (x86/amd64)：**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

**IIS Express (x86/amd64)：**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

### <a name="schema"></a>結構描述

**IIS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

### <a name="configuration"></a>組態

**IIS**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config

* *iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config

在 *applicationHost.config* 檔案中搜尋 *aspnetcore*，即可找到這些檔案。

::: moniker-end

## <a name="additional-resources"></a>其他資源

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* [ASP.NET 核心模組參考來源 [預設分支 (主要) ]](https://github.com/dotnet/aspnetcore/tree/main/src/Servers/IIS/AspNetCoreModuleV2)：使用 [ **分支** ] 下拉式清單來選取特定版本 (例如 `release/3.1`) 。
* <xref:host-and-deploy/iis/modules>
