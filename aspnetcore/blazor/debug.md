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
ms.openlocfilehash: d4fd0d501ff14e37bb55b78bb6493ad43f9e5a87
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805566"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a>Debug ASP.NET Core Blazor WebAssembly

[Daniel Roth](https://github.com/danroth27)

Blazor WebAssembly 您可以使用以 Chromium 為基礎的瀏覽器中的瀏覽器開發工具來調試應用程式 (Edge/Chrome) 。 您也可以使用下列整合式開發環境 (Ide) 來對應用程式進行偵錯工具：

* Visual Studio
* Visual Studio for Mac
* Visual Studio Code

可用的案例包括：

* 設定和移除中斷點。
* 在 Ide 中使用偵錯工具支援來執行應用程式。
* 逐步執行程式碼。
* 使用 Ide 中的鍵盤快速鍵來繼續執行程式碼。
* 在 [ *區域變數* ] 視窗中，觀察本機變數的值。
* 請參閱呼叫堆疊，包括 JavaScript 和 .NET 之間的呼叫鏈。

目前，您 *不能*：

* 中斷未處理的例外狀況。
* 在應用程式啟動期間，在執行 debug proxy 之前叫用中斷點。 這包括 () 中的中斷點 `Program.Main` `Program.cs` ，以及應用程式所要求的第一個頁面所載入之元件[ `OnInitialized{Async}` 方法](xref:blazor/components/lifecycle#component-initialization-methods)中的中斷點。

## <a name="prerequisites"></a>必要條件

偵錯工具需要下列其中一個瀏覽器：

* Google Chrome (70 版或更新版本)  (預設) 
* Microsoft Edge (80 版或更新版本) 

Visual Studio for Mac 需要8.8 版 (組建 1532) 或更新版本：

1. 選取 Microsoft 上的 **下載 Visual Studio for Mac** 按鈕，以安裝最新版本的 Visual Studio for Mac [： Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。
1. 從 Visual Studio 中選取 *預覽* 頻道。 如需詳細資訊，請參閱 [安裝 Visual Studio for Mac 的預覽版本](/visualstudio/mac/install-preview)。

> [!NOTE]
> 目前不支援 macOS 上的 Apple Safari。

## <a name="enable-debugging"></a>啟用偵錯

若要啟用現有 Blazor WebAssembly 應用程式的偵錯工具，請更新 `launchSettings.json` 啟始專案中的檔案，以 `inspectUri` 在每個啟動設定檔中包含下列屬性：

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

更新之後，檔案 `launchSettings.json` 看起來應該會類似下列範例：

[!code-json[](debug/launchSettings.json?highlight=14,22)]

`inspectUri`屬性：

* 讓 IDE 偵測應用程式是否為 Blazor WebAssembly 應用程式。
* 指示腳本的偵錯工具，透過的偵錯工具 proxy 連接到瀏覽器 Blazor 。

在啟動的瀏覽器上，Websocket 通訊協定 (`wsProtocol`) 、主機 (`url.hostname`) 、埠 () 和偵測器 URI 的預留位置值， (`url.port` `browserInspectUri` 由架構提供。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

若要 Blazor WebAssembly 在 Visual Studio 中偵錯工具：

1. 建立新的 ASP.NET Core 託管 Blazor WebAssembly 應用程式。
1. 按 <kbd>F5</kbd> 以在偵錯工具中執行應用程式。

   > [!NOTE]
   > 不支援 (<kbd>Ctrl</kbd>F5) **啟動而不進行調試** + <kbd>F5</kbd> 。 當應用程式在偵錯工具設定中執行時，偵錯工具的額外負荷一律會降低效能。

1. 在 *用戶端* 應用程式中的行上，設定中斷點 `currentCount++;` `Pages/Counter.razor` 。
1. 在瀏覽器中，流覽至 `Counter` 頁面，然後選取 [按 **我** ] 按鈕以點擊中斷點。
1. 在 [Visual Studio] 中，檢查 [ `currentCount` **區域變數** ] 視窗中的域值。
1. 按 <kbd>F5</kbd> 繼續執行。

在對 Blazor WebAssembly 應用程式進行偵錯工具時，您也可以將伺服器程式碼進行偵錯工具：

1. 在的 `Pages/FetchData.razor` 頁面中，設定中斷點 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 。
1. 在動作方法的中，設定中斷點 `WeatherForecastController` `Get` 。
1. 流覽至該 `Fetch Data` 頁面，以叫用元件中的第一個中斷點， `FetchData` 再向伺服器發出 HTTP 要求。
1. 按 <kbd>F5</kbd> 繼續執行，然後在的伺服器上點擊中斷點 `WeatherForecastController` 。
1. 再按一次 <kbd>F5</kbd> ，讓繼續執行，並查看在瀏覽器中轉譯的氣象預報表格。

> [!NOTE]
> 在執行 debug proxy 之前， **不** 會在應用程式啟動期間叫用中斷點。 這包括 () 中的中斷點 `Program.Main` `Program.cs` ，以及應用程式所要求的第一個頁面所載入之元件[ `OnInitialized{Async}` 方法](xref:blazor/components/lifecycle#component-initialization-methods)中的中斷點。

如果應用程式裝載于不同的 [應用程式基底路徑](xref:blazor/host-and-deploy/index#app-base-path) `/` ，請更新中的下列屬性， `Properties/launchSettings.json` 以反映應用程式的基底路徑：

* `applicationUrl`:

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* `inspectUri` 每個設定檔：

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

上述設定中的預留位置：

* `{INSECURE PORT}`：不安全的埠。 預設會提供隨機值，但允許自訂埠。
* `{APP BASE PATH}`：應用程式的基底路徑。
* `{SECURE PORT}`：安全埠。 預設會提供隨機值，但允許自訂埠。
* `{PROFILE 1, 2, ... N}`：啟動設定設定檔。 通常，應用程式預設會指定多個設定檔 (例如，IIS Express 的設定檔以及 Kestrel 伺服器) 所使用的專案設定檔。

在下列範例中，應用程式是 `/OAT` 使用中設定的應用程式基底路徑 `wwwroot/index.html` 來託管 `<base href="/OAT/">` ：

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

如需使用應用程式的自訂應用程式基底路徑的詳細資訊 Blazor WebAssembly ，請參閱 <xref:blazor/host-and-deploy/index#app-base-path> 。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<h2 id="vscode">獨立調試 Blazor WebAssembly</h2>

1. Blazor WebAssembly在 VS Code 中開啟獨立應用程式。

   您可能會收到通知，指出需要額外的設定才能啟用偵錯工具：

   > 需要額外的設定，才能進行 Blazor WebAssembly 應用程式的偵錯工具。

   如果您收到通知：

   * 確認已安裝 [適用于 Visual Studio Code 擴充](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) 功能的最新 c #。 若要檢查已安裝的擴充功能，請從功能表列開啟 [**視圖**  >  **擴充**功能]，或選取 [**活動**] 提要欄位中的**延伸**模組圖示。
   * 確認已啟用 JavaScript preview 的偵錯工具。 從功能表列開啟設定 **， (檔案**  >  **喜好**  >  **設定**) 。 使用關鍵字搜尋 `debug preview` 。 在搜尋結果中，確認已核取 [ **Debug > JavaScript： Use Preview** ] 的核取方塊。 如果不存在啟用預覽偵錯工具的選項，請升級至最新版的 VS Code 或安裝 [JavaScript 偵錯工具擴充](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 功能 (VS Code 1.46 版或更早版本的) 。
   * 重載視窗。

1. 使用 <kbd>F5</kbd> 鍵盤快速鍵或功能表項目來啟動調試。

   > [!NOTE]
   > 不支援 (<kbd>Ctrl</kbd>F5) **啟動而不進行調試** + <kbd>F5</kbd> 。 當應用程式在偵錯工具設定中執行時，偵錯工具的額外負荷一律會降低效能。

1. 出現提示時，請選取 [ ** Blazor WebAssembly Debug** ] 選項以開始進行調試。

1. 獨立應用程式會啟動，並開啟偵錯工具瀏覽器。

1. 在 *用戶端* 應用程式中的行上，設定中斷點 `currentCount++;` `Pages/Counter.razor` 。

1. 在瀏覽器中，流覽至 `Counter` 頁面，然後選取 [按 **我** ] 按鈕以點擊中斷點。

> [!NOTE]
> 在執行 debug proxy 之前， **不** 會在應用程式啟動期間叫用中斷點。 這包括 () 中的中斷點 `Program.Main` `Program.cs` ，以及應用程式所要求的第一個頁面所載入之元件[ `OnInitialized{Async}` 方法](xref:blazor/components/lifecycle#component-initialization-methods)中的中斷點。

## <a name="debug-hosted-no-locblazor-webassembly"></a>主控的調試 Blazor WebAssembly

1. Blazor WebAssembly在 VS Code 中開啟託管應用程式的方案資料夾。

1. 如果未設定專案的啟動設定，則會出現下列通知。 選取 [是]。

   > ' {APPLICATION NAME} ' 中缺少建立和偵測所需的資產。 新增它們嗎？

1. 在視窗頂端的 [命令選擇區] 中，選取主控方案內的 *伺服器* 專案。

`launch.json`使用啟動設定來啟動偵錯工具時，會產生一個檔案。

## <a name="attach-to-an-existing-debugging-session"></a>附加至現有的偵錯工具會話

若要附加至執行 Blazor 中的應用程式，請使用下列設定來建立檔案 `launch.json` ：

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> 只有獨立應用程式才支援附加至偵錯工具會話。 若要使用完整堆疊的偵錯工具，您必須從 VS Code 啟動應用程式。

## <a name="launch-configuration-options"></a>啟動設定選項

下列啟動設定選項支援 `blazorwasm` debug 型別 (`.vscode/launch.json`) 。

| 選項    | 描述 |
| --------- | ----------- |
| `request` | 用 `launch` 來啟動偵錯工具，並將其附加至 Blazor WebAssembly 應用程式，或將偵測 `attach` 會話附加至已在執行中的應用程式。 |
| `url`     | 在瀏覽器中要在瀏覽器中開啟的 URL。 預設值為 `https://localhost:5001`。 |
| `browser` | 要啟動偵錯工具的瀏覽器。 設為 `edge` 或 `chrome`。 預設值為 `chrome`。 |
| `trace`   | 用來從 JS 偵錯工具產生記錄。 設定為 `true` 以產生記錄。 |
| `hosted`  | `true`如果啟動和偵測託管應用程式，則必須設定為 Blazor WebAssembly 。 |
| `webRoot` | 指定 web 伺服器的絕對路徑。 如果從子路由提供應用程式，則應該設定。 |
| `timeout` | 等候偵錯工具附加的毫秒數。 預設值為30000毫秒 (30 秒) 。 |
| `program` | 可執行檔的參考，用來執行裝載應用程式的伺服器。 如果為，則必須設定 `hosted` `true` 。 |
| `cwd`     | 用來啟動應用程式的工作目錄。 如果為，則必須設定 `hosted` `true` 。 |
| `env`     | 要提供給已啟動進程的環境變數。 只有 `hosted` 在設為時才適用 `true` 。 |

## <a name="example-launch-configurations"></a>啟動設定範例

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a>啟動獨立應用程式並進行調試 Blazor WebAssembly 程式

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a>在指定的 URL 附加至執行中的應用程式

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a>使用 Microsoft Edge 啟動及偵測託管 Blazor WebAssembly 應用程式

瀏覽器設定預設為 Google Chrome。 使用 Microsoft Edge 進行偵錯工具時，請將設定 `browser` 為 `edge` 。 若要使用 Google Chrome，請不要設定 `browser` 選項，或將選項的值設為 `chrome` 。

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

在上述範例中， `MyHostedApp.Server.dll` 是 *伺服器* 應用程式的元件。 此 `.vscode` 資料夾位於 `Client` 、和資料夾旁的方案資料夾中 `Server` `Shared` 。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

若要 Blazor WebAssembly 在 Visual Studio for Mac 中偵錯工具：

1. 建立新的 ASP.NET Core 託管 Blazor WebAssembly 應用程式。
1. 按<kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> ，以在偵錯工具中執行應用程式。

   > [!NOTE]
   > **啟動但不** (<kbd>&#8997;</kbd> + <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>) 不受支援。 當應用程式在偵錯工具設定中執行時，偵錯工具的額外負荷一律會降低效能。

   > [!IMPORTANT]
   > Google Chrome 或 Microsoft Edge 必須是所選的瀏覽器，才能進行偵錯工具會話。

1. 在 *用戶端* 應用程式中的行上，設定中斷點 `currentCount++;` `Pages/Counter.razor` 。
1. 在瀏覽器中，流覽至 `Counter` 頁面，然後選取 [按 **我** ] 按鈕以點擊中斷點：
1. 在 [Visual Studio] 中，檢查 [ `currentCount` **區域變數** ] 視窗中的域值。
1. 按<kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>以繼續執行。

在對 Blazor WebAssembly 應用程式進行偵錯工具時，您也可以將伺服器程式碼進行偵錯工具：

1. 在的 `Pages/FetchData.razor` 頁面中，設定中斷點 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 。
1. 在動作方法的中，設定中斷點 `WeatherForecastController` `Get` 。
1. 流覽至該 `Fetch Data` 頁面，以叫用元件中的第一個中斷點， `FetchData` 再向伺服器發出 HTTP 要求。
1. 按<kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>繼續執行，然後在的伺服器上點擊中斷點 `WeatherForecastController` 。
1. 再按一次<kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> ，讓執行繼續執行，並查看在瀏覽器中轉譯的氣象預報表格。

> [!NOTE]
> 在執行 debug proxy 之前， **不** 會在應用程式啟動期間叫用中斷點。 這包括 () 中的中斷點 `Program.Main` `Program.cs` ，以及應用程式所要求的第一個頁面所載入之元件[ `OnInitialized{Async}` 方法](xref:blazor/components/lifecycle#component-initialization-methods)中的中斷點。

如需詳細資訊，請參閱 [使用 Visual Studio for Mac 的調試](/visualstudio/mac/debugging)程式。

---

## <a name="debug-in-the-browser"></a>瀏覽器中的 Debug

*本節中的指導方針適用于 Google Chrome 以及在 Windows 上執行的 Microsoft Edge。*

1. 在開發環境中執行應用程式的偵錯工具組建。

1. 啟動瀏覽器並流覽至應用程式的 URL (例如 `https://localhost:5001`) 。

1. 在瀏覽器中，按<kbd>Shift</kbd> + <kbd>Alt</kbd> + <kbd>d</kbd>嘗試開始遠端偵錯。

   瀏覽器必須在啟用遠端偵錯功能的情況下執行，這並不是預設值。 如果停用遠端偵測，則會轉譯 [找 **不到可調試的瀏覽器]** 索引標籤錯誤頁面，並提供在開啟偵錯工具時啟動瀏覽器的指示。 依照瀏覽器的指示操作，這會開啟新的瀏覽器視窗。 關閉先前的瀏覽器視窗。

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. 當瀏覽器在啟用遠端偵錯程式的情況下執行時，在上一個步驟中的偵錯工具鍵盤快速鍵會開啟新的偵錯工具索引標籤。

1. 經過一段時間後，[ **來源** ] 索引標籤會顯示節點內的應用程式 .net 元件清單 `file://` 。

1. 在元件程式碼 (檔案 `.razor`) 和 c # 程式碼檔案 (`.cs`) 中，當程式碼執行時，就會叫用您設定的中斷點。 叫用中斷點之後，單一步驟 (<kbd>F10</kbd>) 透過程式碼或繼續 (<kbd>F8</kbd>) 程式碼執行正常執行。

Blazor 提供可執行 [Chrome DevTools 通訊協定](https://chromedevtools.github.io/devtools-protocol/) 的偵錯工具 proxy，並使用擴充通訊協定。特定的網路資訊。 當您按下 [偵錯工具] 鍵盤快速鍵時，請將 Blazor Chrome DevTools 指向 proxy。 Proxy 會連接到您想要進行調試的瀏覽器視窗， (因此需要啟用遠端偵錯) 。

## <a name="browser-source-maps"></a>瀏覽器來源對應

瀏覽器來源對應可讓瀏覽器將已編譯的檔案對應回原始來源檔案，且通常用於用戶端的偵錯工具。 不過， Blazor 目前不會將 c # 直接對應至 JavaScript/WASM。 相反地， Blazor 會在瀏覽器中進行 IL 轉譯，因此來源對應不相關。

## <a name="troubleshoot"></a>疑難排解

如果您遇到錯誤，下列秘訣可能會有説明：

* 在 [ **偵錯工具** ] 索引標籤中，開啟瀏覽器中的開發人員工具。 在主控台中，執行 `localStorage.clear()` 以移除任何中斷點。
* 確認您已安裝並信任 ASP.NET Core HTTPS 開發憑證。 如需詳細資訊，請參閱 <xref:security/enforcing-ssl#troubleshoot-certificate-problems> 。
* Visual Studio 需要在 [**工具**選項] 的 [一般] 選項中 **，啟用 ASP.NET (Chrome、Edge 和 IE) 選項的 JavaScript 偵錯工具**  >  **Options**  >  **Debugging**  >  ** **。 這是 Visual Studio 的預設設定。 如果偵錯工具無法運作，請確認已選取該選項。
* 如果您的環境使用 HTTP proxy，請確定 `localhost` 已包含在 proxy 略過設定中。 您可以 `NO_PROXY` 在下列其中一項設定環境變數來完成此動作：
  * 專案的檔案 `launchSettings.json` 。
  * 將其套用至所有應用程式的使用者或系統內容變數層級。 使用環境變數時，請重新開機 Visual Studio，變更才會生效。

### <a name="breakpoints-in-oninitializedasync-not-hit"></a>`OnInitialized{Async}`未命中的中斷點

Blazor架構的偵錯工具 proxy 需要短暫的時間才能啟動，因此可能不會叫用[ `OnInitialized{Async}` 生命週期方法](xref:blazor/components/lifecycle#component-initialization-methods)中的中斷點。 建議您在方法主體的開頭加上延遲，以便在叫用中斷點之前，讓 debug proxy 有一些時間啟動。 您可以根據[ `if` 編譯器](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)指示詞加入延遲，以確保應用程式的發行組建不會有延遲。

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a>Visual Studio (Windows) timeout

如果 Visual Studio 擲回偵測器無法啟動的例外狀況，表示已達到超時時間，您可以使用登錄設定來調整 timeout：

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

`{TIMEOUT}`上述命令中的預留位置以毫秒為單位。 例如，將一分鐘指派為 `60000` 。
