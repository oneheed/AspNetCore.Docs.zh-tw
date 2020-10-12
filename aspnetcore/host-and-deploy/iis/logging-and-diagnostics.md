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
ms.openlocfilehash: 523eec53d7d21723dcf136c4e5ce299533a78cc6
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91901021"
---
# <a name="log-creation-and-redirection"></a><span data-ttu-id="dca4f-103">記錄檔建立和重新導向</span><span class="sxs-lookup"><span data-stu-id="dca4f-103">Log creation and redirection</span></span>

<span data-ttu-id="dca4f-104">如果已設定 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 屬性，ASP.NET Core 模組就會將 stdout 和 stderr 主控台輸出重新導向到磁碟。</span><span class="sxs-lookup"><span data-stu-id="dca4f-104">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="dca4f-105">建立記錄檔後，模組會建立 `stdoutLogFile` 路徑中的所有資料夾。</span><span class="sxs-lookup"><span data-stu-id="dca4f-105">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="dca4f-106">應用程式集區必須具有寫入記錄檔之位置的寫入權限 (用 `IIS AppPool\{APP POOL NAME}` 來提供寫入權限，其中預留位置 `{APP POOL NAME}` 是) 的應用程式集區名稱。</span><span class="sxs-lookup"><span data-stu-id="dca4f-106">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="dca4f-107">除非發生處理序回收/重新啟動，否則不會輪替記錄檔。</span><span class="sxs-lookup"><span data-stu-id="dca4f-107">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="dca4f-108">主機服務提供者必須負責限制記錄檔所使用的磁碟空間。</span><span class="sxs-lookup"><span data-stu-id="dca4f-108">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="dca4f-109">只有在 IIS 上裝載時，或使用 [VISUAL STUDIO iis 的開發階段支援來](xref:host-and-deploy/iis/development-time-iis-support)針對應用程式啟動問題進行疑難排解時，才建議使用 stdout 記錄檔，而不是在本機進行偵錯工具並使用 IIS Express 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="dca4f-109">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="dca4f-110">請勿將 stdout 記錄檔用來進行一般應用程式記錄。</span><span class="sxs-lookup"><span data-stu-id="dca4f-110">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="dca4f-111">針對 ASP.NET Core 應用程式中的例行性記錄，請使用會限制記錄檔大小並輪替記錄檔的記錄程式庫。</span><span class="sxs-lookup"><span data-stu-id="dca4f-111">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="dca4f-112">如需詳細資訊，請參閱[協力廠商記錄提供者](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="dca4f-112">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="dca4f-113">建立記錄檔時，系統會自動新增時間戳記和副檔名。</span><span class="sxs-lookup"><span data-stu-id="dca4f-113">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="dca4f-114">記錄檔名稱的組成方式是將時間戳記、處理序識別碼和副檔名 () 附加 `.log` 至路徑的最後一個區段 `stdoutLogFile` (通常 `stdout`) 以底線分隔。</span><span class="sxs-lookup"><span data-stu-id="dca4f-114">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="dca4f-115">如果 `stdoutLogFile` 路徑以結尾 `stdout` ，則在2/5/2018 （19:42:32）上建立的應用1934程式記錄檔名稱會是 `stdout_20180205194132_1934.log` 。</span><span class="sxs-lookup"><span data-stu-id="dca4f-115">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="dca4f-116">若 `stdoutLogEnabled` 為 false，會擷取在應用程式啟動時發生的錯誤，並發出最大 30KB 的事件記錄檔。</span><span class="sxs-lookup"><span data-stu-id="dca4f-116">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="dca4f-117">啟動之後，就會捨棄其他的記錄檔。</span><span class="sxs-lookup"><span data-stu-id="dca4f-117">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="dca4f-118">下列範例專案會 `aspNetCore` 在相對路徑設定 stdout 記錄 `.\log\` 。</span><span class="sxs-lookup"><span data-stu-id="dca4f-118">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="dca4f-119">請確認 AppPool 使用者身分識別具備所提供路徑的寫入權限。</span><span class="sxs-lookup"><span data-stu-id="dca4f-119">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="dca4f-120">針對 Azure App Service 部署發行應用程式時，Web SDK 會將 `stdoutLogFile` 值設定為 `\\?\%home%\LogFiles\stdout` 。</span><span class="sxs-lookup"><span data-stu-id="dca4f-120">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="dca4f-121">`%home`環境變數是針對 Azure App Service 所裝載的應用程式預先定義。</span><span class="sxs-lookup"><span data-stu-id="dca4f-121">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="dca4f-122">若要建立記錄篩選規則，請參閱 ASP.NET Core 記錄檔[集的設定和](xref:fundamentals/logging/index#log-filtering)[記錄篩選](xref:fundamentals/logging/index#log-filtering)區段。</span><span class="sxs-lookup"><span data-stu-id="dca4f-122">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="dca4f-123">如需路徑格式的詳細資訊，請參閱 [Windows 系統上的檔案路徑格式](/dotnet/standard/io/file-path-formats)。</span><span class="sxs-lookup"><span data-stu-id="dca4f-123">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="dca4f-124">增強型診斷記錄</span><span class="sxs-lookup"><span data-stu-id="dca4f-124">Enhanced diagnostic logs</span></span>

<span data-ttu-id="dca4f-125">ASP.NET Core 模組是可設定的，以提供增強型診斷記錄。</span><span class="sxs-lookup"><span data-stu-id="dca4f-125">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="dca4f-126">將專案加入 `<handlerSettings>` 至 `<aspNetCore>` 中的元素 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="dca4f-126">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="dca4f-127">將 `debugLevel` 設定為 `TRACE` 會公開精確性更高的診斷資訊：</span><span class="sxs-lookup"><span data-stu-id="dca4f-127">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="dca4f-128">`logs`當記錄檔建立時，模組會建立上述範例) 中路徑 (的任何資料夾。</span><span class="sxs-lookup"><span data-stu-id="dca4f-128">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="dca4f-129">應用程式集區必須具有寫入記錄檔之位置的寫入權限 (用 `IIS AppPool\{APP POOL NAME}` 來提供寫入權限，其中預留位置 `{APP POOL NAME}` 是) 的應用程式集區名稱。</span><span class="sxs-lookup"><span data-stu-id="dca4f-129">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="dca4f-130">偵錯層級 (`debugLevel`) 值可以同時包含層級和位置。</span><span class="sxs-lookup"><span data-stu-id="dca4f-130">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="dca4f-131">層級 (順序從最不詳細到最詳細)：</span><span class="sxs-lookup"><span data-stu-id="dca4f-131">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="dca4f-132">ERROR</span><span class="sxs-lookup"><span data-stu-id="dca4f-132">ERROR</span></span>
* <span data-ttu-id="dca4f-133">WARNING</span><span class="sxs-lookup"><span data-stu-id="dca4f-133">WARNING</span></span>
* <span data-ttu-id="dca4f-134">INFO</span><span class="sxs-lookup"><span data-stu-id="dca4f-134">INFO</span></span>
* <span data-ttu-id="dca4f-135">TRACE</span><span class="sxs-lookup"><span data-stu-id="dca4f-135">TRACE</span></span>

<span data-ttu-id="dca4f-136">位置 (允許多個位置)：</span><span class="sxs-lookup"><span data-stu-id="dca4f-136">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="dca4f-137">主控台</span><span class="sxs-lookup"><span data-stu-id="dca4f-137">CONSOLE</span></span>
* <span data-ttu-id="dca4f-138">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="dca4f-138">EVENTLOG</span></span>
* <span data-ttu-id="dca4f-139">FILE</span><span class="sxs-lookup"><span data-stu-id="dca4f-139">FILE</span></span>

<span data-ttu-id="dca4f-140">也可以透過環境變數提供處理常式設定：</span><span class="sxs-lookup"><span data-stu-id="dca4f-140">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="dca4f-141">`ASPNETCORE_MODULE_DEBUG_FILE`： Debug 記錄檔的路徑。</span><span class="sxs-lookup"><span data-stu-id="dca4f-141">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="dca4f-142"> (預設： `aspnetcore-debug.log`) </span><span class="sxs-lookup"><span data-stu-id="dca4f-142">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="dca4f-143">`ASPNETCORE_MODULE_DEBUG`： Debug 層級設定。</span><span class="sxs-lookup"><span data-stu-id="dca4f-143">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="dca4f-144">在部署中保持啟用偵錯記錄的時間，**不要**超過針對問題進行排解疑難所需的時間。</span><span class="sxs-lookup"><span data-stu-id="dca4f-144">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="dca4f-145">記錄的大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="dca4f-145">The size of the log isn't limited.</span></span> <span data-ttu-id="dca4f-146">保持啟用偵錯記錄可能會耗盡可用磁碟空間，並讓伺服器或應用程式服務當機。</span><span class="sxs-lookup"><span data-stu-id="dca4f-146">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="dca4f-147">如需檔案中元素的範例，請參閱[ASP.NET Core 模組 `web.config` ](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig)的設定 `aspNetCore` `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="dca4f-147">See [Configuration of ASP.NET Core Module with `web.config`](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>
