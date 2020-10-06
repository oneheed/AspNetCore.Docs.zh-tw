---
title: 使用 ASP.NET Core 模組記錄建立和重新導向
author: rick-anderson
description: 設定 IIS 和 ASP.NET Core 模組，以捕獲記錄和診斷資訊。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/logging-and-diagnostics
ms.openlocfilehash: 9af2311dd1f42cce3547c8215af22d2613453510
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755252"
---
# <a name="log-creation-and-redirection"></a>記錄檔建立和重新導向

如果已設定 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 屬性，ASP.NET Core 模組就會將 stdout 和 stderr 主控台輸出重新導向到磁碟。 建立記錄檔後，模組會建立 `stdoutLogFile` 路徑中的所有資料夾。 應用程式集區必須具有寫入記錄檔之位置的寫入權限 (用 `IIS AppPool\{APP POOL NAME}` 來提供寫入權限，其中預留位置 `{APP POOL NAME}` 是) 的應用程式集區名稱。

除非發生處理序回收/重新啟動，否則不會輪替記錄檔。 主機服務提供者必須負責限制記錄檔所使用的磁碟空間。

只有在 IIS 上裝載時，或使用 [VISUAL STUDIO iis 的開發階段支援來](xref:host-and-deploy/iis/development-time-iis-support)針對應用程式啟動問題進行疑難排解時，才建議使用 stdout 記錄檔，而不是在本機進行偵錯工具並使用 IIS Express 執行應用程式。

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

若要建立記錄篩選規則，請參閱 ASP.NET Core 記錄檔[集的設定和](xref:fundamentals/logging/index#log-filtering)[記錄篩選](xref:fundamentals/logging/index#log-filtering)區段。

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

`logs`當記錄檔建立時，模組會建立上述範例) 中路徑 (的任何資料夾。 應用程式集區必須具有寫入記錄檔之位置的寫入權限 (用 `IIS AppPool\{APP POOL NAME}` 來提供寫入權限，其中預留位置 `{APP POOL NAME}` 是) 的應用程式集區名稱。

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
> 在部署中保持啟用偵錯記錄的時間，**不要**超過針對問題進行排解疑難所需的時間。 記錄的大小不受限制。 保持啟用偵錯記錄可能會耗盡可用磁碟空間，並讓伺服器或應用程式服務當機。

請參閱的設定，以取得檔案中元素的範例。 [ `web.config` ](xref:host-and-deploy/iis/web-config#configuration-with-webconfig) `aspNetCore` `web.config`
