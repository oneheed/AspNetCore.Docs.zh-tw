---
title: 使用 ASP.NET Core 建立漸進式 Web 應用程式 Blazor WebAssembly
author: guardrex
description: 瞭解如何建立 Blazor (PWA) 的漸進式 Web 應用程式，該應用程式使用新式瀏覽器功能的行為就像桌面應用程式一樣。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/11/2021
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
ms.openlocfilehash: 515da543fc6b6cca0b90968b154d91b611ea3345
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280552"
---
# <a name="build-progressive-web-applications-with-aspnet-core-blazor-webassembly"></a><span data-ttu-id="c6a05-103">使用 ASP.NET Core 建立漸進式 Web 應用程式 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="c6a05-103">Build Progressive Web Applications with ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="c6a05-104"> (PWA) 的漸進式 Web 應用程式通常是使用新式瀏覽器 Api 和功能的單一頁面應用)  (程式，其行為就像桌面應用程式一樣。</span><span class="sxs-lookup"><span data-stu-id="c6a05-104">A Progressive Web Application (PWA) is usually a Single Page Application (SPA) that uses modern browser APIs and capabilities to behave like a desktop app.</span></span> <span data-ttu-id="c6a05-105">Blazor WebAssembly 是以標準為基礎的用戶端 web 應用程式平臺，因此它可以使用任何瀏覽器 API，包括下列功能所需的 PWA Api：</span><span class="sxs-lookup"><span data-stu-id="c6a05-105">Blazor WebAssembly is a standards-based client-side web app platform, so it can use any browser API, including PWA APIs required for the following capabilities:</span></span>

* <span data-ttu-id="c6a05-106">離線工作並立即載入，與網路速度無關。</span><span class="sxs-lookup"><span data-stu-id="c6a05-106">Working offline and loading instantly, independent of network speed.</span></span>
* <span data-ttu-id="c6a05-107">在它自己的應用程式視窗中執行，而不只是瀏覽器視窗。</span><span class="sxs-lookup"><span data-stu-id="c6a05-107">Running in its own app window, not just a browser window.</span></span>
* <span data-ttu-id="c6a05-108">從主機的作業系統 [開始] 功能表、[dock] 或 [主畫面] 啟動。</span><span class="sxs-lookup"><span data-stu-id="c6a05-108">Being launched from the host's operating system start menu, dock, or home screen.</span></span>
* <span data-ttu-id="c6a05-109">即使使用者未使用應用程式，也會從後端伺服器接收推播通知。</span><span class="sxs-lookup"><span data-stu-id="c6a05-109">Receiving push notifications from a backend server, even while the user isn't using the app.</span></span>
* <span data-ttu-id="c6a05-110">在背景中自動更新。</span><span class="sxs-lookup"><span data-stu-id="c6a05-110">Automatically updating in the background.</span></span>

<span data-ttu-id="c6a05-111">「 *漸進式* 」（word）是用來描述這類應用程式，因為：</span><span class="sxs-lookup"><span data-stu-id="c6a05-111">The word *progressive* is used to describe such apps because:</span></span>

* <span data-ttu-id="c6a05-112">使用者可能會先在其網頁瀏覽器中探索及使用應用程式，就像任何其他 SPA 一樣。</span><span class="sxs-lookup"><span data-stu-id="c6a05-112">A user might first discover and use the app within their web browser like any other SPA.</span></span>
* <span data-ttu-id="c6a05-113">之後，使用者會在作業系統中進行安裝，並啟用推播通知。</span><span class="sxs-lookup"><span data-stu-id="c6a05-113">Later, the user progresses to installing it in their OS and enabling push notifications.</span></span>

## <a name="create-a-project-from-the-pwa-template"></a><span data-ttu-id="c6a05-114">從 PWA 範本建立專案</span><span class="sxs-lookup"><span data-stu-id="c6a05-114">Create a project from the PWA template</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6a05-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6a05-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c6a05-116">在 [**建立新專案**] 對話方塊中建立新的 **Blazor WebAssembly 應用** 程式時，選取 [**漸進式 Web 應用程式**] 核取方塊：</span><span class="sxs-lookup"><span data-stu-id="c6a05-116">When creating a new **Blazor WebAssembly App** in the **Create a New Project** dialog, select the **Progressive Web Application** check box:</span></span>

![在 Visual Studio [新增專案] 對話方塊中，已選取 [漸進式 Web 應用程式] 核取方塊。](progressive-web-app/_static/image1.png)

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

-->

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="c6a05-118">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c6a05-118">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="c6a05-119">使用下列命令，在命令列中使用參數建立 PWA 專案 `--pwa` ：</span><span class="sxs-lookup"><span data-stu-id="c6a05-119">Use the following command to create a PWA project in a command shell with the `--pwa` switch:</span></span>

```dotnetcli
dotnet new blazorwasm -o MyBlazorPwa --pwa
```

<span data-ttu-id="c6a05-120">在上述命令中，選項會為 `-o|--output` 應用程式建立名為的新資料夾 `MyBlazorPwa` 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-120">In the preceding command, the `-o|--output` option creates a new folder for the app named `MyBlazorPwa`.</span></span>

---

<span data-ttu-id="c6a05-121">（選擇性）您可以針對從 ASP.NET Core 裝載的範本建立的應用程式設定 PWA。</span><span class="sxs-lookup"><span data-stu-id="c6a05-121">Optionally, PWA can be configured for an app created from the ASP.NET Core Hosted template.</span></span> <span data-ttu-id="c6a05-122">PWA 案例與裝載模型無關。</span><span class="sxs-lookup"><span data-stu-id="c6a05-122">The PWA scenario is independent of the hosting model.</span></span>

## <a name="convert-an-existing-blazor-webassembly-app-into-a-pwa"></a><span data-ttu-id="c6a05-123">將現有的 Blazor WebAssembly 應用程式轉換為 PWA</span><span class="sxs-lookup"><span data-stu-id="c6a05-123">Convert an existing Blazor WebAssembly app into a PWA</span></span>

<span data-ttu-id="c6a05-124">Blazor WebAssembly遵循本節中的指導方針，將現有的應用程式轉換為 PWA。</span><span class="sxs-lookup"><span data-stu-id="c6a05-124">Convert an existing Blazor WebAssembly app into a PWA following the guidance in this section.</span></span>

<span data-ttu-id="c6a05-125">在應用程式的專案檔中：</span><span class="sxs-lookup"><span data-stu-id="c6a05-125">In the app's project file:</span></span>

* <span data-ttu-id="c6a05-126">將下列 `ServiceWorkerAssetsManifest` 屬性新增至 `PropertyGroup` ：</span><span class="sxs-lookup"><span data-stu-id="c6a05-126">Add the following `ServiceWorkerAssetsManifest` property to a `PropertyGroup`:</span></span>

  ```xml
    ...
    <ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
  </PropertyGroup>
   ```

* <span data-ttu-id="c6a05-127">將下列 `ServiceWorker` 專案加入至 `ItemGroup` ：</span><span class="sxs-lookup"><span data-stu-id="c6a05-127">Add the following `ServiceWorker` item to an `ItemGroup`:</span></span>

  ```xml
  <ItemGroup>
    <ServiceWorker Include="wwwroot\service-worker.js" 
      PublishedContent="wwwroot\service-worker.published.js" />
  </ItemGroup>
  ```

<span data-ttu-id="c6a05-128">若要取得靜態資產，請使用下列 **其中一** 種方法：</span><span class="sxs-lookup"><span data-stu-id="c6a05-128">To obtain static assets, use **one** of the following approaches:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="c6a05-129">使用命令 shell 中的命令，建立個別的新 PWA 專案 [`dotnet new`](/dotnet/core/tools/dotnet-new) ：</span><span class="sxs-lookup"><span data-stu-id="c6a05-129">Create a separate, new PWA project with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa
  ```
  
  <span data-ttu-id="c6a05-130">在上述命令中，選項會為 `-o|--output` 應用程式建立名為的新資料夾 `MyBlazorPwa` 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-130">In the preceding command, the `-o|--output` option creates a new folder for the app named `MyBlazorPwa`.</span></span>
  
  <span data-ttu-id="c6a05-131">如果您未將應用程式轉換為最新版本，請傳遞 `-f|--framework` 選項。</span><span class="sxs-lookup"><span data-stu-id="c6a05-131">If you aren't converting an app for the latest release, pass the `-f|--framework` option.</span></span> <span data-ttu-id="c6a05-132">下列範例會建立適用于 ASP.NET Core 3.1 版的應用程式：</span><span class="sxs-lookup"><span data-stu-id="c6a05-132">The following example creates the app for ASP.NET Core version 3.1:</span></span>
  
  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa -f netcoreapp3.1
  ```

* <span data-ttu-id="c6a05-133">流覽至位於下列 URL 的 ASP.NET Core GitHub 存放庫，其連結至5.0 版本參考來源和資產。</span><span class="sxs-lookup"><span data-stu-id="c6a05-133">Navigate to the ASP.NET Core GitHub repository at the following URL, which links to 5.0 release reference source and assets.</span></span> <span data-ttu-id="c6a05-134">如果您未轉換5.0 版的應用程式，請從適用于您應用程式的 [ **切換分支或標記** ] 下拉式清單中，選取您正在使用的版本。</span><span class="sxs-lookup"><span data-stu-id="c6a05-134">If you aren't converting an app for the 5.0 release, select the release that you're working with from the **Switch branches or tags** drop-down list that applies to your app.</span></span>

  [<span data-ttu-id="c6a05-135">dotnet/aspnetcore (版本 5.0) Blazor WebAssembly 專案範本 `wwwroot` 資料夾</span><span class="sxs-lookup"><span data-stu-id="c6a05-135">dotnet/aspnetcore (release 5.0) Blazor WebAssembly project template `wwwroot` folder</span></span>](https://github.com/dotnet/aspnetcore/tree/release/5.0/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="c6a05-136">使用命令 shell 中的命令，建立個別的新 PWA 專案 [`dotnet new`](/dotnet/core/tools/dotnet-new) 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-136">Create a separate, new PWA project with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="c6a05-137">傳遞 `-f|--framework` 選項以選取版本。</span><span class="sxs-lookup"><span data-stu-id="c6a05-137">Pass the `-f|--framework` option to select the version.</span></span> <span data-ttu-id="c6a05-138">下列範例會建立適用于 ASP.NET Core 3.1 版的應用程式：</span><span class="sxs-lookup"><span data-stu-id="c6a05-138">The following example creates the app for ASP.NET Core version 3.1:</span></span>
  
  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa -f netcoreapp3.1
  ```
  
  <span data-ttu-id="c6a05-139">在上述命令中，選項會為 `-o|--output` 應用程式建立名為的新資料夾 `MyBlazorPwa` 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-139">In the preceding command, the `-o|--output` option creates a new folder for the app named `MyBlazorPwa`.</span></span>

* <span data-ttu-id="c6a05-140">流覽至位於下列 URL 的 ASP.NET Core GitHub 存放庫，其連結至3.1 版本參考來源和資產：</span><span class="sxs-lookup"><span data-stu-id="c6a05-140">Navigate to the ASP.NET Core GitHub repository at the following URL, which links to 3.1 release reference source and assets:</span></span>

  [<span data-ttu-id="c6a05-141">dotnet/aspnetcore (版本 3.1) Blazor WebAssembly 專案範本 `wwwroot` 資料夾</span><span class="sxs-lookup"><span data-stu-id="c6a05-141">dotnet/aspnetcore (release 3.1) Blazor WebAssembly project template `wwwroot` folder</span></span>](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/ProjectTemplates/ComponentsWebAssembly.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)

  > [!NOTE]
  > <span data-ttu-id="c6a05-142">專案範本的 URL 會在 Blazor WebAssembly 發行 ASP.NET Core 3.1 之後變更。</span><span class="sxs-lookup"><span data-stu-id="c6a05-142">The URL for Blazor WebAssembly project template changed after the release of ASP.NET Core 3.1.</span></span> <span data-ttu-id="c6a05-143">您可以從下列 URL 取得5.0 或更新版本的參考資產：</span><span class="sxs-lookup"><span data-stu-id="c6a05-143">Reference assets for 5.0 or later are available at the following URL:</span></span>
  >
  > [<span data-ttu-id="c6a05-144">dotnet/aspnetcore (版本 5.0) Blazor WebAssembly 專案範本 `wwwroot` 資料夾</span><span class="sxs-lookup"><span data-stu-id="c6a05-144">dotnet/aspnetcore (release 5.0) Blazor WebAssembly project template `wwwroot` folder</span></span>](https://github.com/dotnet/aspnetcore/tree/release/5.0/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)

::: moniker-end

<span data-ttu-id="c6a05-145">從 `wwwroot` 您建立的應用程式中或從 GitHub 存放庫中的參考資產的源資料夾 `dotnet/aspnetcore` ，將下列檔案複製到應用程式的 `wwwroot` 資料夾中：</span><span class="sxs-lookup"><span data-stu-id="c6a05-145">From the source `wwwroot` folder either in the app that you created or from the reference assets in the `dotnet/aspnetcore` GitHub repository, copy the following files into the app's `wwwroot` folder:</span></span>

* `icon-512.png`
* `manifest.json`
* `service-worker.js`
* `service-worker.published.js`

<span data-ttu-id="c6a05-146">在應用程式的檔案中 `wwwroot/index.html` ：</span><span class="sxs-lookup"><span data-stu-id="c6a05-146">In the app's `wwwroot/index.html` file:</span></span>

* <span data-ttu-id="c6a05-147">新增 `<link>` 資訊清單和應用程式圖示的元素：</span><span class="sxs-lookup"><span data-stu-id="c6a05-147">Add `<link>` elements for the manifest and app icon:</span></span>

  ```html
  <link href="manifest.json" rel="manifest" />
  <link rel="apple-touch-icon" sizes="512x512" href="icon-512.png" />
  ```

* <span data-ttu-id="c6a05-148">在 `<script>` `</body>` 腳本標記後面緊接著結束記號內新增下列標記 `blazor.webassembly.js` ：</span><span class="sxs-lookup"><span data-stu-id="c6a05-148">Add the following `<script>` tag inside the closing `</body>` tag immediately after the `blazor.webassembly.js` script tag:</span></span>

  ```html
      ...
      <script>navigator.serviceWorker.register('service-worker.js');</script>
  </body>
  ```

## <a name="installation-and-app-manifest"></a><span data-ttu-id="c6a05-149">安裝和應用程式資訊清單</span><span class="sxs-lookup"><span data-stu-id="c6a05-149">Installation and app manifest</span></span>

<span data-ttu-id="c6a05-150">造訪使用 PWA 範本建立的應用程式時，使用者可以選擇將應用程式安裝到其作業系統的 [開始] 功能表、[停駐] 或 [主畫面] 中。</span><span class="sxs-lookup"><span data-stu-id="c6a05-150">When visiting an app created using the PWA template, users have the option of installing the app into their OS's start menu, dock, or home screen.</span></span> <span data-ttu-id="c6a05-151">此選項的呈現方式取決於使用者的瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="c6a05-151">The way this option is presented depends on the user's browser.</span></span> <span data-ttu-id="c6a05-152">使用以桌面 Chromium 為基礎的瀏覽器（例如 Edge 或 Chrome）時，[ **新增** ] 按鈕會出現在 URL 列內。</span><span class="sxs-lookup"><span data-stu-id="c6a05-152">When using desktop Chromium-based browsers, such as Edge or Chrome, an **Add** button appears within the URL bar.</span></span> <span data-ttu-id="c6a05-153">當使用者選取 [ **新增** ] 按鈕之後，他們會收到確認對話方塊：</span><span class="sxs-lookup"><span data-stu-id="c6a05-153">After the user selects the **Add** button, they receive a confirmation dialog:</span></span>

![Google Chrome 中的確認對話方塊會向使用者顯示 [My：：： no- (Blazor) ：：:P wa] 應用程式的 [安裝] 按鈕。](progressive-web-app/_static/image2.png)

<span data-ttu-id="c6a05-155">在 iOS 上，訪客可以使用 Safari 的 [ **共用** ] 按鈕和 [ **新增至 Homescreen** ] 選項來安裝 PWA。</span><span class="sxs-lookup"><span data-stu-id="c6a05-155">On iOS, visitors can install the PWA using Safari's **Share** button and its **Add to Homescreen** option.</span></span> <span data-ttu-id="c6a05-156">在適用于 Android 的 Chrome 上，使用者應選取右上角的 **功能表** 按鈕，然後選取 [ **新增至主畫面**]。</span><span class="sxs-lookup"><span data-stu-id="c6a05-156">On Chrome for Android, users should select the **Menu** button in the upper-right corner, followed by **Add to Home screen**.</span></span>

<span data-ttu-id="c6a05-157">一旦安裝之後，應用程式就會出現在其本身的視窗中，而不使用網址列：</span><span class="sxs-lookup"><span data-stu-id="c6a05-157">Once installed, the app appears in its own window without an address bar:</span></span>

![' My：：：非 loc (Blazor) ：：:P wa ' 應用程式會在 Google Chrome 中執行，而不需使用網址列。](progressive-web-app/_static/image3.png)

<span data-ttu-id="c6a05-159">若要自訂視窗的標題、色彩配置、圖示或其他詳細資料，請參閱 `manifest.json` 專案目錄中的檔案 `wwwroot` 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-159">To customize the window's title, color scheme, icon, or other details, see the `manifest.json` file in the project's `wwwroot` directory.</span></span> <span data-ttu-id="c6a05-160">這個檔案的架構是由 web 標準所定義。</span><span class="sxs-lookup"><span data-stu-id="c6a05-160">The schema of this file is defined by web standards.</span></span> <span data-ttu-id="c6a05-161">如需詳細資訊，請參閱 [MDN web 檔： Web 應用程式資訊清單](https://developer.mozilla.org/docs/Web/Manifest)。</span><span class="sxs-lookup"><span data-stu-id="c6a05-161">For more information, see [MDN web docs: Web App Manifest](https://developer.mozilla.org/docs/Web/Manifest).</span></span>

## <a name="offline-support"></a><span data-ttu-id="c6a05-162">離線支援</span><span class="sxs-lookup"><span data-stu-id="c6a05-162">Offline support</span></span>

<span data-ttu-id="c6a05-163">依預設，使用 PWA 範本選項建立的應用程式支援離線執行。</span><span class="sxs-lookup"><span data-stu-id="c6a05-163">By default, apps created using the PWA template option have support for running offline.</span></span> <span data-ttu-id="c6a05-164">使用者在線上時，必須先造訪應用程式。</span><span class="sxs-lookup"><span data-stu-id="c6a05-164">A user must first visit the app while they're online.</span></span> <span data-ttu-id="c6a05-165">瀏覽器會自動下載並快取離線運作所需的所有資源。</span><span class="sxs-lookup"><span data-stu-id="c6a05-165">The browser automatically downloads and caches all of the resources required to operate offline.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c6a05-166">開發支援會干擾進行變更和測試的一般開發週期。</span><span class="sxs-lookup"><span data-stu-id="c6a05-166">Development support would interfere with the usual development cycle of making changes and testing them.</span></span> <span data-ttu-id="c6a05-167">因此，只會針對 *已發佈* 的應用程式啟用離線支援。</span><span class="sxs-lookup"><span data-stu-id="c6a05-167">Therefore, offline support is only enabled for *published* apps.</span></span> 

> [!WARNING]
> <span data-ttu-id="c6a05-168">如果您想要散發啟用離線的 PWA，有 [幾個重要的警告和](#caveats-for-offline-pwas)警告。</span><span class="sxs-lookup"><span data-stu-id="c6a05-168">If you intend to distribute an offline-enabled PWA, there are [several important warnings and caveats](#caveats-for-offline-pwas).</span></span> <span data-ttu-id="c6a05-169">這些案例都是離線 Pwa 固有的，而不是特定的 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-169">These scenarios are inherent to offline PWAs and not specific to Blazor.</span></span> <span data-ttu-id="c6a05-170">在對啟用離線的應用程式運作方式進行假設之前，請務必閱讀並瞭解這些注意事項。</span><span class="sxs-lookup"><span data-stu-id="c6a05-170">Be sure to read and understand these caveats before making assumptions about how your offline-enabled app will work.</span></span>

<span data-ttu-id="c6a05-171">若要查看離線支援的運作方式：</span><span class="sxs-lookup"><span data-stu-id="c6a05-171">To see how offline support works:</span></span>

1. <span data-ttu-id="c6a05-172">發行應用程式。</span><span class="sxs-lookup"><span data-stu-id="c6a05-172">Publish the app.</span></span> <span data-ttu-id="c6a05-173">如需詳細資訊，請參閱<xref:blazor/host-and-deploy/index#publish-the-app>。</span><span class="sxs-lookup"><span data-stu-id="c6a05-173">For more information, see <xref:blazor/host-and-deploy/index#publish-the-app>.</span></span>
1. <span data-ttu-id="c6a05-174">將應用程式部署至支援 HTTPS 的伺服器，並在瀏覽器中以其安全的 HTTPS 位址存取應用程式。</span><span class="sxs-lookup"><span data-stu-id="c6a05-174">Deploy the app to a server that supports HTTPS, and access the app in a browser at its secure HTTPS address.</span></span>
1. <span data-ttu-id="c6a05-175">開啟瀏覽器的開發工具，並確認已在 [**應用程式**] 索引標籤上註冊主機的 *服務工作者*：</span><span class="sxs-lookup"><span data-stu-id="c6a05-175">Open the browser's dev tools and verify that a *Service Worker* is registered for the host on the **Application** tab:</span></span>

   ![Google Chrome 開發人員工具的 [應用程式] 索引標籤會顯示已啟動且正在執行的服務工作者。](progressive-web-app/_static/image4.png)

1. <span data-ttu-id="c6a05-177">重載頁面，並檢查 [ **網路** ] 索引標籤。系統會將 **服務背景工作** 或 **記憶體** 快取列為所有頁面資產的來源：</span><span class="sxs-lookup"><span data-stu-id="c6a05-177">Reload the page and examine the **Network** tab. **Service Worker** or **memory cache** are listed as the sources for all of the page's assets:</span></span>

   ![Google Chrome 開發人員工具 [網路] 索引標籤，顯示所有頁面資產的來源。](progressive-web-app/_static/image5.png)

1. <span data-ttu-id="c6a05-179">若要確認瀏覽器不相依于網路存取以載入應用程式，請執行下列其中一項：</span><span class="sxs-lookup"><span data-stu-id="c6a05-179">To verify that the browser isn't dependent on network access to load the app, either:</span></span>

   * <span data-ttu-id="c6a05-180">關閉網頁伺服器，並查看應用程式繼續正常運作的方式，包括頁面重載。</span><span class="sxs-lookup"><span data-stu-id="c6a05-180">Shut down the web server and see how the app continues to function normally, which includes page reloads.</span></span> <span data-ttu-id="c6a05-181">同樣地，當網路連線速度很慢時，應用程式會繼續正常運作。</span><span class="sxs-lookup"><span data-stu-id="c6a05-181">Likewise, the app continues to function normally when there's a slow network connection.</span></span>
   * <span data-ttu-id="c6a05-182">指示瀏覽器模擬 [ **網路** ] 索引標籤中的離線模式：</span><span class="sxs-lookup"><span data-stu-id="c6a05-182">Instruct the browser to simulate offline mode in the **Network** tab:</span></span>

   ![[Google Chrome 開發人員工具] 的 [網路] 索引標籤，其中的 [瀏覽器模式] 下拉式清單從 [線上] 變更為 [離線]。](progressive-web-app/_static/image6.png)

<span data-ttu-id="c6a05-184">使用服務工作者的離線支援是 web 標準，而非特定的 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-184">Offline support using a service worker is a web standard, not specific to Blazor.</span></span> <span data-ttu-id="c6a05-185">如需服務工作者的詳細資訊，請參閱 [MDN web 檔：服務工作者 API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API)。</span><span class="sxs-lookup"><span data-stu-id="c6a05-185">For more information on service workers, see [MDN web docs: Service Worker API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API).</span></span> <span data-ttu-id="c6a05-186">若要深入瞭解服務工作者的常見使用模式，請參閱 [Google Web：服務工作者生命週期](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle)。</span><span class="sxs-lookup"><span data-stu-id="c6a05-186">To learn more about common usage patterns for service workers, see [Google Web: The Service Worker Lifecycle](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle).</span></span>

<span data-ttu-id="c6a05-187">Blazor的 PWA 範本會產生兩個服務工作者檔案：</span><span class="sxs-lookup"><span data-stu-id="c6a05-187">Blazor's PWA template produces two service worker files:</span></span>

* <span data-ttu-id="c6a05-188">`wwwroot/service-worker.js`，在開發期間使用。</span><span class="sxs-lookup"><span data-stu-id="c6a05-188">`wwwroot/service-worker.js`, which is used during development.</span></span>
* <span data-ttu-id="c6a05-189">`wwwroot/service-worker.published.js`，會在應用程式發行之後使用。</span><span class="sxs-lookup"><span data-stu-id="c6a05-189">`wwwroot/service-worker.published.js`, which is used after the app is published.</span></span>

<span data-ttu-id="c6a05-190">若要在兩個服務工作者檔案之間共用邏輯，請考慮下列方法：</span><span class="sxs-lookup"><span data-stu-id="c6a05-190">To share logic between the two service worker files, consider the following approach:</span></span>

* <span data-ttu-id="c6a05-191">新增第三個 JavaScript 檔案以保存常見的邏輯。</span><span class="sxs-lookup"><span data-stu-id="c6a05-191">Add a third JavaScript file to hold the common logic.</span></span>
* <span data-ttu-id="c6a05-192">用 [`self.importScripts`](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) 來將通用邏輯載入兩個服務工作者檔案中。</span><span class="sxs-lookup"><span data-stu-id="c6a05-192">Use [`self.importScripts`](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) to load the common logic into both service worker files.</span></span>

### <a name="cache-first-fetch-strategy"></a><span data-ttu-id="c6a05-193">快取優先提取策略</span><span class="sxs-lookup"><span data-stu-id="c6a05-193">Cache-first fetch strategy</span></span>

<span data-ttu-id="c6a05-194">內建 `service-worker.published.js` 服務工作者會使用快取 *優先* 策略來解析要求。</span><span class="sxs-lookup"><span data-stu-id="c6a05-194">The built-in `service-worker.published.js` service worker resolves requests using a *cache-first* strategy.</span></span> <span data-ttu-id="c6a05-195">這表示服務工作者偏好傳回快取的內容，不論使用者是否可在伺服器上使用網路存取或更新的內容。</span><span class="sxs-lookup"><span data-stu-id="c6a05-195">This means that the service worker prefers to return cached content, regardless of whether the user has network access or newer content is available on the server.</span></span>

<span data-ttu-id="c6a05-196">快取優先策略很重要，因為：</span><span class="sxs-lookup"><span data-stu-id="c6a05-196">The cache-first strategy is valuable because:</span></span>

* <span data-ttu-id="c6a05-197">**它可確保可靠性。**</span><span class="sxs-lookup"><span data-stu-id="c6a05-197">**It ensures reliability.**</span></span> <span data-ttu-id="c6a05-198">網路存取不是布林狀態。</span><span class="sxs-lookup"><span data-stu-id="c6a05-198">Network access isn't a boolean state.</span></span> <span data-ttu-id="c6a05-199">使用者不只是線上或離線：</span><span class="sxs-lookup"><span data-stu-id="c6a05-199">A user isn't simply online or offline:</span></span>

  * <span data-ttu-id="c6a05-200">使用者的裝置可能會假設它已上線，但網路可能會太慢而無法等候。</span><span class="sxs-lookup"><span data-stu-id="c6a05-200">The user's device may assume it's online, but the network might be so slow as to be impractical to wait for.</span></span>
  * <span data-ttu-id="c6a05-201">網路可能會針對某些 Url 傳回不正確結果，例如，當有一個已封鎖的 WIFI 入口網站正在封鎖或重新導向特定要求時。</span><span class="sxs-lookup"><span data-stu-id="c6a05-201">The network might return invalid results for certain URLs, such as when there's a captive WIFI portal that's currently blocking or redirecting certain requests.</span></span>
  
  <span data-ttu-id="c6a05-202">這就是為什麼瀏覽器的 `navigator.onLine` API 不可靠，也不應該相依的原因。</span><span class="sxs-lookup"><span data-stu-id="c6a05-202">This is why the browser's `navigator.onLine` API isn't reliable and shouldn't be depended upon.</span></span>

* <span data-ttu-id="c6a05-203">**它可確保正確性。**</span><span class="sxs-lookup"><span data-stu-id="c6a05-203">**It ensures correctness.**</span></span> <span data-ttu-id="c6a05-204">當您建立離線資源的快取時，服務背景工作會使用內容雜湊來確保它已在單一時刻取得完整且自我一致的資源快照集。</span><span class="sxs-lookup"><span data-stu-id="c6a05-204">When building a cache of offline resources, the service worker uses content hashing to guarantee it has fetched a complete and self-consistent snapshot of resources at a single instant in time.</span></span> <span data-ttu-id="c6a05-205">然後，此快取會用來做為不可部分完成的單位。</span><span class="sxs-lookup"><span data-stu-id="c6a05-205">This cache is then used as an atomic unit.</span></span> <span data-ttu-id="c6a05-206">不需要要求網路提供較新的資源，因為唯一需要的版本是已快取的版本。</span><span class="sxs-lookup"><span data-stu-id="c6a05-206">There's no point asking the network for newer resources, since the only versions required are the ones already cached.</span></span> <span data-ttu-id="c6a05-207">任何其他風險會造成不一致和不相容 (例如，嘗試使用未一起編譯的 .NET 元件版本) 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-207">Anything else risks inconsistency and incompatibility (for example, trying to use versions of .NET assemblies that weren't compiled together).</span></span>

### <a name="background-updates"></a><span data-ttu-id="c6a05-208">背景更新</span><span class="sxs-lookup"><span data-stu-id="c6a05-208">Background updates</span></span>

<span data-ttu-id="c6a05-209">作為精神模型，您可以將離線優先的 PWA 視為可以安裝的行動應用程式。</span><span class="sxs-lookup"><span data-stu-id="c6a05-209">As a mental model, you can think of an offline-first PWA as behaving like a mobile app that can be installed.</span></span> <span data-ttu-id="c6a05-210">無論網路連線為何，應用程式都會立即啟動，但已安裝的應用程式邏輯來自可能不是最新版本的時間點快照集。</span><span class="sxs-lookup"><span data-stu-id="c6a05-210">The app starts up immediately regardless of network connectivity, but the installed app logic comes from a point-in-time snapshot that might not be the latest version.</span></span>

<span data-ttu-id="c6a05-211">BlazorPWA 範本會產生應用程式，每當使用者造訪並具有運作中的網路連線時，就會自動嘗試在背景中自行更新。</span><span class="sxs-lookup"><span data-stu-id="c6a05-211">The Blazor PWA template produces apps that automatically try to update themselves in the background whenever the user visits and has a working network connection.</span></span> <span data-ttu-id="c6a05-212">其運作方式如下所示：</span><span class="sxs-lookup"><span data-stu-id="c6a05-212">The way this works is as follows:</span></span>

* <span data-ttu-id="c6a05-213">在編譯期間，專案會產生 *服務工作者資產資訊清單*。</span><span class="sxs-lookup"><span data-stu-id="c6a05-213">During compilation, the project generates a *service worker assets manifest*.</span></span> <span data-ttu-id="c6a05-214">根據預設，會呼叫此方法 `service-worker-assets.js` 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-214">By default, this is called `service-worker-assets.js`.</span></span> <span data-ttu-id="c6a05-215">資訊清單會列出應用程式離線運作所需的所有靜態資源，例如 .NET 元件、JavaScript 檔案和 CSS，包括其內容雜湊。</span><span class="sxs-lookup"><span data-stu-id="c6a05-215">The manifest lists all the static resources that the app requires to function offline, such as .NET assemblies, JavaScript files, and CSS, including their content hashes.</span></span> <span data-ttu-id="c6a05-216">資源清單是由服務工作者載入，因此它知道要快取哪些資源。</span><span class="sxs-lookup"><span data-stu-id="c6a05-216">The resource list is loaded by the service worker so that it knows which resources to cache.</span></span>
* <span data-ttu-id="c6a05-217">每次使用者造訪應用程式時，瀏覽器會 `service-worker.js` `service-worker-assets.js` 在背景中重新要求和。</span><span class="sxs-lookup"><span data-stu-id="c6a05-217">Each time the user visits the app, the browser re-requests `service-worker.js` and `service-worker-assets.js` in the background.</span></span> <span data-ttu-id="c6a05-218">這些檔案會與現有已安裝的服務工作者進行位元組的位元組比較。</span><span class="sxs-lookup"><span data-stu-id="c6a05-218">The files are compared byte-for-byte with the existing installed service worker.</span></span> <span data-ttu-id="c6a05-219">如果伺服器傳回任何這些檔案的變更內容，服務工作者會嘗試安裝新版的。</span><span class="sxs-lookup"><span data-stu-id="c6a05-219">If the server returns changed content for either of these files, the service worker attempts to install a new version of itself.</span></span>
* <span data-ttu-id="c6a05-220">當您安裝新的版本時，服務背景工作會為離線資源建立新的個別快取，並開始使用中所列的資源來填入快取 `service-worker-assets.js` 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-220">When installing a new version of itself, the service worker creates a new, separate cache for offline resources and starts populating the cache with resources listed in `service-worker-assets.js`.</span></span> <span data-ttu-id="c6a05-221">此邏輯會在內部的函式中執行 `onInstall` `service-worker.published.js` 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-221">This logic is implemented in the `onInstall` function inside `service-worker.published.js`.</span></span>
* <span data-ttu-id="c6a05-222">當所有資源都已載入且沒有錯誤，且所有內容雜湊相符時，此程式就會順利完成。</span><span class="sxs-lookup"><span data-stu-id="c6a05-222">The process completes successfully when all of the resources are loaded without error and all content hashes match.</span></span> <span data-ttu-id="c6a05-223">如果成功，新的服務工作者會進入 *等待啟用* 狀態。</span><span class="sxs-lookup"><span data-stu-id="c6a05-223">If successful, the new service worker enters a *waiting for activation* state.</span></span> <span data-ttu-id="c6a05-224">一旦使用者關閉應用程式 (沒有剩餘的應用程式索引標籤或 windows) ，新的服務工作者就會變成使用中 *狀態* ，並用於後續的應用程式造訪。</span><span class="sxs-lookup"><span data-stu-id="c6a05-224">As soon as the user closes the app (no remaining app tabs or windows), the new service worker becomes *active* and is used for subsequent app visits.</span></span> <span data-ttu-id="c6a05-225">舊的服務工作者及其快取會被刪除。</span><span class="sxs-lookup"><span data-stu-id="c6a05-225">The old service worker and its cache are deleted.</span></span>
* <span data-ttu-id="c6a05-226">如果程式未順利完成，則會捨棄新的服務背景工作實例。</span><span class="sxs-lookup"><span data-stu-id="c6a05-226">If the process doesn't complete successfully, the new service worker instance is discarded.</span></span> <span data-ttu-id="c6a05-227">當您希望用戶端有更好的網路連線可以完成要求時，會在使用者下一次造訪時再次嘗試更新程式。</span><span class="sxs-lookup"><span data-stu-id="c6a05-227">The update process is attempted again on the user's next visit, when hopefully the client has a better network connection that can complete the requests.</span></span>

<span data-ttu-id="c6a05-228">藉由編輯服務背景工作邏輯來自訂此流程。</span><span class="sxs-lookup"><span data-stu-id="c6a05-228">Customize this process by editing the service worker logic.</span></span> <span data-ttu-id="c6a05-229">上述行為都不是特定的， Blazor 但只是 PWA 範本選項所提供的預設體驗。</span><span class="sxs-lookup"><span data-stu-id="c6a05-229">None of the preceding behavior is specific to Blazor but is merely the default experience provided by the PWA template option.</span></span> <span data-ttu-id="c6a05-230">如需詳細資訊，請參閱 [MDN web 檔：服務工作者 API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API)。</span><span class="sxs-lookup"><span data-stu-id="c6a05-230">For more information, see [MDN web docs: Service Worker API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API).</span></span>

### <a name="how-requests-are-resolved"></a><span data-ttu-id="c6a05-231">要求的解析方式</span><span class="sxs-lookup"><span data-stu-id="c6a05-231">How requests are resolved</span></span>

<span data-ttu-id="c6a05-232">如「快 [取優先提取策略](#cache-first-fetch-strategy) 」一節所述，預設服務背景工作會使用快取 *優先* 策略，這表示它會嘗試提供快取的內容（如果有的話）。</span><span class="sxs-lookup"><span data-stu-id="c6a05-232">As described in the [Cache-first fetch strategy](#cache-first-fetch-strategy) section, the default service worker uses a *cache-first* strategy, meaning that it tries to serve cached content when available.</span></span> <span data-ttu-id="c6a05-233">如果未針對特定 URL 快取任何內容，例如從後端 API 要求資料時，服務背景工作會回復一般網路要求。</span><span class="sxs-lookup"><span data-stu-id="c6a05-233">If there is no content cached for a certain URL, for example when requesting data from a backend API, the service worker falls back on a regular network request.</span></span> <span data-ttu-id="c6a05-234">如果可連線到伺服器，則網路要求會成功。</span><span class="sxs-lookup"><span data-stu-id="c6a05-234">The network request succeeds if the server is reachable.</span></span> <span data-ttu-id="c6a05-235">此邏輯會在的函式內實作為 `onFetch` `service-worker.published.js` 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-235">This logic is implemented inside `onFetch` function within `service-worker.published.js`.</span></span>

<span data-ttu-id="c6a05-236">如果應用程式的 Razor 元件需要從後端 api 要求資料，而您想要為失敗的要求提供易記的使用者體驗（因為網路無法使用），請在應用程式的元件中執行邏輯。</span><span class="sxs-lookup"><span data-stu-id="c6a05-236">If the app's Razor components rely on requesting data from backend APIs and you want to provide a friendly user experience for failed requests due to network unavailability, implement logic within the app's components.</span></span> <span data-ttu-id="c6a05-237">例如，使用 `try/catch` 圍繞 <xref:System.Net.Http.HttpClient> 要求。</span><span class="sxs-lookup"><span data-stu-id="c6a05-237">For example, use `try/catch` around <xref:System.Net.Http.HttpClient> requests.</span></span>

### <a name="support-server-rendered-pages"></a><span data-ttu-id="c6a05-238">支援伺服器呈現的頁面</span><span class="sxs-lookup"><span data-stu-id="c6a05-238">Support server-rendered pages</span></span>

<span data-ttu-id="c6a05-239">考慮當使用者第一次流覽至 URL，例如 `/counter` 或應用程式中的任何其他深層連結時，會發生什麼事。</span><span class="sxs-lookup"><span data-stu-id="c6a05-239">Consider what happens when the user first navigates to a URL such as `/counter` or any other deep link in the app.</span></span> <span data-ttu-id="c6a05-240">在這些情況下，您不會想要傳回快取的內容 `/counter` ，而是需要瀏覽器載入快取的內容， `/index.html` 以啟動您的 Blazor WebAssembly 應用程式。</span><span class="sxs-lookup"><span data-stu-id="c6a05-240">In these cases, you don't want to return content cached as `/counter`, but instead need the browser to load the content cached as `/index.html` to start up your Blazor WebAssembly app.</span></span> <span data-ttu-id="c6a05-241">這些初始要求稱為 *流覽* 要求，而不是：</span><span class="sxs-lookup"><span data-stu-id="c6a05-241">These initial requests are known as *navigation* requests, as opposed to:</span></span>

* <span data-ttu-id="c6a05-242">`subresource` 影像、樣式表單或其他檔案的要求。</span><span class="sxs-lookup"><span data-stu-id="c6a05-242">`subresource` requests for images, stylesheets, or other files.</span></span>
* <span data-ttu-id="c6a05-243">`fetch/XHR` API 資料的要求。</span><span class="sxs-lookup"><span data-stu-id="c6a05-243">`fetch/XHR` requests for API data.</span></span>

<span data-ttu-id="c6a05-244">預設服務背景工作包含導覽要求的特殊案例邏輯。</span><span class="sxs-lookup"><span data-stu-id="c6a05-244">The default service worker contains special-case logic for navigation requests.</span></span> <span data-ttu-id="c6a05-245">無論要求的 URL 為何，服務工作者都會藉由傳回的快取內容來解析要求 `/index.html` 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-245">The service worker resolves the requests by returning the cached content for `/index.html`, regardless of the requested URL.</span></span> <span data-ttu-id="c6a05-246">此邏輯會在內部的函式中執行 `onFetch` `service-worker.published.js` 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-246">This logic is implemented in the `onFetch` function inside `service-worker.published.js`.</span></span>

<span data-ttu-id="c6a05-247">如果您的應用程式有某些 Url 必須傳回伺服器轉譯的 HTML，而不是從快取提供服務 `/index.html` ，則您需要編輯服務背景工作角色中的邏輯。</span><span class="sxs-lookup"><span data-stu-id="c6a05-247">If your app has certain URLs that must return server-rendered HTML, and not serve `/index.html` from the cache, then you need to edit the logic in your service worker.</span></span> <span data-ttu-id="c6a05-248">如果所有包含的 Url 都 `/Identity/` 需要以一般僅線上要求的方式處理到伺服器，則修改 `service-worker.published.js` `onFetch` 邏輯。</span><span class="sxs-lookup"><span data-stu-id="c6a05-248">If all URLs containing `/Identity/` need to be handled as regular online-only requests to the server, then modify `service-worker.published.js` `onFetch` logic.</span></span> <span data-ttu-id="c6a05-249">找出下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="c6a05-249">Locate the following code:</span></span>

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

<span data-ttu-id="c6a05-250">將程式碼變更為下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="c6a05-250">Change the code to the following:</span></span>

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
  && !event.request.url.includes('/Identity/');
```

<span data-ttu-id="c6a05-251">如果您沒有這麼做，則無論網路連線為何，服務工作者都會攔截這類 Url 的要求，並使用加以解析 `/index.html` 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-251">If you don't do this, then regardless of network connectivity, the service worker intercepts requests for such URLs and resolves them using `/index.html`.</span></span>

<span data-ttu-id="c6a05-252">將外部驗證提供者的其他端點新增至檢查。</span><span class="sxs-lookup"><span data-stu-id="c6a05-252">Add additional endpoints for external authentication providers to the check.</span></span> <span data-ttu-id="c6a05-253">在下列範例中， `/signin-google` 會在檢查中新增 Google 驗證：</span><span class="sxs-lookup"><span data-stu-id="c6a05-253">In the following example, `/signin-google` for Google authentication is added to the check:</span></span>

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
  && !event.request.url.includes('/Identity/')
  && !event.request.url.includes('/signin-google');
```

<span data-ttu-id="c6a05-254">開發環境不需要採取任何動作，因為一律會從網路提取內容。</span><span class="sxs-lookup"><span data-stu-id="c6a05-254">No action is required for the Development environment, where content is always fetched from the network.</span></span>

### <a name="control-asset-caching"></a><span data-ttu-id="c6a05-255">控制資產快取</span><span class="sxs-lookup"><span data-stu-id="c6a05-255">Control asset caching</span></span>

<span data-ttu-id="c6a05-256">如果您的專案定義 `ServiceWorkerAssetsManifest` MSBuild 屬性， Blazor 的組建工具會產生具有指定名稱的服務工作者資產資訊清單。</span><span class="sxs-lookup"><span data-stu-id="c6a05-256">If your project defines the `ServiceWorkerAssetsManifest` MSBuild property, Blazor's build tooling generates a service worker assets manifest with the specified name.</span></span> <span data-ttu-id="c6a05-257">預設 PWA 範本會產生包含下列屬性的專案檔：</span><span class="sxs-lookup"><span data-stu-id="c6a05-257">The default PWA template produces a project file containing the following property:</span></span>

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

<span data-ttu-id="c6a05-258">檔案會放在 `wwwroot` 輸出目錄中，因此瀏覽器可以要求取得此檔案 `/service-worker-assets.js` 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-258">The file is placed in the `wwwroot` output directory, so the browser can retrieve this file by requesting `/service-worker-assets.js`.</span></span> <span data-ttu-id="c6a05-259">若要查看這個檔案的內容，請 `/bin/Debug/{TARGET FRAMEWORK}/wwwroot/service-worker-assets.js` 在文字編輯器中開啟。</span><span class="sxs-lookup"><span data-stu-id="c6a05-259">To see the contents of this file, open `/bin/Debug/{TARGET FRAMEWORK}/wwwroot/service-worker-assets.js` in a text editor.</span></span> <span data-ttu-id="c6a05-260">不過，請勿編輯檔案，因為它會在每個組建上重新產生。</span><span class="sxs-lookup"><span data-stu-id="c6a05-260">However, don't edit the file, as it's regenerated on each build.</span></span>

<span data-ttu-id="c6a05-261">依預設，此資訊清單會列出：</span><span class="sxs-lookup"><span data-stu-id="c6a05-261">By default, this manifest lists:</span></span>

* <span data-ttu-id="c6a05-262">任何 Blazor 受管理的資源，例如 .net 元件，以及離線運作所需的 .Net WebAssembly 執行時間檔案。</span><span class="sxs-lookup"><span data-stu-id="c6a05-262">Any Blazor-managed resources, such as .NET assemblies and the .NET WebAssembly runtime files required to function offline.</span></span>
* <span data-ttu-id="c6a05-263">用於發佈至應用程式目錄的所有資源 `wwwroot` ，例如影像、樣式表單和 JavaScript 檔案，包括外部專案和 NuGet 套件所提供的靜態 web 資產。</span><span class="sxs-lookup"><span data-stu-id="c6a05-263">All resources for publishing to the app's `wwwroot` directory, such as images, stylesheets, and JavaScript files, including static web assets supplied by external projects and NuGet packages.</span></span>

<span data-ttu-id="c6a05-264">您可以藉由編輯中的邏輯，來控制服務工作者要提取和快取哪些資源 `onInstall` `service-worker.published.js` 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-264">You can control which of these resources are fetched and cached by the service worker by editing the logic in `onInstall` in `service-worker.published.js`.</span></span> <span data-ttu-id="c6a05-265">根據預設，服務背景工作會提取和快取符合一般 web 副檔名的檔案，例如 `.html` 、、 `.css` `.js` 和 `.wasm` ，以及 Blazor WebAssembly (、) 特有的檔案類型 `.dll` `.pdb` 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-265">By default, the service worker fetches and caches files matching typical web filename extensions such as `.html`, `.css`, `.js`, and `.wasm`, plus file types specific to Blazor WebAssembly (`.dll`, `.pdb`).</span></span>

<span data-ttu-id="c6a05-266">若要包含應用程式目錄中不存在的其他資源 `wwwroot` ，請定義額外的 MSBuild `ItemGroup` 專案，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="c6a05-266">To include additional resources that aren't present in the app's `wwwroot` directory, define extra MSBuild `ItemGroup` entries, as shown in the following example:</span></span>

```xml
<ItemGroup>
  <ServiceWorkerAssetsManifestItem Include="MyDirectory\AnotherFile.json"
    RelativePath="MyDirectory\AnotherFile.json" AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

<span data-ttu-id="c6a05-267">`AssetUrl`中繼資料會指定瀏覽器在提取要快取的資源時，應使用的基底相對 URL。</span><span class="sxs-lookup"><span data-stu-id="c6a05-267">The `AssetUrl` metadata specifies the base-relative URL that the browser should use when fetching the resource to cache.</span></span> <span data-ttu-id="c6a05-268">這可以獨立于其在磁片上的原始來原始檔案名。</span><span class="sxs-lookup"><span data-stu-id="c6a05-268">This can be independent of its original source file name on disk.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c6a05-269">新增 `ServiceWorkerAssetsManifestItem` 不會導致檔案在應用程式的目錄中發行 `wwwroot` 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-269">Adding a `ServiceWorkerAssetsManifestItem` doesn't cause the file to be published in the app's `wwwroot` directory.</span></span> <span data-ttu-id="c6a05-270">發佈輸出必須個別控制。</span><span class="sxs-lookup"><span data-stu-id="c6a05-270">The publish output must be controlled separately.</span></span> <span data-ttu-id="c6a05-271">`ServiceWorkerAssetsManifestItem`只會導致服務背景工作角色資訊清單中出現額外的專案。</span><span class="sxs-lookup"><span data-stu-id="c6a05-271">The `ServiceWorkerAssetsManifestItem` only causes an additional entry to appear in the service worker assets manifest.</span></span>

## <a name="push-notifications"></a><span data-ttu-id="c6a05-272">推送通知</span><span class="sxs-lookup"><span data-stu-id="c6a05-272">Push notifications</span></span>

<span data-ttu-id="c6a05-273">Pwa 與其他任何 PWA 一樣， Blazor WebAssembly 都可以接收來自後端伺服器的推播通知。</span><span class="sxs-lookup"><span data-stu-id="c6a05-273">Like any other PWA, a Blazor WebAssembly PWA can receive push notifications from a backend server.</span></span> <span data-ttu-id="c6a05-274">即使使用者未主動使用應用程式，伺服器也可以隨時傳送推播通知。</span><span class="sxs-lookup"><span data-stu-id="c6a05-274">The server can send push notifications at any time, even when the user isn't actively using the app.</span></span> <span data-ttu-id="c6a05-275">例如，您可以在不同的使用者執行相關動作時傳送推播通知。</span><span class="sxs-lookup"><span data-stu-id="c6a05-275">For example, push notifications can be sent when a different user performs a relevant action.</span></span>

<span data-ttu-id="c6a05-276">傳送推播通知的機制完全獨立于 Blazor WebAssembly ，因為它是由可以使用任何技術的後端伺服器所執行。</span><span class="sxs-lookup"><span data-stu-id="c6a05-276">The mechanism for sending a push notification is entirely independent of Blazor WebAssembly, since it's implemented by the backend server which can use any technology.</span></span> <span data-ttu-id="c6a05-277">如果您想要從 ASP.NET Core 伺服器傳送推播通知，請考慮 [使用類似于「進比薩」研討會所採用的方法](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications)。</span><span class="sxs-lookup"><span data-stu-id="c6a05-277">If you want to send push notifications from an ASP.NET Core server, consider [using a technique similar to the approach taken in the Blazing Pizza workshop](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications).</span></span>

<span data-ttu-id="c6a05-278">在用戶端上接收和顯示推播通知的機制也是獨立的 Blazor WebAssembly ，因為它是在服務工作者 JavaScript 檔案中執行。</span><span class="sxs-lookup"><span data-stu-id="c6a05-278">The mechanism for receiving and displaying a push notification on the client is also independent of Blazor WebAssembly, since it's implemented in the service worker JavaScript file.</span></span> <span data-ttu-id="c6a05-279">如需範例，請參閱適用 [于「中比薩」研討會的方法](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications)。</span><span class="sxs-lookup"><span data-stu-id="c6a05-279">For an example, see [the approach used in the Blazing Pizza workshop](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications).</span></span>

## <a name="caveats-for-offline-pwas"></a><span data-ttu-id="c6a05-280">離線 Pwa 的注意事項</span><span class="sxs-lookup"><span data-stu-id="c6a05-280">Caveats for offline PWAs</span></span>

<span data-ttu-id="c6a05-281">並非所有應用程式都應該嘗試支援離線使用。</span><span class="sxs-lookup"><span data-stu-id="c6a05-281">Not all apps should attempt to support offline use.</span></span> <span data-ttu-id="c6a05-282">離線支援會增加大量的複雜性，而不一定會與所需的使用案例相關。</span><span class="sxs-lookup"><span data-stu-id="c6a05-282">Offline support adds significant complexity, while not always being relevant for the use cases required.</span></span>

<span data-ttu-id="c6a05-283">離線支援通常僅適用于：</span><span class="sxs-lookup"><span data-stu-id="c6a05-283">Offline support is usually relevant only:</span></span>

* <span data-ttu-id="c6a05-284">如果主要資料存放區是瀏覽器的本機資料存放區。</span><span class="sxs-lookup"><span data-stu-id="c6a05-284">If the primary data store is local to the browser.</span></span> <span data-ttu-id="c6a05-285">例如，此方法與應用程式相關，該應用程式具有可將資料儲存在或 IndexedDB 之[IoT](https://en.wikipedia.org/wiki/Internet_of_things)裝置的 UI `localStorage` 。 [](https://developer.mozilla.org/docs/Web/API/IndexedDB_API)</span><span class="sxs-lookup"><span data-stu-id="c6a05-285">For example, the approach is relevant in an app with a UI for an [IoT](https://en.wikipedia.org/wiki/Internet_of_things) device that stores data in `localStorage` or [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API).</span></span>
* <span data-ttu-id="c6a05-286">如果應用程式執行大量的工作來提取和快取與每個使用者相關的後端 API 資料，讓他們可以離線流覽資料。</span><span class="sxs-lookup"><span data-stu-id="c6a05-286">If the app performs a significant amount of work to fetch and cache the backend API data relevant to each user so that they can navigate through the data offline.</span></span> <span data-ttu-id="c6a05-287">如果應用程式必須支援編輯，則必須建立用來追蹤變更的系統，並將資料與後端同步處理。</span><span class="sxs-lookup"><span data-stu-id="c6a05-287">If the app must support editing, a system for tracking changes and synchronizing data with the backend must be built.</span></span>
* <span data-ttu-id="c6a05-288">如果目標是要保證應用程式會立即載入，而不考慮網路狀況。</span><span class="sxs-lookup"><span data-stu-id="c6a05-288">If the goal is to guarantee that the app loads immediately regardless of network conditions.</span></span> <span data-ttu-id="c6a05-289">針對後端 API 要求執行適當的使用者體驗，以顯示要求的進度，並在要求因網路無法使用而失敗時正常運作。</span><span class="sxs-lookup"><span data-stu-id="c6a05-289">Implement a suitable user experience around backend API requests to show the progress of requests and behave gracefully when requests fail due to network unavailability.</span></span>

<span data-ttu-id="c6a05-290">此外，具備離線功能的 Pwa 必須處理一系列額外的複雜性。</span><span class="sxs-lookup"><span data-stu-id="c6a05-290">Additionally, offline-capable PWAs must deal with a range of additional complications.</span></span> <span data-ttu-id="c6a05-291">開發人員應謹慎熟悉下列各節中的注意事項。</span><span class="sxs-lookup"><span data-stu-id="c6a05-291">Developers should carefully familiarize themselves with the caveats in the following sections.</span></span>

### <a name="offline-support-only-when-published"></a><span data-ttu-id="c6a05-292">僅在發行時支援離線</span><span class="sxs-lookup"><span data-stu-id="c6a05-292">Offline support only when published</span></span>

<span data-ttu-id="c6a05-293">在開發期間，您通常會想要在瀏覽器中立即查看每項變更，而不需要進行背景更新程式。</span><span class="sxs-lookup"><span data-stu-id="c6a05-293">During development you typically want to see each change reflected immediately in the browser without going through a background update process.</span></span> <span data-ttu-id="c6a05-294">因此， Blazor 只有在發行時，的 PWA 範本才會啟用離線支援。</span><span class="sxs-lookup"><span data-stu-id="c6a05-294">Therefore, Blazor's PWA template enables offline support only when published.</span></span>

<span data-ttu-id="c6a05-295">建立具備離線功能的應用程式時，在開發環境中測試應用程式不夠。</span><span class="sxs-lookup"><span data-stu-id="c6a05-295">When building an offline-capable app, it's not enough to test the app in the Development environment.</span></span> <span data-ttu-id="c6a05-296">您必須在應用程式的 [已發佈] 狀態中測試應用程式，以瞭解它如何回應不同的網路狀況。</span><span class="sxs-lookup"><span data-stu-id="c6a05-296">You must test the app in its published state to understand how it responds to different network conditions.</span></span>

### <a name="update-completion-after-user-navigation-away-from-app"></a><span data-ttu-id="c6a05-297">在使用者導覽離開應用程式之後更新完成</span><span class="sxs-lookup"><span data-stu-id="c6a05-297">Update completion after user navigation away from app</span></span>

<span data-ttu-id="c6a05-298">在使用者離開所有索引標籤中的應用程式之前，不會完成更新。</span><span class="sxs-lookup"><span data-stu-id="c6a05-298">Updates don't complete until the user has navigated away from the app in all tabs.</span></span> <span data-ttu-id="c6a05-299">如同 [ [背景更新](#background-updates) ] 區段中所述，在您將更新部署到應用程式之後，瀏覽器會提取更新的服務工作者檔案，以開始更新程式。</span><span class="sxs-lookup"><span data-stu-id="c6a05-299">As explained in the [Background updates](#background-updates) section, after you deploy an update to the app, the browser fetches the updated service worker files to begin the update process.</span></span>

<span data-ttu-id="c6a05-300">許多開發人員會發現，即使這項更新完成，在使用者離開所有索引標籤之後，它 **才會生效** 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-300">What surprises many developers is that, even when this update completes, it does **not** take effect until the user has navigated away in all tabs.</span></span> <span data-ttu-id="c6a05-301">重新整理顯示應用程式的索引標籤並 **不** 足夠，即使它是顯示應用程式的唯一索引標籤。</span><span class="sxs-lookup"><span data-stu-id="c6a05-301">It is **not** sufficient to refresh the tab displaying the app, even if it's the only tab displaying the app.</span></span> <span data-ttu-id="c6a05-302">在您的應用程式完全關閉之前，新的服務工作者仍會處於等候中的 *啟動* 狀態。</span><span class="sxs-lookup"><span data-stu-id="c6a05-302">Until your app is completely closed, the new service worker remains in the *waiting to activate* status.</span></span> <span data-ttu-id="c6a05-303">**這並非特有的，而是 Blazor 標準的 web 平臺行為。**</span><span class="sxs-lookup"><span data-stu-id="c6a05-303">**This is not specific to Blazor, but rather is a standard web platform behavior.**</span></span>

<span data-ttu-id="c6a05-304">這通常會麻煩很快找上門嘗試測試其服務工作者或離線快取資源更新的開發人員。</span><span class="sxs-lookup"><span data-stu-id="c6a05-304">This commonly troubles developers who are trying to test updates to their service worker or offline cached resources.</span></span> <span data-ttu-id="c6a05-305">如果您簽入瀏覽器的開發人員工具，您可能會看到類似下列的內容：</span><span class="sxs-lookup"><span data-stu-id="c6a05-305">If you check in the browser's developer tools, you may see something like the following:</span></span>

![Google Chrome 的 [應用程式] 索引標籤會顯示應用程式的服務工作者為「正在等待啟動」。](progressive-web-app/_static/image7.png)

<span data-ttu-id="c6a05-307">只要「用戶端」清單（即顯示您應用程式的索引標籤或視窗）是空的，背景工作角色就會繼續等候。</span><span class="sxs-lookup"><span data-stu-id="c6a05-307">For as long as the list of "clients," which are tabs or windows displaying your app, is nonempty, the worker continues waiting.</span></span> <span data-ttu-id="c6a05-308">服務工作者這麼做的原因是要保證一致性。</span><span class="sxs-lookup"><span data-stu-id="c6a05-308">The reason service workers do this is to guarantee consistency.</span></span> <span data-ttu-id="c6a05-309">一致性表示所有資源都是從相同的不可部分完成的快取中提取。</span><span class="sxs-lookup"><span data-stu-id="c6a05-309">Consistency means that all resources are fetched from the same atomic cache.</span></span>

<span data-ttu-id="c6a05-310">測試變更時，如先前的螢幕擷取畫面所示，按一下 [skipWaiting] 連結會很方便，然後重載頁面。</span><span class="sxs-lookup"><span data-stu-id="c6a05-310">When testing changes, you may find it convenient to click the "skipWaiting" link as shown in the preceding screenshot, then reload the page.</span></span> <span data-ttu-id="c6a05-311">您可以為您的服務背景程式撰寫程式碼，以 [略過「等待中」階段並立即啟用更新，](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase)為所有使用者自動執行此工作。</span><span class="sxs-lookup"><span data-stu-id="c6a05-311">You can automate this for all users by coding your service worker to [skip the "waiting" phase and immediately activate on update](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase).</span></span> <span data-ttu-id="c6a05-312">如果您略過等待階段，您就能保證一律會從相同的快取實例一致地提取資源。</span><span class="sxs-lookup"><span data-stu-id="c6a05-312">If you skip the waiting phase, you're giving up the guarantee that resources are always fetched consistently from the same cache instance.</span></span>

### <a name="users-may-run-any-historical-version-of-the-app"></a><span data-ttu-id="c6a05-313">使用者可以執行應用程式的任何歷程記錄版本</span><span class="sxs-lookup"><span data-stu-id="c6a05-313">Users may run any historical version of the app</span></span>

<span data-ttu-id="c6a05-314">Web 開發人員 habitually 預期使用者只會執行其 web 應用程式的最新部署版本，因為這在傳統的 web 散發模型內是正常的。</span><span class="sxs-lookup"><span data-stu-id="c6a05-314">Web developers habitually expect that users only run the latest deployed version of their web app, since that's normal within the traditional web distribution model.</span></span> <span data-ttu-id="c6a05-315">不過，離線優先的 PWA 更類似于原生行動應用程式，使用者不一定會執行最新版本。</span><span class="sxs-lookup"><span data-stu-id="c6a05-315">However, an offline-first PWA is more akin to a native mobile app, where users aren't necessarily running the latest version.</span></span>

<span data-ttu-id="c6a05-316">如在 [背景更新](#background-updates) 一節中所述，在您將更新部署到您的應用程式之後， **每個現有的使用者都會繼續使用先前的版本來進行至少一次造訪** ，因為更新會在背景中執行，而且在使用者之後離開之前不會啟動。</span><span class="sxs-lookup"><span data-stu-id="c6a05-316">As explained in the [Background updates](#background-updates) section, after you deploy an update to your app, **each existing user continues to use a previous version for at least one further visit** because the update occurs in the background and isn't activated until the user thereafter navigates away.</span></span> <span data-ttu-id="c6a05-317">此外，先前使用的版本不一定是您先前部署的版本。</span><span class="sxs-lookup"><span data-stu-id="c6a05-317">Plus, the previous version being used isn't necessarily the previous one you deployed.</span></span> <span data-ttu-id="c6a05-318">先前的版本可以是 *任何* 歷程記錄版本，取決於使用者上次完成更新的時間。</span><span class="sxs-lookup"><span data-stu-id="c6a05-318">The previous version can be *any* historical version, depending on when the user last completed an update.</span></span>

<span data-ttu-id="c6a05-319">如果您應用程式的前端和後端部分需要 API 要求架構的合約，這可能會是個問題。</span><span class="sxs-lookup"><span data-stu-id="c6a05-319">This can be an issue if the frontend and backend parts of your app require agreement about the schema for API requests.</span></span> <span data-ttu-id="c6a05-320">除非您確定所有使用者都已升級，否則您不能部署後向不相容的 API 架構變更。</span><span class="sxs-lookup"><span data-stu-id="c6a05-320">You must not deploy backward-incompatible API schema changes until you can be sure that all users have upgraded.</span></span> <span data-ttu-id="c6a05-321">或者，封鎖使用者使用不相容的繼承應用程式。</span><span class="sxs-lookup"><span data-stu-id="c6a05-321">Alternatively, block users from using incompatible older versions of the app.</span></span> <span data-ttu-id="c6a05-322">此案例需求與原生行動應用程式的需求相同。</span><span class="sxs-lookup"><span data-stu-id="c6a05-322">This scenario requirement is the same as for native mobile apps.</span></span> <span data-ttu-id="c6a05-323">如果您在伺服器 Api 中部署中斷性變更，用戶端應用程式會中斷尚未更新的使用者。</span><span class="sxs-lookup"><span data-stu-id="c6a05-323">If you deploy a breaking change in server APIs, the client app is broken for users who haven't yet updated.</span></span>

<span data-ttu-id="c6a05-324">可能的話，請勿將重大變更部署至您的後端 Api。</span><span class="sxs-lookup"><span data-stu-id="c6a05-324">If possible, don't deploy breaking changes to your backend APIs.</span></span> <span data-ttu-id="c6a05-325">如果您必須這樣做，請考慮使用 [標準服務工作者 api （例如 ServiceWorkerRegistration](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) ）來判斷應用程式是否為最新狀態，如果不是，則避免使用。</span><span class="sxs-lookup"><span data-stu-id="c6a05-325">If you must do so, consider using [standard Service Worker APIs such as ServiceWorkerRegistration](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) to determine whether the app is up-to-date, and if not, to prevent usage.</span></span>

### <a name="interference-with-server-rendered-pages"></a><span data-ttu-id="c6a05-326">伺服器轉譯頁面的干擾</span><span class="sxs-lookup"><span data-stu-id="c6a05-326">Interference with server-rendered pages</span></span>

<span data-ttu-id="c6a05-327">如「 [支援伺服器轉譯頁面](#support-server-rendered-pages) 」一節所述，如果您想要略過服務工作者 `/index.html` 針對所有導覽要求傳回內容的行為，請編輯您的服務背景工作角色中的邏輯。</span><span class="sxs-lookup"><span data-stu-id="c6a05-327">As described in the [Support server-rendered pages](#support-server-rendered-pages) section, if you want to bypass the service worker's behavior of returning `/index.html` contents for all navigation requests, edit the logic in your service worker.</span></span>

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a><span data-ttu-id="c6a05-328">預設會快取所有服務工作者資產資訊清單內容</span><span class="sxs-lookup"><span data-stu-id="c6a05-328">All service worker asset manifest contents are cached by default</span></span>

<span data-ttu-id="c6a05-329">如「 [控制資產](#control-asset-caching) 快取」一節所述，檔案 `service-worker-assets.js` 會在組建期間產生，並且會列出服務工作者應該提取和快取的所有資產。</span><span class="sxs-lookup"><span data-stu-id="c6a05-329">As described in the [Control asset caching](#control-asset-caching) section, the file `service-worker-assets.js` is generated during build and lists all assets the service worker should fetch and cache.</span></span>

<span data-ttu-id="c6a05-330">由於此清單預設包含發出的所有專案 `wwwroot` ，包括外部封裝和專案所提供的內容，因此您必須小心不要在該處放入太多內容。</span><span class="sxs-lookup"><span data-stu-id="c6a05-330">Since this list by default includes everything emitted to `wwwroot`, including content supplied by external packages and projects, you must be careful not to put too much content there.</span></span> <span data-ttu-id="c6a05-331">如果 `wwwroot` 目錄包含數百萬個影像，服務工作者會嘗試提取並快取它們，耗用過度的頻寬且很可能無法順利完成。</span><span class="sxs-lookup"><span data-stu-id="c6a05-331">If the `wwwroot` directory contains millions of images, the service worker tries to fetch and cache them all, consuming excessive bandwidth and most likely not completing successfully.</span></span>

<span data-ttu-id="c6a05-332">藉由編輯中的函式來執行任意邏輯，以控制應提取和快取的資訊清單內容子集 `onInstall` `service-worker.published.js` 。</span><span class="sxs-lookup"><span data-stu-id="c6a05-332">Implement arbitrary logic to control which subset of the manifest's contents should be fetched and cached by editing the `onInstall` function in `service-worker.published.js`.</span></span>

### <a name="interaction-with-authentication"></a><span data-ttu-id="c6a05-333">與驗證互動</span><span class="sxs-lookup"><span data-stu-id="c6a05-333">Interaction with authentication</span></span>

<span data-ttu-id="c6a05-334">PWA 範本可以與驗證搭配使用。</span><span class="sxs-lookup"><span data-stu-id="c6a05-334">The PWA template can be used in conjunction with authentication.</span></span> <span data-ttu-id="c6a05-335">當使用者具有初始網路連線能力時，支援離線的 PWA 也可以支援驗證。</span><span class="sxs-lookup"><span data-stu-id="c6a05-335">An offline-capable PWA can also support authentication when the user has initial network connectivity.</span></span>

<span data-ttu-id="c6a05-336">當使用者沒有網路連線時，他們無法驗證或取得存取權杖。</span><span class="sxs-lookup"><span data-stu-id="c6a05-336">When a user doesn't have network connectivity, they can't authenticate or obtain access tokens.</span></span> <span data-ttu-id="c6a05-337">依預設，嘗試在沒有網路存取的情況下流覽登入頁面會導致「網路錯誤」訊息。</span><span class="sxs-lookup"><span data-stu-id="c6a05-337">By default, attempting to visit the login page without network access results in a "network error" message.</span></span> <span data-ttu-id="c6a05-338">您必須設計 UI 流程，讓使用者可以在離線時執行有用的工作，而不需要嘗試驗證使用者或取得存取權杖。</span><span class="sxs-lookup"><span data-stu-id="c6a05-338">You must design a UI flow that allows the user perform useful tasks while offline without attempting to authenticate the user or obtain access tokens.</span></span> <span data-ttu-id="c6a05-339">或者，您可以在網路無法使用時，將應用程式設計成可正常地失敗。</span><span class="sxs-lookup"><span data-stu-id="c6a05-339">Alternatively, you can design the app to gracefully fail when the network isn't available.</span></span> <span data-ttu-id="c6a05-340">如果應用程式無法設計來處理這些案例，您可能不會想要啟用離線支援。</span><span class="sxs-lookup"><span data-stu-id="c6a05-340">If the app can't be designed to handle these scenarios, you might not want to enable offline support.</span></span>

<span data-ttu-id="c6a05-341">當針對線上和離線使用而設計的應用程式再次上線時：</span><span class="sxs-lookup"><span data-stu-id="c6a05-341">When an app that's designed for online and offline use is online again:</span></span>

* <span data-ttu-id="c6a05-342">應用程式可能需要布建新的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="c6a05-342">The app might need to provision a new access token.</span></span>
* <span data-ttu-id="c6a05-343">應用程式必須偵測是否有不同的使用者登入服務，讓它可以將作業套用至使用者在離線時所做的帳戶。</span><span class="sxs-lookup"><span data-stu-id="c6a05-343">The app must detect if a different user is signed into the service so that it can apply operations to the user's account that were made while they were offline.</span></span>

<span data-ttu-id="c6a05-344">若要建立與驗證互動的離線 PWA 應用程式：</span><span class="sxs-lookup"><span data-stu-id="c6a05-344">To create an offline PWA app that interacts with authentication:</span></span>

* <span data-ttu-id="c6a05-345">將取代為 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> 儲存最後登入使用者的處理站，並在應用程式離線時使用已儲存的使用者。</span><span class="sxs-lookup"><span data-stu-id="c6a05-345">Replace the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> with a factory that stores the last signed-in user and uses the stored user when the app is offline.</span></span>
* <span data-ttu-id="c6a05-346">應用程式離線時的佇列作業，並在應用程式回到線上時套用它們。</span><span class="sxs-lookup"><span data-stu-id="c6a05-346">Queue operations while the app is offline and apply them when the app returns online.</span></span>
* <span data-ttu-id="c6a05-347">登出時，請清除儲存的使用者。</span><span class="sxs-lookup"><span data-stu-id="c6a05-347">During sign out, clear the stored user.</span></span>

<span data-ttu-id="c6a05-348">[`CarChecker`](https://github.com/SteveSandersonMS/CarChecker)範例應用程式會示範上述方法。</span><span class="sxs-lookup"><span data-stu-id="c6a05-348">The [`CarChecker`](https://github.com/SteveSandersonMS/CarChecker) sample app demonstrates the preceding approaches.</span></span> <span data-ttu-id="c6a05-349">請參閱應用程式的下列部分：</span><span class="sxs-lookup"><span data-stu-id="c6a05-349">See the following parts of the app:</span></span>

* <span data-ttu-id="c6a05-350">`OfflineAccountClaimsPrincipalFactory` (`Client/Data/OfflineAccountClaimsPrincipalFactory.cs`)</span><span class="sxs-lookup"><span data-stu-id="c6a05-350">`OfflineAccountClaimsPrincipalFactory` (`Client/Data/OfflineAccountClaimsPrincipalFactory.cs`)</span></span>
* <span data-ttu-id="c6a05-351">`LocalVehiclesStore` (`Client/Data/LocalVehiclesStore.cs`)</span><span class="sxs-lookup"><span data-stu-id="c6a05-351">`LocalVehiclesStore` (`Client/Data/LocalVehiclesStore.cs`)</span></span>
* <span data-ttu-id="c6a05-352">`LoginStatus` 元件 (`Client/Shared/LoginStatus.razor`) </span><span class="sxs-lookup"><span data-stu-id="c6a05-352">`LoginStatus` component (`Client/Shared/LoginStatus.razor`)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c6a05-353">其他資源</span><span class="sxs-lookup"><span data-stu-id="c6a05-353">Additional resources</span></span>

* [<span data-ttu-id="c6a05-354">針對完整性 PowerShell 腳本進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="c6a05-354">Troubleshoot integrity PowerShell script</span></span>](xref:blazor/host-and-deploy/webassembly#troubleshoot-integrity-powershell-script)
* [<span data-ttu-id="c6a05-355">SignalR 驗證的跨原始來源協商</span><span class="sxs-lookup"><span data-stu-id="c6a05-355">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
