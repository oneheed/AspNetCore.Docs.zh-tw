---
title: 'ASP.NET Core 中的記錄和診斷 :::no-loc(SignalR):::'
author: anurse
description: '瞭解如何從您的 ASP.NET Core 應用程式收集診斷資訊 :::no-loc(SignalR)::: 。'
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: devx-track-csharp, signalr, devx-track-js
ms.date: 06/12/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/diagnostics
ms.openlocfilehash: 6e5e9d866a1e03e69856cc63dcfe30284048dd6d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061310"
---
# <a name="logging-and-diagnostics-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="2959d-103">ASP.NET Core 中的記錄和診斷 :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="2959d-103">Logging and diagnostics in ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="2959d-104">[Andrew Stanton-護士](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="2959d-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="2959d-105">本文提供從 ASP.NET Core :::no-loc(SignalR)::: 應用程式收集診斷資訊，以協助疑難排解問題的指引。</span><span class="sxs-lookup"><span data-stu-id="2959d-105">This article provides guidance for gathering diagnostics from your ASP.NET Core :::no-loc(SignalR)::: app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="2959d-106">伺服器端記錄</span><span class="sxs-lookup"><span data-stu-id="2959d-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="2959d-107">伺服器端記錄檔可能包含來自您應用程式的機密資訊。</span><span class="sxs-lookup"><span data-stu-id="2959d-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="2959d-108">**切勿** 將未經處理的記錄從生產環境應用程式張貼到 GitHub 之類的公用論壇。</span><span class="sxs-lookup"><span data-stu-id="2959d-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="2959d-109">由於 :::no-loc(SignalR)::: 是 ASP.NET Core 的一部分，因此會使用 ASP.NET Core 記錄系統。</span><span class="sxs-lookup"><span data-stu-id="2959d-109">Since :::no-loc(SignalR)::: is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="2959d-110">在預設設定中， :::no-loc(SignalR)::: 會記錄極少的資訊，但這可以設定。</span><span class="sxs-lookup"><span data-stu-id="2959d-110">In the default configuration, :::no-loc(SignalR)::: logs very little information, but this can configured.</span></span> <span data-ttu-id="2959d-111">如需有關設定 ASP.NET Core 記錄的詳細資訊，請參閱 [ASP.NET Core 記錄](xref:fundamentals/logging/index#configuration) 檔。</span><span class="sxs-lookup"><span data-stu-id="2959d-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="2959d-112">:::no-loc(SignalR)::: 使用兩個記錄器類別：</span><span class="sxs-lookup"><span data-stu-id="2959d-112">:::no-loc(SignalR)::: uses two logger categories:</span></span>

* <span data-ttu-id="2959d-113">`Microsoft.AspNetCore.:::no-loc(SignalR):::`：適用于與中樞通訊協定相關的記錄、啟動中樞、叫用方法，以及其他中樞相關的活動。</span><span class="sxs-lookup"><span data-stu-id="2959d-113">`Microsoft.AspNetCore.:::no-loc(SignalR):::`: For logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="2959d-114">`Microsoft.AspNetCore.Http.Connections`：適用于傳輸的相關記錄，例如 Websocket、長時間輪詢、Server-Sent 事件，以及低層級的 :::no-loc(SignalR)::: 基礎結構。</span><span class="sxs-lookup"><span data-stu-id="2959d-114">`Microsoft.AspNetCore.Http.Connections`: For logs related to transports, such as WebSockets, Long Polling, Server-Sent Events, and low-level :::no-loc(SignalR)::: infrastructure.</span></span>

<span data-ttu-id="2959d-115">若要從啟用詳細記錄 :::no-loc(SignalR)::: ，請將 `Debug` *:::no-loc(appsettings.json):::* 下列專案新增至檔案中的 `LogLevel` 子區段，以將上述兩個前置詞設定為檔案中的層級 `Logging` ：</span><span class="sxs-lookup"><span data-stu-id="2959d-115">To enable detailed logs from :::no-loc(SignalR):::, configure both of the preceding prefixes to the `Debug` level in your *:::no-loc(appsettings.json):::* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="2959d-116">您也可以在方法的程式碼中進行這項設定 `CreateWebHostBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="2959d-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="2959d-117">如果您未使用以 JSON 為基礎的設定，請在設定系統中設定下列設定值：</span><span class="sxs-lookup"><span data-stu-id="2959d-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.:::no-loc(SignalR):::` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="2959d-118">請檢查設定系統的檔，以判斷如何指定嵌套設定值。</span><span class="sxs-lookup"><span data-stu-id="2959d-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="2959d-119">例如，使用環境變數時， `_` 會使用兩個字元，而不是 `:` (例如 `Logging__LogLevel__Microsoft.AspNetCore.:::no-loc(SignalR):::`) 。</span><span class="sxs-lookup"><span data-stu-id="2959d-119">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.:::no-loc(SignalR):::`).</span></span>

<span data-ttu-id="2959d-120">`Debug`針對您的應用程式收集更詳細的診斷時，建議使用此層級。</span><span class="sxs-lookup"><span data-stu-id="2959d-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="2959d-121">`Trace`層級會產生非常低層級的診斷，而且很少需要診斷您應用程式中的問題。</span><span class="sxs-lookup"><span data-stu-id="2959d-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="2959d-122">存取伺服器端記錄</span><span class="sxs-lookup"><span data-stu-id="2959d-122">Access server-side logs</span></span>

<span data-ttu-id="2959d-123">您存取伺服器端記錄的方式取決於您執行的環境。</span><span class="sxs-lookup"><span data-stu-id="2959d-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="2959d-124">作為 IIS 外部的主控台應用程式</span><span class="sxs-lookup"><span data-stu-id="2959d-124">As a console app outside IIS</span></span>

<span data-ttu-id="2959d-125">如果您是在主控台應用程式中執行，則預設應啟用 [主控台記錄器](xref:fundamentals/logging/index#console) 。</span><span class="sxs-lookup"><span data-stu-id="2959d-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="2959d-126">:::no-loc(SignalR)::: 記錄檔將會出現在主控台中。</span><span class="sxs-lookup"><span data-stu-id="2959d-126">:::no-loc(SignalR)::: logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="2959d-127">從 Visual Studio 的 IIS Express 內</span><span class="sxs-lookup"><span data-stu-id="2959d-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="2959d-128">Visual Studio 會在 [ **輸出** ] 視窗中顯示記錄輸出。</span><span class="sxs-lookup"><span data-stu-id="2959d-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="2959d-129">選取 [ **ASP.NET Core Web 服務器** ] 下拉式清單選項。</span><span class="sxs-lookup"><span data-stu-id="2959d-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="2959d-130">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="2959d-130">Azure App Service</span></span>

<span data-ttu-id="2959d-131">在 Azure App Service 入口網站的 [ **診斷記錄** ] 區段中，啟用 **(Filesystem) 選項的應用程式記錄** ，並將 **層級** 設定為 `Verbose` 。</span><span class="sxs-lookup"><span data-stu-id="2959d-131">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="2959d-132">記錄應該可從 **記錄串流** 服務和 App Service 檔案系統的記錄中取得。</span><span class="sxs-lookup"><span data-stu-id="2959d-132">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="2959d-133">如需詳細資訊，請參閱 [Azure 記錄資料流程](xref:fundamentals/logging/index#azure-log-streaming)。</span><span class="sxs-lookup"><span data-stu-id="2959d-133">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="2959d-134">其他環境</span><span class="sxs-lookup"><span data-stu-id="2959d-134">Other environments</span></span>

<span data-ttu-id="2959d-135">如果應用程式部署到另一個環境 (例如 Docker、Kubernetes 或 Windows 服務) ，請參閱， <xref:fundamentals/logging/index> 以取得如何設定適用于環境的記錄提供者的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="2959d-135">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="2959d-136">JavaScript 用戶端記錄</span><span class="sxs-lookup"><span data-stu-id="2959d-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="2959d-137">用戶端記錄可能包含來自您應用程式的機密資訊。</span><span class="sxs-lookup"><span data-stu-id="2959d-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="2959d-138">**切勿** 將未經處理的記錄從生產環境應用程式張貼到 GitHub 之類的公用論壇。</span><span class="sxs-lookup"><span data-stu-id="2959d-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="2959d-139">使用 JavaScript 用戶端時，您可以使用上的方法設定記錄選項 `configureLogging` `HubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="2959d-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="2959d-140">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="2959d-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="2959d-141">下表顯示適用于 JavaScript 用戶端的記錄層級。</span><span class="sxs-lookup"><span data-stu-id="2959d-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="2959d-142">將記錄層級設定為下列其中一個值，就可以在該層級以及在資料表中其上方的所有層級進行記錄。</span><span class="sxs-lookup"><span data-stu-id="2959d-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="2959d-143">層級</span><span class="sxs-lookup"><span data-stu-id="2959d-143">Level</span></span> | <span data-ttu-id="2959d-144">描述</span><span class="sxs-lookup"><span data-stu-id="2959d-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="2959d-145">不會記錄任何訊息。</span><span class="sxs-lookup"><span data-stu-id="2959d-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="2959d-146">指出整個應用程式中失敗的訊息。</span><span class="sxs-lookup"><span data-stu-id="2959d-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="2959d-147">指出目前操作失敗的訊息。</span><span class="sxs-lookup"><span data-stu-id="2959d-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="2959d-148">指出非嚴重問題的訊息。</span><span class="sxs-lookup"><span data-stu-id="2959d-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="2959d-149">參考訊息。</span><span class="sxs-lookup"><span data-stu-id="2959d-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="2959d-150">適用于偵錯工具的診斷訊息。</span><span class="sxs-lookup"><span data-stu-id="2959d-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="2959d-151">專為診斷特定問題而設計的非常詳細診斷訊息。</span><span class="sxs-lookup"><span data-stu-id="2959d-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="2959d-152">設定詳細資訊之後，記錄會寫入至瀏覽器主控台 (或 NodeJS 應用程式) 中的標準輸出。</span><span class="sxs-lookup"><span data-stu-id="2959d-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="2959d-153">如果您想要將記錄傳送至自訂記錄系統，您可以提供執行介面的 JavaScript 物件 `ILogger` 。</span><span class="sxs-lookup"><span data-stu-id="2959d-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="2959d-154">唯一需要執行的方法是 `log` ，它會採用事件的層級以及與事件相關聯的訊息。</span><span class="sxs-lookup"><span data-stu-id="2959d-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="2959d-155">例如：</span><span class="sxs-lookup"><span data-stu-id="2959d-155">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="2959d-156">.NET 用戶端記錄</span><span class="sxs-lookup"><span data-stu-id="2959d-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="2959d-157">用戶端記錄可能包含來自您應用程式的機密資訊。</span><span class="sxs-lookup"><span data-stu-id="2959d-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="2959d-158">**切勿** 將未經處理的記錄從生產環境應用程式張貼到 GitHub 之類的公用論壇。</span><span class="sxs-lookup"><span data-stu-id="2959d-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="2959d-159">若要從 .NET 用戶端取得記錄，您可以 `ConfigureLogging` 在上使用方法 `HubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="2959d-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="2959d-160">其運作方式與與上的 `ConfigureLogging` 方法相同 `WebHostBuilder` `HostBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="2959d-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="2959d-161">您可以設定 ASP.NET Core 中所使用的相同記錄提供者。</span><span class="sxs-lookup"><span data-stu-id="2959d-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="2959d-162">不過，您必須手動安裝和啟用個別記錄提供者的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="2959d-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

<span data-ttu-id="2959d-163">若要將 .NET 用戶端記錄新增至 :::no-loc(Blazor WebAssembly)::: 應用程式，請參閱 <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging> 。</span><span class="sxs-lookup"><span data-stu-id="2959d-163">To add .NET client logging to a :::no-loc(Blazor WebAssembly)::: app, see <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging>.</span></span>

### <a name="console-logging"></a><span data-ttu-id="2959d-164">主控台記錄</span><span class="sxs-lookup"><span data-stu-id="2959d-164">Console logging</span></span>

<span data-ttu-id="2959d-165">若要啟用主控台記錄，請新增 [ [記錄] 主控台](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 套件。</span><span class="sxs-lookup"><span data-stu-id="2959d-165">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="2959d-166">然後，使用 `AddConsole` 方法來設定主控台記錄器：</span><span class="sxs-lookup"><span data-stu-id="2959d-166">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="2959d-167">調試輸出視窗記錄</span><span class="sxs-lookup"><span data-stu-id="2959d-167">Debug output window logging</span></span>

<span data-ttu-id="2959d-168">您也可以在 Visual Studio 中設定記錄以移至 [ **輸出** ] 視窗。</span><span class="sxs-lookup"><span data-stu-id="2959d-168">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="2959d-169">安裝 [ [記錄](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 檔]，然後使用 `AddDebug` 方法：</span><span class="sxs-lookup"><span data-stu-id="2959d-169">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="2959d-170">其他記錄提供者</span><span class="sxs-lookup"><span data-stu-id="2959d-170">Other logging providers</span></span>

<span data-ttu-id="2959d-171">:::no-loc(SignalR)::: 支援其他記錄提供者，例如 Serilog、Seq、NLog 或與整合的任何其他記錄系統 `Microsoft.Extensions.Logging` 。</span><span class="sxs-lookup"><span data-stu-id="2959d-171">:::no-loc(SignalR)::: supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="2959d-172">如果您的記錄系統提供 `ILoggerProvider` ，您可以使用下列程式來註冊 `AddProvider` ：</span><span class="sxs-lookup"><span data-stu-id="2959d-172">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="2959d-173">控制詳細資訊</span><span class="sxs-lookup"><span data-stu-id="2959d-173">Control verbosity</span></span>

<span data-ttu-id="2959d-174">如果您是從應用程式中的其他位置進行記錄，將預設層級變更為 `Debug` 可能太詳細。</span><span class="sxs-lookup"><span data-stu-id="2959d-174">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="2959d-175">您可以使用篩選來設定記錄的記錄層級 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="2959d-175">You can use a Filter to configure the logging level for :::no-loc(SignalR)::: logs.</span></span> <span data-ttu-id="2959d-176">這可以在程式碼中完成，與伺服器上的方式大致相同：</span><span class="sxs-lookup"><span data-stu-id="2959d-176">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="2959d-177">網路追蹤</span><span class="sxs-lookup"><span data-stu-id="2959d-177">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="2959d-178">網路追蹤包含您的應用程式所傳送之每個訊息的完整內容。</span><span class="sxs-lookup"><span data-stu-id="2959d-178">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="2959d-179">**切勿** 將未經處理的網路追蹤從生產環境應用程式張貼到 GitHub 之類的公用論壇。</span><span class="sxs-lookup"><span data-stu-id="2959d-179">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="2959d-180">如果您遇到問題，網路追蹤有時可能會提供許多有用的資訊。</span><span class="sxs-lookup"><span data-stu-id="2959d-180">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="2959d-181">如果您要在問題追蹤程式上提出問題，這項功能特別有用。</span><span class="sxs-lookup"><span data-stu-id="2959d-181">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="2959d-182">使用 Fiddler (慣用選項收集網路追蹤) </span><span class="sxs-lookup"><span data-stu-id="2959d-182">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="2959d-183">此方法適用于所有應用程式。</span><span class="sxs-lookup"><span data-stu-id="2959d-183">This method works for all apps.</span></span>

<span data-ttu-id="2959d-184">Fiddler 是一個非常強大的工具，可用於收集 HTTP 追蹤。</span><span class="sxs-lookup"><span data-stu-id="2959d-184">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="2959d-185">從 [telerik.com/fiddler](https://www.telerik.com/fiddler)安裝它、加以啟動，然後執行您的應用程式並重現問題。</span><span class="sxs-lookup"><span data-stu-id="2959d-185">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="2959d-186">Fiddler 適用于 Windows，而且有適用于 macOS 和 Linux 的 Beta 版。</span><span class="sxs-lookup"><span data-stu-id="2959d-186">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="2959d-187">如果您使用 HTTPS 進行連線，則有一些額外的步驟可確保 Fiddler 可將 HTTPS 流量解密。</span><span class="sxs-lookup"><span data-stu-id="2959d-187">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="2959d-188">如需詳細資訊，請參閱 [Fiddler 檔](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)。</span><span class="sxs-lookup"><span data-stu-id="2959d-188">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="2959d-189">收集追蹤之後，您可以從功能表列 **選擇 [** 檔案  >  **儲存**  >  **所有會話** ] 來匯出追蹤。</span><span class="sxs-lookup"><span data-stu-id="2959d-189">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![從 Fiddler 匯出所有會話](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="2959d-191">使用 tcpdump 只收集網路追蹤)  (的 macOS 和 Linux</span><span class="sxs-lookup"><span data-stu-id="2959d-191">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="2959d-192">此方法適用于所有應用程式。</span><span class="sxs-lookup"><span data-stu-id="2959d-192">This method works for all apps.</span></span>

<span data-ttu-id="2959d-193">您可以從命令列介面執行下列命令，以使用 tcpdump 收集原始 TCP 追蹤。</span><span class="sxs-lookup"><span data-stu-id="2959d-193">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="2959d-194">`root` `sudo` 如果您收到許可權錯誤，則可能需要或在命令前面加上前置詞：</span><span class="sxs-lookup"><span data-stu-id="2959d-194">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="2959d-195">取代 `[interface]` 為您想要捕捉的網路介面。</span><span class="sxs-lookup"><span data-stu-id="2959d-195">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="2959d-196">通常，這類似于 `/dev/eth0` 標準乙太網路介面的 (，) 或 `/dev/lo0` (localhost 流量) 。</span><span class="sxs-lookup"><span data-stu-id="2959d-196">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="2959d-197">如需詳細資訊，請參閱 `tcpdump` 主機系統上的 man 頁面。</span><span class="sxs-lookup"><span data-stu-id="2959d-197">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="2959d-198">在瀏覽器中收集網路追蹤</span><span class="sxs-lookup"><span data-stu-id="2959d-198">Collect a network trace in the browser</span></span>

<span data-ttu-id="2959d-199">此方法僅適用于以瀏覽器為基礎的應用程式。</span><span class="sxs-lookup"><span data-stu-id="2959d-199">This method only works for browser-based apps.</span></span>

<span data-ttu-id="2959d-200">大部分的瀏覽器開發人員工具都有一個 [網路] 索引標籤，可讓您在瀏覽器與伺服器之間抓取網路活動。</span><span class="sxs-lookup"><span data-stu-id="2959d-200">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="2959d-201">不過，這些追蹤不會包含 WebSocket 和 Server-Sent 事件訊息。</span><span class="sxs-lookup"><span data-stu-id="2959d-201">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="2959d-202">如果您使用這些傳輸，則使用 Fiddler 或 TcpDump 等工具 (如下所述) 是較佳的方法。</span><span class="sxs-lookup"><span data-stu-id="2959d-202">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="2959d-203">Microsoft Edge 和 Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="2959d-203">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="2959d-204"> (Edge 和 Internet Explorer 的指示都相同) </span><span class="sxs-lookup"><span data-stu-id="2959d-204">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="2959d-205">按 F12 以開啟 Dev Tools</span><span class="sxs-lookup"><span data-stu-id="2959d-205">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="2959d-206">按一下 [網路] 索引標籤</span><span class="sxs-lookup"><span data-stu-id="2959d-206">Click the Network Tab</span></span>
3. <span data-ttu-id="2959d-207">視需要重新整理頁面 (，) 並重現問題</span><span class="sxs-lookup"><span data-stu-id="2959d-207">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="2959d-208">按一下工具列中的 [儲存] 圖示，將追蹤匯出為「HAR」檔案：</span><span class="sxs-lookup"><span data-stu-id="2959d-208">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![[Microsoft Edge Dev 工具網路] 索引標籤上的 [儲存] 圖示](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="2959d-210">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="2959d-210">Google Chrome</span></span>

1. <span data-ttu-id="2959d-211">按 F12 以開啟 Dev Tools</span><span class="sxs-lookup"><span data-stu-id="2959d-211">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="2959d-212">按一下 [網路] 索引標籤</span><span class="sxs-lookup"><span data-stu-id="2959d-212">Click the Network Tab</span></span>
3. <span data-ttu-id="2959d-213">視需要重新整理頁面 (，) 並重現問題</span><span class="sxs-lookup"><span data-stu-id="2959d-213">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="2959d-214">以滑鼠右鍵按一下要求清單中的任意位置，然後選擇 [以內容另存為 HAR]：</span><span class="sxs-lookup"><span data-stu-id="2959d-214">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Google Chrome 開發工具網路索引標籤中的 [以內容另存為 HAR] 選項](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="2959d-216">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="2959d-216">Mozilla Firefox</span></span>

1. <span data-ttu-id="2959d-217">按 F12 以開啟 Dev Tools</span><span class="sxs-lookup"><span data-stu-id="2959d-217">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="2959d-218">按一下 [網路] 索引標籤</span><span class="sxs-lookup"><span data-stu-id="2959d-218">Click the Network Tab</span></span>
3. <span data-ttu-id="2959d-219">視需要重新整理頁面 (，) 並重現問題</span><span class="sxs-lookup"><span data-stu-id="2959d-219">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="2959d-220">以滑鼠右鍵按一下要求清單中的任意位置，然後選擇 [全部儲存為 HAR]</span><span class="sxs-lookup"><span data-stu-id="2959d-220">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Mozilla Firefox Dev Tools 網路索引標籤中的 [全部儲存為 HAR] 選項](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="2959d-222">將診斷檔案附加至 GitHub 問題</span><span class="sxs-lookup"><span data-stu-id="2959d-222">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="2959d-223">您可以藉由重新命名來將診斷檔案附加至 GitHub 問題，使其具有延伸模組，然後將 `.txt` 其拖放到問題上。</span><span class="sxs-lookup"><span data-stu-id="2959d-223">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="2959d-224">請勿將記錄檔或網路追蹤的內容貼入 GitHub 問題。</span><span class="sxs-lookup"><span data-stu-id="2959d-224">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="2959d-225">這些記錄和追蹤可能很大，而且 GitHub 通常會將它們截斷。</span><span class="sxs-lookup"><span data-stu-id="2959d-225">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![將記錄檔拖曳至 GitHub 問題](diagnostics/attaching-diagnostics-files.png)

## <a name="metrics"></a><span data-ttu-id="2959d-227">計量</span><span class="sxs-lookup"><span data-stu-id="2959d-227">Metrics</span></span>

<span data-ttu-id="2959d-228">計量是在一段時間內的資料量值標記法。</span><span class="sxs-lookup"><span data-stu-id="2959d-228">Metrics is a representation of data measures over intervals of time.</span></span> <span data-ttu-id="2959d-229">例如，每秒的要求數。</span><span class="sxs-lookup"><span data-stu-id="2959d-229">For example, requests per second.</span></span> <span data-ttu-id="2959d-230">計量資料可讓您觀察高階應用程式的狀態。</span><span class="sxs-lookup"><span data-stu-id="2959d-230">Metrics data allows observation of the state of an app at a high level.</span></span> <span data-ttu-id="2959d-231">.NET gRPC 計量是使用發出的 <xref:System.Diagnostics.Tracing.EventCounter> 。</span><span class="sxs-lookup"><span data-stu-id="2959d-231">.NET gRPC metrics are emitted using <xref:System.Diagnostics.Tracing.EventCounter>.</span></span>

### <a name="no-locsignalr-server-metrics"></a><span data-ttu-id="2959d-232">:::no-loc(SignalR)::: 伺服器計量</span><span class="sxs-lookup"><span data-stu-id="2959d-232">:::no-loc(SignalR)::: server metrics</span></span>

<span data-ttu-id="2959d-233">:::no-loc(SignalR)::: 系統會報告事件來源的伺服器計量 <xref:Microsoft.AspNetCore.Http.Connections> 。</span><span class="sxs-lookup"><span data-stu-id="2959d-233">:::no-loc(SignalR)::: server metrics are reported on the <xref:Microsoft.AspNetCore.Http.Connections> event source.</span></span>

| <span data-ttu-id="2959d-234">名稱</span><span class="sxs-lookup"><span data-stu-id="2959d-234">Name</span></span>                    | <span data-ttu-id="2959d-235">描述</span><span class="sxs-lookup"><span data-stu-id="2959d-235">Description</span></span>                 |
|-------------------------|-----------------------------|
| `connections-started`   | <span data-ttu-id="2959d-236">已啟動的連接總數</span><span class="sxs-lookup"><span data-stu-id="2959d-236">Total connections started</span></span>   |
| `connections-stopped`   | <span data-ttu-id="2959d-237">已停止的連接總數</span><span class="sxs-lookup"><span data-stu-id="2959d-237">Total connections stopped</span></span>   |
| `connections-timed-out` | <span data-ttu-id="2959d-238">總連接逾時</span><span class="sxs-lookup"><span data-stu-id="2959d-238">Total connections timed out</span></span> |
| `current-connections`   | <span data-ttu-id="2959d-239">目前的連線數</span><span class="sxs-lookup"><span data-stu-id="2959d-239">Current connections</span></span>         |
| `connections-duration`  | <span data-ttu-id="2959d-240">平均連接持續時間</span><span class="sxs-lookup"><span data-stu-id="2959d-240">Average connection duration</span></span> |

### <a name="observe-metrics"></a><span data-ttu-id="2959d-241">觀察計量</span><span class="sxs-lookup"><span data-stu-id="2959d-241">Observe metrics</span></span>

<span data-ttu-id="2959d-242">[dotnet-計數器](/dotnet/core/diagnostics/dotnet-counters) 是一種效能監視工具，適用于臨機操作健全狀況監視和第一層效能調查。</span><span class="sxs-lookup"><span data-stu-id="2959d-242">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="2959d-243">使用 `Microsoft.AspNetCore.Http.Connections` 作為提供者名稱監視 .net 應用程式。</span><span class="sxs-lookup"><span data-stu-id="2959d-243">Monitor a .NET app with `Microsoft.AspNetCore.Http.Connections` as the provider name.</span></span> <span data-ttu-id="2959d-244">例如：</span><span class="sxs-lookup"><span data-stu-id="2959d-244">For example:</span></span>

```console
> dotnet-counters monitor --process-id 37016 Microsoft.AspNetCore.Http.Connections

Press p to pause, r to resume, q to quit.
    Status: Running
[Microsoft.AspNetCore.Http.Connections]
    Average Connection Duration (ms)       16,040.56
    Current Connections                         1
    Total Connections Started                   8
    Total Connections Stopped                   7
    Total Connections Timed Out                 0
```

## <a name="additional-resources"></a><span data-ttu-id="2959d-245">其他資源</span><span class="sxs-lookup"><span data-stu-id="2959d-245">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
