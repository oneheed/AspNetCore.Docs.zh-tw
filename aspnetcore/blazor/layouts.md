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
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="0c3e2-103">ASP.NET 核心 Blazor 版面配置</span><span class="sxs-lookup"><span data-stu-id="0c3e2-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="0c3e2-104">某些應用程式元素（例如功能表、著作權訊息和公司標誌）通常是應用程式整體簡報的一部分。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-104">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall presentation.</span></span> <span data-ttu-id="0c3e2-105">將這些元素的標記複本放入應用程式的所有元件中，並不有效率。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-105">Placing a copy of the markup for these elements into all of the components of an app isn't efficient.</span></span> <span data-ttu-id="0c3e2-106">每次更新其中一個專案時，都必須更新使用該元素的每個元件。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-106">Every time that one of these elements is updated, every component that uses the element must be updated.</span></span> <span data-ttu-id="0c3e2-107">這種方法的維護成本很高，而且如果缺少更新，可能會導致不一致的內容。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-107">This approach is costly to maintain and can lead to inconsistent content if an update is missed.</span></span> <span data-ttu-id="0c3e2-108">*版面* 配置會解決這些問題。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-108">*Layouts* solve these problems.</span></span>

<span data-ttu-id="0c3e2-109">Blazor版面配置是一種 Razor 元件，可與參考它的元件共用標記。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-109">A Blazor layout is a Razor component that shares markup with components that reference it.</span></span> <span data-ttu-id="0c3e2-110">版面配置可以使用 [資料](xref:blazor/components/data-binding)系結、相依性 [插入](xref:blazor/fundamentals/dependency-injection)，以及元件的其他功能。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-110">Layouts can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other features of components.</span></span>

## <a name="layout-components"></a><span data-ttu-id="0c3e2-111">版面配置元件</span><span class="sxs-lookup"><span data-stu-id="0c3e2-111">Layout components</span></span>

### <a name="create-a-layout-component"></a><span data-ttu-id="0c3e2-112">建立版面配置元件</span><span class="sxs-lookup"><span data-stu-id="0c3e2-112">Create a layout component</span></span>

<span data-ttu-id="0c3e2-113">若要建立版面配置元件：</span><span class="sxs-lookup"><span data-stu-id="0c3e2-113">To create a layout component:</span></span>

* <span data-ttu-id="0c3e2-114">建立 Razor 由 Razor 範本或 c # 程式碼定義的元件。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-114">Create a Razor component defined by a Razor template or C# code.</span></span> <span data-ttu-id="0c3e2-115">以範本為基礎的版面配置元件 Razor 使用 `.razor` 副檔名，就像一般 Razor 元件一樣。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-115">Layout components based on a Razor template use the `.razor` file extension just like ordinary Razor components.</span></span> <span data-ttu-id="0c3e2-116">因為版面配置元件會在應用程式的元件之間共用，所以通常會放在應用程式的 `Shared` 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-116">Because layout components are shared across an app's components, they're usually placed in the app's `Shared` folder.</span></span> <span data-ttu-id="0c3e2-117">不過，您可以將版面配置放在使用它的元件可存取的任何位置。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-117">However, layouts can be placed in any location accessible to the components that use it.</span></span> <span data-ttu-id="0c3e2-118">例如，版面配置可以放在與使用它的元件相同的資料夾中。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-118">For example, a layout can be placed in the same folder as the components that use it.</span></span>
* <span data-ttu-id="0c3e2-119">從繼承元件 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-119">Inherit the component from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>.</span></span> <span data-ttu-id="0c3e2-120">會 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> 針對配置 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> 內轉譯 <xref:Microsoft.AspNetCore.Components.RenderFragment> 的內容定義屬性 (類型) 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-120">The <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property (<xref:Microsoft.AspNetCore.Components.RenderFragment> type) for the rendered content inside the layout.</span></span>
* <span data-ttu-id="0c3e2-121">使用 Razor 語法 `@Body` 來指定版面配置標記中轉譯內容的位置。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-121">Use the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="0c3e2-122">下列 `DoctorWhoLayout` 元件顯示版面配置 Razor 元件的範本。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-122">The following `DoctorWhoLayout` component shows the Razor template of a layout component.</span></span> <span data-ttu-id="0c3e2-123">版面配置會繼承 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> 並設定 `@Body` 導覽列 (`<nav>...</nav>`) 和頁尾 () 之間的 `<footer>...</footer>` 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-123">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar (`<nav>...</nav>`) and the footer (`<footer>...</footer>`).</span></span>

<span data-ttu-id="0c3e2-124">`Shared/DoctorWhoLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="0c3e2-124">`Shared/DoctorWhoLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor?highlight=1,13)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor?highlight=1,13)]

::: moniker-end

### <a name="mainlayout-component"></a><span data-ttu-id="0c3e2-125">`MainLayout` 元件</span><span class="sxs-lookup"><span data-stu-id="0c3e2-125">`MainLayout` component</span></span>

<span data-ttu-id="0c3e2-126">在從[ Blazor 專案範本](xref:blazor/project-structure)建立的應用程式中， `MainLayout` 元件是應用程式的[預設版面](#apply-a-default-layout-to-an-app)配置。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-126">In an app created from a [Blazor project template](xref:blazor/project-structure), the `MainLayout` component is the app's [default layout](#apply-a-default-layout-to-an-app).</span></span>

<span data-ttu-id="0c3e2-127">`Shared/MainLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="0c3e2-127">`Shared/MainLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/MainLayout.razor)]

<span data-ttu-id="0c3e2-128">[ Blazor 的 css 隔離功能](xref:blazor/components/css-isolation)會將隔離的 css 樣式套用至 `MainLayout` 元件。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-128">[Blazor's CSS isolation feature](xref:blazor/components/css-isolation) applies isolated CSS styles to the `MainLayout` component.</span></span> <span data-ttu-id="0c3e2-129">依照慣例，樣式會由相同名稱的隨附樣式表單提供 `Shared/MainLayout.razor.css` 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-129">By convention, the styles are provided by the accompanying stylesheet of the same name, `Shared/MainLayout.razor.css`.</span></span> <span data-ttu-id="0c3e2-130">您可以在 [ASP.NET core 參考來源 (dotnet/Aspnetcore GitHub 存放庫) ](https://github.com/dotnet/aspnetcore/blob/main/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/Shared/MainLayout.razor.css)中檢查樣式表單的 ASP.NET core framework 執行。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-130">The ASP.NET Core framework implementation of the stylesheet is available for inspection in the [ASP.NET Core reference source (dotnet/aspnetcore GitHub repository)](https://github.com/dotnet/aspnetcore/blob/main/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/Shared/MainLayout.razor.css).</span></span>

[!INCLUDE[](~/blazor/includes/aspnetcore-repo-ref-source-links.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/MainLayout.razor)]

::: moniker-end

## <a name="apply-a-layout"></a><span data-ttu-id="0c3e2-131">套用版面配置</span><span class="sxs-lookup"><span data-stu-id="0c3e2-131">Apply a layout</span></span>

### <a name="apply-a-layout-to-a-component"></a><span data-ttu-id="0c3e2-132">將版面配置套用至元件</span><span class="sxs-lookup"><span data-stu-id="0c3e2-132">Apply a layout to a component</span></span>

<span data-ttu-id="0c3e2-133">使用指示詞將配置套用 [`@layout`](xref:mvc/views/razor#layout) Razor 至具有指示詞的可路由 Razor 元件 [`@page`](xref:mvc/views/razor#page) 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-133">Use the [`@layout`](xref:mvc/views/razor#layout) Razor directive to apply a layout to a routable Razor component that has an [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="0c3e2-134">編譯器會將轉換 `@layout` 成 <xref:Microsoft.AspNetCore.Components.LayoutAttribute> ，並將屬性套用至元件類別。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-134">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute> and applies the attribute to the component class.</span></span>

<span data-ttu-id="0c3e2-135">下列元件的內容 `Episodes` 會插入至的 `DoctorWhoLayout` 位置 `@Body` 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-135">The content of the following `Episodes` component is inserted into the `DoctorWhoLayout` at the position of `@Body`.</span></span>

<span data-ttu-id="0c3e2-136">`Pages/Episodes.razor`:</span><span class="sxs-lookup"><span data-stu-id="0c3e2-136">`Pages/Episodes.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor?highlight=2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="0c3e2-137">下列呈現的 HTML 標籤是由上述 `DoctorWhoLayout` 和元件所產生 `Episodes` 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-137">The following rendered HTML markup is produced by the preceding `DoctorWhoLayout` and `Episodes` component.</span></span> <span data-ttu-id="0c3e2-138">不會出現無關的標記以專注于所涉及的兩個元件所提供的內容：</span><span class="sxs-lookup"><span data-stu-id="0c3e2-138">Extraneous markup doesn't appear in order to focus on the content provided by the two components involved:</span></span>

* <span data-ttu-id="0c3e2-139">在頁尾中，將 **&trade; 資料庫** 標題 (`<h1>...</h1>`)  (`<header>...</header>`) 、導覽列 (`<nav>...</nav>`) 和 [商標資訊專案] () `<div>...</div>` `<footer>...</footer>` 來自 `DoctorWhoLayout` 元件的醫生。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-139">The **Doctor Who&trade; Episode Database** heading (`<h1>...</h1>`) in the header (`<header>...</header>`), navigation bar (`<nav>...</nav>`), and trademark information element (`<div>...</div>`) in the footer (`<footer>...</footer>`) come from the `DoctorWhoLayout` component.</span></span>
* <span data-ttu-id="0c3e2-140"> () 來自元件的 **劇集** 標題 (`<h2>...</h2>`) 和劇集清單 `<ul>...</ul>` `Episodes` 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-140">The **Episodes** heading (`<h2>...</h2>`) and episode list (`<ul>...</ul>`) come from the `Episodes` component.</span></span>

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

<span data-ttu-id="0c3e2-141">直接在元件中指定配置會覆寫 *預設版面* 配置：</span><span class="sxs-lookup"><span data-stu-id="0c3e2-141">Specifying the layout directly in a component overrides a *default layout*:</span></span>

* <span data-ttu-id="0c3e2-142">由 `@layout` 從元件 () 所匯入的指示詞設定 `_Imports` `_Imports.razor` ，如下所述： [ [將版面配置套用至元件的資料夾](#apply-a-layout-to-a-folder-of-components) ] 區段中所述。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-142">Set by an `@layout` directive imported from an `_Imports` component (`_Imports.razor`), as described in the following [Apply a layout to a folder of components](#apply-a-layout-to-a-folder-of-components) section.</span></span>
* <span data-ttu-id="0c3e2-143">設定為應用程式的預設版面配置，如本文稍後的 [將預設版面配置套用至應用程式](#apply-a-default-layout-to-an-app) 一節中所述。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-143">Set as the app's default layout, as described in the [Apply a default layout to an app](#apply-a-default-layout-to-an-app) section later in this article.</span></span>

### <a name="apply-a-layout-to-a-folder-of-components"></a><span data-ttu-id="0c3e2-144">將版面配置套用至元件的資料夾</span><span class="sxs-lookup"><span data-stu-id="0c3e2-144">Apply a layout to a folder of components</span></span>

<span data-ttu-id="0c3e2-145">應用程式的每個資料夾都可以選擇性地包含名為的範本檔案 `_Imports.razor` 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-145">Every folder of an app can optionally contain a template file named `_Imports.razor`.</span></span> <span data-ttu-id="0c3e2-146">編譯器包含在相同資料夾中所有範本的 imports 檔中所指定的指示詞 Razor ，並以遞迴方式在其所有子資料夾中包含這些指令程式。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-146">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="0c3e2-147">因此， `_Imports.razor` 包含的檔案 `@layout DoctorWhoLayout` 可確保資料夾中的所有元件都使用該 `DoctorWhoLayout` 元件。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-147">Therefore, an `_Imports.razor` file containing `@layout DoctorWhoLayout` ensures that all of the components in a folder use the `DoctorWhoLayout` component.</span></span> <span data-ttu-id="0c3e2-148">在 `@layout DoctorWhoLayout` Razor `.razor` 資料夾和子資料夾內，不需要重複新增至 () 的所有元件。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-148">There's no need to repeatedly add `@layout DoctorWhoLayout` to all of the Razor components (`.razor`) within the folder and subfolders.</span></span>

<span data-ttu-id="0c3e2-149">`_Imports.razor`:</span><span class="sxs-lookup"><span data-stu-id="0c3e2-149">`_Imports.razor`:</span></span>

```razor
@layout DoctorWhoLayout
...
```

<span data-ttu-id="0c3e2-150">檔案 `_Imports.razor` 類似于 [ Razor views 和 pages 的 _ViewImports cshtml](xref:mvc/views/layout#importing-shared-directives) 檔案，但特別適用于 Razor 元件檔。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-150">The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="0c3e2-151">在中指定配置 `_Imports.razor` 會覆寫指定為路由器 [預設應用程式](#apply-a-default-layout-to-an-app)配置的版面配置，如下一節所述。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-151">Specifying a layout in `_Imports.razor` overrides a layout specified as the router's [default app layout](#apply-a-default-layout-to-an-app), which is described in the following section.</span></span>

> [!WARNING]
> <span data-ttu-id="0c3e2-152">請勿將指示詞加入 Razor `@layout` 至根檔案 `_Imports.razor` ，這會產生無限迴圈的版面配置。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-152">Do **not** add a Razor `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts.</span></span> <span data-ttu-id="0c3e2-153">若要控制預設的應用程式版面配置，請在元件中指定版面配置 `Router` 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-153">To control the default app layout, specify the layout in the `Router` component.</span></span> <span data-ttu-id="0c3e2-154">如需詳細資訊，請參閱下列 [將預設版面配置套用至應用程式](#apply-a-default-layout-to-an-app) 一節。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-154">For more information, see the following [Apply a default layout to an app](#apply-a-default-layout-to-an-app) section.</span></span>

> [!NOTE]
> <span data-ttu-id="0c3e2-155">指示詞 [`@layout`](xref:mvc/views/razor#layout) Razor 只會將配置套用至具有指示詞的可路由 Razor 元件 [`@page`](xref:mvc/views/razor#page) 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-155">The [`@layout`](xref:mvc/views/razor#layout) Razor directive only applies a layout to routable Razor components with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>

### <a name="apply-a-default-layout-to-an-app"></a><span data-ttu-id="0c3e2-156">將預設版面配置套用至應用程式</span><span class="sxs-lookup"><span data-stu-id="0c3e2-156">Apply a default layout to an app</span></span>

<span data-ttu-id="0c3e2-157">在元件的元件中，指定預設的應用程式版面配置 `App` <xref:Microsoft.AspNetCore.Components.Routing.Router> 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-157">Specify the default app layout in the in the `App` component's <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="0c3e2-158">下列以[ Blazor 專案範本](xref:blazor/project-structure)為基礎的應用程式範例，會將預設版面配置設定為 `MainLayout` 元件。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-158">The following example from an app based on a [Blazor project template](xref:blazor/project-structure) sets the default layout to the `MainLayout` component.</span></span>

<span data-ttu-id="0c3e2-159">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="0c3e2-159">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/layouts/App1.razor?highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/layouts/App1.razor?highlight=3)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="0c3e2-160">如需元件的詳細資訊 <xref:Microsoft.AspNetCore.Components.Routing.Router> ，請參閱 <xref:blazor/fundamentals/routing> 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-160">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/fundamentals/routing>.</span></span>

<span data-ttu-id="0c3e2-161">將版面配置指定為元件中的預設配置 `Router` 是很有用的作法，因為您可以覆寫個別元件或每個資料夾的版面配置，如本文先前各節所述。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-161">Specifying the layout as a default layout in the `Router` component is a useful practice because you can override the layout on a per-component or per-folder basis, as described in the preceding sections of this article.</span></span> <span data-ttu-id="0c3e2-162">建議您使用 `Router` 元件來設定應用程式的預設版面配置，因為這是使用版面配置最常見且彈性的方法。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-162">We recommend using the `Router` component to set the app's default layout because it's the most general and flexible approach for using layouts.</span></span>

### <a name="apply-a-layout-to-arbitrary-content-layoutview-component"></a><span data-ttu-id="0c3e2-163">將版面配置套用至任意內容 (`LayoutView` 元件) </span><span class="sxs-lookup"><span data-stu-id="0c3e2-163">Apply a layout to arbitrary content (`LayoutView` component)</span></span>

<span data-ttu-id="0c3e2-164">若要設定任意範本內容的版面配置 Razor ，請指定具有元件的配置 <xref:Microsoft.AspNetCore.Components.LayoutView> 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-164">To set a layout for arbitrary Razor template content, specify the layout with a <xref:Microsoft.AspNetCore.Components.LayoutView> component.</span></span> <span data-ttu-id="0c3e2-165">您可以 <xref:Microsoft.AspNetCore.Components.LayoutView> 在任何元件中使用 Razor 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-165">You can use a <xref:Microsoft.AspNetCore.Components.LayoutView> in any Razor component.</span></span> <span data-ttu-id="0c3e2-166">下列範例會 `ErrorLayout` 針對元件的範本，設定名為的版面配置元件 `MainLayout` <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> (`<NotFound>...</NotFound>`) 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-166">The following example sets a layout component named `ErrorLayout` for the `MainLayout` component's <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template (`<NotFound>...</NotFound>`).</span></span>

<span data-ttu-id="0c3e2-167">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="0c3e2-167">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/layouts/App2.razor?name=snippet&highlight=6,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/layouts/App2.razor?name=snippet&highlight=6,9)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

## <a name="nested-layouts"></a><span data-ttu-id="0c3e2-168">嵌套版面配置</span><span class="sxs-lookup"><span data-stu-id="0c3e2-168">Nested layouts</span></span>

<span data-ttu-id="0c3e2-169">元件可以參考配置，然後再參考另一個版面配置。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-169">A component can reference a layout that in turn references another layout.</span></span> <span data-ttu-id="0c3e2-170">例如，使用嵌套版面配置來建立多層級的功能表結構。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-170">For example, nested layouts are used to create a multi-level menu structures.</span></span>

<span data-ttu-id="0c3e2-171">下列範例示範如何使用嵌套版面配置。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-171">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="0c3e2-172">`Episodes`[將版面配置套用[至元件](#apply-a-layout-to-a-component)] 區段中顯示的元件是要顯示的元件。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-172">The `Episodes` component shown in the [Apply a layout to a component](#apply-a-layout-to-a-component) section is the component to display.</span></span> <span data-ttu-id="0c3e2-173">元件參考 `DoctorWhoLayout` 元件。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-173">The component references the `DoctorWhoLayout` component.</span></span>

<span data-ttu-id="0c3e2-174">下列 `DoctorWhoLayout` 元件是本文稍早所示之範例的修改版本。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-174">The following `DoctorWhoLayout` component is a modified version of the example shown earlier in this article.</span></span> <span data-ttu-id="0c3e2-175">標頭和頁尾元素會被移除，而配置會參考另一個版面配置 `ProductionsLayout` 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-175">The header and footer elements are removed, and the layout references another layout, `ProductionsLayout`.</span></span> <span data-ttu-id="0c3e2-176">`Episodes`元件會呈現 `@Body` 在中的位置 `DoctorWhoLayout` 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-176">The `Episodes` component is rendered where `@Body` appears in the `DoctorWhoLayout`.</span></span>

<span data-ttu-id="0c3e2-177">`Shared/DoctorWhoLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="0c3e2-177">`Shared/DoctorWhoLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor?highlight=2,12)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor?highlight=2,12)]

::: moniker-end

<span data-ttu-id="0c3e2-178">此 `ProductionsLayout` 元件包含最上層的版面配置元素，其中標頭 (`<header>...</header>`) 和頁尾 (`<footer>...</footer>`) 元素目前所在。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-178">The `ProductionsLayout` component contains the top-level layout elements, where the header (`<header>...</header>`) and footer (`<footer>...</footer>`) elements now reside.</span></span> <span data-ttu-id="0c3e2-179">具有元件的會在 `DoctorWhoLayout` `Episodes` 出現時呈現 `@Body` 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-179">The `DoctorWhoLayout` with the `Episodes` component is rendered where `@Body` appears.</span></span>

<span data-ttu-id="0c3e2-180">`Shared/ProductionsLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="0c3e2-180">`Shared/ProductionsLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor?highlight=13)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor?highlight=13)]

::: moniker-end

<span data-ttu-id="0c3e2-181">下列呈現的 HTML 標籤是由上述的嵌套配置所產生。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-181">The following rendered HTML markup is produced by the preceding nested layout.</span></span> <span data-ttu-id="0c3e2-182">不會出現無關的標記，以便專注于所涉及的三個元件所提供的嵌套內容：</span><span class="sxs-lookup"><span data-stu-id="0c3e2-182">Extraneous markup doesn't appear in order to focus on the nested content provided by the three components involved:</span></span>

* <span data-ttu-id="0c3e2-183">標頭 (`<header>...</header>`) 、生產導覽列 (`<nav>...</nav>`) 和頁尾 (`<footer>...</footer>` 元素和其內容來自 `ProductionsLayout` 元件。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-183">The header (`<header>...</header>`), production navigation bar (`<nav>...</nav>`), and footer (`<footer>...</footer>`) elements and their content come from the `ProductionsLayout` component.</span></span>
* <span data-ttu-id="0c3e2-184">將 **&trade; 資料庫** 標題 (`<h1>...</h1>`) 、集中導覽列 (`<nav>...</nav>`) 和商標資訊專案 () 來自元件的醫生 `<div>...</div>` `DoctorWhoLayout` 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-184">The **Doctor Who&trade; Episode Database** heading (`<h1>...</h1>`), episode navigation bar (`<nav>...</nav>`), and trademark information element (`<div>...</div>`) come from the `DoctorWhoLayout` component.</span></span>
* <span data-ttu-id="0c3e2-185"> () 來自元件的 **劇集** 標題 (`<h2>...</h2>`) 和劇集清單 `<ul>...</ul>` `Episodes` 。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-185">The **Episodes** heading (`<h2>...</h2>`) and episode list (`<ul>...</ul>`) come from the `Episodes` component.</span></span>

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

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="0c3e2-186">Razor使用整合元件共用頁面配置</span><span class="sxs-lookup"><span data-stu-id="0c3e2-186">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="0c3e2-187">當可路由的元件整合到 Razor 頁面應用程式時，應用程式的共用配置可以與元件一起使用。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-187">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="0c3e2-188">如需詳細資訊，請參閱<xref:blazor/components/prerendering-and-integration>。</span><span class="sxs-lookup"><span data-stu-id="0c3e2-188">For more information, see <xref:blazor/components/prerendering-and-integration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0c3e2-189">其他資源</span><span class="sxs-lookup"><span data-stu-id="0c3e2-189">Additional resources</span></span>

* <xref:mvc/views/layout>
