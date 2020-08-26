---
title: Debug ASP.NET Core Blazor WebAssembly
author: guardrex
description: 瞭解如何對 Blazor 應用程式進行偵錯工具。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/debug
ms.openlocfilehash: 7681deb70610a8fbc27ccda7317b73921646794a
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876772"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="c24a8-103">Debug ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="c24a8-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="c24a8-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="c24a8-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="c24a8-105">Blazor WebAssembly 您可以使用以 Chromium 為基礎的瀏覽器中的瀏覽器開發工具來調試應用程式 (Edge/Chrome) 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="c24a8-106">您也可以使用下列整合式開發環境 (Ide) 來對應用程式進行偵錯工具：</span><span class="sxs-lookup"><span data-stu-id="c24a8-106">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="c24a8-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c24a8-107">Visual Studio</span></span>
* <span data-ttu-id="c24a8-108">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c24a8-108">Visual Studio for Mac</span></span>
* <span data-ttu-id="c24a8-109">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c24a8-109">Visual Studio Code</span></span>

<span data-ttu-id="c24a8-110">可用的案例包括：</span><span class="sxs-lookup"><span data-stu-id="c24a8-110">Available scenarios include:</span></span>

* <span data-ttu-id="c24a8-111">設定和移除中斷點。</span><span class="sxs-lookup"><span data-stu-id="c24a8-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="c24a8-112">在 Ide 中使用偵錯工具支援來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="c24a8-112">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="c24a8-113">逐步執行程式碼。</span><span class="sxs-lookup"><span data-stu-id="c24a8-113">Single-step through the code.</span></span>
* <span data-ttu-id="c24a8-114">使用 Ide 中的鍵盤快速鍵來繼續執行程式碼。</span><span class="sxs-lookup"><span data-stu-id="c24a8-114">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="c24a8-115">在 [ *區域變數* ] 視窗中，觀察本機變數的值。</span><span class="sxs-lookup"><span data-stu-id="c24a8-115">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="c24a8-116">請參閱呼叫堆疊，包括 JavaScript 和 .NET 之間的呼叫鏈。</span><span class="sxs-lookup"><span data-stu-id="c24a8-116">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="c24a8-117">目前，您 *不能*：</span><span class="sxs-lookup"><span data-stu-id="c24a8-117">For now, you *can't*:</span></span>

* <span data-ttu-id="c24a8-118">中斷未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="c24a8-118">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="c24a8-119">在應用程式啟動期間，在執行 debug proxy 之前叫用中斷點。</span><span class="sxs-lookup"><span data-stu-id="c24a8-119">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="c24a8-120">這包括 () 中的中斷點 `Program.Main` `Program.cs` ，以及應用程式所要求的第一個頁面所載入之元件[ `OnInitialized{Async}` 方法](xref:blazor/components/lifecycle#component-initialization-methods)中的中斷點。</span><span class="sxs-lookup"><span data-stu-id="c24a8-120">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c24a8-121">先決條件</span><span class="sxs-lookup"><span data-stu-id="c24a8-121">Prerequisites</span></span>

<span data-ttu-id="c24a8-122">偵錯工具需要下列其中一個瀏覽器：</span><span class="sxs-lookup"><span data-stu-id="c24a8-122">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="c24a8-123">Google Chrome (70 版或更新版本)  (預設) </span><span class="sxs-lookup"><span data-stu-id="c24a8-123">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="c24a8-124">Microsoft Edge (80 版或更新版本) </span><span class="sxs-lookup"><span data-stu-id="c24a8-124">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="c24a8-125">Visual Studio for Mac 需要8.8 版 (組建 1532) 或更新版本：</span><span class="sxs-lookup"><span data-stu-id="c24a8-125">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="c24a8-126">選取 Microsoft 上的 **下載 Visual Studio for Mac** 按鈕，以安裝最新版本的 Visual Studio for Mac [： Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="c24a8-126">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="c24a8-127">從 Visual Studio 中選取 *預覽* 頻道。</span><span class="sxs-lookup"><span data-stu-id="c24a8-127">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="c24a8-128">如需詳細資訊，請參閱 [安裝 Visual Studio for Mac 的預覽版本](/visualstudio/mac/install-preview)。</span><span class="sxs-lookup"><span data-stu-id="c24a8-128">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="c24a8-129">目前不支援 macOS 上的 Apple Safari。</span><span class="sxs-lookup"><span data-stu-id="c24a8-129">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="c24a8-130">啟用偵錯</span><span class="sxs-lookup"><span data-stu-id="c24a8-130">Enable debugging</span></span>

<span data-ttu-id="c24a8-131">若要啟用現有 Blazor WebAssembly 應用程式的偵錯工具，請更新 `launchSettings.json` 啟始專案中的檔案，以 `inspectUri` 在每個啟動設定檔中包含下列屬性：</span><span class="sxs-lookup"><span data-stu-id="c24a8-131">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="c24a8-132">更新之後，檔案 `launchSettings.json` 看起來應該會類似下列範例：</span><span class="sxs-lookup"><span data-stu-id="c24a8-132">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="c24a8-133">`inspectUri`屬性：</span><span class="sxs-lookup"><span data-stu-id="c24a8-133">The `inspectUri` property:</span></span>

* <span data-ttu-id="c24a8-134">讓 IDE 偵測應用程式是否為 Blazor WebAssembly 應用程式。</span><span class="sxs-lookup"><span data-stu-id="c24a8-134">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="c24a8-135">指示腳本的偵錯工具，透過的偵錯工具 proxy 連接到瀏覽器 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-135">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="c24a8-136">在啟動的瀏覽器上，Websocket 通訊協定 (`wsProtocol`) 、主機 (`url.hostname`) 、埠 () 和偵測器 URI 的預留位置值， (`url.port` `browserInspectUri` 由架構提供。</span><span class="sxs-lookup"><span data-stu-id="c24a8-136">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c24a8-137">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c24a8-137">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c24a8-138">若要 Blazor WebAssembly 在 Visual Studio 中偵錯工具：</span><span class="sxs-lookup"><span data-stu-id="c24a8-138">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="c24a8-139">建立新的 ASP.NET Core 託管 Blazor WebAssembly 應用程式。</span><span class="sxs-lookup"><span data-stu-id="c24a8-139">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="c24a8-140">按 <kbd>F5</kbd> 以在偵錯工具中執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="c24a8-140">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="c24a8-141">不支援 (<kbd>Ctrl</kbd>F5) **啟動而不進行調試** + <kbd>F5</kbd> 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-141">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="c24a8-142">當應用程式在偵錯工具設定中執行時，偵錯工具的額外負荷一律會降低效能。</span><span class="sxs-lookup"><span data-stu-id="c24a8-142">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="c24a8-143">在 *用戶端* 應用程式中的行上，設定中斷點 `currentCount++;` `Pages/Counter.razor` 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-143">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="c24a8-144">在瀏覽器中，流覽至 `Counter` 頁面，然後選取 [按 **我** ] 按鈕以點擊中斷點。</span><span class="sxs-lookup"><span data-stu-id="c24a8-144">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="c24a8-145">在 [Visual Studio] 中，檢查 [ `currentCount` **區域變數** ] 視窗中的域值。</span><span class="sxs-lookup"><span data-stu-id="c24a8-145">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="c24a8-146">按 <kbd>F5</kbd> 繼續執行。</span><span class="sxs-lookup"><span data-stu-id="c24a8-146">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="c24a8-147">在對 Blazor WebAssembly 應用程式進行偵錯工具時，您也可以將伺服器程式碼進行偵錯工具：</span><span class="sxs-lookup"><span data-stu-id="c24a8-147">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="c24a8-148">在的 `Pages/FetchData.razor` 頁面中，設定中斷點 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-148">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="c24a8-149">在動作方法的中，設定中斷點 `WeatherForecastController` `Get` 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-149">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="c24a8-150">流覽至該 `Fetch Data` 頁面，以叫用元件中的第一個中斷點， `FetchData` 再向伺服器發出 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="c24a8-150">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="c24a8-151">按 <kbd>F5</kbd> 繼續執行，然後在的伺服器上點擊中斷點 `WeatherForecastController` 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-151">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="c24a8-152">再按一次 <kbd>F5</kbd> ，讓繼續執行，並查看在瀏覽器中轉譯的氣象預報表格。</span><span class="sxs-lookup"><span data-stu-id="c24a8-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="c24a8-153">在執行 debug proxy 之前， **不** 會在應用程式啟動期間叫用中斷點。</span><span class="sxs-lookup"><span data-stu-id="c24a8-153">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="c24a8-154">這包括 () 中的中斷點 `Program.Main` `Program.cs` ，以及應用程式所要求的第一個頁面所載入之元件[ `OnInitialized{Async}` 方法](xref:blazor/components/lifecycle#component-initialization-methods)中的中斷點。</span><span class="sxs-lookup"><span data-stu-id="c24a8-154">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c24a8-155">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c24a8-155">Visual Studio Code</span></span>](#tab/visual-studio-code)

<a id="vscode"></a>

## <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="c24a8-156">獨立調試 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="c24a8-156">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="c24a8-157">Blazor WebAssembly在 VS Code 中開啟獨立應用程式。</span><span class="sxs-lookup"><span data-stu-id="c24a8-157">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="c24a8-158">您可能會收到通知，指出需要額外的設定才能啟用偵錯工具：</span><span class="sxs-lookup"><span data-stu-id="c24a8-158">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="c24a8-159">需要額外的設定，才能進行 Blazor WebAssembly 應用程式的偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="c24a8-159">Additional setup is required to debug Blazor WebAssembly applications.</span></span>

   <span data-ttu-id="c24a8-160">如果您收到通知：</span><span class="sxs-lookup"><span data-stu-id="c24a8-160">If you receive the notification:</span></span>

   * <span data-ttu-id="c24a8-161">確認已安裝 [適用于 Visual Studio Code 擴充](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) 功能的最新 c #。</span><span class="sxs-lookup"><span data-stu-id="c24a8-161">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="c24a8-162">若要檢查已安裝的擴充功能，請從功能表列開啟 [**視圖**  >  **擴充**功能]，或選取 [**活動**] 提要欄位中的**延伸**模組圖示。</span><span class="sxs-lookup"><span data-stu-id="c24a8-162">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="c24a8-163">確認已啟用 JavaScript preview 的偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="c24a8-163">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="c24a8-164">從功能表列開啟設定 **， (檔案**  >  **喜好**  >  **設定**) 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-164">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="c24a8-165">使用關鍵字搜尋 `debug preview` 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-165">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="c24a8-166">在搜尋結果中，確認已核取 [ **Debug > JavaScript： Use Preview** ] 的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="c24a8-166">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="c24a8-167">如果不存在啟用預覽偵錯工具的選項，請升級至最新版的 VS Code 或安裝 [JavaScript 偵錯工具擴充](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 功能 (VS Code 1.46 版或更早版本的) 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-167">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="c24a8-168">重載視窗。</span><span class="sxs-lookup"><span data-stu-id="c24a8-168">Reload the window.</span></span>

1. <span data-ttu-id="c24a8-169">使用 <kbd>F5</kbd> 鍵盤快速鍵或功能表項目來啟動調試。</span><span class="sxs-lookup"><span data-stu-id="c24a8-169">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="c24a8-170">不支援 (<kbd>Ctrl</kbd>F5) **啟動而不進行調試** + <kbd>F5</kbd> 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-170">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="c24a8-171">當應用程式在偵錯工具設定中執行時，偵錯工具的額外負荷一律會降低效能。</span><span class="sxs-lookup"><span data-stu-id="c24a8-171">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="c24a8-172">出現提示時，請選取 [ \*\* Blazor WebAssembly Debug\*\* ] 選項以開始進行調試。</span><span class="sxs-lookup"><span data-stu-id="c24a8-172">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="c24a8-173">獨立應用程式會啟動，並開啟偵錯工具瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="c24a8-173">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="c24a8-174">在 *用戶端* 應用程式中的行上，設定中斷點 `currentCount++;` `Pages/Counter.razor` 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-174">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="c24a8-175">在瀏覽器中，流覽至 `Counter` 頁面，然後選取 [按 **我** ] 按鈕以點擊中斷點。</span><span class="sxs-lookup"><span data-stu-id="c24a8-175">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="c24a8-176">在執行 debug proxy 之前， **不** 會在應用程式啟動期間叫用中斷點。</span><span class="sxs-lookup"><span data-stu-id="c24a8-176">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="c24a8-177">這包括 () 中的中斷點 `Program.Main` `Program.cs` ，以及應用程式所要求的第一個頁面所載入之元件[ `OnInitialized{Async}` 方法](xref:blazor/components/lifecycle#component-initialization-methods)中的中斷點。</span><span class="sxs-lookup"><span data-stu-id="c24a8-177">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="c24a8-178">主控的調試 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="c24a8-178">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="c24a8-179">Blazor WebAssembly在 VS Code 中開啟託管應用程式的方案資料夾。</span><span class="sxs-lookup"><span data-stu-id="c24a8-179">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="c24a8-180">如果未設定專案的啟動設定，則會出現下列通知。</span><span class="sxs-lookup"><span data-stu-id="c24a8-180">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="c24a8-181">選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="c24a8-181">Select **Yes**.</span></span>

   > <span data-ttu-id="c24a8-182">' {APPLICATION NAME} ' 中缺少建立和偵測所需的資產。</span><span class="sxs-lookup"><span data-stu-id="c24a8-182">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="c24a8-183">新增它們嗎？</span><span class="sxs-lookup"><span data-stu-id="c24a8-183">Add them?</span></span>

1. <span data-ttu-id="c24a8-184">在視窗頂端的 [命令選擇區] 中，選取主控方案內的 *伺服器* 專案。</span><span class="sxs-lookup"><span data-stu-id="c24a8-184">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="c24a8-185">`launch.json`使用啟動設定來啟動偵錯工具時，會產生一個檔案。</span><span class="sxs-lookup"><span data-stu-id="c24a8-185">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="c24a8-186">附加至現有的偵錯工具會話</span><span class="sxs-lookup"><span data-stu-id="c24a8-186">Attach to an existing debugging session</span></span>

<span data-ttu-id="c24a8-187">若要附加至執行 Blazor 中的應用程式，請使用下列設定來建立檔案 `launch.json` ：</span><span class="sxs-lookup"><span data-stu-id="c24a8-187">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="c24a8-188">只有獨立應用程式才支援附加至偵錯工具會話。</span><span class="sxs-lookup"><span data-stu-id="c24a8-188">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="c24a8-189">若要使用完整堆疊的偵錯工具，您必須從 VS Code 啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="c24a8-189">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="c24a8-190">啟動設定選項</span><span class="sxs-lookup"><span data-stu-id="c24a8-190">Launch configuration options</span></span>

<span data-ttu-id="c24a8-191">下列啟動設定選項支援 `blazorwasm` debug 型別 (`.vscode/launch.json`) 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-191">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="c24a8-192">選項</span><span class="sxs-lookup"><span data-stu-id="c24a8-192">Option</span></span>    | <span data-ttu-id="c24a8-193">描述</span><span class="sxs-lookup"><span data-stu-id="c24a8-193">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="c24a8-194">用 `launch` 來啟動偵錯工具，並將其附加至 Blazor WebAssembly 應用程式，或將偵測 `attach` 會話附加至已在執行中的應用程式。</span><span class="sxs-lookup"><span data-stu-id="c24a8-194">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="c24a8-195">在瀏覽器中要在瀏覽器中開啟的 URL。</span><span class="sxs-lookup"><span data-stu-id="c24a8-195">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="c24a8-196">預設為 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="c24a8-196">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="c24a8-197">要啟動偵錯工具的瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="c24a8-197">The browser to launch for the debugging session.</span></span> <span data-ttu-id="c24a8-198">設為 `edge` 或 `chrome`。</span><span class="sxs-lookup"><span data-stu-id="c24a8-198">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="c24a8-199">預設為 `chrome`。</span><span class="sxs-lookup"><span data-stu-id="c24a8-199">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="c24a8-200">用來從 JS 偵錯工具產生記錄。</span><span class="sxs-lookup"><span data-stu-id="c24a8-200">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="c24a8-201">設定為 `true` 以產生記錄。</span><span class="sxs-lookup"><span data-stu-id="c24a8-201">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="c24a8-202">`true`如果啟動和偵測託管應用程式，則必須設定為 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-202">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="c24a8-203">指定 web 伺服器的絕對路徑。</span><span class="sxs-lookup"><span data-stu-id="c24a8-203">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="c24a8-204">如果從子路由提供應用程式，則應該設定。</span><span class="sxs-lookup"><span data-stu-id="c24a8-204">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="c24a8-205">等候偵錯工具附加的毫秒數。</span><span class="sxs-lookup"><span data-stu-id="c24a8-205">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="c24a8-206">預設值為30000毫秒 (30 秒) 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-206">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="c24a8-207">可執行檔的參考，用來執行裝載應用程式的伺服器。</span><span class="sxs-lookup"><span data-stu-id="c24a8-207">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="c24a8-208">如果為，則必須設定 `hosted` `true` 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-208">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="c24a8-209">用來啟動應用程式的工作目錄。</span><span class="sxs-lookup"><span data-stu-id="c24a8-209">The working directory to launch the app under.</span></span> <span data-ttu-id="c24a8-210">如果為，則必須設定 `hosted` `true` 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-210">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="c24a8-211">要提供給已啟動進程的環境變數。</span><span class="sxs-lookup"><span data-stu-id="c24a8-211">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="c24a8-212">只有 `hosted` 在設為時才適用 `true` 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-212">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="c24a8-213">啟動設定範例</span><span class="sxs-lookup"><span data-stu-id="c24a8-213">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="c24a8-214">啟動獨立應用程式並進行調試 Blazor WebAssembly 程式</span><span class="sxs-lookup"><span data-stu-id="c24a8-214">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="c24a8-215">在指定的 URL 附加至執行中的應用程式</span><span class="sxs-lookup"><span data-stu-id="c24a8-215">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="c24a8-216">使用 Microsoft Edge 啟動及偵測託管 Blazor WebAssembly 應用程式</span><span class="sxs-lookup"><span data-stu-id="c24a8-216">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="c24a8-217">瀏覽器設定預設為 Google Chrome。</span><span class="sxs-lookup"><span data-stu-id="c24a8-217">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="c24a8-218">使用 Microsoft Edge 進行偵錯工具時，請將設定 `browser` 為 `edge` 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-218">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="c24a8-219">若要使用 Google Chrome，請不要設定 `browser` 選項，或將選項的值設為 `chrome` 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-219">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

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

<span data-ttu-id="c24a8-220">在上述範例中， `MyHostedApp.Server.dll` 是 *伺服器* 應用程式的元件。</span><span class="sxs-lookup"><span data-stu-id="c24a8-220">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="c24a8-221">此 `.vscode` 資料夾位於 `Client` 、和資料夾旁的方案資料夾中 `Server` `Shared` 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-221">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c24a8-222">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c24a8-222">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c24a8-223">若要 Blazor WebAssembly 在 Visual Studio for Mac 中偵錯工具：</span><span class="sxs-lookup"><span data-stu-id="c24a8-223">To debug a Blazor WebAssembly app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="c24a8-224">建立新的 ASP.NET Core 託管 Blazor WebAssembly 應用程式。</span><span class="sxs-lookup"><span data-stu-id="c24a8-224">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="c24a8-225">按<kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> ，以在偵錯工具中執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="c24a8-225">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="c24a8-226">**啟動但不** (<kbd>&#8997;</kbd> + <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>) 不受支援。</span><span class="sxs-lookup"><span data-stu-id="c24a8-226">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="c24a8-227">當應用程式在偵錯工具設定中執行時，偵錯工具的額外負荷一律會降低效能。</span><span class="sxs-lookup"><span data-stu-id="c24a8-227">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="c24a8-228">Google Chrome 或 Microsoft Edge 必須是所選的瀏覽器，才能進行偵錯工具會話。</span><span class="sxs-lookup"><span data-stu-id="c24a8-228">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="c24a8-229">在 *用戶端* 應用程式中的行上，設定中斷點 `currentCount++;` `Pages/Counter.razor` 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-229">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="c24a8-230">在瀏覽器中，流覽至 `Counter` 頁面，然後選取 [按 **我** ] 按鈕以點擊中斷點：</span><span class="sxs-lookup"><span data-stu-id="c24a8-230">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="c24a8-231">在 [Visual Studio] 中，檢查 [ `currentCount` **區域變數** ] 視窗中的域值。</span><span class="sxs-lookup"><span data-stu-id="c24a8-231">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="c24a8-232">按<kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>以繼續執行。</span><span class="sxs-lookup"><span data-stu-id="c24a8-232">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="c24a8-233">在對 Blazor WebAssembly 應用程式進行偵錯工具時，您也可以將伺服器程式碼進行偵錯工具：</span><span class="sxs-lookup"><span data-stu-id="c24a8-233">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="c24a8-234">在的 `Pages/FetchData.razor` 頁面中，設定中斷點 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-234">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="c24a8-235">在動作方法的中，設定中斷點 `WeatherForecastController` `Get` 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-235">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="c24a8-236">流覽至該 `Fetch Data` 頁面，以叫用元件中的第一個中斷點， `FetchData` 再向伺服器發出 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="c24a8-236">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="c24a8-237">按<kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>繼續執行，然後在的伺服器上點擊中斷點 `WeatherForecastController` 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-237">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="c24a8-238">再按一次<kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> ，讓執行繼續執行，並查看在瀏覽器中轉譯的氣象預報表格。</span><span class="sxs-lookup"><span data-stu-id="c24a8-238">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="c24a8-239">在執行 debug proxy 之前， **不** 會在應用程式啟動期間叫用中斷點。</span><span class="sxs-lookup"><span data-stu-id="c24a8-239">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="c24a8-240">這包括 () 中的中斷點 `Program.Main` `Program.cs` ，以及應用程式所要求的第一個頁面所載入之元件[ `OnInitialized{Async}` 方法](xref:blazor/components/lifecycle#component-initialization-methods)中的中斷點。</span><span class="sxs-lookup"><span data-stu-id="c24a8-240">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="c24a8-241">如需詳細資訊，請參閱 [使用 Visual Studio for Mac 的調試](/visualstudio/mac/debugging?view=vsmac-2019)程式。</span><span class="sxs-lookup"><span data-stu-id="c24a8-241">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging?view=vsmac-2019).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="c24a8-242">瀏覽器中的 Debug</span><span class="sxs-lookup"><span data-stu-id="c24a8-242">Debug in the browser</span></span>

<span data-ttu-id="c24a8-243">*本節中的指導方針適用于 Google Chrome 以及在 Windows 上執行的 Microsoft Edge。*</span><span class="sxs-lookup"><span data-stu-id="c24a8-243">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="c24a8-244">在開發環境中執行應用程式的偵錯工具組建。</span><span class="sxs-lookup"><span data-stu-id="c24a8-244">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="c24a8-245">啟動瀏覽器並流覽至應用程式的 URL (例如 `https://localhost:5001`) 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-245">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="c24a8-246">在瀏覽器中，按<kbd>Shift</kbd> + <kbd>Alt</kbd> + <kbd>d</kbd>嘗試開始遠端偵錯。</span><span class="sxs-lookup"><span data-stu-id="c24a8-246">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="c24a8-247">瀏覽器必須在啟用遠端偵錯功能的情況下執行，這並不是預設值。</span><span class="sxs-lookup"><span data-stu-id="c24a8-247">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="c24a8-248">如果停用遠端偵測，則會轉譯 [找 **不到可調試的瀏覽器]** 索引標籤錯誤頁面，並提供在開啟偵錯工具時啟動瀏覽器的指示。</span><span class="sxs-lookup"><span data-stu-id="c24a8-248">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="c24a8-249">依照瀏覽器的指示操作，這會開啟新的瀏覽器視窗。</span><span class="sxs-lookup"><span data-stu-id="c24a8-249">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="c24a8-250">關閉先前的瀏覽器視窗。</span><span class="sxs-lookup"><span data-stu-id="c24a8-250">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="c24a8-251">當瀏覽器在啟用遠端偵錯程式的情況下執行時，在上一個步驟中的偵錯工具鍵盤快速鍵會開啟新的偵錯工具索引標籤。</span><span class="sxs-lookup"><span data-stu-id="c24a8-251">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="c24a8-252">經過一段時間後，[ **來源** ] 索引標籤會顯示節點內的應用程式 .net 元件清單 `file://` 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-252">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="c24a8-253">在元件程式碼 (檔案 `.razor`) 和 c # 程式碼檔案 (`.cs`) 中，當程式碼執行時，就會叫用您設定的中斷點。</span><span class="sxs-lookup"><span data-stu-id="c24a8-253">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="c24a8-254">叫用中斷點之後，單一步驟 (<kbd>F10</kbd>) 透過程式碼或繼續 (<kbd>F8</kbd>) 程式碼執行正常執行。</span><span class="sxs-lookup"><span data-stu-id="c24a8-254">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="c24a8-255">Blazor 提供可執行 [Chrome DevTools 通訊協定](https://chromedevtools.github.io/devtools-protocol/) 的偵錯工具 proxy，並使用擴充通訊協定。特定的網路資訊。</span><span class="sxs-lookup"><span data-stu-id="c24a8-255">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="c24a8-256">當您按下 [偵錯工具] 鍵盤快速鍵時，請將 Blazor Chrome DevTools 指向 proxy。</span><span class="sxs-lookup"><span data-stu-id="c24a8-256">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="c24a8-257">Proxy 會連接到您想要進行調試的瀏覽器視窗， (因此需要啟用遠端偵錯) 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-257">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="c24a8-258">瀏覽器來源對應</span><span class="sxs-lookup"><span data-stu-id="c24a8-258">Browser source maps</span></span>

<span data-ttu-id="c24a8-259">瀏覽器來源對應可讓瀏覽器將已編譯的檔案對應回原始來源檔案，且通常用於用戶端的偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="c24a8-259">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="c24a8-260">不過， Blazor 目前不會將 c # 直接對應至 JavaScript/WASM。</span><span class="sxs-lookup"><span data-stu-id="c24a8-260">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="c24a8-261">相反地， Blazor 會在瀏覽器中進行 IL 轉譯，因此來源對應不相關。</span><span class="sxs-lookup"><span data-stu-id="c24a8-261">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="c24a8-262">疑難排解</span><span class="sxs-lookup"><span data-stu-id="c24a8-262">Troubleshoot</span></span>

<span data-ttu-id="c24a8-263">如果您遇到錯誤，下列秘訣可能會有説明：</span><span class="sxs-lookup"><span data-stu-id="c24a8-263">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="c24a8-264">在 [ **偵錯工具** ] 索引標籤中，開啟瀏覽器中的開發人員工具。</span><span class="sxs-lookup"><span data-stu-id="c24a8-264">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="c24a8-265">在主控台中，執行 `localStorage.clear()` 以移除任何中斷點。</span><span class="sxs-lookup"><span data-stu-id="c24a8-265">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="c24a8-266">確認您已安裝並信任 ASP.NET Core HTTPS 開發憑證。</span><span class="sxs-lookup"><span data-stu-id="c24a8-266">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="c24a8-267">如需詳細資訊，請參閱<xref:security/enforcing-ssl#troubleshoot-certificate-problems>。</span><span class="sxs-lookup"><span data-stu-id="c24a8-267">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="c24a8-268">Visual Studio 需要在 [**工具**選項] 的 [一般] 選項中 **，啟用 ASP.NET (Chrome、Edge 和 IE) 選項的 JavaScript 偵錯工具**  >  **Options**  >  **Debugging**  >  \*\* \*\*。</span><span class="sxs-lookup"><span data-stu-id="c24a8-268">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="c24a8-269">這是 Visual Studio 的預設設定。</span><span class="sxs-lookup"><span data-stu-id="c24a8-269">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="c24a8-270">如果偵錯工具無法運作，請確認已選取該選項。</span><span class="sxs-lookup"><span data-stu-id="c24a8-270">If debugging isn't working, confirm that the option is selected.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="c24a8-271">`OnInitialized{Async}`未命中的中斷點</span><span class="sxs-lookup"><span data-stu-id="c24a8-271">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="c24a8-272">Blazor架構的偵錯工具 proxy 需要短暫的時間才能啟動，因此可能不會叫用[ `OnInitialized{Async}` 生命週期方法](xref:blazor/components/lifecycle#component-initialization-methods)中的中斷點。</span><span class="sxs-lookup"><span data-stu-id="c24a8-272">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="c24a8-273">建議您在方法主體的開頭加上延遲，以便在叫用中斷點之前，讓 debug proxy 有一些時間啟動。</span><span class="sxs-lookup"><span data-stu-id="c24a8-273">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="c24a8-274">您可以根據[ `if` 編譯器](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)指示詞加入延遲，以確保應用程式的發行組建不會有延遲。</span><span class="sxs-lookup"><span data-stu-id="c24a8-274">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="c24a8-275"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="c24a8-275"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="c24a8-276"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="c24a8-276"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="c24a8-277">Visual Studio (Windows) timeout</span><span class="sxs-lookup"><span data-stu-id="c24a8-277">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="c24a8-278">如果 Visual Studio 擲回偵測器無法啟動的例外狀況，表示已達到超時時間，您可以使用登錄設定來調整 timeout：</span><span class="sxs-lookup"><span data-stu-id="c24a8-278">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="c24a8-279">`{TIMEOUT}`上述命令中的預留位置以毫秒為單位。</span><span class="sxs-lookup"><span data-stu-id="c24a8-279">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="c24a8-280">例如，將一分鐘指派為 `60000` 。</span><span class="sxs-lookup"><span data-stu-id="c24a8-280">For example, one minute is assigned as `60000`.</span></span>
