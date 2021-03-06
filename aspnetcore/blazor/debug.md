---
title: Debug ASP.NET Core Blazor WebAssembly
author: guardrex
description: 瞭解如何對 Blazor 應用程式進行偵錯工具。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/debug
ms.openlocfilehash: adf22001e7d9b8ee4f36456cd4b07d2791a7331f
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395146"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="b5445-103">Debug ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b5445-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="b5445-104">Blazor WebAssembly 您可以使用以 Chromium 為基礎的瀏覽器中的瀏覽器開發工具來調試應用程式 (Edge/Chrome) 。</span><span class="sxs-lookup"><span data-stu-id="b5445-104">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="b5445-105">您也可以使用下列整合式開發環境 (Ide) 來對應用程式進行偵錯工具：</span><span class="sxs-lookup"><span data-stu-id="b5445-105">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="b5445-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b5445-106">Visual Studio</span></span>
* <span data-ttu-id="b5445-107">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b5445-107">Visual Studio for Mac</span></span>
* <span data-ttu-id="b5445-108">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b5445-108">Visual Studio Code</span></span>

<span data-ttu-id="b5445-109">可用的案例包括：</span><span class="sxs-lookup"><span data-stu-id="b5445-109">Available scenarios include:</span></span>

* <span data-ttu-id="b5445-110">設定和移除中斷點。</span><span class="sxs-lookup"><span data-stu-id="b5445-110">Set and remove breakpoints.</span></span>
* <span data-ttu-id="b5445-111">在 Ide 中使用偵錯工具支援來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5445-111">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="b5445-112">逐步執行程式碼。</span><span class="sxs-lookup"><span data-stu-id="b5445-112">Single-step through the code.</span></span>
* <span data-ttu-id="b5445-113">使用 Ide 中的鍵盤快速鍵來繼續執行程式碼。</span><span class="sxs-lookup"><span data-stu-id="b5445-113">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="b5445-114">在 [ *區域變數* ] 視窗中，觀察本機變數的值。</span><span class="sxs-lookup"><span data-stu-id="b5445-114">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="b5445-115">請參閱呼叫堆疊，包括 JavaScript 和 .NET 之間的呼叫鏈。</span><span class="sxs-lookup"><span data-stu-id="b5445-115">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="b5445-116">目前，您 *不能*：</span><span class="sxs-lookup"><span data-stu-id="b5445-116">For now, you *can't*:</span></span>

* <span data-ttu-id="b5445-117">中斷未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="b5445-117">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="b5445-118">在應用程式啟動期間，在執行 debug proxy 之前叫用中斷點。</span><span class="sxs-lookup"><span data-stu-id="b5445-118">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="b5445-119">這包括 () 中的中斷點 `Program.Main` `Program.cs` ，以及應用程式所要求的第一個頁面所載入之元件[ `OnInitialized{Async}` 方法](xref:blazor/components/lifecycle#component-initialization-methods)中的中斷點。</span><span class="sxs-lookup"><span data-stu-id="b5445-119">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>
* <span data-ttu-id="b5445-120">在非本機案例中進行 Debug (例如， [適用于 Linux 的 Windows 子系統 (WSL) ](/windows/wsl/) 或 [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline)) 。</span><span class="sxs-lookup"><span data-stu-id="b5445-120">Debug in non-local scenarios (for example, [Windows Subsystem for Linux (WSL)](/windows/wsl/) or [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline)).</span></span>
* <span data-ttu-id="b5445-121">`*Server*`在偵錯工具期間自動重建託管解決方案的後端應用程式 Blazor WebAssembly ，例如藉由執行應用程式與 [`dotnet watch run`](xref:tutorials/dotnet-watch) 。</span><span class="sxs-lookup"><span data-stu-id="b5445-121">Automatically rebuild the backend `*Server*` app of a hosted Blazor WebAssembly solution during debugging, for example by running the app with [`dotnet watch run`](xref:tutorials/dotnet-watch).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b5445-122">必要條件</span><span class="sxs-lookup"><span data-stu-id="b5445-122">Prerequisites</span></span>

<span data-ttu-id="b5445-123">偵錯工具需要下列其中一個瀏覽器：</span><span class="sxs-lookup"><span data-stu-id="b5445-123">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="b5445-124">Google Chrome (70 版或更新版本)  (預設) </span><span class="sxs-lookup"><span data-stu-id="b5445-124">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="b5445-125">Microsoft Edge (80 版或更新版本) </span><span class="sxs-lookup"><span data-stu-id="b5445-125">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="b5445-126">確定防火牆或 proxy 不會封鎖與 debug proxy (`NodeJS` 進程) 的通訊。</span><span class="sxs-lookup"><span data-stu-id="b5445-126">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="b5445-127">如需詳細資訊，請參閱 [防火牆](#firewall-configuration) 設定一節。</span><span class="sxs-lookup"><span data-stu-id="b5445-127">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

<span data-ttu-id="b5445-128">Visual Studio Code 使用者需要下列擴充功能：</span><span class="sxs-lookup"><span data-stu-id="b5445-128">Visual Studio Code users require the following extensions:</span></span>

* [<span data-ttu-id="b5445-129">C # for Visual Studio Code 擴充功能</span><span class="sxs-lookup"><span data-stu-id="b5445-129">C# for Visual Studio Code Extension</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="b5445-130">使用 c # for Visual Studio Code 延伸模組版本1.23.9 或更新版本時， [ Blazor WASM 偵錯工具延伸](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.blazorwasm-companion)模組 () </span><span class="sxs-lookup"><span data-stu-id="b5445-130">[Blazor WASM Debugging Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.blazorwasm-companion) (when using the C# for Visual Studio Code Extension version 1.23.9 or later)</span></span>

<span data-ttu-id="b5445-131">在 VS Code 中開啟專案之後，您可能會收到通知，指出需要額外的設定才能啟用偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="b5445-131">After opening a project in VS Code, you may receive a notification that additional setup is required to enable debugging.</span></span> <span data-ttu-id="b5445-132">如果要求，請從 Visual Studio Marketplace 安裝必要的延伸模組。</span><span class="sxs-lookup"><span data-stu-id="b5445-132">If requested, install the required extensions from the Visual Studio Marketplace.</span></span> <span data-ttu-id="b5445-133">若要檢查已安裝的擴充功能，請從功能表列開啟 [**視圖**  >  **擴充** 功能]，或選取 [**活動**] 提要欄位中的 **延伸** 模組圖示。</span><span class="sxs-lookup"><span data-stu-id="b5445-133">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>

<span data-ttu-id="b5445-134">Visual Studio for Mac 需要版本 8.8 (build 1532) 或更新版本：</span><span class="sxs-lookup"><span data-stu-id="b5445-134">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="b5445-135">選取 [Microsoft： Visual studio For mac](https://visualstudio.microsoft.com/vs/mac/)的 [**下載 Visual studio for mac** ] 按鈕，以安裝最新版本的 visual studio for mac。</span><span class="sxs-lookup"><span data-stu-id="b5445-135">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="b5445-136">從 Visual Studio 內選取 *預覽* 頻道。</span><span class="sxs-lookup"><span data-stu-id="b5445-136">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="b5445-137">如需詳細資訊，請參閱 [安裝 Visual Studio For Mac 的預覽版本](/visualstudio/mac/install-preview)。</span><span class="sxs-lookup"><span data-stu-id="b5445-137">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="b5445-138">目前不支援 macOS 上的 Apple Safari。</span><span class="sxs-lookup"><span data-stu-id="b5445-138">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="b5445-139">啟用偵錯</span><span class="sxs-lookup"><span data-stu-id="b5445-139">Enable debugging</span></span>

<span data-ttu-id="b5445-140">若要啟用現有 Blazor WebAssembly 應用程式的偵錯工具，請更新 `launchSettings.json` 啟始專案中的檔案，以 `inspectUri` 在每個啟動設定檔中包含下列屬性：</span><span class="sxs-lookup"><span data-stu-id="b5445-140">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="b5445-141">更新之後，檔案 `launchSettings.json` 看起來應該會類似下列範例：</span><span class="sxs-lookup"><span data-stu-id="b5445-141">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="b5445-142">`inspectUri`屬性：</span><span class="sxs-lookup"><span data-stu-id="b5445-142">The `inspectUri` property:</span></span>

* <span data-ttu-id="b5445-143">讓 IDE 偵測應用程式是否為 Blazor WebAssembly 應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5445-143">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="b5445-144">指示腳本的偵錯工具，透過的偵錯工具 proxy 連接到瀏覽器 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="b5445-144">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="b5445-145">在啟動的瀏覽器上，Websocket 通訊協定 (`wsProtocol`) 、主機 (`url.hostname`) 、埠 () 和偵測器 URI 的預留位置值， (`url.port` `browserInspectUri` 由架構提供。</span><span class="sxs-lookup"><span data-stu-id="b5445-145">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b5445-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b5445-146">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b5445-147">若要 Blazor WebAssembly 在 Visual Studio 中對應用程式進行偵錯工具：</span><span class="sxs-lookup"><span data-stu-id="b5445-147">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="b5445-148">建立新的託管 Blazor WebAssembly 方案。</span><span class="sxs-lookup"><span data-stu-id="b5445-148">Create a new hosted Blazor WebAssembly solution.</span></span>
1. <span data-ttu-id="b5445-149">按 <kbd>F5</kbd> 以在偵錯工具中執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5445-149">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="b5445-150">不支援 (<kbd>Ctrl</kbd>F5) **啟動而不進行調試** + <kbd></kbd> 。</span><span class="sxs-lookup"><span data-stu-id="b5445-150">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="b5445-151">當應用程式在偵錯工具設定中執行時，偵錯工具的額外負荷一律會降低效能。</span><span class="sxs-lookup"><span data-stu-id="b5445-151">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="b5445-152">在 `*Client*` 應用程式中的行上設定中斷點 `currentCount++;` `Pages/Counter.razor` 。</span><span class="sxs-lookup"><span data-stu-id="b5445-152">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="b5445-153">在瀏覽器中，流覽至 `Counter` 頁面，然後選取 [按 **我** ] 按鈕以點擊中斷點。</span><span class="sxs-lookup"><span data-stu-id="b5445-153">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="b5445-154">在 Visual Studio 中，檢查 `currentCount` [ **區域變數** ] 視窗中的域值。</span><span class="sxs-lookup"><span data-stu-id="b5445-154">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="b5445-155">按 <kbd>F5</kbd> 繼續執行。</span><span class="sxs-lookup"><span data-stu-id="b5445-155">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="b5445-156">在對 Blazor WebAssembly 應用程式進行偵錯工具時，您也可以將伺服器程式碼進行偵錯工具：</span><span class="sxs-lookup"><span data-stu-id="b5445-156">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="b5445-157">在的 `Pages/FetchData.razor` 頁面中，設定中斷點 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="b5445-157">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="b5445-158">在動作方法的中，設定中斷點 `WeatherForecastController` `Get` 。</span><span class="sxs-lookup"><span data-stu-id="b5445-158">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="b5445-159">流覽至該 `Fetch Data` 頁面，以叫用元件中的第一個中斷點， `FetchData` 再向伺服器發出 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="b5445-159">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="b5445-160">按 <kbd>F5</kbd> 繼續執行，然後在的伺服器上點擊中斷點 `WeatherForecastController` 。</span><span class="sxs-lookup"><span data-stu-id="b5445-160">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="b5445-161">再按一次 <kbd>F5</kbd> ，讓繼續執行，並查看在瀏覽器中轉譯的氣象預報表格。</span><span class="sxs-lookup"><span data-stu-id="b5445-161">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="b5445-162">在執行 debug proxy 之前， **不** 會在應用程式啟動期間叫用中斷點。</span><span class="sxs-lookup"><span data-stu-id="b5445-162">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="b5445-163">這包括 () 中的中斷點 `Program.Main` `Program.cs` ，以及應用程式所要求的第一個頁面所載入之元件[ `OnInitialized{Async}` 方法](xref:blazor/components/lifecycle#component-initialization-methods)中的中斷點。</span><span class="sxs-lookup"><span data-stu-id="b5445-163">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="b5445-164">如果應用程式裝載于不同的 [應用程式基底路徑](xref:blazor/host-and-deploy/index#app-base-path) `/` ，請更新中的下列屬性， `Properties/launchSettings.json` 以反映應用程式的基底路徑：</span><span class="sxs-lookup"><span data-stu-id="b5445-164">If the app is hosted at a different [app base path](xref:blazor/host-and-deploy/index#app-base-path) than `/`, update the following properties in `Properties/launchSettings.json` to reflect the app's base path:</span></span>

* <span data-ttu-id="b5445-165">`applicationUrl`:</span><span class="sxs-lookup"><span data-stu-id="b5445-165">`applicationUrl`:</span></span>

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* <span data-ttu-id="b5445-166">`inspectUri` 每個設定檔：</span><span class="sxs-lookup"><span data-stu-id="b5445-166">`inspectUri` of each profile:</span></span>

  ```json
  "profiles": {
    ...
    "{PROFILE 1, 2, ... N}": {
      ...
      "inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/{APP BASE PATH}/_framework/debug/ws-proxy?browser={browserInspectUri}",
      ...
    }
  }
  ```

<span data-ttu-id="b5445-167">上述設定中的預留位置：</span><span class="sxs-lookup"><span data-stu-id="b5445-167">The placeholders in the preceding settings:</span></span>

* <span data-ttu-id="b5445-168">`{INSECURE PORT}`：不安全的埠。</span><span class="sxs-lookup"><span data-stu-id="b5445-168">`{INSECURE PORT}`: The insecure port.</span></span> <span data-ttu-id="b5445-169">預設會提供隨機值，但允許自訂埠。</span><span class="sxs-lookup"><span data-stu-id="b5445-169">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="b5445-170">`{APP BASE PATH}`：應用程式的基底路徑。</span><span class="sxs-lookup"><span data-stu-id="b5445-170">`{APP BASE PATH}`: The app's base path.</span></span>
* <span data-ttu-id="b5445-171">`{SECURE PORT}`：安全埠。</span><span class="sxs-lookup"><span data-stu-id="b5445-171">`{SECURE PORT}`: The secure port.</span></span> <span data-ttu-id="b5445-172">預設會提供隨機值，但允許自訂埠。</span><span class="sxs-lookup"><span data-stu-id="b5445-172">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="b5445-173">`{PROFILE 1, 2, ... N}`：啟動設定設定檔。</span><span class="sxs-lookup"><span data-stu-id="b5445-173">`{PROFILE 1, 2, ... N}`: Launch settings profiles.</span></span> <span data-ttu-id="b5445-174">通常，應用程式預設會指定多個設定檔 (例如，IIS Express 的設定檔和 Kestrel 伺服器) 所使用的專案設定檔。</span><span class="sxs-lookup"><span data-stu-id="b5445-174">Usually, an app specifies more than one profile by default (for example, a profile for IIS Express and a project profile, which is used by Kestrel server).</span></span>

<span data-ttu-id="b5445-175">在下列範例中，應用程式是 `/OAT` 使用中設定的應用程式基底路徑 `wwwroot/index.html` 來託管 `<base href="/OAT/">` ：</span><span class="sxs-lookup"><span data-stu-id="b5445-175">In the following examples, the app is hosted at `/OAT` with an app base path configured in `wwwroot/index.html` as `<base href="/OAT/">`:</span></span>

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

<span data-ttu-id="b5445-176">如需使用應用程式的自訂應用程式基底路徑的詳細資訊 Blazor WebAssembly ，請參閱 <xref:blazor/host-and-deploy/index#app-base-path> 。</span><span class="sxs-lookup"><span data-stu-id="b5445-176">For information on using a custom app base path for Blazor WebAssembly apps, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b5445-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b5445-177">Visual Studio Code</span></span>](#tab/visual-studio-code)

<h2 id="vscode"><span data-ttu-id="b5445-178">獨立調試 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b5445-178">Debug standalone Blazor WebAssembly</span></span></h2>

<span data-ttu-id="b5445-179">如需在資料夾中設定 VS Code 資產的相關資訊 `.vscode` ，請參閱中的 **Linux** 作業系統指引 <xref:blazor/tooling> 。</span><span class="sxs-lookup"><span data-stu-id="b5445-179">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="b5445-180">Blazor WebAssembly在 VS Code 中開啟獨立應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5445-180">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="b5445-181">您可能會收到通知，指出需要額外的設定才能啟用偵錯工具：</span><span class="sxs-lookup"><span data-stu-id="b5445-181">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="b5445-182">需要額外的設定，才能進行 Blazor WebAssembly 應用程式的偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="b5445-182">Additional setup is required to debug Blazor WebAssembly applications.</span></span>

   <span data-ttu-id="b5445-183">如果您收到通知：</span><span class="sxs-lookup"><span data-stu-id="b5445-183">If you receive the notification:</span></span>

   * <span data-ttu-id="b5445-184">確認已安裝最新的 [c # For Visual Studio Code 延伸](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) 模組。</span><span class="sxs-lookup"><span data-stu-id="b5445-184">Confirm that the latest [C# for Visual Studio Code Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="b5445-185">若要檢查已安裝的擴充功能，請從功能表列開啟 [**視圖**  >  **擴充** 功能]，或選取 [**活動**] 提要欄位中的 **延伸** 模組圖示。</span><span class="sxs-lookup"><span data-stu-id="b5445-185">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="b5445-186">使用 [c # For Visual Studio Code 延伸](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)模組 **版本1.23.9 或更新** 版本時，請確認已安裝最新的 [ Blazor WASM 偵錯工具延伸](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.blazorwasm-companion)模組。</span><span class="sxs-lookup"><span data-stu-id="b5445-186">When using the [C# for Visual Studio Code Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) **version 1.23.9 or later**, confirm that the latest [Blazor WASM Debugging Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.blazorwasm-companion) is installed.</span></span> <span data-ttu-id="b5445-187">若要檢查已安裝的擴充功能，請從功能表列開啟 [**視圖**  >  **擴充** 功能]，或選取 [**活動**] 提要欄位中的 **延伸** 模組圖示。</span><span class="sxs-lookup"><span data-stu-id="b5445-187">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="b5445-188">確認已啟用 JavaScript preview 的偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="b5445-188">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="b5445-189">從功能表列開啟設定 **， (檔案**  >  **喜好**  >  **設定**) 。</span><span class="sxs-lookup"><span data-stu-id="b5445-189">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="b5445-190">使用關鍵字搜尋 `debug preview` 。</span><span class="sxs-lookup"><span data-stu-id="b5445-190">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="b5445-191">在搜尋結果中，設定或確認 **Debug > [JavaScript： Use Preview** ] 的核取方塊已核取。</span><span class="sxs-lookup"><span data-stu-id="b5445-191">In the search results, set or confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="b5445-192">如果不存在啟用預覽偵錯工具的選項，請升級至最新版的 VS Code 或安裝 [JavaScript 偵錯工具擴充](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 功能 (vs code 1.46 或更早版本的) 。</span><span class="sxs-lookup"><span data-stu-id="b5445-192">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="b5445-193">重載視窗。</span><span class="sxs-lookup"><span data-stu-id="b5445-193">Reload the window.</span></span>

1. <span data-ttu-id="b5445-194">使用 <kbd>F5</kbd> 鍵盤快速鍵或功能表項目來啟動調試。</span><span class="sxs-lookup"><span data-stu-id="b5445-194">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="b5445-195">不支援 (<kbd>Ctrl</kbd>F5) **啟動而不進行調試** + <kbd></kbd> 。</span><span class="sxs-lookup"><span data-stu-id="b5445-195">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="b5445-196">當應用程式在偵錯工具設定中執行時，偵錯工具的額外負荷一律會降低效能。</span><span class="sxs-lookup"><span data-stu-id="b5445-196">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="b5445-197">出現提示時，請選取 [ **Blazor WebAssembly Debug** ] 選項以開始進行調試。</span><span class="sxs-lookup"><span data-stu-id="b5445-197">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="b5445-198">獨立應用程式會啟動，並開啟偵錯工具瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="b5445-198">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="b5445-199">在 `*Client*` 應用程式中的行上設定中斷點 `currentCount++;` `Pages/Counter.razor` 。</span><span class="sxs-lookup"><span data-stu-id="b5445-199">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="b5445-200">在瀏覽器中，流覽至 `Counter` 頁面，然後選取 [按 **我** ] 按鈕以點擊中斷點。</span><span class="sxs-lookup"><span data-stu-id="b5445-200">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="b5445-201">在執行 debug proxy 之前， **不** 會在應用程式啟動期間叫用中斷點。</span><span class="sxs-lookup"><span data-stu-id="b5445-201">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="b5445-202">這包括 () 中的中斷點 `Program.Main` `Program.cs` ，以及應用程式所要求的第一個頁面所載入之元件[ `OnInitialized{Async}` 方法](xref:blazor/components/lifecycle#component-initialization-methods)中的中斷點。</span><span class="sxs-lookup"><span data-stu-id="b5445-202">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="b5445-203">主控的調試 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b5445-203">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="b5445-204">**`Client`** Blazor 在 VS Code 中開啟主控方案資料夾的專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="b5445-204">Open the **`Client`** project folder of the hosted Blazor solution folder in VS Code.</span></span>

1. <span data-ttu-id="b5445-205">如果未設定專案的啟動設定，則會出現下列通知。</span><span class="sxs-lookup"><span data-stu-id="b5445-205">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="b5445-206">選取 [是]  。</span><span class="sxs-lookup"><span data-stu-id="b5445-206">Select **Yes**.</span></span>

   > <span data-ttu-id="b5445-207">' {APPLICATION NAME} ' 中缺少建立和偵測所需的資產。</span><span class="sxs-lookup"><span data-stu-id="b5445-207">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="b5445-208">新增它們嗎？</span><span class="sxs-lookup"><span data-stu-id="b5445-208">Add them?</span></span>

   <span data-ttu-id="b5445-209">如需在資料夾中設定 VS Code 資產的相關資訊 `.vscode` ，請參閱中的 **Linux** 作業系統指引 <xref:blazor/tooling> 。</span><span class="sxs-lookup"><span data-stu-id="b5445-209">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="b5445-210">在視窗頂端的 [命令選擇區] 中，選取主控方案內的 *伺服器* 專案。</span><span class="sxs-lookup"><span data-stu-id="b5445-210">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="b5445-211">`launch.json`使用啟動設定來啟動偵錯工具時，會產生一個檔案。</span><span class="sxs-lookup"><span data-stu-id="b5445-211">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="b5445-212">附加至現有的偵錯工具會話</span><span class="sxs-lookup"><span data-stu-id="b5445-212">Attach to an existing debugging session</span></span>

<span data-ttu-id="b5445-213">若要附加至執行 Blazor 中的應用程式，請使用下列設定來建立檔案 `launch.json` ：</span><span class="sxs-lookup"><span data-stu-id="b5445-213">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="b5445-214">只有獨立應用程式才支援附加至偵錯工具會話。</span><span class="sxs-lookup"><span data-stu-id="b5445-214">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="b5445-215">若要使用完整堆疊的偵錯工具，您必須從 VS Code 啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5445-215">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="b5445-216">啟動設定選項</span><span class="sxs-lookup"><span data-stu-id="b5445-216">Launch configuration options</span></span>

<span data-ttu-id="b5445-217">下列啟動設定選項支援 `blazorwasm` debug 型別 (`.vscode/launch.json`) 。</span><span class="sxs-lookup"><span data-stu-id="b5445-217">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="b5445-218">選項</span><span class="sxs-lookup"><span data-stu-id="b5445-218">Option</span></span>    | <span data-ttu-id="b5445-219">描述</span><span class="sxs-lookup"><span data-stu-id="b5445-219">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="b5445-220">用 `launch` 來啟動偵錯工具，並將其附加至 Blazor WebAssembly 應用程式，或將偵測 `attach` 會話附加至已在執行中的應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5445-220">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="b5445-221">在瀏覽器中要在瀏覽器中開啟的 URL。</span><span class="sxs-lookup"><span data-stu-id="b5445-221">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="b5445-222">預設值為 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="b5445-222">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="b5445-223">要啟動偵錯工具的瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="b5445-223">The browser to launch for the debugging session.</span></span> <span data-ttu-id="b5445-224">設為 `edge` 或 `chrome`。</span><span class="sxs-lookup"><span data-stu-id="b5445-224">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="b5445-225">預設值為 `chrome`。</span><span class="sxs-lookup"><span data-stu-id="b5445-225">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="b5445-226">用來從 JS 偵錯工具產生記錄。</span><span class="sxs-lookup"><span data-stu-id="b5445-226">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="b5445-227">設定為 `true` 以產生記錄。</span><span class="sxs-lookup"><span data-stu-id="b5445-227">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="b5445-228">`true`如果啟動和偵測託管應用程式，則必須設定為 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="b5445-228">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="b5445-229">指定 web 伺服器的絕對路徑。</span><span class="sxs-lookup"><span data-stu-id="b5445-229">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="b5445-230">如果從子路由提供應用程式，則應該設定。</span><span class="sxs-lookup"><span data-stu-id="b5445-230">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="b5445-231">等候偵錯工具附加的毫秒數。</span><span class="sxs-lookup"><span data-stu-id="b5445-231">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="b5445-232">預設值為30000毫秒 (30 秒) 。</span><span class="sxs-lookup"><span data-stu-id="b5445-232">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="b5445-233">可執行檔的參考，用來執行裝載應用程式的伺服器。</span><span class="sxs-lookup"><span data-stu-id="b5445-233">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="b5445-234">如果為，則必須設定 `hosted` `true` 。</span><span class="sxs-lookup"><span data-stu-id="b5445-234">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="b5445-235">用來啟動應用程式的工作目錄。</span><span class="sxs-lookup"><span data-stu-id="b5445-235">The working directory to launch the app under.</span></span> <span data-ttu-id="b5445-236">如果為，則必須設定 `hosted` `true` 。</span><span class="sxs-lookup"><span data-stu-id="b5445-236">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="b5445-237">要提供給已啟動進程的環境變數。</span><span class="sxs-lookup"><span data-stu-id="b5445-237">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="b5445-238">只有 `hosted` 在設為時才適用 `true` 。</span><span class="sxs-lookup"><span data-stu-id="b5445-238">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="b5445-239">啟動設定範例</span><span class="sxs-lookup"><span data-stu-id="b5445-239">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="b5445-240">啟動獨立應用程式並進行調試 Blazor WebAssembly 程式</span><span class="sxs-lookup"><span data-stu-id="b5445-240">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="b5445-241">在指定的 URL 附加至執行中的應用程式</span><span class="sxs-lookup"><span data-stu-id="b5445-241">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-blazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="b5445-242">使用 Microsoft Edge 啟動及偵測託管 Blazor WebAssembly 應用程式</span><span class="sxs-lookup"><span data-stu-id="b5445-242">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="b5445-243">瀏覽器設定預設為 Google Chrome。</span><span class="sxs-lookup"><span data-stu-id="b5445-243">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="b5445-244">使用 Microsoft Edge 進行偵錯工具時，請將設定 `browser` 為 `edge` 。</span><span class="sxs-lookup"><span data-stu-id="b5445-244">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="b5445-245">若要使用 Google Chrome，請不要設定 `browser` 選項，或將選項的值設為 `chrome` 。</span><span class="sxs-lookup"><span data-stu-id="b5445-245">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

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

<span data-ttu-id="b5445-246">在上述範例中， `MyHostedApp.Server.dll` 是 *伺服器* 應用程式的元件。</span><span class="sxs-lookup"><span data-stu-id="b5445-246">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="b5445-247">此 `.vscode` 資料夾位於 `Client` 、和資料夾旁的方案資料夾中 `Server` `Shared` 。</span><span class="sxs-lookup"><span data-stu-id="b5445-247">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b5445-248">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b5445-248">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b5445-249">若要 Blazor WebAssembly 在 Visual Studio For Mac 中對應用程式進行偵錯工具：</span><span class="sxs-lookup"><span data-stu-id="b5445-249">To debug a Blazor WebAssembly app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="b5445-250">建立新的 ASP.NET Core 託管 Blazor WebAssembly 應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5445-250">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="b5445-251">按<kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> ，以在偵錯工具中執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5445-251">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="b5445-252">**啟動但不** (<kbd>&#8997;</kbd> + <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>) 不受支援。</span><span class="sxs-lookup"><span data-stu-id="b5445-252">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="b5445-253">當應用程式在偵錯工具設定中執行時，偵錯工具的額外負荷一律會降低效能。</span><span class="sxs-lookup"><span data-stu-id="b5445-253">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="b5445-254">Google Chrome 或 Microsoft Edge 必須是所選的瀏覽器，才能進行偵錯工具會話。</span><span class="sxs-lookup"><span data-stu-id="b5445-254">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="b5445-255">在 `*Client*` 應用程式中的行上設定中斷點 `currentCount++;` `Pages/Counter.razor` 。</span><span class="sxs-lookup"><span data-stu-id="b5445-255">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="b5445-256">在瀏覽器中，流覽至 `Counter` 頁面，然後選取 [按 **我** ] 按鈕以點擊中斷點：</span><span class="sxs-lookup"><span data-stu-id="b5445-256">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="b5445-257">在 Visual Studio 中，檢查 `currentCount` [ **區域變數** ] 視窗中的域值。</span><span class="sxs-lookup"><span data-stu-id="b5445-257">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="b5445-258">按<kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>以繼續執行。</span><span class="sxs-lookup"><span data-stu-id="b5445-258">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="b5445-259">在對 Blazor WebAssembly 應用程式進行偵錯工具時，您也可以將伺服器程式碼進行偵錯工具：</span><span class="sxs-lookup"><span data-stu-id="b5445-259">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="b5445-260">在的 `Pages/FetchData.razor` 頁面中，設定中斷點 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="b5445-260">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="b5445-261">在動作方法的中，設定中斷點 `WeatherForecastController` `Get` 。</span><span class="sxs-lookup"><span data-stu-id="b5445-261">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="b5445-262">流覽至該 `Fetch Data` 頁面，以叫用元件中的第一個中斷點， `FetchData` 再向伺服器發出 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="b5445-262">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="b5445-263">按<kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>繼續執行，然後在的伺服器上點擊中斷點 `WeatherForecastController` 。</span><span class="sxs-lookup"><span data-stu-id="b5445-263">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="b5445-264">再按一次<kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> ，讓執行繼續執行，並查看在瀏覽器中轉譯的氣象預報表格。</span><span class="sxs-lookup"><span data-stu-id="b5445-264">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="b5445-265">在執行 debug proxy 之前， **不** 會在應用程式啟動期間叫用中斷點。</span><span class="sxs-lookup"><span data-stu-id="b5445-265">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="b5445-266">這包括 () 中的中斷點 `Program.Main` `Program.cs` ，以及應用程式所要求的第一個頁面所載入之元件[ `OnInitialized{Async}` 方法](xref:blazor/components/lifecycle#component-initialization-methods)中的中斷點。</span><span class="sxs-lookup"><span data-stu-id="b5445-266">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="b5445-267">如需詳細資訊，請參閱 [使用 Visual Studio For Mac](/visualstudio/mac/debugging)進行的偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="b5445-267">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="b5445-268">瀏覽器中的 Debug</span><span class="sxs-lookup"><span data-stu-id="b5445-268">Debug in the browser</span></span>

<span data-ttu-id="b5445-269">*本節中的指導方針適用于在 Windows 上執行的 Google Chrome 和 Microsoft Edge。*</span><span class="sxs-lookup"><span data-stu-id="b5445-269">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="b5445-270">在開發環境中執行應用程式的偵錯工具組建。</span><span class="sxs-lookup"><span data-stu-id="b5445-270">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="b5445-271">啟動瀏覽器並流覽至應用程式的 URL (例如 `https://localhost:5001`) 。</span><span class="sxs-lookup"><span data-stu-id="b5445-271">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="b5445-272">在瀏覽器中，按<kbd>Shift</kbd> + <kbd>Alt</kbd> + <kbd>d</kbd>嘗試開始遠端偵錯。</span><span class="sxs-lookup"><span data-stu-id="b5445-272">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="b5445-273">瀏覽器必須在啟用遠端偵錯功能的情況下執行，這並不是預設值。</span><span class="sxs-lookup"><span data-stu-id="b5445-273">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="b5445-274">如果停用遠端偵測，則會轉譯 [找 **不到可調試的瀏覽器]** 索引標籤錯誤頁面，並提供在開啟偵錯工具時啟動瀏覽器的指示。</span><span class="sxs-lookup"><span data-stu-id="b5445-274">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="b5445-275">依照瀏覽器的指示操作，這會開啟新的瀏覽器視窗。</span><span class="sxs-lookup"><span data-stu-id="b5445-275">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="b5445-276">關閉先前的瀏覽器視窗。</span><span class="sxs-lookup"><span data-stu-id="b5445-276">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="b5445-277">當瀏覽器在啟用遠端偵錯程式的情況下執行時，在上一個步驟中的偵錯工具鍵盤快速鍵會開啟新的偵錯工具索引標籤。</span><span class="sxs-lookup"><span data-stu-id="b5445-277">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="b5445-278">經過一段時間後，[ **來源** ] 索引標籤會顯示節點內的應用程式 .net 元件清單 `file://` 。</span><span class="sxs-lookup"><span data-stu-id="b5445-278">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="b5445-279">在元件程式碼 (檔案 `.razor`) 和 c # 程式碼檔案 (`.cs`) 中，當程式碼執行時，就會叫用您設定的中斷點。</span><span class="sxs-lookup"><span data-stu-id="b5445-279">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="b5445-280">叫用中斷點之後，單一步驟 (<kbd>F10</kbd>) 透過程式碼或繼續 (<kbd>F8</kbd>) 程式碼執行正常執行。</span><span class="sxs-lookup"><span data-stu-id="b5445-280">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="b5445-281">Blazor 提供可執行 [Chrome DevTools 通訊協定](https://chromedevtools.github.io/devtools-protocol/) 的偵錯工具 proxy，並使用擴充通訊協定。特定的網路資訊。</span><span class="sxs-lookup"><span data-stu-id="b5445-281">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="b5445-282">當您按下 [偵錯工具] 鍵盤快速鍵時，請將 Blazor Chrome DevTools 指向 proxy。</span><span class="sxs-lookup"><span data-stu-id="b5445-282">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="b5445-283">Proxy 會連接到您想要進行調試的瀏覽器視窗， (因此需要啟用遠端偵錯) 。</span><span class="sxs-lookup"><span data-stu-id="b5445-283">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="b5445-284">瀏覽器來源對應</span><span class="sxs-lookup"><span data-stu-id="b5445-284">Browser source maps</span></span>

<span data-ttu-id="b5445-285">瀏覽器來源對應可讓瀏覽器將已編譯的檔案對應回原始來源檔案，且通常用於用戶端的偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="b5445-285">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="b5445-286">不過， Blazor 目前不會將 c # 直接對應至 JavaScript/WASM。</span><span class="sxs-lookup"><span data-stu-id="b5445-286">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="b5445-287">相反地， Blazor 會在瀏覽器中進行 IL 轉譯，因此來源對應不相關。</span><span class="sxs-lookup"><span data-stu-id="b5445-287">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="firewall-configuration"></a><span data-ttu-id="b5445-288">防火牆設定</span><span class="sxs-lookup"><span data-stu-id="b5445-288">Firewall configuration</span></span>

<span data-ttu-id="b5445-289">如果防火牆封鎖與 debug proxy 的通訊，請建立防火牆例外規則，以允許瀏覽器與處理常式之間的通訊 `NodeJS` 。</span><span class="sxs-lookup"><span data-stu-id="b5445-289">If a firewall blocks communication with the debug proxy, create a firewall exception rule that permits communication between the browser and the `NodeJS` process.</span></span>

> [!WARNING]
> <span data-ttu-id="b5445-290">您必須小心修改防火牆設定，以避免建立安全性 vulnerablities。</span><span class="sxs-lookup"><span data-stu-id="b5445-290">Modification of a firewall configuration must be made with care to avoid creating security vulnerablities.</span></span> <span data-ttu-id="b5445-291">請仔細套用安全性指導方針、遵循最佳安全性作法，以及尊重防火牆製造商發出的警告。</span><span class="sxs-lookup"><span data-stu-id="b5445-291">Carefully apply security guidance, follow best security practices, and respect warnings issued by the firewall's manufacturer.</span></span>
>
> <span data-ttu-id="b5445-292">允許與進程進行開放式通訊 `NodeJS` ：</span><span class="sxs-lookup"><span data-stu-id="b5445-292">Permitting open communication with the `NodeJS` process:</span></span>
>
> * <span data-ttu-id="b5445-293">會根據防火牆的功能和設定，將節點伺服器開啟至任何連接。</span><span class="sxs-lookup"><span data-stu-id="b5445-293">Opens up the Node server to any connection, depending on the firewall's capabilities and configuration.</span></span>
> * <span data-ttu-id="b5445-294">可能會有風險，視您的網路而定。</span><span class="sxs-lookup"><span data-stu-id="b5445-294">Might be risky depending on your network.</span></span>
> * <span data-ttu-id="b5445-295">**只有在開發人員電腦上才建議使用。**</span><span class="sxs-lookup"><span data-stu-id="b5445-295">**Is only recommended on developer machines.**</span></span>
>
> <span data-ttu-id="b5445-296">可能的話，只允許 `NodeJS` **在受信任或私人網路上** 的進程開啟通訊。</span><span class="sxs-lookup"><span data-stu-id="b5445-296">If possible, only allow open communication with the `NodeJS` process **on trusted or private networks**.</span></span>

<span data-ttu-id="b5445-297">如需 [Windows 防火牆](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) 設定的指引，請參閱 [建立輸入程式或服務規則](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule)。</span><span class="sxs-lookup"><span data-stu-id="b5445-297">For [Windows Firewall](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) configuration guidance, see [Create an Inbound Program or Service Rule](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span></span> <span data-ttu-id="b5445-298">如需詳細資訊，請參閱 Windows 防火牆檔集中的 [Windows Defender 防火牆與 Advanced Security](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) 和相關文章。</span><span class="sxs-lookup"><span data-stu-id="b5445-298">For more information, see [Windows Defender Firewall with Advanced Security](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) and related articles in the Windows Firewall documentation set.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b5445-299">疑難排解</span><span class="sxs-lookup"><span data-stu-id="b5445-299">Troubleshoot</span></span>

<span data-ttu-id="b5445-300">如果您遇到錯誤，下列秘訣可能會有説明：</span><span class="sxs-lookup"><span data-stu-id="b5445-300">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="b5445-301">在 [ **偵錯工具** ] 索引標籤中，開啟瀏覽器中的開發人員工具。</span><span class="sxs-lookup"><span data-stu-id="b5445-301">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="b5445-302">在主控台中，執行 `localStorage.clear()` 以移除任何中斷點。</span><span class="sxs-lookup"><span data-stu-id="b5445-302">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="b5445-303">確認您已安裝並信任 ASP.NET Core HTTPS 開發憑證。</span><span class="sxs-lookup"><span data-stu-id="b5445-303">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="b5445-304">如需詳細資訊，請參閱<xref:security/enforcing-ssl#troubleshoot-certificate-problems>。</span><span class="sxs-lookup"><span data-stu-id="b5445-304">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="b5445-305">Visual Studio 需要在 [**工具** 選項] 的 [一般] 選項中 **，啟用 ASP.NET (Chrome、Edge 和 IE) 選項的 JavaScript 偵錯工具**  >    >    >  \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b5445-305">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="b5445-306">這是 Visual Studio 的預設設定。</span><span class="sxs-lookup"><span data-stu-id="b5445-306">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="b5445-307">如果偵錯工具無法運作，請確認已選取該選項。</span><span class="sxs-lookup"><span data-stu-id="b5445-307">If debugging isn't working, confirm that the option is selected.</span></span>
* <span data-ttu-id="b5445-308">如果您的環境使用 HTTP proxy，請確定 `localhost` 已包含在 proxy 略過設定中。</span><span class="sxs-lookup"><span data-stu-id="b5445-308">If your environment uses an HTTP proxy, make sure that `localhost` is included in the proxy bypass settings.</span></span> <span data-ttu-id="b5445-309">您可以 `NO_PROXY` 在下列其中一項設定環境變數來完成此動作：</span><span class="sxs-lookup"><span data-stu-id="b5445-309">This can be done by setting the `NO_PROXY` environment variable in either:</span></span>
  * <span data-ttu-id="b5445-310">專案的檔案 `launchSettings.json` 。</span><span class="sxs-lookup"><span data-stu-id="b5445-310">The `launchSettings.json` file for the project.</span></span>
  * <span data-ttu-id="b5445-311">將其套用至所有應用程式的使用者或系統內容變數層級。</span><span class="sxs-lookup"><span data-stu-id="b5445-311">At the user or system environment variables level for it to apply to all apps.</span></span> <span data-ttu-id="b5445-312">使用環境變數時，請重新開機 Visual Studio，變更才會生效。</span><span class="sxs-lookup"><span data-stu-id="b5445-312">When using an environment variable, restart Visual Studio for the change to take effect.</span></span>
* <span data-ttu-id="b5445-313">確定防火牆或 proxy 不會封鎖與 debug proxy (`NodeJS` 進程) 的通訊。</span><span class="sxs-lookup"><span data-stu-id="b5445-313">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="b5445-314">如需詳細資訊，請參閱 [防火牆](#firewall-configuration) 設定一節。</span><span class="sxs-lookup"><span data-stu-id="b5445-314">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="b5445-315">`OnInitialized{Async}`未命中的中斷點</span><span class="sxs-lookup"><span data-stu-id="b5445-315">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="b5445-316">Blazor架構的偵錯工具 proxy 需要短暫的時間才能啟動，因此可能不會叫用[ `OnInitialized{Async}` 生命週期方法](xref:blazor/components/lifecycle#component-initialization-methods)中的中斷點。</span><span class="sxs-lookup"><span data-stu-id="b5445-316">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="b5445-317">建議您在方法主體的開頭加上延遲，以便在叫用中斷點之前，讓 debug proxy 有一些時間啟動。</span><span class="sxs-lookup"><span data-stu-id="b5445-317">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="b5445-318">您可以根據[ `if` 編譯器](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)指示詞加入延遲，以確保應用程式的發行組建不會有延遲。</span><span class="sxs-lookup"><span data-stu-id="b5445-318">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="b5445-319"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="b5445-319"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="b5445-320"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="b5445-320"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="b5445-321">Visual Studio (Windows) timeout</span><span class="sxs-lookup"><span data-stu-id="b5445-321">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="b5445-322">如果 Visual Studio 擲回偵測器無法啟動的例外狀況，表示已達到超時時間，您可以使用登錄設定來調整 timeout：</span><span class="sxs-lookup"><span data-stu-id="b5445-322">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="b5445-323">`{TIMEOUT}`上述命令中的預留位置以毫秒為單位。</span><span class="sxs-lookup"><span data-stu-id="b5445-323">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="b5445-324">例如，將一分鐘指派為 `60000` 。</span><span class="sxs-lookup"><span data-stu-id="b5445-324">For example, one minute is assigned as `60000`.</span></span>
