---
title: Debug ASP.NET Core Blazor WebAssembly
author: guardrex
description: 瞭解如何對 Blazor 應用程式進行偵錯工具。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/17/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: ac649b80a6cb1106de8acbb4764b53017afaa238
ms.sourcegitcommit: dfea24471f4f3d7904faa92fe60c000853bddc3b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504589"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="3cab6-103">Debug ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3cab6-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="3cab6-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="3cab6-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="3cab6-105">Blazor WebAssembly 您可以使用以 Chromium 為基礎的瀏覽器中的瀏覽器開發工具來調試應用程式 (Edge/Chrome) 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="3cab6-106">或者，您可以使用 Visual Studio 或 Visual Studio Code 來對應用程式進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="3cab6-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="3cab6-107">可用的案例包括：</span><span class="sxs-lookup"><span data-stu-id="3cab6-107">Available scenarios include:</span></span>

* <span data-ttu-id="3cab6-108">設定和移除中斷點。</span><span class="sxs-lookup"><span data-stu-id="3cab6-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="3cab6-109">使用 Visual Studio 中的偵錯工具支援執行應用程式，並 Visual Studio Code (<kbd>F5</kbd> 支援) 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="3cab6-110">單一步驟 (<kbd>F10</kbd>) 程式碼。</span><span class="sxs-lookup"><span data-stu-id="3cab6-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="3cab6-111">在瀏覽器或<kbd>F5</kbd>的 Visual Studio 或 Visual Studio Code 中，使用<kbd>F8</kbd>繼續執行程式碼。</span><span class="sxs-lookup"><span data-stu-id="3cab6-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="3cab6-112">在 [ *區域變數* ] 顯示中，觀察本機變數的值。</span><span class="sxs-lookup"><span data-stu-id="3cab6-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="3cab6-113">查看呼叫堆疊，包括從 JavaScript 進入 .NET 以及從 .NET 到 JavaScript 的呼叫鏈。</span><span class="sxs-lookup"><span data-stu-id="3cab6-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="3cab6-114">目前，您 *不能*：</span><span class="sxs-lookup"><span data-stu-id="3cab6-114">For now, you *can't*:</span></span>

* <span data-ttu-id="3cab6-115">中斷未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="3cab6-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="3cab6-116">在應用程式啟動期間，在執行 debug proxy 之前叫用中斷點。</span><span class="sxs-lookup"><span data-stu-id="3cab6-116">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="3cab6-117">這包括 () 中的中斷點 `Program.Main` `Program.cs` ，以及應用程式所要求的第一個頁面所載入之元件[ `OnInitialized{Async}` 方法](xref:blazor/components/lifecycle#component-initialization-methods)中的中斷點。</span><span class="sxs-lookup"><span data-stu-id="3cab6-117">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="3cab6-118">我們將繼續改進即將推出的版本中的調試過程。</span><span class="sxs-lookup"><span data-stu-id="3cab6-118">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3cab6-119">先決條件</span><span class="sxs-lookup"><span data-stu-id="3cab6-119">Prerequisites</span></span>

<span data-ttu-id="3cab6-120">偵錯工具需要下列其中一個瀏覽器：</span><span class="sxs-lookup"><span data-stu-id="3cab6-120">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="3cab6-121">Google Chrome (70 版或更新版本)  (預設) </span><span class="sxs-lookup"><span data-stu-id="3cab6-121">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="3cab6-122">Microsoft Edge (80 版或更新版本) </span><span class="sxs-lookup"><span data-stu-id="3cab6-122">Microsoft Edge (version 80 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="3cab6-123">啟用 Visual Studio 和 Visual Studio Code 的偵錯工具</span><span class="sxs-lookup"><span data-stu-id="3cab6-123">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="3cab6-124">若要啟用現有 Blazor WebAssembly 應用程式的偵錯工具，請更新 `launchSettings.json` 啟始專案中的檔案，以 `inspectUri` 在每個啟動設定檔中包含下列屬性：</span><span class="sxs-lookup"><span data-stu-id="3cab6-124">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="3cab6-125">更新之後，檔案 `launchSettings.json` 看起來應該會類似下列範例：</span><span class="sxs-lookup"><span data-stu-id="3cab6-125">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="3cab6-126">`inspectUri`屬性：</span><span class="sxs-lookup"><span data-stu-id="3cab6-126">The `inspectUri` property:</span></span>

* <span data-ttu-id="3cab6-127">讓 IDE 偵測應用程式是否為 Blazor WebAssembly 應用程式。</span><span class="sxs-lookup"><span data-stu-id="3cab6-127">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="3cab6-128">指示腳本的偵錯工具，透過的偵錯工具 proxy 連接到瀏覽器 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-128">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="3cab6-129">在啟動的瀏覽器上，Websocket 通訊協定 (`wsProtocol`) 、主機 (`url.hostname`) 、埠 () 和偵測器 URI 的預留位置值， (`url.port` `browserInspectUri` 由架構提供。</span><span class="sxs-lookup"><span data-stu-id="3cab6-129">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="3cab6-130">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cab6-130">Visual Studio</span></span>

<span data-ttu-id="3cab6-131">若要 Blazor WebAssembly 在 Visual Studio 中偵錯工具：</span><span class="sxs-lookup"><span data-stu-id="3cab6-131">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="3cab6-132">建立新的 ASP.NET Core 託管 Blazor WebAssembly 應用程式。</span><span class="sxs-lookup"><span data-stu-id="3cab6-132">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="3cab6-133">按 <kbd>F5</kbd> 以在偵錯工具中執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="3cab6-133">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="3cab6-134">不支援 (<kbd>Ctrl</kbd>F5) **啟動而不進行調試** + <kbd>F5</kbd> 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-134">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="3cab6-135">當應用程式在偵錯工具設定中執行時，偵錯工具的額外負荷一律會降低效能。</span><span class="sxs-lookup"><span data-stu-id="3cab6-135">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="3cab6-136">在方法中設定的中斷點 `Pages/Counter.razor` `IncrementCount` 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-136">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="3cab6-137">流覽至索引標籤 **`Counter`** ，然後選取按鈕以點擊中斷點：</span><span class="sxs-lookup"><span data-stu-id="3cab6-137">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![Debug 計數器](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="3cab6-139">查看 `currentCount` [區域變數] 視窗中的欄位值：</span><span class="sxs-lookup"><span data-stu-id="3cab6-139">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![視圖區域變數](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="3cab6-141">按 <kbd>F5</kbd> 繼續執行。</span><span class="sxs-lookup"><span data-stu-id="3cab6-141">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="3cab6-142">在對 Blazor WebAssembly 應用程式進行偵錯工具時，您也可以將伺服器程式碼進行偵錯工具：</span><span class="sxs-lookup"><span data-stu-id="3cab6-142">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="3cab6-143">在的 `Pages/FetchData.razor` 頁面中，設定中斷點 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-143">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="3cab6-144">在動作方法的中，設定中斷點 `WeatherForecastController` `Get` 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-144">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="3cab6-145">流覽至索引 **`Fetch Data`** 標籤，以在 `FetchData` 發出 HTTP 要求給伺服器之前，叫用元件中的第一個中斷點：</span><span class="sxs-lookup"><span data-stu-id="3cab6-145">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Debug Fetch 資料](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="3cab6-147">按下 <kbd>F5</kbd> 繼續執行，然後點擊伺服器上的中斷點 `WeatherForecastController` ：</span><span class="sxs-lookup"><span data-stu-id="3cab6-147">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Debug server](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="3cab6-149">再按一次 <kbd>F5</kbd> ，讓執行繼續執行，並查看轉譯的氣象預報表格。</span><span class="sxs-lookup"><span data-stu-id="3cab6-149">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="3cab6-150">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3cab6-150">Visual Studio Code</span></span>

### <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="3cab6-151">獨立調試 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3cab6-151">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="3cab6-152">Blazor WebAssembly在 VS Code 中開啟獨立應用程式。</span><span class="sxs-lookup"><span data-stu-id="3cab6-152">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="3cab6-153">您可能會收到下列通知，指出需要額外的設定才能啟用偵錯工具：</span><span class="sxs-lookup"><span data-stu-id="3cab6-153">You may receive the following notification that additional setup is required to enable debugging:</span></span>
   
   ![需要額外的設定](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)
   
   <span data-ttu-id="3cab6-155">如果您收到通知：</span><span class="sxs-lookup"><span data-stu-id="3cab6-155">If you receive the notification:</span></span>

   * <span data-ttu-id="3cab6-156">確認已安裝 [適用于 Visual Studio Code 擴充](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) 功能的最新 c #。</span><span class="sxs-lookup"><span data-stu-id="3cab6-156">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="3cab6-157">若要檢查已安裝的擴充功能，請從功能表列開啟 [**視圖**  >  **擴充**功能]，或選取 [**活動**] 提要欄位中的**延伸**模組圖示。</span><span class="sxs-lookup"><span data-stu-id="3cab6-157">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="3cab6-158">確認已啟用 JavaScript preview 的偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="3cab6-158">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="3cab6-159">從功能表列開啟設定 **， (檔案**  >  **喜好**  >  **設定**) 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-159">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="3cab6-160">使用關鍵字搜尋 `debug preview` 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-160">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="3cab6-161">在搜尋結果中，確認已核取 [ **Debug > JavaScript： Use Preview** ] 的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="3cab6-161">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="3cab6-162">如果不存在啟用預覽偵錯工具的選項，請升級至最新版的 VS Code 或安裝 [JavaScript 偵錯工具擴充](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 功能 (VS Code 1.46 版或更早版本的) 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-162">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="3cab6-163">重載視窗。</span><span class="sxs-lookup"><span data-stu-id="3cab6-163">Reload the window.</span></span>

1. <span data-ttu-id="3cab6-164">使用 <kbd>F5</kbd> 鍵盤快速鍵或功能表項目來啟動調試。</span><span class="sxs-lookup"><span data-stu-id="3cab6-164">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="3cab6-165">不支援 (<kbd>Ctrl</kbd>F5) **啟動而不進行調試** + <kbd>F5</kbd> 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-165">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="3cab6-166">當應用程式在偵錯工具設定中執行時，偵錯工具的額外負荷一律會降低效能。</span><span class="sxs-lookup"><span data-stu-id="3cab6-166">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="3cab6-167">出現提示時，請選取 [ \*\* Blazor WebAssembly Debug\*\* ] 選項以開始進行調試。</span><span class="sxs-lookup"><span data-stu-id="3cab6-167">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![可用的偵錯工具選項清單](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="3cab6-169">獨立應用程式會啟動，並開啟偵錯工具瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="3cab6-169">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="3cab6-170">在 `IncrementCount` 元件的方法中設定中斷點 `Counter` ，然後選取要叫用中斷點的按鈕：</span><span class="sxs-lookup"><span data-stu-id="3cab6-170">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![VS Code 中的 Debug 計數器](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="3cab6-172">主控的調試 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3cab6-172">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="3cab6-173">Blazor WebAssembly在 VS Code 中開啟託管應用程式的方案資料夾。</span><span class="sxs-lookup"><span data-stu-id="3cab6-173">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="3cab6-174">如果未設定專案的啟動設定，則會出現下列通知。</span><span class="sxs-lookup"><span data-stu-id="3cab6-174">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="3cab6-175">選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="3cab6-175">Select **Yes**.</span></span>

   ![新增所需的資產](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="3cab6-177">在視窗頂端的 [命令選擇區] 中，選取主控方案內的 *伺服器* 專案。</span><span class="sxs-lookup"><span data-stu-id="3cab6-177">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="3cab6-178">`launch.json`使用啟動設定來啟動偵錯工具時，會產生一個檔案。</span><span class="sxs-lookup"><span data-stu-id="3cab6-178">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="3cab6-179">附加至現有的偵錯工具會話</span><span class="sxs-lookup"><span data-stu-id="3cab6-179">Attach to an existing debugging session</span></span>

<span data-ttu-id="3cab6-180">若要附加至執行 Blazor 中的應用程式，請使用下列設定來建立檔案 `launch.json` ：</span><span class="sxs-lookup"><span data-stu-id="3cab6-180">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="3cab6-181">只有獨立應用程式才支援附加至偵錯工具會話。</span><span class="sxs-lookup"><span data-stu-id="3cab6-181">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="3cab6-182">若要使用完整堆疊的偵錯工具，您必須從 VS Code 啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="3cab6-182">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="3cab6-183">啟動設定選項</span><span class="sxs-lookup"><span data-stu-id="3cab6-183">Launch configuration options</span></span>

<span data-ttu-id="3cab6-184">下列啟動設定選項支援 `blazorwasm` debug 型別 (`.vscode/launch.json`) 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-184">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="3cab6-185">選項</span><span class="sxs-lookup"><span data-stu-id="3cab6-185">Option</span></span>    | <span data-ttu-id="3cab6-186">描述</span><span class="sxs-lookup"><span data-stu-id="3cab6-186">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="3cab6-187">用 `launch` 來啟動偵錯工具，並將其附加至 Blazor WebAssembly 應用程式，或將偵測 `attach` 會話附加至已在執行中的應用程式。</span><span class="sxs-lookup"><span data-stu-id="3cab6-187">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="3cab6-188">在瀏覽器中要在瀏覽器中開啟的 URL。</span><span class="sxs-lookup"><span data-stu-id="3cab6-188">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="3cab6-189">預設為 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="3cab6-189">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="3cab6-190">要啟動偵錯工具的瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="3cab6-190">The browser to launch for the debugging session.</span></span> <span data-ttu-id="3cab6-191">設為 `edge` 或 `chrome`。</span><span class="sxs-lookup"><span data-stu-id="3cab6-191">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="3cab6-192">預設為 `chrome`。</span><span class="sxs-lookup"><span data-stu-id="3cab6-192">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="3cab6-193">用來從 JS 偵錯工具產生記錄。</span><span class="sxs-lookup"><span data-stu-id="3cab6-193">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="3cab6-194">設定為 `true` 以產生記錄。</span><span class="sxs-lookup"><span data-stu-id="3cab6-194">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="3cab6-195">`true`如果啟動和偵測託管應用程式，則必須設定為 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-195">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="3cab6-196">指定 web 伺服器的絕對路徑。</span><span class="sxs-lookup"><span data-stu-id="3cab6-196">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="3cab6-197">如果從子路由提供應用程式，則應該設定。</span><span class="sxs-lookup"><span data-stu-id="3cab6-197">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="3cab6-198">等候偵錯工具附加的毫秒數。</span><span class="sxs-lookup"><span data-stu-id="3cab6-198">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="3cab6-199">預設值為30000毫秒 (30 秒) 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-199">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="3cab6-200">可執行檔的參考，用來執行裝載應用程式的伺服器。</span><span class="sxs-lookup"><span data-stu-id="3cab6-200">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="3cab6-201">如果為，則必須設定 `hosted` `true` 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-201">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="3cab6-202">用來啟動應用程式的工作目錄。</span><span class="sxs-lookup"><span data-stu-id="3cab6-202">The working directory to launch the app under.</span></span> <span data-ttu-id="3cab6-203">如果為，則必須設定 `hosted` `true` 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-203">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="3cab6-204">要提供給已啟動進程的環境變數。</span><span class="sxs-lookup"><span data-stu-id="3cab6-204">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="3cab6-205">只有 `hosted` 在設為時才適用 `true` 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-205">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="3cab6-206">啟動設定範例</span><span class="sxs-lookup"><span data-stu-id="3cab6-206">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="3cab6-207">啟動獨立應用程式並進行調試 Blazor WebAssembly 程式</span><span class="sxs-lookup"><span data-stu-id="3cab6-207">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="3cab6-208">在指定的 URL 附加至執行中的應用程式</span><span class="sxs-lookup"><span data-stu-id="3cab6-208">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="3cab6-209">使用 Microsoft Edge 啟動及偵測託管 Blazor WebAssembly 應用程式</span><span class="sxs-lookup"><span data-stu-id="3cab6-209">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="3cab6-210">瀏覽器設定預設為 Google Chrome。</span><span class="sxs-lookup"><span data-stu-id="3cab6-210">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="3cab6-211">使用 Microsoft Edge 進行偵錯工具時，請將設定 `browser` 為 `edge` 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-211">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="3cab6-212">若要使用 Google Chrome，請不要設定 `browser` 選項，或將選項的值設為 `chrome` 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-212">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="3cab6-213">在上述範例中， `MyHostedApp.Server.dll` 是 *伺服器* 應用程式的元件。</span><span class="sxs-lookup"><span data-stu-id="3cab6-213">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="3cab6-214">此 `.vscode` 資料夾位於 `Client` 、和資料夾旁的方案資料夾中 `Server` `Shared` 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-214">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

## <a name="debug-in-the-browser"></a><span data-ttu-id="3cab6-215">瀏覽器中的 Debug</span><span class="sxs-lookup"><span data-stu-id="3cab6-215">Debug in the browser</span></span>

1. <span data-ttu-id="3cab6-216">在開發環境中執行應用程式的偵錯工具組建。</span><span class="sxs-lookup"><span data-stu-id="3cab6-216">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="3cab6-217">啟動瀏覽器並流覽至應用程式的 URL (例如 `https://localhost:5001`) 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-217">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="3cab6-218">在瀏覽器中，按<kbd>Shift</kbd> + <kbd>Alt</kbd> + <kbd>D</kbd>嘗試開始遠端偵錯。</span><span class="sxs-lookup"><span data-stu-id="3cab6-218">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

   <span data-ttu-id="3cab6-219">瀏覽器必須在啟用遠端偵錯功能的情況下執行，這並不是預設值。</span><span class="sxs-lookup"><span data-stu-id="3cab6-219">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="3cab6-220">如果停用遠端偵測，則會轉譯 [找 **不到可調試的瀏覽器]** 索引標籤錯誤頁面，並提供在開啟偵錯工具時啟動瀏覽器的指示。</span><span class="sxs-lookup"><span data-stu-id="3cab6-220">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="3cab6-221">依照瀏覽器的指示操作，這會開啟新的瀏覽器視窗。</span><span class="sxs-lookup"><span data-stu-id="3cab6-221">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="3cab6-222">關閉先前的瀏覽器視窗。</span><span class="sxs-lookup"><span data-stu-id="3cab6-222">Close the previous browser window.</span></span>

1. <span data-ttu-id="3cab6-223">當瀏覽器在啟用遠端偵錯程式的情況下執行時，偵錯工具鍵盤快速鍵 (<kbd>Shift</kbd> + <kbd>Alt</kbd> + <kbd>D</kbd>) 會開啟新的偵錯工具索引標籤。</span><span class="sxs-lookup"><span data-stu-id="3cab6-223">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.</span></span>

1. <span data-ttu-id="3cab6-224">經過一段時間後，[ **來源** ] 索引標籤會顯示節點內的應用程式 .net 元件清單 `file://` 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-224">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="3cab6-225">在元件程式碼 (檔案 `.razor`) 和 c # 程式碼檔案 (`.cs`) 中，當程式碼執行時，就會叫用您設定的中斷點。</span><span class="sxs-lookup"><span data-stu-id="3cab6-225">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="3cab6-226">叫用中斷點之後，單一步驟 (<kbd>F10</kbd>) 透過程式碼或繼續 (<kbd>F8</kbd>) 程式碼執行正常執行。</span><span class="sxs-lookup"><span data-stu-id="3cab6-226">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="3cab6-227">Blazor 提供可執行 [Chrome DevTools 通訊協定](https://chromedevtools.github.io/devtools-protocol/) 的偵錯工具 proxy，並使用擴充通訊協定。特定的網路資訊。</span><span class="sxs-lookup"><span data-stu-id="3cab6-227">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="3cab6-228">當您按下 [偵錯工具] 鍵盤快速鍵時，請將 Blazor Chrome DevTools 指向 proxy。</span><span class="sxs-lookup"><span data-stu-id="3cab6-228">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="3cab6-229">Proxy 會連接到您想要進行調試的瀏覽器視窗， (因此需要啟用遠端偵錯) 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-229">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="3cab6-230">瀏覽器來源對應</span><span class="sxs-lookup"><span data-stu-id="3cab6-230">Browser source maps</span></span>

<span data-ttu-id="3cab6-231">瀏覽器來源對應可讓瀏覽器將已編譯的檔案對應回原始來源檔案，且通常用於用戶端的偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="3cab6-231">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="3cab6-232">不過， Blazor 目前不會將 c # 直接對應至 JavaScript/WASM。</span><span class="sxs-lookup"><span data-stu-id="3cab6-232">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="3cab6-233">相反地， Blazor 會在瀏覽器中進行 IL 轉譯，因此來源對應不相關。</span><span class="sxs-lookup"><span data-stu-id="3cab6-233">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="3cab6-234">疑難排解</span><span class="sxs-lookup"><span data-stu-id="3cab6-234">Troubleshoot</span></span>

<span data-ttu-id="3cab6-235">如果您遇到錯誤，下列秘訣可能會有説明：</span><span class="sxs-lookup"><span data-stu-id="3cab6-235">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="3cab6-236">在 [ **偵錯工具** ] 索引標籤中，開啟瀏覽器中的開發人員工具。</span><span class="sxs-lookup"><span data-stu-id="3cab6-236">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="3cab6-237">在主控台中，執行 `localStorage.clear()` 以移除任何中斷點。</span><span class="sxs-lookup"><span data-stu-id="3cab6-237">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="3cab6-238">確認您已安裝並信任 ASP.NET Core HTTPS 開發憑證。</span><span class="sxs-lookup"><span data-stu-id="3cab6-238">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="3cab6-239">如需詳細資訊，請參閱<xref:security/enforcing-ssl#troubleshoot-certificate-problems>。</span><span class="sxs-lookup"><span data-stu-id="3cab6-239">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="3cab6-240">Visual Studio 需要在 [**工具**選項] 的 [一般] 選項中 **，啟用 ASP.NET (Chrome、Edge 和 IE) 選項的 JavaScript 偵錯工具**  >  **Options**  >  **Debugging**  >  \*\* \*\*。</span><span class="sxs-lookup"><span data-stu-id="3cab6-240">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="3cab6-241">這是 Visual Studio 的預設設定。</span><span class="sxs-lookup"><span data-stu-id="3cab6-241">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="3cab6-242">如果偵錯工具無法運作，請確認已選取該選項。</span><span class="sxs-lookup"><span data-stu-id="3cab6-242">If debugging isn't working, confirm that the option is selected.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="3cab6-243">`OnInitialized{Async}`未命中的中斷點</span><span class="sxs-lookup"><span data-stu-id="3cab6-243">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="3cab6-244">Blazor架構的偵錯工具 proxy 需要短暫的時間才能啟動，因此可能不會叫用[ `OnInitialized{Async}` 生命週期方法](xref:blazor/components/lifecycle#component-initialization-methods)中的中斷點。</span><span class="sxs-lookup"><span data-stu-id="3cab6-244">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="3cab6-245">建議您在方法主體的開頭加上延遲，以便在叫用中斷點之前，讓 debug proxy 有一些時間啟動。</span><span class="sxs-lookup"><span data-stu-id="3cab6-245">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="3cab6-246">您可以根據[ `if` 編譯器](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)指示詞加入延遲，以確保應用程式的發行組建不會有延遲。</span><span class="sxs-lookup"><span data-stu-id="3cab6-246">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="3cab6-247"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="3cab6-247"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="3cab6-248"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="3cab6-248"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-timeout"></a><span data-ttu-id="3cab6-249">Visual Studio 超時</span><span class="sxs-lookup"><span data-stu-id="3cab6-249">Visual Studio timeout</span></span>

<span data-ttu-id="3cab6-250">如果 Visual Studio 擲回偵測器無法啟動的例外狀況，表示已達到超時時間，您可以使用登錄設定來調整 timeout：</span><span class="sxs-lookup"><span data-stu-id="3cab6-250">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="3cab6-251">`{TIMEOUT}`上述命令中的預留位置以毫秒為單位。</span><span class="sxs-lookup"><span data-stu-id="3cab6-251">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="3cab6-252">例如，將一分鐘指派為 `60000` 。</span><span class="sxs-lookup"><span data-stu-id="3cab6-252">For example, one minute is assigned as `60000`.</span></span>
