---
title: 在 ASP.NET Core 中組合和縮短靜態資產
author: scottaddie
description: 瞭解如何藉由套用配套和縮制技術，將 ASP.NET Core web 應用程式中的靜態資源優化。
ms.author: scaddie
ms.custom: mvc
ms.date: 07/23/2020
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
ms.openlocfilehash: 84123464e8f01f8a3caa65035b3174cc04aea7cf
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625852"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="5cdf3-103">在 ASP.NET Core 中組合和縮短靜態資產</span><span class="sxs-lookup"><span data-stu-id="5cdf3-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="5cdf3-104">[Scott Addie](https://twitter.com/Scott_Addie)和[David 松樹](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="5cdf3-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="5cdf3-105">本文說明套用配套和縮制的優點，包括這些功能如何搭配 ASP.NET Core 的 web 應用程式來使用。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="5cdf3-106">什麼是組合和縮制</span><span class="sxs-lookup"><span data-stu-id="5cdf3-106">What is bundling and minification</span></span>

<span data-ttu-id="5cdf3-107">組合和縮制是您可以在 web 應用程式中套用的兩個不同的效能優化。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="5cdf3-108">結合和縮制會藉由減少伺服器要求數目和減少要求的靜態資產的大小，來改善效能。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="5cdf3-109">組合和縮制主要會改善第一頁要求載入時間。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="5cdf3-110">一旦要求網頁之後，瀏覽器會將靜態資產快取 (JavaScript、CSS 和影像) 。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="5cdf3-111">因此，在要求相同資產的相同網站上要求相同頁面或頁面時，組合和縮制並不會改善效能。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="5cdf3-112">如果資產上的 expires 標頭未正確設定，而且未使用組合和縮制，則瀏覽器的有效啟發學習會在幾天後標記為過時的資產。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="5cdf3-113">此外，瀏覽器需要針對每個資產進行驗證要求。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="5cdf3-114">在此情況下，即使在第一頁要求之後，組合和縮制也可提供效能改進。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="5cdf3-115">捆綁</span><span class="sxs-lookup"><span data-stu-id="5cdf3-115">Bundling</span></span>

<span data-ttu-id="5cdf3-116">統合可將多個檔案合併成單一檔案。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="5cdf3-117">組合可減少轉譯 web 資產所需的伺服器要求數目，例如網頁。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="5cdf3-118">您可以針對 CSS、JavaScript 等建立任意數目的個別套件組合。較少的檔案表示從瀏覽器到伺服器的 HTTP 要求較少，或從提供您應用程式的服務。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="5cdf3-119">這會導致第一個頁面載入效能提高。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="5cdf3-120">縮製</span><span class="sxs-lookup"><span data-stu-id="5cdf3-120">Minification</span></span>

<span data-ttu-id="5cdf3-121">縮制會從程式碼移除不必要的字元，而不會改變功能。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="5cdf3-122">結果會大幅減少要求的資產 (例如 CSS、影像和 JavaScript 檔案) 。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="5cdf3-123">縮制的常見副作用包括將變數名稱縮短為一個字元，以及移除批註和不必要的空白字元。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="5cdf3-124">請考慮下列 JavaScript 函數：</span><span class="sxs-lookup"><span data-stu-id="5cdf3-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="5cdf3-125">縮制會將函數縮減為下列內容：</span><span class="sxs-lookup"><span data-stu-id="5cdf3-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="5cdf3-126">除了移除批註和不必要的空白字元之外，下列參數和變數名稱已重新命名如下：</span><span class="sxs-lookup"><span data-stu-id="5cdf3-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="5cdf3-127">原始</span><span class="sxs-lookup"><span data-stu-id="5cdf3-127">Original</span></span> | <span data-ttu-id="5cdf3-128">已重新命名</span><span class="sxs-lookup"><span data-stu-id="5cdf3-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="5cdf3-129">組合和縮制的影響</span><span class="sxs-lookup"><span data-stu-id="5cdf3-129">Impact of bundling and minification</span></span>

<span data-ttu-id="5cdf3-130">下表概述個別載入資產與使用包裝和縮制之間的差異：</span><span class="sxs-lookup"><span data-stu-id="5cdf3-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="5cdf3-131">動作</span><span class="sxs-lookup"><span data-stu-id="5cdf3-131">Action</span></span> | <span data-ttu-id="5cdf3-132">使用 B/M</span><span class="sxs-lookup"><span data-stu-id="5cdf3-132">With B/M</span></span> | <span data-ttu-id="5cdf3-133">沒有 B/M</span><span class="sxs-lookup"><span data-stu-id="5cdf3-133">Without B/M</span></span> | <span data-ttu-id="5cdf3-134">變更</span><span class="sxs-lookup"><span data-stu-id="5cdf3-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="5cdf3-135">檔案要求</span><span class="sxs-lookup"><span data-stu-id="5cdf3-135">File Requests</span></span>  | <span data-ttu-id="5cdf3-136">7</span><span class="sxs-lookup"><span data-stu-id="5cdf3-136">7</span></span>   | <span data-ttu-id="5cdf3-137">18</span><span class="sxs-lookup"><span data-stu-id="5cdf3-137">18</span></span>     | <span data-ttu-id="5cdf3-138">157%</span><span class="sxs-lookup"><span data-stu-id="5cdf3-138">157%</span></span>
<span data-ttu-id="5cdf3-139">已傳送 KB</span><span class="sxs-lookup"><span data-stu-id="5cdf3-139">KB Transferred</span></span> | <span data-ttu-id="5cdf3-140">156</span><span class="sxs-lookup"><span data-stu-id="5cdf3-140">156</span></span> | <span data-ttu-id="5cdf3-141">264.68</span><span class="sxs-lookup"><span data-stu-id="5cdf3-141">264.68</span></span> | <span data-ttu-id="5cdf3-142">70%</span><span class="sxs-lookup"><span data-stu-id="5cdf3-142">70%</span></span>
<span data-ttu-id="5cdf3-143">載入時間 (ms) </span><span class="sxs-lookup"><span data-stu-id="5cdf3-143">Load Time (ms)</span></span> | <span data-ttu-id="5cdf3-144">885</span><span class="sxs-lookup"><span data-stu-id="5cdf3-144">885</span></span> | <span data-ttu-id="5cdf3-145">2360</span><span class="sxs-lookup"><span data-stu-id="5cdf3-145">2360</span></span>   | <span data-ttu-id="5cdf3-146">167%</span><span class="sxs-lookup"><span data-stu-id="5cdf3-146">167%</span></span>

<span data-ttu-id="5cdf3-147">對 HTTP 要求標頭而言，瀏覽器相當詳細。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="5cdf3-148">「傳送的位元組總數」計量在組合時看到明顯的縮減。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="5cdf3-149">載入時間顯示大幅改進，不過這個範例是在本機執行。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="5cdf3-150">在透過網路傳輸的資產使用包裝和縮制時，可實現更佳的效能提升。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="5cdf3-151">選擇捆綁和縮制策略</span><span class="sxs-lookup"><span data-stu-id="5cdf3-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="5cdf3-152">MVC 和 Razor Pages 專案範本提供了由 JSON 設定檔群組成的組合和縮制解決方案。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-152">The MVC and Razor Pages project templates provide a solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="5cdf3-153">協力廠商工具（例如 [Grunt](xref:client-side/using-grunt) 工作執行器）會稍微複雜一點，才能完成相同的工作。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="5cdf3-154">當您的開發工作流程需要除了組合和縮制 &mdash; （例如 linting 和影像優化）之外的處理時，協力廠商工具相當適合。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="5cdf3-155">藉由使用設計階段組合和縮制，縮減檔會在應用程式部署之前建立。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="5cdf3-156">在部署之前進行包裝和縮小，可提供降低伺服器負載的優點。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="5cdf3-157">不過，請務必瞭解設計階段組合和縮制會增加組建複雜性，而且只適用于靜態檔案。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="5cdf3-158">設定捆綁和縮制</span><span class="sxs-lookup"><span data-stu-id="5cdf3-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="5cdf3-159">在 ASP.NET Core 2.0 或更早版本中，MVC 和 Razor Pages 專案範本會提供設定檔案的 *bundleconfig.js* ，以定義每個套件組合的選項：</span><span class="sxs-lookup"><span data-stu-id="5cdf3-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="5cdf3-160">在 ASP.NET Core 2.1 或更新版本中，將名為 *bundleconfig.js*的新 JSON 檔案新增至 MVC 或 Razor Pages 專案根目錄。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="5cdf3-161">在該檔案中包含下列 JSON 作為起點：</span><span class="sxs-lookup"><span data-stu-id="5cdf3-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="5cdf3-162">檔案 \* 上的bundleconfig.js\* 會定義每個組合的選項。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="5cdf3-163">在上述範例中，會為自訂 JavaScript (*wwwroot/js/site.js*) 和樣式表單定義單一套件組合， (*wwwroot/css/網站 .css*) 檔案。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="5cdf3-164">設定選項包括：</span><span class="sxs-lookup"><span data-stu-id="5cdf3-164">Configuration options include:</span></span>

* <span data-ttu-id="5cdf3-165">`outputFileName`：要輸出的組合檔案名稱。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="5cdf3-166">可以包含檔案 *bundleconfig.js* 的相對路徑。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="5cdf3-167">**必填**</span><span class="sxs-lookup"><span data-stu-id="5cdf3-167">**required**</span></span>
* <span data-ttu-id="5cdf3-168">`inputFiles`：要一起組合的檔案陣列。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="5cdf3-169">這些是設定檔的相對路徑。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="5cdf3-170">（**選擇性**） \* 空白值會產生空白的輸出檔。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="5cdf3-171">支援[萬用字元模式。](https://www.tldp.org/LDP/abs/html/globbingref.html)</span><span class="sxs-lookup"><span data-stu-id="5cdf3-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="5cdf3-172">`minify`：輸出型別的縮制選項。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="5cdf3-173">**選用**，*預設值 `minify: { enabled: true }` -*</span><span class="sxs-lookup"><span data-stu-id="5cdf3-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="5cdf3-174">每個輸出檔案類型都可以使用設定選項。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="5cdf3-175">CSS Minifier</span><span class="sxs-lookup"><span data-stu-id="5cdf3-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="5cdf3-176">JavaScript Minifier</span><span class="sxs-lookup"><span data-stu-id="5cdf3-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="5cdf3-177">HTML Minifier</span><span class="sxs-lookup"><span data-stu-id="5cdf3-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="5cdf3-178">`includeInProject`：旗標，指出是否要將產生的檔案加入至專案檔。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="5cdf3-179">**選用**， *預設值-false*</span><span class="sxs-lookup"><span data-stu-id="5cdf3-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="5cdf3-180">`sourceMap`：旗標，指出是否要產生配套檔案的來源對應。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="5cdf3-181">**選用**， *預設值-false*</span><span class="sxs-lookup"><span data-stu-id="5cdf3-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="5cdf3-182">`sourceMapRootPath`：用來儲存所產生之來源對應檔的根路徑。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="5cdf3-183">將檔案新增至工作流程</span><span class="sxs-lookup"><span data-stu-id="5cdf3-183">Add files to workflow</span></span>

<span data-ttu-id="5cdf3-184">假設有一個範例，其中加入了類似下列的其他 *自訂 .css* 檔案：</span><span class="sxs-lookup"><span data-stu-id="5cdf3-184">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="5cdf3-185">若要縮短 *自訂 .css* ，並將 *其與 node.js* 配套到 *網站* .css 檔案中，請新增 *bundleconfig.js*的相對路徑：</span><span class="sxs-lookup"><span data-stu-id="5cdf3-185">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="5cdf3-186">或者，您可以使用下列萬用字元模式：</span><span class="sxs-lookup"><span data-stu-id="5cdf3-186">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="5cdf3-187">此萬用字元模式會比對所有 CSS 檔案，並排除縮減檔案模式。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-187">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="5cdf3-188">建置應用程式。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-188">Build the application.</span></span> <span data-ttu-id="5cdf3-189">開啟 [ *.css* ]，並注意 *自訂 .css* 的內容會附加至檔案結尾。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-189">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="5cdf3-190">以環境為基礎的組合和縮制</span><span class="sxs-lookup"><span data-stu-id="5cdf3-190">Environment-based bundling and minification</span></span>

<span data-ttu-id="5cdf3-191">最佳做法是，應用程式的配套和縮減檔案應該用於生產環境。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-191">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="5cdf3-192">在開發期間，原始檔案可讓您更輕鬆地對應用程式進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-192">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="5cdf3-193">使用您的視圖中的 [環境](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) 標籤協助程式，指定要包含在頁面中的檔案。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-193">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="5cdf3-194">環境標籤協助程式只會在特定 [環境](xref:fundamentals/environments)中執行時呈現其內容。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-194">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="5cdf3-195">在 `environment` 環境中執行時，下列標記會呈現未處理的 CSS 檔案 `Development` ：</span><span class="sxs-lookup"><span data-stu-id="5cdf3-195">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="5cdf3-196">在以外的 `environment` 環境中執行時，下列標記會呈現配套和縮減的 CSS 檔案 `Development` 。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-196">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="5cdf3-197">例如，在中 `Production` 執行或會 `Staging` 觸發這些樣式表單的轉譯：</span><span class="sxs-lookup"><span data-stu-id="5cdf3-197">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="5cdf3-198">從 Gulp 使用 bundleconfig.js</span><span class="sxs-lookup"><span data-stu-id="5cdf3-198">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="5cdf3-199">在某些情況下，應用程式的包裝和縮制工作流程需要額外的處理。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-199">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="5cdf3-200">範例包括映射優化、快取破壞和 CDN 資產處理。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-200">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="5cdf3-201">為了滿足這些需求，您可以將包裝和縮制工作流程轉換為使用 Gulp。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-201">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a><span data-ttu-id="5cdf3-202">手動將組合和縮制工作流程轉換為使用 Gulp</span><span class="sxs-lookup"><span data-stu-id="5cdf3-202">Manually convert the bundling and minification workflow to use Gulp</span></span>

<span data-ttu-id="5cdf3-203">使用下列內容將 *package.js* 新增 `devDependencies` 至專案根目錄：</span><span class="sxs-lookup"><span data-stu-id="5cdf3-203">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="5cdf3-204">`gulp-uglify`模組不支援 ECMAScript (ES) 2015/ES6 和更新版本。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-204">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="5cdf3-205">安裝 [gulp-terser](https://www.npmjs.com/package/gulp-terser) 而不是 `gulp-uglify` 使用 ES2015/ES6 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-205">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="5cdf3-206">在與package.js的相同層級 \* 上\*執行下列命令，以安裝相依性：</span><span class="sxs-lookup"><span data-stu-id="5cdf3-206">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```bash
npm i
```

<span data-ttu-id="5cdf3-207">將 Gulp CLI 安裝為全域相依性：</span><span class="sxs-lookup"><span data-stu-id="5cdf3-207">Install the Gulp CLI as a global dependency:</span></span>

```bash
npm i -g gulp-cli
```

<span data-ttu-id="5cdf3-208">將以下的 *gulpfile.js* 檔案複製到專案根目錄：</span><span class="sxs-lookup"><span data-stu-id="5cdf3-208">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="5cdf3-209">執行 Gulp 工作</span><span class="sxs-lookup"><span data-stu-id="5cdf3-209">Run Gulp tasks</span></span>

<span data-ttu-id="5cdf3-210">在 Visual Studio 中建立專案之前，觸發 Gulp 縮制工作：</span><span class="sxs-lookup"><span data-stu-id="5cdf3-210">To trigger the Gulp minification task before the project builds in Visual Studio:</span></span>

1. <span data-ttu-id="5cdf3-211">安裝 [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-211">Install the [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet package.</span></span>
1. <span data-ttu-id="5cdf3-212">將下列 [MSBuild 目標](/visualstudio/msbuild/msbuild-targets) 新增至專案檔：</span><span class="sxs-lookup"><span data-stu-id="5cdf3-212">Add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the project file:</span></span>

    [!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="5cdf3-213">在此範例中，在目標內定義的所有工作都會在 `MyPreCompileTarget` 預先定義的目標之前執行 `Build` 。</span><span class="sxs-lookup"><span data-stu-id="5cdf3-213">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="5cdf3-214">Visual Studio 的輸出視窗中會出現類似下列的輸出：</span><span class="sxs-lookup"><span data-stu-id="5cdf3-214">Output similar to the following appears in Visual Studio's Output window:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="5cdf3-215">其他資源</span><span class="sxs-lookup"><span data-stu-id="5cdf3-215">Additional resources</span></span>

* [<span data-ttu-id="5cdf3-216">使用 Grunt</span><span class="sxs-lookup"><span data-stu-id="5cdf3-216">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="5cdf3-217">使用多重環境</span><span class="sxs-lookup"><span data-stu-id="5cdf3-217">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="5cdf3-218">標籤協助程式</span><span class="sxs-lookup"><span data-stu-id="5cdf3-218">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
