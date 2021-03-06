---
title: ASP.NET 核心 Blazor 版面配置
author: guardrex
description: 瞭解如何為應用程式建立可重複使用的版面配置元件 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2021
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
uid: blazor/layouts
ms.openlocfilehash: 9f3400b766a043fdf3872838bffe392eddba4049
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2021
ms.locfileid: "102394939"
---
# <a name="aspnet-core-blazor-layouts"></a>ASP.NET 核心 Blazor 版面配置

某些應用程式元素（例如功能表、著作權訊息和公司標誌）通常是應用程式整體簡報的一部分。 將這些元素的標記複本放入應用程式的所有元件中，並不有效率。 每次更新其中一個專案時，都必須更新使用該元素的每個元件。 這種方法的維護成本很高，而且如果缺少更新，可能會導致不一致的內容。 *版面* 配置會解決這些問題。

Blazor版面配置是一種 Razor 元件，可與參考它的元件共用標記。 版面配置可以使用 [資料](xref:blazor/components/data-binding)系結、相依性 [插入](xref:blazor/fundamentals/dependency-injection)，以及元件的其他功能。

## <a name="layout-components"></a>版面配置元件

### <a name="create-a-layout-component"></a>建立版面配置元件

若要建立版面配置元件：

* 建立 Razor 由 Razor 範本或 c # 程式碼定義的元件。 以範本為基礎的版面配置元件 Razor 使用 `.razor` 副檔名，就像一般 Razor 元件一樣。 因為版面配置元件會在應用程式的元件之間共用，所以通常會放在應用程式的 `Shared` 資料夾中。 不過，您可以將版面配置放在使用它的元件可存取的任何位置。 例如，版面配置可以放在與使用它的元件相同的資料夾中。
* 從繼承元件 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> 。 會 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> 針對配置 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> 內轉譯 <xref:Microsoft.AspNetCore.Components.RenderFragment> 的內容定義屬性 (類型) 。
* 使用 Razor 語法 `@Body` 來指定版面配置標記中轉譯內容的位置。

下列 `DoctorWhoLayout` 元件顯示版面配置 Razor 元件的範本。 版面配置會繼承 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> 並設定 `@Body` 導覽列 (`<nav>...</nav>`) 和頁尾 () 之間的 `<footer>...</footer>` 。

`Shared/DoctorWhoLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor?highlight=1,13)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor?highlight=1,13)]

::: moniker-end

### <a name="mainlayout-component"></a>`MainLayout` 元件

在從[ Blazor 專案範本](xref:blazor/project-structure)建立的應用程式中， `MainLayout` 元件是應用程式的[預設版面](#apply-a-default-layout-to-an-app)配置。

`Shared/MainLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/MainLayout.razor)]

[ Blazor 的 css 隔離功能](xref:blazor/components/css-isolation)會將隔離的 css 樣式套用至 `MainLayout` 元件。 依照慣例，樣式會由相同名稱的隨附樣式表單提供 `Shared/MainLayout.razor.css` 。 您可以在 [ASP.NET core 參考來源 (dotnet/Aspnetcore GitHub 存放庫) ](https://github.com/dotnet/aspnetcore/blob/main/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/Shared/MainLayout.razor.css)中檢查樣式表單的 ASP.NET core framework 執行。

[!INCLUDE[](~/blazor/includes/aspnetcore-repo-ref-source-links.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/MainLayout.razor)]

::: moniker-end

## <a name="apply-a-layout"></a>套用版面配置

### <a name="apply-a-layout-to-a-component"></a>將版面配置套用至元件

使用指示詞將配置套用 [`@layout`](xref:mvc/views/razor#layout) Razor 至具有指示詞的可路由 Razor 元件 [`@page`](xref:mvc/views/razor#page) 。 編譯器會將轉換 `@layout` 成 <xref:Microsoft.AspNetCore.Components.LayoutAttribute> ，並將屬性套用至元件類別。

下列元件的內容 `Episodes` 會插入至的 `DoctorWhoLayout` 位置 `@Body` 。

`Pages/Episodes.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor?highlight=2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor?highlight=2)]

::: moniker-end

下列呈現的 HTML 標籤是由上述 `DoctorWhoLayout` 和元件所產生 `Episodes` 。 不會出現無關的標記以專注于所涉及的兩個元件所提供的內容：

* 在頁尾中，將 **&trade; 資料庫** 標題 (`<h1>...</h1>`)  (`<header>...</header>`) 、導覽列 (`<nav>...</nav>`) 和 [商標資訊專案] () `<div>...</div>` `<footer>...</footer>` 來自 `DoctorWhoLayout` 元件的醫生。
*  () 來自元件的 **劇集** 標題 (`<h2>...</h2>`) 和劇集清單 `<ul>...</ul>` `Episodes` 。

```html
<body>
    <div id="app">
        <header>
            <h1>Doctor Who&trade; Episode Database</h1>
        </header>

        <nav>
            <a href="masterlist">Master Episode List</a>
            <a href="search">Search</a>
            <a href="new">Add Episode</a>
        </nav>

        <h2>Episodes</h2>

        <ul>
            <li>...</li>
            <li>...</li>
            <li>...</li>
        </ul>

        <footer>
            Doctor Who is a registered trademark of the BBC. 
            https://www.doctorwho.tv/
        </footer>
    </div>
</body>
```

直接在元件中指定配置會覆寫 *預設版面* 配置：

* 由 `@layout` 從元件 () 所匯入的指示詞設定 `_Imports` `_Imports.razor` ，如下所述： [ [將版面配置套用至元件的資料夾](#apply-a-layout-to-a-folder-of-components) ] 區段中所述。
* 設定為應用程式的預設版面配置，如本文稍後的 [將預設版面配置套用至應用程式](#apply-a-default-layout-to-an-app) 一節中所述。

### <a name="apply-a-layout-to-a-folder-of-components"></a>將版面配置套用至元件的資料夾

應用程式的每個資料夾都可以選擇性地包含名為的範本檔案 `_Imports.razor` 。 編譯器包含在相同資料夾中所有範本的 imports 檔中所指定的指示詞 Razor ，並以遞迴方式在其所有子資料夾中包含這些指令程式。 因此， `_Imports.razor` 包含的檔案 `@layout DoctorWhoLayout` 可確保資料夾中的所有元件都使用該 `DoctorWhoLayout` 元件。 在 `@layout DoctorWhoLayout` Razor `.razor` 資料夾和子資料夾內，不需要重複新增至 () 的所有元件。

`_Imports.razor`:

```razor
@layout DoctorWhoLayout
...
```

檔案 `_Imports.razor` 類似于 [ Razor views 和 pages 的 _ViewImports cshtml](xref:mvc/views/layout#importing-shared-directives) 檔案，但特別適用于 Razor 元件檔。

在中指定配置 `_Imports.razor` 會覆寫指定為路由器 [預設應用程式](#apply-a-default-layout-to-an-app)配置的版面配置，如下一節所述。

> [!WARNING]
> 請勿將指示詞加入 Razor `@layout` 至根檔案 `_Imports.razor` ，這會產生無限迴圈的版面配置。 若要控制預設的應用程式版面配置，請在元件中指定版面配置 `Router` 。 如需詳細資訊，請參閱下列 [將預設版面配置套用至應用程式](#apply-a-default-layout-to-an-app) 一節。

> [!NOTE]
> 指示詞 [`@layout`](xref:mvc/views/razor#layout) Razor 只會將配置套用至具有指示詞的可路由 Razor 元件 [`@page`](xref:mvc/views/razor#page) 。

### <a name="apply-a-default-layout-to-an-app"></a>將預設版面配置套用至應用程式

在元件的元件中，指定預設的應用程式版面配置 `App` <xref:Microsoft.AspNetCore.Components.Routing.Router> 。 下列以[ Blazor 專案範本](xref:blazor/project-structure)為基礎的應用程式範例，會將預設版面配置設定為 `MainLayout` 元件。

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/layouts/App1.razor?highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/layouts/App1.razor?highlight=3)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

如需元件的詳細資訊 <xref:Microsoft.AspNetCore.Components.Routing.Router> ，請參閱 <xref:blazor/fundamentals/routing> 。

將版面配置指定為元件中的預設配置 `Router` 是很有用的作法，因為您可以覆寫個別元件或每個資料夾的版面配置，如本文先前各節所述。 建議您使用 `Router` 元件來設定應用程式的預設版面配置，因為這是使用版面配置最常見且彈性的方法。

### <a name="apply-a-layout-to-arbitrary-content-layoutview-component"></a>將版面配置套用至任意內容 (`LayoutView` 元件) 

若要設定任意範本內容的版面配置 Razor ，請指定具有元件的配置 <xref:Microsoft.AspNetCore.Components.LayoutView> 。 您可以 <xref:Microsoft.AspNetCore.Components.LayoutView> 在任何元件中使用 Razor 。 下列範例會 `ErrorLayout` 針對元件的範本，設定名為的版面配置元件 `MainLayout` <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> (`<NotFound>...</NotFound>`) 。

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/layouts/App2.razor?name=snippet&highlight=6,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/layouts/App2.razor?name=snippet&highlight=6,9)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

## <a name="nested-layouts"></a>嵌套版面配置

元件可以參考配置，然後再參考另一個版面配置。 例如，使用嵌套版面配置來建立多層級的功能表結構。

下列範例示範如何使用嵌套版面配置。 `Episodes`[將版面配置套用[至元件](#apply-a-layout-to-a-component)] 區段中顯示的元件是要顯示的元件。 元件參考 `DoctorWhoLayout` 元件。

下列 `DoctorWhoLayout` 元件是本文稍早所示之範例的修改版本。 標頭和頁尾元素會被移除，而配置會參考另一個版面配置 `ProductionsLayout` 。 `Episodes`元件會呈現 `@Body` 在中的位置 `DoctorWhoLayout` 。

`Shared/DoctorWhoLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor?highlight=2,12)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor?highlight=2,12)]

::: moniker-end

此 `ProductionsLayout` 元件包含最上層的版面配置元素，其中標頭 (`<header>...</header>`) 和頁尾 (`<footer>...</footer>`) 元素目前所在。 具有元件的會在 `DoctorWhoLayout` `Episodes` 出現時呈現 `@Body` 。

`Shared/ProductionsLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor?highlight=13)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor?highlight=13)]

::: moniker-end

下列呈現的 HTML 標籤是由上述的嵌套配置所產生。 不會出現無關的標記，以便專注于所涉及的三個元件所提供的嵌套內容：

* 標頭 (`<header>...</header>`) 、生產導覽列 (`<nav>...</nav>`) 和頁尾 (`<footer>...</footer>` 元素和其內容來自 `ProductionsLayout` 元件。
* 將 **&trade; 資料庫** 標題 (`<h1>...</h1>`) 、集中導覽列 (`<nav>...</nav>`) 和商標資訊專案 () 來自元件的醫生 `<div>...</div>` `DoctorWhoLayout` 。
*  () 來自元件的 **劇集** 標題 (`<h2>...</h2>`) 和劇集清單 `<ul>...</ul>` `Episodes` 。

```html
<body>
    <div id="app">
        <header>
            <h1>Productions</h1>
        </header>

        <nav>
            <a href="master-production-list">Master Production List</a>
            <a href="production-search">Search</a>
            <a href="new-production">Add Production</a>
        </nav>

        <h1>Doctor Who&trade; Episode Database</h1>

        <nav>
            <a href="episode-masterlist">Master Episode List</a>
            <a href="episode-search">Search</a>
            <a href="new-episode">Add Episode</a>
        </nav>

        <h2>Episodes</h2>

        <ul>
            <li>...</li>
            <li>...</li>
            <li>...</li>
        </ul>

        <div>
            Doctor Who is a registered trademark of the BBC. 
            https://www.doctorwho.tv/
        </div>

        <footer>
            Footer of Productions Layout
        </footer>
    </div>
</body>
```

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Razor使用整合元件共用頁面配置

當可路由的元件整合到 Razor 頁面應用程式時，應用程式的共用配置可以與元件一起使用。 如需詳細資訊，請參閱<xref:blazor/components/prerendering-and-integration>。

## <a name="additional-resources"></a>其他資源

* <xref:mvc/views/layout>
