---
title: 在 ASP.NET Core 中組合和縮短靜態資產
author: rick-anderson
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
ms.openlocfilehash: 12701d02ed38e68c1833ee51d9ea87337d17065a
ms.sourcegitcommit: 0abfe496fed8e9470037c8128efa8a50069ccd52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2021
ms.locfileid: "106563591"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>在 ASP.NET Core 中組合和縮短靜態資產

[Scott Addie](https://twitter.com/Scott_Addie)和[David 松樹](https://twitter.com/davidpine7)

本文說明套用配套和縮制的優點，包括這些功能如何搭配 ASP.NET Core 的 web 應用程式來使用。

## <a name="what-is-bundling-and-minification"></a>什麼是組合和縮制

組合和縮制是您可以在 web 應用程式中套用的兩個不同的效能優化。 結合和縮制會藉由減少伺服器要求數目和減少要求的靜態資產的大小，來改善效能。

組合和縮制主要會改善第一頁要求載入時間。 一旦要求網頁之後，瀏覽器會將靜態資產快取 (JavaScript、CSS 和影像) 。 因此，在要求相同資產的相同網站上要求相同頁面或頁面時，組合和縮制不會改善效能。 如果資產上的 expires 標頭未正確設定，而且未使用組合和縮制，則瀏覽器的有效啟發學習會在幾天後標記為過時的資產。 此外，瀏覽器需要針對每個資產進行驗證要求。 在此情況下，即使在第一頁要求之後，組合和縮制也可提供效能改進。

### <a name="bundling"></a>捆綁

統合可將多個檔案合併成單一檔案。 組合可減少轉譯 web 資產所需的伺服器要求數目，例如網頁。 您可以針對 CSS、JavaScript 等建立任意數目的個別套件組合。較少的檔案表示從瀏覽器到伺服器的 HTTP 要求較少，或從提供您應用程式的服務。 這會導致第一個頁面載入效能提高。

### <a name="minification"></a>縮製

縮制會從程式碼移除不必要的字元，而不會改變功能。 結果會大幅減少要求的資產 (例如 CSS、影像和 JavaScript 檔案) 。 縮制的常見副作用包括將變數名稱縮短為一個字元，以及移除批註和不必要的空白字元。

請考慮下列 JavaScript 函數：

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

縮制會將函數縮減為下列內容：

```javascript
AddAltToImg=function(t,a){var r=$(t,a);r.attr("alt",r.attr("id").replace(/ID/,""))};
```

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

瀏覽器對於 HTTP 要求標頭相當詳細。 「傳送的位元組總數」計量在組合時看到明顯的縮減。 載入時間顯示大幅改進，不過這個範例是在本機執行。 在透過網路傳輸的資產使用包裝和縮制時，可實現更佳的效能提升。

## <a name="choose-a-bundling-and-minification-strategy"></a>選擇捆綁和縮制策略

ASP.NET Core 與 WebOptimizer 相容，這是一個開放原始碼的包裝和縮制解決方案。 如需安裝指示和範例專案，請參閱 [WebOptimizer](https://github.com/ligershark/WebOptimizer)。 ASP.NET Core 不提供原生包裝和縮制解決方案。

協力廠商工具（例如 [Gulp](https://gulpjs.com) 和 [Webpack](https://webpack.js.org)）可為組合和縮制提供工作流程自動化，以及 linting 和映射優化。 藉由使用設計階段組合和縮制，縮減檔會在應用程式部署之前建立。 在部署之前進行包裝和縮小，可提供降低伺服器負載的優點。 不過，請務必瞭解設計階段組合和縮制會增加組建複雜性，而且只適用于靜態檔案。

## <a name="environment-based-bundling-and-minification"></a>以環境為基礎的組合和縮制

最佳做法是，應用程式的配套和縮減檔案應該用於生產環境。 在開發期間，原始檔案可讓您更輕鬆地對應用程式進行偵錯工具。

使用您的視圖中的 [環境](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) 標籤協助程式，指定要包含在頁面中的檔案。 環境標籤協助程式只會在特定 [環境](xref:fundamentals/environments)中執行時呈現其內容。

在 `environment` 環境中執行時，下列標記會呈現未處理的 CSS 檔案 `Development` ：

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

在以外的 `environment` 環境中執行時，下列標記會呈現配套和縮減的 CSS 檔案 `Development` 。 例如，在中 `Production` 執行或會 `Staging` 觸發這些樣式表單的轉譯：

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

## <a name="additional-resources"></a>其他資源

* [使用多重環境](xref:fundamentals/environments)
* [標籤協助程式](xref:mvc/views/tag-helpers/intro)
