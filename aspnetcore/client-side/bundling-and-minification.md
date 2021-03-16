---
title: 在 ASP.NET Core 中組合和縮短靜態資產
author: scottaddie
description: 瞭解如何藉由套用配套和縮制技術，將 ASP.NET Core web 應用程式中的靜態資源優化。
ms.author: scaddie
ms.custom: mvc
ms.date: 03/14/2021
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
uid: client-side/bundling-and-minification
ms.openlocfilehash: d594bbf277907e22b0299b0451e480e9d533d506
ms.sourcegitcommit: 00368bb6a5420983beaced5b62dabc1f94abdeba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/16/2021
ms.locfileid: "103557799"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="04079-103">在 ASP.NET Core 中組合和縮短靜態資產</span><span class="sxs-lookup"><span data-stu-id="04079-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="04079-104">[Scott Addie](https://twitter.com/Scott_Addie)和[David 松樹](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="04079-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="04079-105">本文說明套用配套和縮制的優點，包括如何搭配 ASP.NET Core web 應用程式使用這些功能。</span><span class="sxs-lookup"><span data-stu-id="04079-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="04079-106">什麼是組合和縮制</span><span class="sxs-lookup"><span data-stu-id="04079-106">What is bundling and minification</span></span>

<span data-ttu-id="04079-107">組合和縮制是您可以在 web 應用程式中套用的兩個不同的效能優化。</span><span class="sxs-lookup"><span data-stu-id="04079-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="04079-108">結合和縮制會藉由減少伺服器要求數目和減少要求的靜態資產的大小，來改善效能。</span><span class="sxs-lookup"><span data-stu-id="04079-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="04079-109">組合和縮制主要會改善第一頁要求載入時間。</span><span class="sxs-lookup"><span data-stu-id="04079-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="04079-110">一旦要求網頁之後，瀏覽器會將靜態資產快取 (JavaScript、CSS 和影像) 。</span><span class="sxs-lookup"><span data-stu-id="04079-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="04079-111">因此，在要求相同資產的相同網站上要求相同頁面或頁面時，組合和縮制不會改善效能。</span><span class="sxs-lookup"><span data-stu-id="04079-111">So, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="04079-112">如果資產上的 expires 標頭未正確設定，而且未使用組合和縮制，則瀏覽器的有效啟發學習會在幾天後標記為過時的資產。</span><span class="sxs-lookup"><span data-stu-id="04079-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="04079-113">此外，瀏覽器需要針對每個資產進行驗證要求。</span><span class="sxs-lookup"><span data-stu-id="04079-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="04079-114">在此情況下，即使在第一頁要求之後，組合和縮制也可提供效能改進。</span><span class="sxs-lookup"><span data-stu-id="04079-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="04079-115">捆綁</span><span class="sxs-lookup"><span data-stu-id="04079-115">Bundling</span></span>

<span data-ttu-id="04079-116">統合可將多個檔案合併成單一檔案。</span><span class="sxs-lookup"><span data-stu-id="04079-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="04079-117">組合可減少轉譯 web 資產所需的伺服器要求數目，例如網頁。</span><span class="sxs-lookup"><span data-stu-id="04079-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="04079-118">您可以針對 CSS、JavaScript 等建立任意數目的個別套件組合。較少的檔案表示從瀏覽器到伺服器的 HTTP 要求較少，或從提供您應用程式的服務。</span><span class="sxs-lookup"><span data-stu-id="04079-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files mean fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="04079-119">這會導致第一個頁面載入效能提高。</span><span class="sxs-lookup"><span data-stu-id="04079-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="04079-120">縮製</span><span class="sxs-lookup"><span data-stu-id="04079-120">Minification</span></span>

<span data-ttu-id="04079-121">縮制會從程式碼移除不必要的字元，而不會改變功能。</span><span class="sxs-lookup"><span data-stu-id="04079-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="04079-122">結果會大幅減少要求的資產 (例如 CSS、影像和 JavaScript 檔案) 。</span><span class="sxs-lookup"><span data-stu-id="04079-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="04079-123">縮制的常見副作用包括將變數名稱縮短為一個字元，以及移除批註和不必要的空白字元。</span><span class="sxs-lookup"><span data-stu-id="04079-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="04079-124">請考慮下列 JavaScript 函數：</span><span class="sxs-lookup"><span data-stu-id="04079-124">Consider the following JavaScript function:</span></span>

```javascript
AddAltToImg = function (imageTagAndImageID, imageContext) {
    ///<signature>
    ///<summary> Adds an alt tab to the image
    // </summary>
    //<param name="imgElement" type="String">The image selector.</param>
    //<param name="ContextForImage" type="String">The image context.</param>
    ///</signature>
    var imageElement = $(imageTagAndImageID, imageContext);
    imageElement.attr('alt', imageElement.attr('id').replace(/ID/, ''));
}
```

<span data-ttu-id="04079-125">縮制會將函數縮減為下列內容：</span><span class="sxs-lookup"><span data-stu-id="04079-125">Minification reduces the function to the following:</span></span>

```javascript
AddAltToImg=function(t,a){var r=$(t,a);r.attr("alt",r.attr("id").replace(/ID/,""))};
```

<span data-ttu-id="04079-126">除了移除批註和不必要的空白字元之外，下列參數和變數名稱已重新命名如下：</span><span class="sxs-lookup"><span data-stu-id="04079-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="04079-127">原始</span><span class="sxs-lookup"><span data-stu-id="04079-127">Original</span></span> | <span data-ttu-id="04079-128">已重新命名</span><span class="sxs-lookup"><span data-stu-id="04079-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="04079-129">組合和縮制的影響</span><span class="sxs-lookup"><span data-stu-id="04079-129">Impact of bundling and minification</span></span>

<span data-ttu-id="04079-130">下表概述個別載入資產與使用包裝和縮制之間的差異：</span><span class="sxs-lookup"><span data-stu-id="04079-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="04079-131">動作</span><span class="sxs-lookup"><span data-stu-id="04079-131">Action</span></span> | <span data-ttu-id="04079-132">使用 B/M</span><span class="sxs-lookup"><span data-stu-id="04079-132">With B/M</span></span> | <span data-ttu-id="04079-133">沒有 B/M</span><span class="sxs-lookup"><span data-stu-id="04079-133">Without B/M</span></span> | <span data-ttu-id="04079-134">變更</span><span class="sxs-lookup"><span data-stu-id="04079-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="04079-135">檔案要求</span><span class="sxs-lookup"><span data-stu-id="04079-135">File Requests</span></span>  | <span data-ttu-id="04079-136">7</span><span class="sxs-lookup"><span data-stu-id="04079-136">7</span></span>   | <span data-ttu-id="04079-137">18</span><span class="sxs-lookup"><span data-stu-id="04079-137">18</span></span>     | <span data-ttu-id="04079-138">157%</span><span class="sxs-lookup"><span data-stu-id="04079-138">157%</span></span>
<span data-ttu-id="04079-139">已傳送 KB</span><span class="sxs-lookup"><span data-stu-id="04079-139">KB Transferred</span></span> | <span data-ttu-id="04079-140">156</span><span class="sxs-lookup"><span data-stu-id="04079-140">156</span></span> | <span data-ttu-id="04079-141">264.68</span><span class="sxs-lookup"><span data-stu-id="04079-141">264.68</span></span> | <span data-ttu-id="04079-142">70%</span><span class="sxs-lookup"><span data-stu-id="04079-142">70%</span></span>
<span data-ttu-id="04079-143">載入時間 (ms) </span><span class="sxs-lookup"><span data-stu-id="04079-143">Load Time (ms)</span></span> | <span data-ttu-id="04079-144">885</span><span class="sxs-lookup"><span data-stu-id="04079-144">885</span></span> | <span data-ttu-id="04079-145">2360</span><span class="sxs-lookup"><span data-stu-id="04079-145">2360</span></span>   | <span data-ttu-id="04079-146">167%</span><span class="sxs-lookup"><span data-stu-id="04079-146">167%</span></span>

<span data-ttu-id="04079-147">瀏覽器對於 HTTP 要求標頭相當詳細。</span><span class="sxs-lookup"><span data-stu-id="04079-147">Browsers are fairly verbose regarding HTTP request headers.</span></span> <span data-ttu-id="04079-148">「傳送的位元組總數」計量在組合時看到明顯的縮減。</span><span class="sxs-lookup"><span data-stu-id="04079-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="04079-149">載入時間顯示大幅改進，不過這個範例是在本機執行。</span><span class="sxs-lookup"><span data-stu-id="04079-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="04079-150">在透過網路傳輸的資產使用包裝和縮制時，可實現更佳的效能提升。</span><span class="sxs-lookup"><span data-stu-id="04079-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="04079-151">選擇捆綁和縮制策略</span><span class="sxs-lookup"><span data-stu-id="04079-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="04079-152">ASP.NET Core 與 WebOptimizer （開放原始碼包裝和縮制解決方案）相容。</span><span class="sxs-lookup"><span data-stu-id="04079-152">ASP.NET Core is compatible with WebOptimizer, an open-source bundling and minification solution.</span></span> <span data-ttu-id="04079-153">如需安裝指示和範例專案，請參閱 [WebOptimizer](https://github.com/ligershark/WebOptimizer)。</span><span class="sxs-lookup"><span data-stu-id="04079-153">For set up instructions and sample projects, see [WebOptimizer](https://github.com/ligershark/WebOptimizer).</span></span> <span data-ttu-id="04079-154">ASP.NET Core 不提供原生包裝和縮制解決方案。</span><span class="sxs-lookup"><span data-stu-id="04079-154">ASP.NET Core doesn't provide a native bundling and minification solution.</span></span>

<span data-ttu-id="04079-155">協力廠商工具（例如 [Gulp](https://gulpjs.com) 和 [Webpack](https://webpack.js.org)）可為組合和縮制提供工作流程自動化，以及 linting 和映射優化。</span><span class="sxs-lookup"><span data-stu-id="04079-155">Third-party tools, such as [Gulp](https://gulpjs.com) and [Webpack](https://webpack.js.org), provide workflow automation for bundling and minification, as well as linting and image optimization.</span></span> <span data-ttu-id="04079-156">藉由使用設計階段組合和縮制，縮減檔會在應用程式部署之前建立。</span><span class="sxs-lookup"><span data-stu-id="04079-156">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="04079-157">在部署之前進行包裝和縮小，可提供降低伺服器負載的優點。</span><span class="sxs-lookup"><span data-stu-id="04079-157">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="04079-158">不過，請務必瞭解設計階段組合和縮制會增加組建複雜性，而且只適用于靜態檔案。</span><span class="sxs-lookup"><span data-stu-id="04079-158">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="04079-159">以環境為基礎的組合和縮制</span><span class="sxs-lookup"><span data-stu-id="04079-159">Environment-based bundling and minification</span></span>

<span data-ttu-id="04079-160">最佳做法是，應用程式的配套和縮減檔案應該用於生產環境。</span><span class="sxs-lookup"><span data-stu-id="04079-160">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="04079-161">在開發期間，原始檔案可讓您更輕鬆地對應用程式進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="04079-161">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="04079-162">使用您的視圖中的 [環境](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) 標籤協助程式，指定要包含在頁面中的檔案。</span><span class="sxs-lookup"><span data-stu-id="04079-162">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="04079-163">環境標籤協助程式只會在特定 [環境](xref:fundamentals/environments)中執行時呈現其內容。</span><span class="sxs-lookup"><span data-stu-id="04079-163">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="04079-164">在 `environment` 環境中執行時，下列標記會呈現未處理的 CSS 檔案 `Development` ：</span><span class="sxs-lookup"><span data-stu-id="04079-164">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

```cshtml
<environment include="Development">
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
    <link rel="stylesheet" href="~/css/site.css" />
</environment>
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```cshtml
<environment names="Staging,Production">
    <link rel="stylesheet" href="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap.min.css"
          asp-fallback-href="~/lib/bootstrap/dist/css/bootstrap.min.css"
          asp-fallback-test-class="sr-only" asp-fallback-test-property="position" asp-fallback-test-value="absolute" />
    <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
</environment>
```

::: moniker-end

<span data-ttu-id="04079-165">在以外的 `environment` 環境中執行時，下列標記會呈現配套和縮減的 CSS 檔案 `Development` 。</span><span class="sxs-lookup"><span data-stu-id="04079-165">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="04079-166">例如，在中 `Production` 執行或會 `Staging` 觸發這些樣式表單的轉譯：</span><span class="sxs-lookup"><span data-stu-id="04079-166">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

```cshtml
<environment exclude="Development">
    <link rel="stylesheet" href="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap.min.css"
          asp-fallback-href="~/lib/bootstrap/dist/css/bootstrap.min.css"
          asp-fallback-test-class="sr-only" asp-fallback-test-property="position" asp-fallback-test-value="absolute" />
    <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
</environment>
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```cshtml
<environment names="Staging,Production">
    <link rel="stylesheet" href="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap.min.css"
          asp-fallback-href="~/lib/bootstrap/dist/css/bootstrap.min.css"
          asp-fallback-test-class="sr-only" asp-fallback-test-property="position" asp-fallback-test-value="absolute" />
    <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
</environment>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="04079-167">其他資源</span><span class="sxs-lookup"><span data-stu-id="04079-167">Additional resources</span></span>

* [<span data-ttu-id="04079-168">使用多重環境</span><span class="sxs-lookup"><span data-stu-id="04079-168">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="04079-169">標籤協助程式</span><span class="sxs-lookup"><span data-stu-id="04079-169">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
