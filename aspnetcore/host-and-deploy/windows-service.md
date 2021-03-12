---
title: 在 Windows 服務上裝載 ASP.NET Core
author: rick-anderson
description: 了解如何在 Windows 服務上裝載 ASP.NET Core 應用程式。
monikerRange: '>= aspnetcore-2.1'
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
uid: host-and-deploy/windows-service
ms.openlocfilehash: e5c7dd0e52f1246d3ac6ad9622573db4c276654b
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588811"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>在 Windows 服務上裝載 ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core 應用程式可以裝載在 Windows 上作為 [Windows 服務](/dotnet/framework/windows-services/introduction-to-windows-service-applications)，不需要使用 IIS。 當裝載為 Windows 服務時，應用程式將會在伺服器重新開機後自動啟動。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="prerequisites"></a>必要條件

* [ASP.NET Core SDK 2.1 或更新版本](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 或更新版本](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a>背景工作服務範本

ASP.NET Core 背景工作服務範本提供撰寫長期執行服務應用程式的起點。 使用範本作為 Windows 服務應用程式的基礎：

1. 從 .NET Core 範本建立背景工作服務應用程式。
1. 請遵循[應用程式組態](#app-configuration)一節中的指導方針，更新背景工作服務應用程式，以便其執行為 Windows 服務。

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a>應用程式組態

應用程式需要 [WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices)的套件參考。

`IHostBuilder.UseWindowsService` 在建立主機時呼叫。 如果應用程式以 Windows 服務形式執行，則方法會：

* 將主機存留期設定為 `WindowsServiceLifetime`。
* 將 [內容根目錄](xref:fundamentals/index#content-root) 設定為 [AppCoNtext. BaseDirectory](xref:System.AppContext.BaseDirectory)。 如需詳細資訊，請參閱[目前目錄與內容根目錄](#current-directory-and-content-root)一節。
* 啟用記錄至事件記錄檔：
  * 應用程式名稱會用來做為預設來源名稱。
  * 根據呼叫來建立主機的 ASP.NET Core 範本，應用程式的預設記錄層級為 *警告* 或更高 `CreateDefaultBuilder` 。
  * 使用 appsettings 中的金鑰覆寫預設記錄層級 `Logging:EventLog:LogLevel:Default` *appsettings.json* / *。 {環境}. json* 或其他設定提供者。
  * 只有系統管理員才能建立新的事件來源。 如果無法使用應用程式名稱建立事件來源，則會向「應用程式」來源記錄警告，並停用事件記錄檔。

在 `CreateHostBuilder` *Program.cs* 中：

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

本主題隨附的下列範例應用程式：

* 背景工作角色服務範例：以背景工作使用[託管服務](xref:fundamentals/host/hosted-services)的背景工作[服務範本](#worker-service-template)為基礎的非 web 應用程式範例。
* Web App Service 範例： Razor 頁面 web 應用程式範例，該範例會以 Windows 服務的形式執行，並具有適用于背景工作的 [託管服務](xref:fundamentals/host/hosted-services) 。

如需 MVC 指引，請參閱和底下的文章 <xref:mvc/overview> <xref:migration/22-to-30> 。

## <a name="deployment-type"></a>部署類型

如需詳細資訊與部署案例建議，請參閱 [.NET Core 應用程式部署](/dotnet/core/deploying/)。

### <a name="sdk"></a>SDK

針對使用頁面或 MVC 架構的 web 應用程式型服務 Razor ，請在專案檔中指定 WEB SDK：

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

如果服務只會執行背景工作 (例如， [託管服務](xref:fundamentals/host/hosted-services)) ，請在專案檔中指定背景工作 SDK：

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>架構相依部署 (FDD)

架構相依部署 (FDD) 仰賴存在於目標系統上的全系統共用 .NET Core 版本。 依照此文章中的指導方針採用 FDD 案例時，SDK 會產生可執行檔 (*.exe*)，稱為「架構相依可執行檔」。

如果使用 [WEB SDK](#sdk)，則在發佈 ASP.NET Core 應用程式時通常會產生的 *web.config* 檔案，對 Windows 服務應用程式而言是不必要的。 若要停用 *web.config* 檔案的建立，請新增 `<IsTransformWebConfigDisabled>` 屬性集到 `true`。

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>自封式部署 (SCD)

自封式部署 (SCD) 不仰賴任何存在於主機系統上的共用架構。 執行階段與應用程式的相依性會隨著應用程式進行部署。

Windows [執行階段識別碼 (RID)](/dotnet/core/rid-catalog) 會納入包含目標 Framework 的 `<PropertyGroup>` 中：

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

發行多個 RID：

* 以分號分隔的清單提供 RID。
* 使用屬性名稱 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (複數) 。

如需詳細資訊，請參閱 [.NET Core RID 目錄](/dotnet/core/rid-catalog)。

## <a name="service-user-account"></a>服務使用者帳戶

若要為服務建立使用者帳戶，請從系統管理 PowerShell 6 命令殼層使用 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) Cmdlet。

Windows 10 2018 年 10 月更新 (版本 1809/組建 10.0.17763) 或更新版本：

```powershell
New-LocalUser -Name {SERVICE NAME}
```

在 Windows 10 2018 年 10 月更新之前 (1809 版/組建 10.0.17763) 的 Windows OS 上：

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

在系統提示時提供[強式密碼](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)。

除非搭配過期 <xref:System.DateTime> 將 `-AccountExpires` 參數提供給 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) Cmdlet，否則該帳戶將不會過期。

如需詳細資訊，請參閱 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 和[服務使用者帳戶](/windows/desktop/services/service-user-accounts)。

使用 Active Directory 時有一個管理使用者的替代方法，就是使用「受控服務帳戶」。 如需詳細資訊，請參閱[群組受控服務帳戶概觀](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。

## <a name="log-on-as-a-service-rights"></a>以服務方式登入權限

若要為服務使用者帳戶建立「以服務方式登入」權限：

1. 執行 *secpol.msc* 來開啟 [本機安全性原則編輯器]。
1. 展開 [本機原則] 節點，然後選取 [使用者權限指派]。
1. 開啟 [以服務方式登入] 原則。
1. 選取 [新增使用者或群組]。
1. 使用下列其中一種方法提供物件名稱 (使用者帳戶)：
   1. 在物件名稱欄位中輸入使用者帳戶 (`{DOMAIN OR COMPUTER NAME\USER}`)，然後選取 [確定] 將使用者新增至原則。
   1. 選取 [進階]  。 選取 [立即尋找]。 從清單中選取使用者帳戶。 選取 [確定]。 再次選取 [確定] 將使用者新增至原則。
1. 選取 [確定] 或 [套用] 以接受變更。

## <a name="create-and-manage-the-windows-service"></a>建立及管理 Windows 服務

### <a name="create-a-service"></a>建立服務

使用 PowerShell 命令來註冊服務。 透過系統管理 PowerShell 6 命令殼層，執行下列命令：

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = "{DOMAIN OR COMPUTER NAME\USER}", "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName "{EXE FILE PATH}" -Credential "{DOMAIN OR COMPUTER NAME\USER}" -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`：應用程式在主機上的資料夾路徑 (例如 `d:\myservice`) 。 請勿包含路徑中應用程式的可執行檔。 不需要結尾的斜線。
* `{DOMAIN OR COMPUTER NAME\USER}`：服務使用者帳戶 (例如 `Contoso\ServiceUser`) 。
* `{SERVICE NAME}`：服務名稱 (例如 `MyService`) 。
* `{EXE FILE PATH}`：應用程式的可執行檔路徑 (例如 `d:\myservice\myservice.exe`) 。 包含可執行檔的檔案名稱 (包含副檔名)。
* `{DESCRIPTION}`：服務描述 (例如 `My sample service`) 。
* `{DISPLAY NAME}`：服務顯示名稱 (例如 `My Service`) 。

### <a name="start-a-service"></a>啟動服務

以下列 PowerShell 6 命令啟動服務：

```powershell
Start-Service -Name {SERVICE NAME}
```

此命令需要幾秒鐘啓動服務。

### <a name="determine-a-services-status"></a>判斷服務的狀態

若要檢查服務狀態，請使用下列 PowerShell 6 命令：

```powershell
Get-Service -Name {SERVICE NAME}
```

狀態會回報為下列值之一：

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>停止服務

使用下列 Powershell 6 命令停止服務：

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>移除服務

在停止服務的短暫延遲之後，請以下列 Powershell 6 命令移除服務：

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy 伺服器和負載平衡器案例

服務如果會與來自網際網路或公司網路的要求進行互動，並且位於 Proxy 或負載平衡器後方，可能會需要額外的設定。 如需詳細資訊，請參閱<xref:host-and-deploy/proxy-load-balancer>。

## <a name="configure-endpoints"></a>設定端點

ASP.NET Core 預設會繫結至 `http://localhost:5000`。 藉由設定環境變數來設定 URL 和埠 `ASPNETCORE_URLS` 。

如需其他 URL 和埠設定方法，請參閱相關的伺服器文章：

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* <xref:fundamentals/servers/kestrel/endpoints>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

* <xref:fundamentals/servers/kestrel#endpoint-configuration>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <xref:fundamentals/servers/httpsys#configure-windows-server>

上述指引涵蓋 HTTPS 端點的支援。 例如，在搭配 Windows 服務使用驗證時，請設定 HTTPS 的應用程式。

> [!NOTE]
> 不支援使用 ASP.NET Core HTTPS 開發憑證來保護服務端點。

## <a name="current-directory-and-content-root"></a>目前目錄和內容根目錄

呼叫 Windows 服務所傳回的目前工作目錄 <xref:System.IO.Directory.GetCurrentDirectory%2A> 是 *C： \\ Windows \\ system32* 資料夾。 *System32* 資料夾不是儲存服務檔案 (例如，設定檔) 的合適位置。 使用下列其中一個方式來維護及存取服務的資產與設定檔。

### <a name="use-contentrootpath-or-contentrootfileprovider"></a>使用 ContentRootPath 或 ContentRootFileProvider

使用 [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) 或 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> 來找出應用程式的資源。

當應用程式以服務的形式執行時，會 <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService%2A> 將設定 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> 為 [AppCoNtext. BaseDirectory](xref:System.AppContext.BaseDirectory)。

應用程式的預設設定檔案， *appsettings.json* 以及 *appsettings. {* 從應用程式的內容根目錄載入環境} json，方法是 [在主機結構期間呼叫 >createdefaultbuilder](xref:fundamentals/host/generic-host#set-up-a-host)。

若為開發人員程式碼在中載入的其他設定檔案 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> ，則不需要呼叫 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> 。 在下列範例中，檔案 *上的custom_settings.js* 會存在於應用程式的內容根目錄中，並在未明確設定基底路徑的情況下載入：

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

請勿嘗試使用 <xref:System.IO.Directory.GetCurrentDirectory%2A> 來取得資源路徑，因為 Windows 服務應用程式會傳回 *C： \\ windows \\ system32* 資料夾做為其目前的目錄。

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>將服務的檔案儲存在磁碟上的適當位置

使用包含檔案的 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 資料夾，使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> 來指定絕對路徑。

## <a name="troubleshoot"></a>疑難排解

若要針對 Windows 服務應用程式進行疑難排解，請參閱 <xref:test/troubleshoot> 。

### <a name="common-errors"></a>常見錯誤

* 舊版或發行前版本的 PowerShell 正在使用中。
* 註冊的服務不會使用來自 [dotnet publish](/dotnet/core/tools/dotnet-publish)命令的應用程式 **已發佈** 輸出。 應用程式部署不支援 [dotnet 組建](/dotnet/core/tools/dotnet-build) 命令的輸出。 根據部署類型，您可以在下列其中一個資料夾中找到已發佈的資產：
  * *bin/Release/{目標 FRAMEWORK}/publish* (FDD) 
  * *bin/Release/{目標 FRAMEWORK}/{RUNTIME 識別碼}/publish* (SCD) 
* 服務未處於執行中狀態。
* 應用程式使用的資源路徑 (例如，憑證) 不正確。 Windows 服務的基底路徑是 *c： \\ windows \\ System32*。
* 使用者沒有 [ *以服務方式登* 入] 許可權。
* 執行 PowerShell 命令時，使用者的密碼已過期或不正確地傳遞 `New-Service` 。
* 應用程式需要 ASP.NET 核心驗證，但未設定 (HTTPS) 的安全連線。
* 要求 URL 埠不正確，或在應用程式中未正確設定。

### <a name="system-and-application-event-logs"></a>系統和應用程式事件記錄檔

存取系統和應用程式事件記錄：

1. 開啟 [開始] 功能表、搜尋 [ *事件檢視器]*，然後選取 [ **事件檢視器]** 應用程式。
1. 在 [事件檢視器] 中，開啟 [Windows 記錄] 節點。
1. 選取 [ **系統** ] 以開啟 [系統事件記錄檔]。 選取 [應用程式] 以開啟「應用程式事件記錄檔」。
1. 搜尋與失敗應用程式相關的錯誤。

### <a name="run-the-app-at-a-command-prompt"></a>在命令提示字元中執行應用程式

許多啟動錯誤都不會在事件記錄檔中產生有用的資訊。 您可以藉由在主控系統上的命令提示字元中執行應用程式，來找出一些錯誤的原因。 若要從應用程式記錄額外的詳細資料，請降低 [記錄層級](xref:fundamentals/logging/index#log-level) ，或在 [開發環境](xref:fundamentals/environments)中執行應用程式。

### <a name="clear-package-caches"></a>清除套件快取

在開發電腦上升級 .NET Core SDK 或變更應用程式內的套件版本之後，正常運作的應用程式可能會立即失敗。 在某些情況下，執行主要升級時，不一致的套件可能會中斷應用程式。 大多數這些問題都可依照下列指示來進行修正：

1. 刪除 [bin] 和 [obj] 資料夾。
1. 從命令列介面執行 [dotnet nuget 區域變數](/dotnet/core/tools/dotnet-nuget-locals) ，以清除套件快取。

   清除套件快取也可以使用 [nuget.exe](https://www.nuget.org/downloads) 工具和執行命令來完成 `nuget locals all -clear` 。 *nuget.exe* 並未隨附在 Windows 桌面作業系統的安裝中，必須另外從 [NuGet 網站](https://www.nuget.org/downloads)取得。

1. 還原並重建專案。
1. 重新部署應用程式之前，請先刪除伺服器上 [部署] 資料夾中的所有檔案。

### <a name="slow-or-hanging-app"></a>回應緩慢或無回應的應用程式

損 *毀* 傾印是系統記憶體的快照，可協助判斷應用程式損毀、啟動失敗或應用程式緩慢的原因。

#### <a name="app-crashes-or-encounters-an-exception"></a>應用程式損毀或發生例外狀況

從 [Windows 錯誤報告 (WER)](/windows/desktop/wer/windows-error-reporting) 取得並分析傾印：

1. 在 `c:\dumps` 中建立資料夾以保存損毀傾印檔案。
1. 使用應用程式可執行檔名稱來執行 [EnableDumps PowerShell 腳本](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) ：

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. 在會導致損毀的情況下，執行應用程式。
1. 發生損毀之後，請執行 [DisableDumps PowerShell 指令碼](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1)：

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

在應用程式損毀且傾印收集完成之後，即可正常結束應用程式。 PowerShell 指令碼會將 WER 設定為每個應用程式收集最多五個傾印。

> [!WARNING]
> 損毀傾印可能會佔用大量磁碟空間 (每個高達好幾 GB)。

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>應用程式停止回應、在啟動期間失敗，或正常執行

當 *應用程式* 停止回應時 (停止回應，但不會當機) 、啟動期間失敗，或正常執行時，請參閱 [使用者模式](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) 傾印檔案：選擇最適合的工具來選取適當的工具來產生傾印。

#### <a name="analyze-the-dump"></a>分析傾印

您可以使用數種方法來分析傾印。 如需詳細資訊，請參閱[分析使用者模式傾印檔案](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。

## <a name="additional-resources"></a>其他資源

::: moniker-end

::: moniker range=">= aspnetcore-5.0"
* [Kestrel 端點組態](xref:fundamentals/servers/kestrel/endpoints) (包括 HTTPS 組態與 SNI 支援)
::: moniker-end
::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"
* [Kestrel 端點組態](xref:fundamentals/servers/kestrel#endpoint-configuration) (包括 HTTPS 組態與 SNI 支援)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

ASP.NET Core 應用程式可以裝載在 Windows 上作為 [Windows 服務](/dotnet/framework/windows-services/introduction-to-windows-service-applications)，不需要使用 IIS。 當裝載為 Windows 服務時，應用程式將會在伺服器重新開機後自動啟動。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="prerequisites"></a>必要條件

* [ASP.NET Core SDK 2.1 或更新版本](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 或更新版本](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>應用程式組態

應用程式需要 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 和 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 的套件參考。

若要在於服務外執行時測試及偵錯，請新增程式碼以判斷應用程式是以服務形式執行或以主控台應用程式形式執行。 檢查偵錯工具是否已附加或 `--console` 切換參數是否存在。 若任一條件為真 (應用程式不是以服務形式執行)，請呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>。 若條件為偽 (應用程式是以服務形式執行)：

* 呼叫 <xref:System.IO.Directory.SetCurrentDirectory*> 並使用應用程式發佈位置的路徑。 請勿呼叫 <xref:System.IO.Directory.GetCurrentDirectory*> 來取得路徑，因為當呼叫 <xref:System.IO.Directory.GetCurrentDirectory*> 時，Windows 服務應用程式會傳回 *C:\\WINDOWS\\system32* 資料夾。 如需詳細資訊，請參閱[目前目錄與內容根目錄](#current-directory-and-content-root)一節。 在 `CreateWebHostBuilder` 中設定應用程式之前執行此步驟。
* 呼叫 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 以服務形式執行應用程式。

因為[命令列設定提供者](xref:fundamentals/configuration/index#command-line-configuration-provider)需要命令列引數的名稱值組，在 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 收到引數之前，`--console` 切換參數就會從引數中移除。

若要寫入 Windows 事件記錄檔，請新增 EventLog 提供者到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>。 使用 *appsettings.Production.json* 檔案中的 `Logging:LogLevel:Default` 機碼設定記錄層級。

在範例應用程式的下列範例中，為了處理應用程式內的存留期事件，會呼叫 `RunAsCustomService` 而不是 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>。 如需詳細資訊，請參閱[處理開始與停止事件](#handle-starting-and-stopping-events)一節。

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>部署類型

如需詳細資訊與部署案例建議，請參閱 [.NET Core 應用程式部署](/dotnet/core/deploying/)。

### <a name="sdk"></a>SDK

針對使用頁面或 MVC 架構的 web 應用程式型服務 Razor ，請在專案檔中指定 WEB SDK：

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

如果服務只會執行背景工作 (例如， [託管服務](xref:fundamentals/host/hosted-services)) ，請在專案檔中指定背景工作 SDK：

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>架構相依部署 (FDD)

架構相依部署 (FDD) 仰賴存在於目標系統上的全系統共用 .NET Core 版本。 依照此文章中的指導方針採用 FDD 案例時，SDK 會產生可執行檔 (*.exe*)，稱為「架構相依可執行檔」。

Windows [執行時間識別碼 (RID) ](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) 包含目標 framework。 在下列範例中，RID 已設定為 `win7-x64`。 `<SelfContained>` 屬性會設定為 `false`。 這些屬性會指示 SDK，針對 Windows 和相依於共用 .NET Core framework 的應用程式產生可執行檔 (*.exe*)。

針對 Windows Services 應用程式，不需要 *web.config* 檔案 (發行 ASP.NET Core 應用程式時通常會產生此檔案)。 若要停用 *web.config* 檔案的建立，請新增 `<IsTransformWebConfigDisabled>` 屬性集到 `true`。

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>自封式部署 (SCD)

自封式部署 (SCD) 不仰賴任何存在於主機系統上的共用架構。 執行階段與應用程式的相依性會隨著應用程式進行部署。

Windows [執行階段識別碼 (RID)](/dotnet/core/rid-catalog) 會納入包含目標 Framework 的 `<PropertyGroup>` 中：

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

發行多個 RID：

* 以分號分隔的清單提供 RID。
* 使用屬性名稱 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (複數) 。

如需詳細資訊，請參閱 [.NET Core RID 目錄](/dotnet/core/rid-catalog)。

`<SelfContained>` 屬性設定為 `true`：

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>服務使用者帳戶

若要為服務建立使用者帳戶，請從系統管理 PowerShell 6 命令殼層使用 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) Cmdlet。

Windows 10 2018 年 10 月更新 (版本 1809/組建 10.0.17763) 或更新版本：

```powershell
New-LocalUser -Name {SERVICE NAME}
```

在 Windows 10 2018 年 10 月更新之前 (1809 版/組建 10.0.17763) 的 Windows OS 上：

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

在系統提示時提供[強式密碼](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)。

除非搭配過期 <xref:System.DateTime> 將 `-AccountExpires` 參數提供給 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) Cmdlet，否則該帳戶將不會過期。

如需詳細資訊，請參閱 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 和[服務使用者帳戶](/windows/desktop/services/service-user-accounts)。

使用 Active Directory 時有一個管理使用者的替代方法，就是使用「受控服務帳戶」。 如需詳細資訊，請參閱[群組受控服務帳戶概觀](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。

## <a name="log-on-as-a-service-rights"></a>以服務方式登入權限

若要為服務使用者帳戶建立「以服務方式登入」權限：

1. 執行 *secpol.msc* 來開啟 [本機安全性原則編輯器]。
1. 展開 [本機原則] 節點，然後選取 [使用者權限指派]。
1. 開啟 [以服務方式登入] 原則。
1. 選取 [新增使用者或群組]。
1. 使用下列其中一種方法提供物件名稱 (使用者帳戶)：
   1. 在物件名稱欄位中輸入使用者帳戶 (`{DOMAIN OR COMPUTER NAME\USER}`)，然後選取 [確定] 將使用者新增至原則。
   1. 選取 [進階]  。 選取 [立即尋找]。 從清單中選取使用者帳戶。 選取 [確定]。 再次選取 [確定] 將使用者新增至原則。
1. 選取 [確定] 或 [套用] 以接受變更。

## <a name="create-and-manage-the-windows-service"></a>建立及管理 Windows 服務

### <a name="create-a-service"></a>建立服務

使用 PowerShell 命令來註冊服務。 透過系統管理 PowerShell 6 命令殼層，執行下列命令：

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`：應用程式在主機上的資料夾路徑 (例如 `d:\myservice`) 。 請勿包含路徑中應用程式的可執行檔。 不需要結尾的斜線。
* `{DOMAIN OR COMPUTER NAME\USER}`：服務使用者帳戶 (例如 `Contoso\ServiceUser`) 。
* `{SERVICE NAME}`：服務名稱 (例如 `MyService`) 。
* `{EXE FILE PATH}`：應用程式的可執行檔路徑 (例如 `d:\myservice\myservice.exe`) 。 包含可執行檔的檔案名稱 (包含副檔名)。
* `{DESCRIPTION}`：服務描述 (例如 `My sample service`) 。
* `{DISPLAY NAME}`：服務顯示名稱 (例如 `My Service`) 。

### <a name="start-a-service"></a>啟動服務

以下列 PowerShell 6 命令啟動服務：

```powershell
Start-Service -Name {SERVICE NAME}
```

此命令需要幾秒鐘啓動服務。

### <a name="determine-a-services-status"></a>判斷服務的狀態

若要檢查服務狀態，請使用下列 PowerShell 6 命令：

```powershell
Get-Service -Name {SERVICE NAME}
```

狀態會回報為下列值之一：

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>停止服務

使用下列 Powershell 6 命令停止服務：

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>移除服務

在停止服務的短暫延遲之後，請以下列 Powershell 6 命令移除服務：

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>處理開始與停止事件

若要處理 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 與 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 事件：

1. 使用 `OnStarting`、`OnStarted` 與 `OnStopping` 方法建立衍生自 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> 的類別：

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. 為 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 建立一個會將 `CustomWebHostService` 傳遞給 <xref:System.ServiceProcess.ServiceBase.Run*> 的擴充方法：

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. 在 `Program.Main` 中，呼叫 `RunAsCustomService` 擴充方法，而不是呼叫 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>：

   ```csharp
   host.RunAsCustomService();
   ```

   若要查看 `Program.Main` 中的 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 位置，請參閱[部署類型](#deployment-type)一節中的範例程式碼。

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy 伺服器和負載平衡器案例

服務如果會與來自網際網路或公司網路的要求進行互動，並且位於 Proxy 或負載平衡器後方，可能會需要額外的設定。 如需詳細資訊，請參閱<xref:host-and-deploy/proxy-load-balancer>。

## <a name="configure-endpoints"></a>設定端點

ASP.NET Core 預設會繫結至 `http://localhost:5000`。 藉由設定環境變數來設定 URL 和埠 `ASPNETCORE_URLS` 。

如需其他 URL 和埠設定方法，請參閱相關的伺服器文章：

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

上述指引涵蓋 HTTPS 端點的支援。 例如，在搭配 Windows 服務使用驗證時，請設定 HTTPS 的應用程式。

> [!NOTE]
> 不支援使用 ASP.NET Core HTTPS 開發憑證來保護服務端點。

## <a name="current-directory-and-content-root"></a>目前目錄和內容根目錄

呼叫 Windows 服務所傳回的目前工作目錄 <xref:System.IO.Directory.GetCurrentDirectory*> 是 *C： \\ Windows \\ system32* 資料夾。 *System32* 資料夾不是儲存服務檔案 (例如，設定檔) 的合適位置。 使用下列其中一個方式來維護及存取服務的資產與設定檔。

### <a name="set-the-content-root-path-to-the-apps-folder"></a>將內容根目錄路徑設定到應用程式的資料夾

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> 與建立服務時提供給 `binPath` 引數的路徑相同。 `GetCurrentDirectory`請呼叫 <xref:System.IO.Directory.SetCurrentDirectory*> 應用程式[內容根目錄](xref:fundamentals/index#content-root)的路徑，而不是呼叫來建立設定檔的路徑。

在 `Program.Main` 中，判斷服務可執行檔資料夾的路徑，然後使用該路徑來建立應用程式的內容根目錄：

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>將服務的檔案儲存在磁碟上的適當位置

使用包含檔案的 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 資料夾，使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 來指定絕對路徑。

## <a name="troubleshoot"></a>疑難排解

若要針對 Windows 服務應用程式進行疑難排解，請參閱 <xref:test/troubleshoot> 。

### <a name="common-errors"></a>常見錯誤

* 舊版或發行前版本的 PowerShell 正在使用中。
* 註冊的服務不會使用來自 [dotnet publish](/dotnet/core/tools/dotnet-publish)命令的應用程式 **已發佈** 輸出。 應用程式部署不支援 [dotnet 組建](/dotnet/core/tools/dotnet-build) 命令的輸出。 根據部署類型，您可以在下列其中一個資料夾中找到已發佈的資產：
  * *bin/Release/{目標 FRAMEWORK}/publish* (FDD) 
  * *bin/Release/{目標 FRAMEWORK}/{RUNTIME 識別碼}/publish* (SCD) 
* 服務未處於執行中狀態。
* 應用程式使用的資源路徑 (例如，憑證) 不正確。 Windows 服務的基底路徑是 *c： \\ windows \\ System32*。
* 使用者沒有 [ *以服務方式登* 入] 許可權。
* 執行 PowerShell 命令時，使用者的密碼已過期或不正確地傳遞 `New-Service` 。
* 應用程式需要 ASP.NET 核心驗證，但未設定 (HTTPS) 的安全連線。
* 要求 URL 埠不正確，或在應用程式中未正確設定。

### <a name="system-and-application-event-logs"></a>系統和應用程式事件記錄檔

存取系統和應用程式事件記錄：

1. 開啟 [開始] 功能表、搜尋 [ *事件檢視器]*，然後選取 [ **事件檢視器]** 應用程式。
1. 在 [事件檢視器] 中，開啟 [Windows 記錄] 節點。
1. 選取 [ **系統** ] 以開啟 [系統事件記錄檔]。 選取 [應用程式] 以開啟「應用程式事件記錄檔」。
1. 搜尋與失敗應用程式相關的錯誤。

### <a name="run-the-app-at-a-command-prompt"></a>在命令提示字元中執行應用程式

許多啟動錯誤都不會在事件記錄檔中產生有用的資訊。 您可以藉由在主控系統上的命令提示字元中執行應用程式，來找出一些錯誤的原因。 若要從應用程式記錄額外的詳細資料，請降低 [記錄層級](xref:fundamentals/logging/index#log-level) ，或在 [開發環境](xref:fundamentals/environments)中執行應用程式。

### <a name="clear-package-caches"></a>清除套件快取

在開發電腦上升級 .NET Core SDK 或變更應用程式內的套件版本之後，正常運作的應用程式可能會立即失敗。 在某些情況下，執行主要升級時，不一致的套件可能會中斷應用程式。 大多數這些問題都可依照下列指示來進行修正：

1. 刪除 [bin] 和 [obj] 資料夾。
1. 從命令列介面執行 [dotnet nuget 區域變數](/dotnet/core/tools/dotnet-nuget-locals) ，以清除套件快取。

   清除套件快取也可以使用 [nuget.exe](https://www.nuget.org/downloads) 工具和執行命令來完成 `nuget locals all -clear` 。 *nuget.exe* 並未隨附在 Windows 桌面作業系統的安裝中，必須另外從 [NuGet 網站](https://www.nuget.org/downloads)取得。

1. 還原並重建專案。
1. 重新部署應用程式之前，請先刪除伺服器上 [部署] 資料夾中的所有檔案。

### <a name="slow-or-hanging-app"></a>回應緩慢或無回應的應用程式

損 *毀* 傾印是系統記憶體的快照，可協助判斷應用程式損毀、啟動失敗或應用程式緩慢的原因。

#### <a name="app-crashes-or-encounters-an-exception"></a>應用程式損毀或發生例外狀況

從 [Windows 錯誤報告 (WER)](/windows/desktop/wer/windows-error-reporting) 取得並分析傾印：

1. 在 `c:\dumps` 中建立資料夾以保存損毀傾印檔案。
1. 使用應用程式可執行檔名稱來執行 [EnableDumps PowerShell 腳本](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) ：

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. 在會導致損毀的情況下，執行應用程式。
1. 發生損毀之後，請執行 [DisableDumps PowerShell 指令碼](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)：

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

在應用程式損毀且傾印收集完成之後，即可正常結束應用程式。 PowerShell 指令碼會將 WER 設定為每個應用程式收集最多五個傾印。

> [!WARNING]
> 損毀傾印可能會佔用大量磁碟空間 (每個高達好幾 GB)。

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>應用程式停止回應、在啟動期間失敗，或正常執行

當 *應用程式* 停止回應時 (停止回應，但不會當機) 、啟動期間失敗，或正常執行時，請參閱 [使用者模式](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) 傾印檔案：選擇最適合的工具來選取適當的工具來產生傾印。

#### <a name="analyze-the-dump"></a>分析傾印

您可以使用數種方法來分析傾印。 如需詳細資訊，請參閱[分析使用者模式傾印檔案](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。

## <a name="additional-resources"></a>其他資源

* [Kestrel 端點組態](xref:fundamentals/servers/kestrel#endpoint-configuration) (包括 HTTPS 組態與 SNI 支援)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

ASP.NET Core 應用程式可以裝載在 Windows 上作為 [Windows 服務](/dotnet/framework/windows-services/introduction-to-windows-service-applications)，不需要使用 IIS。 當裝載為 Windows 服務時，應用程式將會在伺服器重新開機後自動啟動。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="prerequisites"></a>必要條件

* [ASP.NET Core SDK 2.1 或更新版本](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 或更新版本](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>應用程式組態

應用程式需要 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 和 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 的套件參考。

若要在於服務外執行時測試及偵錯，請新增程式碼以判斷應用程式是以服務形式執行或以主控台應用程式形式執行。 檢查偵錯工具是否已附加或 `--console` 切換參數是否存在。 若任一條件為真 (應用程式不是以服務形式執行)，請呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>。 若條件為偽 (應用程式是以服務形式執行)：

* 呼叫 <xref:System.IO.Directory.SetCurrentDirectory*> 並使用應用程式發佈位置的路徑。 請勿呼叫 <xref:System.IO.Directory.GetCurrentDirectory*> 來取得路徑，因為當呼叫 <xref:System.IO.Directory.GetCurrentDirectory*> 時，Windows 服務應用程式會傳回 *C:\\WINDOWS\\system32* 資料夾。 如需詳細資訊，請參閱[目前目錄與內容根目錄](#current-directory-and-content-root)一節。 在 `CreateWebHostBuilder` 中設定應用程式之前執行此步驟。
* 呼叫 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 以服務形式執行應用程式。

因為[命令列設定提供者](xref:fundamentals/configuration/index#command-line-configuration-provider)需要命令列引數的名稱值組，在 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 收到引數之前，`--console` 切換參數就會從引數中移除。

若要寫入 Windows 事件記錄檔，請新增 EventLog 提供者到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>。 使用 *appsettings.Production.json* 檔案中的 `Logging:LogLevel:Default` 機碼設定記錄層級。

在範例應用程式的下列範例中，為了處理應用程式內的存留期事件，會呼叫 `RunAsCustomService` 而不是 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>。 如需詳細資訊，請參閱[處理開始與停止事件](#handle-starting-and-stopping-events)一節。

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>部署類型

如需詳細資訊與部署案例建議，請參閱 [.NET Core 應用程式部署](/dotnet/core/deploying/)。

### <a name="sdk"></a>SDK

針對使用頁面或 MVC 架構的 web 應用程式型服務 Razor ，請在專案檔中指定 WEB SDK：

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

如果服務只會執行背景工作 (例如， [託管服務](xref:fundamentals/host/hosted-services)) ，請在專案檔中指定背景工作 SDK：

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>架構相依部署 (FDD)

架構相依部署 (FDD) 仰賴存在於目標系統上的全系統共用 .NET Core 版本。 依照此文章中的指導方針採用 FDD 案例時，SDK 會產生可執行檔 (*.exe*)，稱為「架構相依可執行檔」。

Windows [執行時間識別碼 (RID) ](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) 包含目標 framework。 在下列範例中，RID 已設定為 `win7-x64`。 `<SelfContained>` 屬性會設定為 `false`。 這些屬性會指示 SDK，針對 Windows 和相依於共用 .NET Core framework 的應用程式產生可執行檔 (*.exe*)。

`<UseAppHost>` 屬性會設定為 `true`。 此屬性為服務提供 FDD 的啟用路徑 (可執行檔 *.exe*)。

針對 Windows Services 應用程式，不需要 *web.config* 檔案 (發行 ASP.NET Core 應用程式時通常會產生此檔案)。 若要停用 *web.config* 檔案的建立，請新增 `<IsTransformWebConfigDisabled>` 屬性集到 `true`。

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>自封式部署 (SCD)

自封式部署 (SCD) 不仰賴任何存在於主機系統上的共用架構。 執行階段與應用程式的相依性會隨著應用程式進行部署。

Windows [執行階段識別碼 (RID)](/dotnet/core/rid-catalog) 會納入包含目標 Framework 的 `<PropertyGroup>` 中：

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

發行多個 RID：

* 以分號分隔的清單提供 RID。
* 使用屬性名稱 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (複數) 。

如需詳細資訊，請參閱 [.NET Core RID 目錄](/dotnet/core/rid-catalog)。

`<SelfContained>` 屬性設定為 `true`：

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>服務使用者帳戶

若要為服務建立使用者帳戶，請從系統管理 PowerShell 6 命令殼層使用 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) Cmdlet。

Windows 10 2018 年 10 月更新 (版本 1809/組建 10.0.17763) 或更新版本：

```powershell
New-LocalUser -Name {SERVICE NAME}
```

在 Windows 10 2018 年 10 月更新之前 (1809 版/組建 10.0.17763) 的 Windows OS 上：

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

在系統提示時提供[強式密碼](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)。

除非搭配過期 <xref:System.DateTime> 將 `-AccountExpires` 參數提供給 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) Cmdlet，否則該帳戶將不會過期。

如需詳細資訊，請參閱 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 和[服務使用者帳戶](/windows/desktop/services/service-user-accounts)。

使用 Active Directory 時有一個管理使用者的替代方法，就是使用「受控服務帳戶」。 如需詳細資訊，請參閱[群組受控服務帳戶概觀](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。

## <a name="log-on-as-a-service-rights"></a>以服務方式登入權限

若要為服務使用者帳戶建立「以服務方式登入」權限：

1. 執行 *secpol.msc* 來開啟 [本機安全性原則編輯器]。
1. 展開 [本機原則] 節點，然後選取 [使用者權限指派]。
1. 開啟 [以服務方式登入] 原則。
1. 選取 [新增使用者或群組]。
1. 使用下列其中一種方法提供物件名稱 (使用者帳戶)：
   1. 在物件名稱欄位中輸入使用者帳戶 (`{DOMAIN OR COMPUTER NAME\USER}`)，然後選取 [確定] 將使用者新增至原則。
   1. 選取 [進階]  。 選取 [立即尋找]。 從清單中選取使用者帳戶。 選取 [確定]。 再次選取 [確定] 將使用者新增至原則。
1. 選取 [確定] 或 [套用] 以接受變更。

## <a name="create-and-manage-the-windows-service"></a>建立及管理 Windows 服務

### <a name="create-a-service"></a>建立服務

使用 PowerShell 命令來註冊服務。 透過系統管理 PowerShell 6 命令殼層，執行下列命令：

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`：應用程式在主機上的資料夾路徑 (例如 `d:\myservice`) 。 請勿包含路徑中應用程式的可執行檔。 不需要結尾的斜線。
* `{DOMAIN OR COMPUTER NAME\USER}`：服務使用者帳戶 (例如 `Contoso\ServiceUser`) 。
* `{SERVICE NAME}`：服務名稱 (例如 `MyService`) 。
* `{EXE FILE PATH}`：應用程式的可執行檔路徑 (例如 `d:\myservice\myservice.exe`) 。 包含可執行檔的檔案名稱 (包含副檔名)。
* `{DESCRIPTION}`：服務描述 (例如 `My sample service`) 。
* `{DISPLAY NAME}`：服務顯示名稱 (例如 `My Service`) 。

### <a name="start-a-service"></a>啟動服務

以下列 PowerShell 6 命令啟動服務：

```powershell
Start-Service -Name {SERVICE NAME}
```

此命令需要幾秒鐘啓動服務。

### <a name="determine-a-services-status"></a>判斷服務的狀態

若要檢查服務狀態，請使用下列 PowerShell 6 命令：

```powershell
Get-Service -Name {SERVICE NAME}
```

狀態會回報為下列值之一：

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>停止服務

使用下列 Powershell 6 命令停止服務：

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>移除服務

在停止服務的短暫延遲之後，請以下列 Powershell 6 命令移除服務：

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>處理開始與停止事件

若要處理 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 與 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 事件：

1. 使用 `OnStarting`、`OnStarted` 與 `OnStopping` 方法建立衍生自 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> 的類別：

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. 為 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 建立一個會將 `CustomWebHostService` 傳遞給 <xref:System.ServiceProcess.ServiceBase.Run*> 的擴充方法：

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. 在 `Program.Main` 中，呼叫 `RunAsCustomService` 擴充方法，而不是呼叫 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>：

   ```csharp
   host.RunAsCustomService();
   ```

   若要查看 `Program.Main` 中的 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 位置，請參閱[部署類型](#deployment-type)一節中的範例程式碼。

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy 伺服器和負載平衡器案例

服務如果會與來自網際網路或公司網路的要求進行互動，並且位於 Proxy 或負載平衡器後方，可能會需要額外的設定。 如需詳細資訊，請參閱<xref:host-and-deploy/proxy-load-balancer>。

## <a name="configure-endpoints"></a>設定端點

ASP.NET Core 預設會繫結至 `http://localhost:5000`。 藉由設定環境變數來設定 URL 和埠 `ASPNETCORE_URLS` 。

如需其他 URL 和埠設定方法，請參閱相關的伺服器文章：

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

上述指引涵蓋 HTTPS 端點的支援。 例如，在搭配 Windows 服務使用驗證時，請設定 HTTPS 的應用程式。

> [!NOTE]
> 不支援使用 ASP.NET Core HTTPS 開發憑證來保護服務端點。

## <a name="current-directory-and-content-root"></a>目前目錄和內容根目錄

呼叫 Windows 服務所傳回的目前工作目錄 <xref:System.IO.Directory.GetCurrentDirectory*> 是 *C： \\ Windows \\ system32* 資料夾。 *System32* 資料夾不是儲存服務檔案 (例如，設定檔) 的合適位置。 使用下列其中一個方式來維護及存取服務的資產與設定檔。

### <a name="set-the-content-root-path-to-the-apps-folder"></a>將內容根目錄路徑設定到應用程式的資料夾

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> 與建立服務時提供給 `binPath` 引數的路徑相同。 `GetCurrentDirectory`請呼叫 <xref:System.IO.Directory.SetCurrentDirectory*> 應用程式[內容根目錄](xref:fundamentals/index#content-root)的路徑，而不是呼叫來建立設定檔的路徑。

在 `Program.Main` 中，判斷服務可執行檔資料夾的路徑，然後使用該路徑來建立應用程式的內容根目錄：

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>將服務的檔案儲存在磁碟上的適當位置

使用包含檔案的 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 資料夾，使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 來指定絕對路徑。

## <a name="troubleshoot"></a>疑難排解

若要針對 Windows 服務應用程式進行疑難排解，請參閱 <xref:test/troubleshoot> 。

### <a name="common-errors"></a>常見錯誤

* 舊版或發行前版本的 PowerShell 正在使用中。
* 註冊的服務不會使用來自 [dotnet publish](/dotnet/core/tools/dotnet-publish)命令的應用程式 **已發佈** 輸出。 應用程式部署不支援 [dotnet 組建](/dotnet/core/tools/dotnet-build) 命令的輸出。 根據部署類型，您可以在下列其中一個資料夾中找到已發佈的資產：
  * *bin/Release/{目標 FRAMEWORK}/publish* (FDD) 
  * *bin/Release/{目標 FRAMEWORK}/{RUNTIME 識別碼}/publish* (SCD) 
* 服務未處於執行中狀態。
* 應用程式使用的資源路徑 (例如，憑證) 不正確。 Windows 服務的基底路徑是 *c： \\ windows \\ System32*。
* 使用者沒有 [ *以服務方式登* 入] 許可權。
* 執行 PowerShell 命令時，使用者的密碼已過期或不正確地傳遞 `New-Service` 。
* 應用程式需要 ASP.NET 核心驗證，但未設定 (HTTPS) 的安全連線。
* 要求 URL 埠不正確，或在應用程式中未正確設定。

### <a name="system-and-application-event-logs"></a>系統和應用程式事件記錄檔

存取系統和應用程式事件記錄：

1. 開啟 [開始] 功能表、搜尋 [ *事件檢視器]*，然後選取 [ **事件檢視器]** 應用程式。
1. 在 [事件檢視器] 中，開啟 [Windows 記錄] 節點。
1. 選取 [ **系統** ] 以開啟 [系統事件記錄檔]。 選取 [應用程式] 以開啟「應用程式事件記錄檔」。
1. 搜尋與失敗應用程式相關的錯誤。

### <a name="run-the-app-at-a-command-prompt"></a>在命令提示字元中執行應用程式

許多啟動錯誤都不會在事件記錄檔中產生有用的資訊。 您可以藉由在主控系統上的命令提示字元中執行應用程式，來找出一些錯誤的原因。 若要從應用程式記錄額外的詳細資料，請降低 [記錄層級](xref:fundamentals/logging/index#log-level) ，或在 [開發環境](xref:fundamentals/environments)中執行應用程式。

### <a name="clear-package-caches"></a>清除套件快取

在開發電腦上升級 .NET Core SDK 或變更應用程式內的套件版本之後，正常運作的應用程式可能會立即失敗。 在某些情況下，執行主要升級時，不一致的套件可能會中斷應用程式。 大多數這些問題都可依照下列指示來進行修正：

1. 刪除 [bin] 和 [obj] 資料夾。
1. 從命令列介面執行 [dotnet nuget 區域變數](/dotnet/core/tools/dotnet-nuget-locals) ，以清除套件快取。

   清除套件快取也可以使用 [nuget.exe](https://www.nuget.org/downloads) 工具和執行命令來完成 `nuget locals all -clear` 。 *nuget.exe* 並未隨附在 Windows 桌面作業系統的安裝中，必須另外從 [NuGet 網站](https://www.nuget.org/downloads)取得。

1. 還原並重建專案。
1. 重新部署應用程式之前，請先刪除伺服器上 [部署] 資料夾中的所有檔案。

### <a name="slow-or-hanging-app"></a>回應緩慢或無回應的應用程式

損 *毀* 傾印是系統記憶體的快照，可協助判斷應用程式損毀、啟動失敗或應用程式緩慢的原因。

#### <a name="app-crashes-or-encounters-an-exception"></a>應用程式損毀或發生例外狀況

從 [Windows 錯誤報告 (WER)](/windows/desktop/wer/windows-error-reporting) 取得並分析傾印：

1. 在 `c:\dumps` 中建立資料夾以保存損毀傾印檔案。
1. 使用應用程式可執行檔名稱來執行 [EnableDumps PowerShell 腳本](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) ：

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. 在會導致損毀的情況下，執行應用程式。
1. 發生損毀之後，請執行 [DisableDumps PowerShell 指令碼](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)：

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

在應用程式損毀且傾印收集完成之後，即可正常結束應用程式。 PowerShell 指令碼會將 WER 設定為每個應用程式收集最多五個傾印。

> [!WARNING]
> 損毀傾印可能會佔用大量磁碟空間 (每個高達好幾 GB)。

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>應用程式停止回應、在啟動期間失敗，或正常執行

當 *應用程式* 停止回應時 (停止回應，但不會當機) 、啟動期間失敗，或正常執行時，請參閱 [使用者模式](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) 傾印檔案：選擇最適合的工具來選取適當的工具來產生傾印。

#### <a name="analyze-the-dump"></a>分析傾印

您可以使用數種方法來分析傾印。 如需詳細資訊，請參閱[分析使用者模式傾印檔案](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。

## <a name="additional-resources"></a>其他資源

* [Kestrel 端點組態](xref:fundamentals/servers/kestrel#endpoint-configuration) (包括 HTTPS 組態與 SNI 支援)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
