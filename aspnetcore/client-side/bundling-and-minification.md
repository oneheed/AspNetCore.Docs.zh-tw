---
title: 在 ASP.NET Core 中組合和縮短靜態資產
author: scottaddie
description: 瞭解如何藉由套用配套和縮制技術，將 ASP.NET Core web 應用程式中的靜態資源優化。
ms.author: scaddie
ms.custom: mvc
ms.date: 09/02/2020
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
uid: client-side/bundling-and-minification
ms.openlocfilehash: f696df0b421e5aab6f50cfaec3ca8edac894cea9
ms.sourcegitcommit: c9b03d8a6a4dcc59e4aacb30a691f349235a74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379389"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>在 ASP.NET Core 中組合和縮短靜態資產

[Scott Addie](https://twitter.com/Scott_Addie)和[David 松樹](https://twitter.com/davidpine7)

本文說明套用配套和縮制的優點，包括這些功能如何搭配 ASP.NET Core 的 web 應用程式來使用。

## <a name="what-is-bundling-and-minification"></a>什麼是組合和縮制

組合和縮制是您可以在 web 應用程式中套用的兩個不同的效能優化。 結合和縮制會藉由減少伺服器要求數目和減少要求的靜態資產的大小，來改善效能。

組合和縮制主要會改善第一頁要求載入時間。 一旦要求網頁之後，瀏覽器會將靜態資產快取 (JavaScript、CSS 和影像) 。 因此，在要求相同資產的相同網站上要求相同頁面或頁面時，組合和縮制並不會改善效能。 如果資產上的 expires 標頭未正確設定，而且未使用組合和縮制，則瀏覽器的有效啟發學習會在幾天後標記為過時的資產。 此外，瀏覽器需要針對每個資產進行驗證要求。 在此情況下，即使在第一頁要求之後，組合和縮制也可提供效能改進。

### <a name="bundling"></a>捆綁

統合可將多個檔案合併成單一檔案。 組合可減少轉譯 web 資產所需的伺服器要求數目，例如網頁。 您可以針對 CSS、JavaScript 等建立任意數目的個別套件組合。較少的檔案表示從瀏覽器到伺服器的 HTTP 要求較少，或從提供您應用程式的服務。 這會導致第一個頁面載入效能提高。

### <a name="minification"></a>縮製

縮制會從程式碼移除不必要的字元，而不會改變功能。 結果會大幅減少要求的資產 (例如 CSS、影像和 JavaScript 檔案) 。 縮制的常見副作用包括將變數名稱縮短為一個字元，以及移除批註和不必要的空白字元。

請考慮下列 JavaScript 函數：

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

縮制會將函數縮減為下列內容：

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

除了移除批註和不必要的空白字元之外，下列參數和變數名稱已重新命名如下：

原始 | 已重新命名
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>組合和縮制的影響

下表概述個別載入資產與使用包裝和縮制之間的差異：

動作 | 使用 B/M | 沒有 B/M | 變更
--- | :---: | :---: | :---:
檔案要求  | 7   | 18     | 157%
已傳送 KB | 156 | 264.68 | 70%
載入時間 (ms)  | 885 | 2360   | 167%

對 HTTP 要求標頭而言，瀏覽器相當詳細。 「傳送的位元組總數」計量在組合時看到明顯的縮減。 載入時間顯示大幅改進，不過這個範例是在本機執行。 在透過網路傳輸的資產使用包裝和縮制時，可實現更佳的效能提升。

## <a name="choose-a-bundling-and-minification-strategy"></a>選擇捆綁和縮制策略

MVC 和 Razor Pages 專案範本提供了由 JSON 設定檔群組成的組合和縮制解決方案。 協力廠商工具（例如 [Grunt](xref:client-side/using-grunt) 工作執行器）會稍微複雜一點，才能完成相同的工作。 當您的開發工作流程需要除了組合和縮制 &mdash; （例如 linting 和影像優化）之外的處理時，協力廠商工具相當適合。 藉由使用設計階段組合和縮制，縮減檔會在應用程式部署之前建立。 在部署之前進行包裝和縮小，可提供降低伺服器負載的優點。 不過，請務必瞭解設計階段組合和縮制會增加組建複雜性，而且只適用于靜態檔案。

## <a name="configure-bundling-and-minification"></a>設定捆綁和縮制

> [!NOTE]
> [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet 套件必須新增至您的專案，才能運作。

::: moniker range="<= aspnetcore-2.0"

在 ASP.NET Core 2.0 或更早版本中，MVC 和 Razor Pages 專案範本會提供設定檔案的 *bundleconfig.js* ，以定義每個套件組合的選項：

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

在 ASP.NET Core 2.1 或更新版本中，將名為 *bundleconfig.js*的新 JSON 檔案新增至 MVC 或 Razor Pages 專案根目錄。 在該檔案中包含下列 JSON 作為起點：

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

檔案 * 上的bundleconfig.js* 會定義每個組合的選項。 在上述範例中，會為自訂 JavaScript (*wwwroot/js/site.js*) 和樣式表單定義單一套件組合， (*wwwroot/css/網站 .css*) 檔案。

設定選項包括：

* `outputFileName`：要輸出的組合檔案名稱。 可以包含檔案 *bundleconfig.js* 的相對路徑。 **必填**
* `inputFiles`：要一起組合的檔案陣列。 這些是設定檔的相對路徑。 （**選擇性**） * 空白值會產生空白的輸出檔。 支援[萬用字元模式。](https://www.tldp.org/LDP/abs/html/globbingref.html)
* `minify`：輸出型別的縮制選項。 **選用**，*預設值 `minify: { enabled: true }` -*
  * 每個輸出檔案類型都可以使用設定選項。
    * [CSS Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [JavaScript Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [HTML Minifier](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`：旗標，指出是否要將產生的檔案加入至專案檔。 **選用**， *預設值-false*
* `sourceMap`：旗標，指出是否要產生配套檔案的來源對應。 **選用**， *預設值-false*
* `sourceMapRootPath`：用來儲存所產生之來源對應檔的根路徑。

## <a name="add-files-to-workflow"></a>將檔案新增至工作流程

假設有一個範例，其中加入了類似下列的其他 *自訂 .css* 檔案：

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

若要縮短 *自訂 .css* ，並將 *其與 node.js* 配套到 *網站* .css 檔案中，請新增 *bundleconfig.js*的相對路徑：

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> 或者，您可以使用下列萬用字元模式：
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> 此萬用字元模式會比對所有 CSS 檔案，並排除縮減檔案模式。

建置應用程式。 開啟 [ *.css* ]，並注意 *自訂 .css* 的內容會附加至檔案結尾。

## <a name="environment-based-bundling-and-minification"></a>以環境為基礎的組合和縮制

最佳做法是，應用程式的配套和縮減檔案應該用於生產環境。 在開發期間，原始檔案可讓您更輕鬆地對應用程式進行偵錯工具。

使用您的視圖中的 [環境](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) 標籤協助程式，指定要包含在頁面中的檔案。 環境標籤協助程式只會在特定 [環境](xref:fundamentals/environments)中執行時呈現其內容。

在 `environment` 環境中執行時，下列標記會呈現未處理的 CSS 檔案 `Development` ：

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

在以外的 `environment` 環境中執行時，下列標記會呈現配套和縮減的 CSS 檔案 `Development` 。 例如，在中 `Production` 執行或會 `Staging` 觸發這些樣式表單的轉譯：

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>從 Gulp 使用 bundleconfig.js

在某些情況下，應用程式的包裝和縮制工作流程需要額外的處理。 範例包括映射優化、快取破壞和 CDN 資產處理。 為了滿足這些需求，您可以將包裝和縮制工作流程轉換為使用 Gulp。

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a>手動將組合和縮制工作流程轉換為使用 Gulp

使用下列內容將 *package.js* 新增 `devDependencies` 至專案根目錄：

> [!WARNING]
> `gulp-uglify`模組不支援 ECMAScript (ES) 2015/ES6 和更新版本。 安裝 [gulp-terser](https://www.npmjs.com/package/gulp-terser) 而不是 `gulp-uglify` 使用 ES2015/ES6 或更新版本。

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

在與package.js的相同層級 * 上*執行下列命令，以安裝相依性：

```bash
npm i
```

將 Gulp CLI 安裝為全域相依性：

```bash
npm i -g gulp-cli
```

將以下的 *gulpfile.js* 檔案複製到專案根目錄：

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>執行 Gulp 工作

在 Visual Studio 中建立專案之前，觸發 Gulp 縮制工作：

1. 安裝 [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet 套件。
1. 將下列 [MSBuild 目標](/visualstudio/msbuild/msbuild-targets) 新增至專案檔：

    [!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

在此範例中，在目標內定義的所有工作都會在 `MyPreCompileTarget` 預先定義的目標之前執行 `Build` 。 Visual Studio 的輸出視窗中會出現類似下列的輸出：

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

## <a name="additional-resources"></a>其他資源

* [使用 Grunt](xref:client-side/using-grunt)
* [使用多重環境](xref:fundamentals/environments)
* [標籤協助程式](xref:mvc/views/tag-helpers/intro)
