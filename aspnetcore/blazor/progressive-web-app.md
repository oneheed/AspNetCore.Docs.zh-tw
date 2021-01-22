---
title: 使用 ASP.NET Core 建立漸進式 Web 應用程式 Blazor WebAssembly
author: guardrex
description: 瞭解如何建立 Blazor (PWA) 的漸進式 Web 應用程式，該應用程式使用新式瀏覽器功能的行為就像桌面應用程式一樣。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/11/2020
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
uid: blazor/progressive-web-app
ms.openlocfilehash: 1706d3502dc68f1c25e0c35ba8f5dd44b55ce690
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658647"
---
# <a name="build-progressive-web-applications-with-aspnet-core-no-locblazor-webassembly"></a>使用 ASP.NET Core 建立漸進式 Web 應用程式 Blazor WebAssembly

作者：[Steve Sanderson](https://github.com/SteveSandersonMS)

 (PWA) 的漸進式 Web 應用程式通常是使用新式瀏覽器 Api 和功能的單一頁面應用)  (程式，其行為就像桌面應用程式一樣。 Blazor WebAssembly 是以標準為基礎的用戶端 web 應用程式平臺，因此它可以使用任何瀏覽器 API，包括下列功能所需的 PWA Api：

* 離線工作並立即載入，與網路速度無關。
* 在它自己的應用程式視窗中執行，而不只是瀏覽器視窗。
* 從主機的作業系統 [開始] 功能表、[dock] 或 [主畫面] 啟動。
* 即使使用者未使用應用程式，也會從後端伺服器接收推播通知。
* 在背景中自動更新。

「 *漸進式* 」（word）是用來描述這類應用程式，因為：

* 使用者可能會先在其網頁瀏覽器中探索及使用應用程式，就像任何其他 SPA 一樣。
* 之後，使用者會在作業系統中進行安裝，並啟用推播通知。

## <a name="create-a-project-from-the-pwa-template"></a>從 PWA 範本建立專案

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在 [**建立新專案**] 對話方塊中建立新的 **Blazor WebAssembly 應用** 程式時，選取 [**漸進式 Web 應用程式**] 核取方塊：

![在 Visual Studio [新增專案] 對話方塊中，已選取 [漸進式 Web 應用程式] 核取方塊。](progressive-web-app/_static/image1.png)

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

-->

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code / .NET Core CLI](#tab/visual-studio-code+netcore-cli)

使用下列命令，在命令列中使用參數建立 PWA 專案 `--pwa` ：

```dotnetcli
dotnet new blazorwasm -o MyBlazorPwa --pwa
```

在上述命令中，選項會為 `-o|--output` 應用程式建立名為的新資料夾 `MyBlazorPwa` 。

---

（選擇性）您可以針對從 ASP.NET Core 裝載的範本建立的應用程式設定 PWA。 PWA 案例與裝載模型無關。

## <a name="convert-an-existing-no-locblazor-webassembly-app-into-a-pwa"></a>將現有的 Blazor WebAssembly 應用程式轉換為 PWA

Blazor WebAssembly遵循本節中的指導方針，將現有的應用程式轉換為 PWA。

在應用程式的專案檔中：

* 將下列 `ServiceWorkerAssetsManifest` 屬性新增至 `PropertyGroup` ：

  ```xml
    ...
    <ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
  </PropertyGroup>
   ```

* 將下列 `ServiceWorker` 專案加入至 `ItemGroup` ：

  ```xml
  <ItemGroup>
    <ServiceWorker Include="wwwroot\service-worker.js" 
      PublishedContent="wwwroot\service-worker.published.js" />
  </ItemGroup>
  ```

若要取得靜態資產，請使用下列 **其中一** 種方法：

::: moniker range=">= aspnetcore-5.0"

* 使用命令 shell 中的命令，建立個別的新 PWA 專案 [`dotnet new`](/dotnet/core/tools/dotnet-new) ：

  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa
  ```
  
  在上述命令中，選項會為 `-o|--output` 應用程式建立名為的新資料夾 `MyBlazorPwa` 。
  
  如果您未將應用程式轉換為最新版本，請傳遞 `-f|--framework` 選項。 下列範例會建立適用于 ASP.NET Core 3.1 版的應用程式：
  
  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa -f netcoreapp3.1
  ```

* 流覽至位於下列 URL 的 ASP.NET Core GitHub 存放庫，其連結至5.0 版本參考來源和資產。 如果您未轉換5.0 版的應用程式，請從適用于您應用程式的 [ **切換分支或標記** ] 下拉式清單中，選取您正在使用的版本。

  [dotnet/aspnetcore (版本 5.0) Blazor WebAssembly 專案範本 `wwwroot` 資料夾](https://github.com/dotnet/aspnetcore/tree/release/5.0/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* 使用命令 shell 中的命令，建立個別的新 PWA 專案 [`dotnet new`](/dotnet/core/tools/dotnet-new) 。 傳遞 `-f|--framework` 選項以選取版本。 下列範例會建立適用于 ASP.NET Core 3.1 版的應用程式：
  
  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa -f netcoreapp3.1
  ```
  
  在上述命令中，選項會為 `-o|--output` 應用程式建立名為的新資料夾 `MyBlazorPwa` 。

* 流覽至位於下列 URL 的 ASP.NET Core GitHub 存放庫，其連結至3.1 版本參考來源和資產：

  [dotnet/aspnetcore (版本 3.1) Blazor WebAssembly 專案範本 `wwwroot` 資料夾](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/ProjectTemplates/ComponentsWebAssembly.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)

  > [!NOTE]
  > 專案範本的 URL 會在 Blazor WebAssembly 發行 ASP.NET Core 3.1 之後變更。 您可以從下列 URL 取得5.0 或更新版本的參考資產：
  >
  > [dotnet/aspnetcore (版本 5.0) Blazor WebAssembly 專案範本 `wwwroot` 資料夾](https://github.com/dotnet/aspnetcore/tree/release/5.0/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)

::: moniker-end

從 `wwwroot` 您建立的應用程式中或從 GitHub 存放庫中的參考資產的源資料夾 `dotnet/aspnetcore` ，將下列檔案複製到應用程式的 `wwwroot` 資料夾中：

* `icon-512.png`
* `manifest.json`
* `service-worker.js`
* `service-worker.published.js`

在應用程式的檔案中 `wwwroot/index.html` ：

* 新增 `<link>` 資訊清單和應用程式圖示的元素：

  ```html
  <link href="manifest.json" rel="manifest" />
  <link rel="apple-touch-icon" sizes="512x512" href="icon-512.png" />
  ```

* 在 `<script>` `</body>` 腳本標記後面緊接著結束記號內新增下列標記 `blazor.webassembly.js` ：

  ```html
      ...
      <script>navigator.serviceWorker.register('service-worker.js');</script>
  </body>
  ```

## <a name="installation-and-app-manifest"></a>安裝和應用程式資訊清單

造訪使用 PWA 範本建立的應用程式時，使用者可以選擇將應用程式安裝到其作業系統的 [開始] 功能表、[停駐] 或 [主畫面] 中。 此選項的呈現方式取決於使用者的瀏覽器。 使用以桌面 Chromium 為基礎的瀏覽器（例如 Edge 或 Chrome）時，[ **新增** ] 按鈕會出現在 URL 列內。 當使用者選取 [ **新增** ] 按鈕之後，他們會收到確認對話方塊：

![Google Chrome 中的確認對話方塊會向使用者顯示 [My：：： no- (Blazor) ：：:P wa] 應用程式的 [安裝] 按鈕。](progressive-web-app/_static/image2.png)

在 iOS 上，訪客可以使用 Safari 的 [ **共用** ] 按鈕和 [ **新增至 Homescreen** ] 選項來安裝 PWA。 在適用于 Android 的 Chrome 上，使用者應選取右上角的 **功能表** 按鈕，然後選取 [ **新增至主畫面**]。

一旦安裝之後，應用程式就會出現在其本身的視窗中，而不使用網址列：

![' My：：：非 loc (Blazor) ：：:P wa ' 應用程式會在 Google Chrome 中執行，而不需使用網址列。](progressive-web-app/_static/image3.png)

若要自訂視窗的標題、色彩配置、圖示或其他詳細資料，請參閱 `manifest.json` 專案目錄中的檔案 `wwwroot` 。 這個檔案的架構是由 web 標準所定義。 如需詳細資訊，請參閱 [MDN web 檔： Web 應用程式資訊清單](https://developer.mozilla.org/docs/Web/Manifest)。

## <a name="offline-support"></a>離線支援

依預設，使用 PWA 範本選項建立的應用程式支援離線執行。 使用者在線上時，必須先造訪應用程式。 瀏覽器會自動下載並快取離線運作所需的所有資源。

> [!IMPORTANT]
> 開發支援會干擾進行變更和測試的一般開發週期。 因此，只會針對 *已發佈* 的應用程式啟用離線支援。 

> [!WARNING]
> 如果您想要散發啟用離線的 PWA，有 [幾個重要的警告和](#caveats-for-offline-pwas)警告。 這些案例都是離線 Pwa 固有的，而不是特定的 Blazor 。 在對啟用離線的應用程式運作方式進行假設之前，請務必閱讀並瞭解這些注意事項。

若要查看離線支援的運作方式：

1. 發行應用程式。 如需詳細資訊，請參閱<xref:blazor/host-and-deploy/index#publish-the-app>。
1. 將應用程式部署至支援 HTTPS 的伺服器，並在瀏覽器中以其安全的 HTTPS 位址存取應用程式。
1. 開啟瀏覽器的開發工具，並確認已在 [**應用程式**] 索引標籤上註冊主機的 *服務工作者*：

   ![Google Chrome 開發人員工具的 [應用程式] 索引標籤會顯示已啟動且正在執行的服務工作者。](progressive-web-app/_static/image4.png)

1. 重載頁面，並檢查 [ **網路** ] 索引標籤。系統會將 **服務背景工作** 或 **記憶體** 快取列為所有頁面資產的來源：

   ![Google Chrome 開發人員工具 [網路] 索引標籤，顯示所有頁面資產的來源。](progressive-web-app/_static/image5.png)

1. 若要確認瀏覽器不相依于網路存取以載入應用程式，請執行下列其中一項：

   * 關閉網頁伺服器，並查看應用程式繼續正常運作的方式，包括頁面重載。 同樣地，當網路連線速度很慢時，應用程式會繼續正常運作。
   * 指示瀏覽器模擬 [ **網路** ] 索引標籤中的離線模式：

   ![[Google Chrome 開發人員工具] 的 [網路] 索引標籤，其中的 [瀏覽器模式] 下拉式清單從 [線上] 變更為 [離線]。](progressive-web-app/_static/image6.png)

使用服務工作者的離線支援是 web 標準，而非特定的 Blazor 。 如需服務工作者的詳細資訊，請參閱 [MDN web 檔：服務工作者 API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API)。 若要深入瞭解服務工作者的常見使用模式，請參閱 [Google Web：服務工作者生命週期](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle)。

Blazor的 PWA 範本會產生兩個服務工作者檔案：

* `wwwroot/service-worker.js`，在開發期間使用。
* `wwwroot/service-worker.published.js`，會在應用程式發行之後使用。

若要在兩個服務工作者檔案之間共用邏輯，請考慮下列方法：

* 新增第三個 JavaScript 檔案以保存常見的邏輯。
* 用 [`self.importScripts`](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) 來將通用邏輯載入兩個服務工作者檔案中。

### <a name="cache-first-fetch-strategy"></a>快取優先提取策略

內建 `service-worker.published.js` 服務工作者會使用快取 *優先* 策略來解析要求。 這表示服務工作者偏好傳回快取的內容，不論使用者是否可在伺服器上使用網路存取或更新的內容。

快取優先策略很重要，因為：

* **它可確保可靠性。** 網路存取不是布林狀態。 使用者不只是線上或離線：

  * 使用者的裝置可能會假設它已上線，但網路可能會太慢而無法等候。
  * 網路可能會針對某些 Url 傳回不正確結果，例如，當有一個已封鎖的 WIFI 入口網站正在封鎖或重新導向特定要求時。
  
  這就是為什麼瀏覽器的 `navigator.onLine` API 不可靠，也不應該相依的原因。

* **它可確保正確性。** 當您建立離線資源的快取時，服務背景工作會使用內容雜湊來確保它已在單一時刻取得完整且自我一致的資源快照集。 然後，此快取會用來做為不可部分完成的單位。 不需要要求網路提供較新的資源，因為唯一需要的版本是已快取的版本。 任何其他風險會造成不一致和不相容 (例如，嘗試使用未一起編譯的 .NET 元件版本) 。

### <a name="background-updates"></a>背景更新

作為精神模型，您可以將離線優先的 PWA 視為可以安裝的行動應用程式。 無論網路連線為何，應用程式都會立即啟動，但已安裝的應用程式邏輯來自可能不是最新版本的時間點快照集。

BlazorPWA 範本會產生應用程式，每當使用者造訪並具有運作中的網路連線時，就會自動嘗試在背景中自行更新。 其運作方式如下所示：

* 在編譯期間，專案會產生 *服務工作者資產資訊清單*。 根據預設，會呼叫此方法 `service-worker-assets.js` 。 資訊清單會列出應用程式離線運作所需的所有靜態資源，例如 .NET 元件、JavaScript 檔案和 CSS，包括其內容雜湊。 資源清單是由服務工作者載入，因此它知道要快取哪些資源。
* 每次使用者造訪應用程式時，瀏覽器會 `service-worker.js` `service-worker-assets.js` 在背景中重新要求和。 這些檔案會與現有已安裝的服務工作者進行位元組的位元組比較。 如果伺服器傳回任何這些檔案的變更內容，服務工作者會嘗試安裝新版的。
* 當您安裝新的版本時，服務背景工作會為離線資源建立新的個別快取，並開始使用中所列的資源來填入快取 `service-worker-assets.js` 。 此邏輯會在內部的函式中執行 `onInstall` `service-worker.published.js` 。
* 當所有資源都已載入且沒有錯誤，且所有內容雜湊相符時，此程式就會順利完成。 如果成功，新的服務工作者會進入 *等待啟用* 狀態。 一旦使用者關閉應用程式 (沒有剩餘的應用程式索引標籤或 windows) ，新的服務工作者就會變成使用中 *狀態* ，並用於後續的應用程式造訪。 舊的服務工作者及其快取會被刪除。
* 如果程式未順利完成，則會捨棄新的服務背景工作實例。 當您希望用戶端有更好的網路連線可以完成要求時，會在使用者下一次造訪時再次嘗試更新程式。

藉由編輯服務背景工作邏輯來自訂此流程。 上述行為都不是特定的， Blazor 但只是 PWA 範本選項所提供的預設體驗。 如需詳細資訊，請參閱 [MDN web 檔：服務工作者 API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API)。

### <a name="how-requests-are-resolved"></a>要求的解析方式

如「快 [取優先提取策略](#cache-first-fetch-strategy) 」一節所述，預設服務背景工作會使用快取 *優先* 策略，這表示它會嘗試提供快取的內容（如果有的話）。 如果未針對特定 URL 快取任何內容，例如從後端 API 要求資料時，服務背景工作會回復一般網路要求。 如果可連線到伺服器，則網路要求會成功。 此邏輯會在的函式內實作為 `onFetch` `service-worker.published.js` 。

如果應用程式的 Razor 元件需要從後端 api 要求資料，而您想要為失敗的要求提供易記的使用者體驗（因為網路無法使用），請在應用程式的元件中執行邏輯。 例如，使用 `try/catch` 圍繞 <xref:System.Net.Http.HttpClient> 要求。

### <a name="support-server-rendered-pages"></a>支援伺服器呈現的頁面

考慮當使用者第一次流覽至 URL，例如 `/counter` 或應用程式中的任何其他深層連結時，會發生什麼事。 在這些情況下，您不會想要傳回快取的內容 `/counter` ，而是需要瀏覽器載入快取的內容， `/index.html` 以啟動您的 Blazor WebAssembly 應用程式。 這些初始要求稱為 *流覽* 要求，而不是：

* `subresource` 影像、樣式表單或其他檔案的要求。
* `fetch/XHR` API 資料的要求。

預設服務背景工作包含導覽要求的特殊案例邏輯。 無論要求的 URL 為何，服務工作者都會藉由傳回的快取內容來解析要求 `/index.html` 。 此邏輯會在內部的函式中執行 `onFetch` `service-worker.published.js` 。

如果您的應用程式有某些 Url 必須傳回伺服器轉譯的 HTML，而不是從快取提供服務 `/index.html` ，則您需要編輯服務背景工作角色中的邏輯。 如果所有包含的 Url 都 `/Identity/` 需要以一般僅線上要求的方式處理到伺服器，則修改 `service-worker.published.js` `onFetch` 邏輯。 找出下列程式碼：

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

將程式碼變更為下列程式碼：

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
  && !event.request.url.includes('/Identity/');
```

如果您沒有這麼做，則無論網路連線為何，服務工作者都會攔截這類 Url 的要求，並使用加以解析 `/index.html` 。

將外部驗證提供者的其他端點新增至檢查。 在下列範例中， `/signin-google` 會在檢查中新增 Google 驗證：

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
  && !event.request.url.includes('/Identity/')
  && !event.request.url.includes('/signin-google');
```

開發環境不需要採取任何動作，因為一律會從網路提取內容。

### <a name="control-asset-caching"></a>控制資產快取

如果您的專案定義 `ServiceWorkerAssetsManifest` MSBuild 屬性， Blazor 的組建工具會產生具有指定名稱的服務工作者資產資訊清單。 預設 PWA 範本會產生包含下列屬性的專案檔：

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

檔案會放在 `wwwroot` 輸出目錄中，因此瀏覽器可以要求取得此檔案 `/service-worker-assets.js` 。 若要查看這個檔案的內容，請 `/bin/Debug/{TARGET FRAMEWORK}/wwwroot/service-worker-assets.js` 在文字編輯器中開啟。 不過，請勿編輯檔案，因為它會在每個組建上重新產生。

依預設，此資訊清單會列出：

* 任何 Blazor 受管理的資源，例如 .net 元件，以及離線運作所需的 .Net WebAssembly 執行時間檔案。
* 用於發佈至應用程式目錄的所有資源 `wwwroot` ，例如影像、樣式表單和 JavaScript 檔案，包括外部專案和 NuGet 套件所提供的靜態 web 資產。

您可以藉由編輯中的邏輯，來控制服務工作者要提取和快取哪些資源 `onInstall` `service-worker.published.js` 。 根據預設，服務背景工作會提取和快取符合一般 web 副檔名的檔案，例如 `.html` 、、 `.css` `.js` 和 `.wasm` ，以及 Blazor WebAssembly (、) 特有的檔案類型 `.dll` `.pdb` 。

若要包含應用程式目錄中不存在的其他資源 `wwwroot` ，請定義額外的 MSBuild `ItemGroup` 專案，如下列範例所示：

```xml
<ItemGroup>
  <ServiceWorkerAssetsManifestItem Include="MyDirectory\AnotherFile.json"
    RelativePath="MyDirectory\AnotherFile.json" AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

`AssetUrl`中繼資料會指定瀏覽器在提取要快取的資源時，應使用的基底相對 URL。 這可以獨立于其在磁片上的原始來原始檔案名。

> [!IMPORTANT]
> 新增 `ServiceWorkerAssetsManifestItem` 不會導致檔案在應用程式的目錄中發行 `wwwroot` 。 發佈輸出必須個別控制。 `ServiceWorkerAssetsManifestItem`只會導致服務背景工作角色資訊清單中出現額外的專案。

## <a name="push-notifications"></a>推送通知

Pwa 與其他任何 PWA 一樣， Blazor WebAssembly 都可以接收來自後端伺服器的推播通知。 即使使用者未主動使用應用程式，伺服器也可以隨時傳送推播通知。 例如，您可以在不同的使用者執行相關動作時傳送推播通知。

傳送推播通知的機制完全獨立于 Blazor WebAssembly ，因為它是由可以使用任何技術的後端伺服器所執行。 如果您想要從 ASP.NET Core 伺服器傳送推播通知，請考慮 [使用類似于「進比薩」研討會所採用的方法](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications)。

在用戶端上接收和顯示推播通知的機制也是獨立的 Blazor WebAssembly ，因為它是在服務工作者 JavaScript 檔案中執行。 如需範例，請參閱適用 [于「中比薩」研討會的方法](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications)。

## <a name="caveats-for-offline-pwas"></a>離線 Pwa 的注意事項

並非所有應用程式都應該嘗試支援離線使用。 離線支援會增加大量的複雜性，而不一定會與所需的使用案例相關。

離線支援通常僅適用于：

* 如果主要資料存放區是瀏覽器的本機資料存放區。 例如，此方法與應用程式相關，該應用程式具有可將資料儲存在或 IndexedDB 之[IoT](https://en.wikipedia.org/wiki/Internet_of_things)裝置的 UI `localStorage` 。 [](https://developer.mozilla.org/docs/Web/API/IndexedDB_API)
* 如果應用程式執行大量的工作來提取和快取與每個使用者相關的後端 API 資料，讓他們可以離線流覽資料。 如果應用程式必須支援編輯，則必須建立用來追蹤變更的系統，並將資料與後端同步處理。
* 如果目標是要保證應用程式會立即載入，而不考慮網路狀況。 針對後端 API 要求執行適當的使用者體驗，以顯示要求的進度，並在要求因網路無法使用而失敗時正常運作。

此外，具備離線功能的 Pwa 必須處理一系列額外的複雜性。 開發人員應謹慎熟悉下列各節中的注意事項。

### <a name="offline-support-only-when-published"></a>僅在發行時支援離線

在開發期間，您通常會想要在瀏覽器中立即查看每項變更，而不需要進行背景更新程式。 因此， Blazor 只有在發行時，的 PWA 範本才會啟用離線支援。

建立具備離線功能的應用程式時，在開發環境中測試應用程式不夠。 您必須在應用程式的 [已發佈] 狀態中測試應用程式，以瞭解它如何回應不同的網路狀況。

### <a name="update-completion-after-user-navigation-away-from-app"></a>在使用者導覽離開應用程式之後更新完成

在使用者離開所有索引標籤中的應用程式之前，不會完成更新。 如同 [ [背景更新](#background-updates) ] 區段中所述，在您將更新部署到應用程式之後，瀏覽器會提取更新的服務工作者檔案，以開始更新程式。

許多開發人員會發現，即使這項更新完成，在使用者離開所有索引標籤之後，它 **才會生效** 。 重新整理顯示應用程式的索引標籤並 **不** 足夠，即使它是顯示應用程式的唯一索引標籤。 在您的應用程式完全關閉之前，新的服務工作者仍會處於等候中的 *啟動* 狀態。 **這並非特有的，而是 Blazor 標準的 web 平臺行為。**

這通常會麻煩很快找上門嘗試測試其服務工作者或離線快取資源更新的開發人員。 如果您簽入瀏覽器的開發人員工具，您可能會看到類似下列的內容：

![Google Chrome 的 [應用程式] 索引標籤會顯示應用程式的服務工作者為「正在等待啟動」。](progressive-web-app/_static/image7.png)

只要「用戶端」清單（即顯示您應用程式的索引標籤或視窗）是空的，背景工作角色就會繼續等候。 服務工作者這麼做的原因是要保證一致性。 一致性表示所有資源都是從相同的不可部分完成的快取中提取。

測試變更時，如先前的螢幕擷取畫面所示，按一下 [skipWaiting] 連結會很方便，然後重載頁面。 您可以為您的服務背景程式撰寫程式碼，以 [略過「等待中」階段並立即啟用更新，](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase)為所有使用者自動執行此工作。 如果您略過等待階段，您就能保證一律會從相同的快取實例一致地提取資源。

### <a name="users-may-run-any-historical-version-of-the-app"></a>使用者可以執行應用程式的任何歷程記錄版本

Web 開發人員 habitually 預期使用者只會執行其 web 應用程式的最新部署版本，因為這在傳統的 web 散發模型內是正常的。 不過，離線優先的 PWA 更類似于原生行動應用程式，使用者不一定會執行最新版本。

如在 [背景更新](#background-updates) 一節中所述，在您將更新部署到您的應用程式之後， **每個現有的使用者都會繼續使用先前的版本來進行至少一次造訪** ，因為更新會在背景中執行，而且在使用者之後離開之前不會啟動。 此外，先前使用的版本不一定是您先前部署的版本。 先前的版本可以是 *任何* 歷程記錄版本，取決於使用者上次完成更新的時間。

如果您應用程式的前端和後端部分需要 API 要求架構的合約，這可能會是個問題。 除非您確定所有使用者都已升級，否則您不能部署後向不相容的 API 架構變更。 或者，封鎖使用者使用不相容的繼承應用程式。 此案例需求與原生行動應用程式的需求相同。 如果您在伺服器 Api 中部署中斷性變更，用戶端應用程式會中斷尚未更新的使用者。

可能的話，請勿將重大變更部署至您的後端 Api。 如果您必須這樣做，請考慮使用 [標準服務工作者 api （例如 ServiceWorkerRegistration](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) ）來判斷應用程式是否為最新狀態，如果不是，則避免使用。

### <a name="interference-with-server-rendered-pages"></a>伺服器轉譯頁面的干擾

如「 [支援伺服器轉譯頁面](#support-server-rendered-pages) 」一節所述，如果您想要略過服務工作者 `/index.html` 針對所有導覽要求傳回內容的行為，請編輯您的服務背景工作角色中的邏輯。

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a>預設會快取所有服務工作者資產資訊清單內容

如「 [控制資產](#control-asset-caching) 快取」一節所述，檔案 `service-worker-assets.js` 會在組建期間產生，並且會列出服務工作者應該提取和快取的所有資產。

由於此清單預設包含發出的所有專案 `wwwroot` ，包括外部封裝和專案所提供的內容，因此您必須小心不要在該處放入太多內容。 如果 `wwwroot` 目錄包含數百萬個影像，服務工作者會嘗試提取並快取它們，耗用過度的頻寬且很可能無法順利完成。

藉由編輯中的函式來執行任意邏輯，以控制應提取和快取的資訊清單內容子集 `onInstall` `service-worker.published.js` 。

### <a name="interaction-with-authentication"></a>與驗證互動

PWA 範本可以與驗證搭配使用。 當使用者具有初始網路連線能力時，支援離線的 PWA 也可以支援驗證。

當使用者沒有網路連線時，他們無法驗證或取得存取權杖。 依預設，嘗試在沒有網路存取的情況下流覽登入頁面會導致「網路錯誤」訊息。 您必須設計 UI 流程，讓使用者可以在離線時執行有用的工作，而不需要嘗試驗證使用者或取得存取權杖。 或者，您可以在網路無法使用時，將應用程式設計成可正常地失敗。 如果應用程式無法設計來處理這些案例，您可能不會想要啟用離線支援。

當針對線上和離線使用而設計的應用程式再次上線時：

* 應用程式可能需要布建新的存取權杖。
* 應用程式必須偵測是否有不同的使用者登入服務，讓它可以將作業套用至使用者在離線時所做的帳戶。

若要建立與驗證互動的離線 PWA 應用程式：

* 將取代為 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> 儲存最後登入使用者的處理站，並在應用程式離線時使用已儲存的使用者。
* 應用程式離線時的佇列作業，並在應用程式回到線上時套用它們。
* 登出時，請清除儲存的使用者。

[`CarChecker`](https://github.com/SteveSandersonMS/CarChecker)範例應用程式會示範上述方法。 請參閱應用程式的下列部分：

* `OfflineAccountClaimsPrincipalFactory` (`Client/Data/OfflineAccountClaimsPrincipalFactory.cs`)
* `LocalVehiclesStore` (`Client/Data/LocalVehiclesStore.cs`)
* `LoginStatus` 元件 (`Client/Shared/LoginStatus.razor`) 

## <a name="additional-resources"></a>其他資源

* [針對完整性 PowerShell 腳本進行疑難排解](xref:blazor/host-and-deploy/webassembly#troubleshoot-integrity-powershell-script)
* [SignalR 驗證的跨原始來源協商](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
