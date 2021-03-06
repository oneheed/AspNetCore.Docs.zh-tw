---
title: ASP.NET 核心元件的已呈現和整合 Razor
author: guardrex
description: 深入瞭解 Razor 應用程式的元件整合案例 Blazor ，包括伺服器上的元件的可呈現 Razor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: a86b50abff9c5ec52aab2bdb7eb6d563a5197d1a
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395171"
---
# <a name="prerender-and-integrate-aspnet-core-razor-components"></a><span data-ttu-id="7746d-103">ASP.NET 核心元件的已呈現和整合 Razor</span><span class="sxs-lookup"><span data-stu-id="7746d-103">Prerender and integrate ASP.NET Core Razor components</span></span>

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7746d-104">Razor 元件可以整合到 Razor 託管解決方案中的頁面和 MVC 應用程式 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="7746d-104">Razor components can be integrated into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="7746d-105">轉譯頁面或視圖時，可同時資源清單元件。</span><span class="sxs-lookup"><span data-stu-id="7746d-105">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="configuration"></a><span data-ttu-id="7746d-106">組態</span><span class="sxs-lookup"><span data-stu-id="7746d-106">Configuration</span></span>

<span data-ttu-id="7746d-107">若要設定應用程式的預先安裝 Blazor WebAssembly ：</span><span class="sxs-lookup"><span data-stu-id="7746d-107">To set up prerendering for a Blazor WebAssembly app:</span></span>

1. <span data-ttu-id="7746d-108">Blazor WebAssembly在 ASP.NET Core 應用程式中裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="7746d-108">Host the Blazor WebAssembly app in an ASP.NET Core app.</span></span> <span data-ttu-id="7746d-109">獨立 Blazor WebAssembly 應用程式可以新增至 ASP.NET Core 方案，或者您可以使用 Blazor WebAssembly 從[ Blazor WebAssembly 專案範本](xref:blazor/project-structure)建立的託管應用程式。</span><span class="sxs-lookup"><span data-stu-id="7746d-109">A standalone Blazor WebAssembly app can be added to an ASP.NET Core solution, or you can use a hosted Blazor WebAssembly app created from the [Blazor WebAssembly project template](xref:blazor/project-structure).</span></span>

1. <span data-ttu-id="7746d-110">`wwwroot/index.html`從用戶端專案中移除預設靜態檔案 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="7746d-110">Remove the default static `wwwroot/index.html` file from the Blazor WebAssembly client project.</span></span>

1. <span data-ttu-id="7746d-111">刪除用戶端專案中的下列程式 `Program.Main` 程式碼：</span><span class="sxs-lookup"><span data-stu-id="7746d-111">Delete the following line in `Program.Main` in the client project:</span></span>

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. <span data-ttu-id="7746d-112">將檔案加入 `Pages/_Host.cshtml` 至伺服器專案。</span><span class="sxs-lookup"><span data-stu-id="7746d-112">Add a `Pages/_Host.cshtml` file to the server project.</span></span> <span data-ttu-id="7746d-113">您可以 `_Host.cshtml` Blazor Server 使用命令 shell 中的命令，從範本建立的應用程式取得檔案 `dotnet new blazorserver -o BlazorServer` 。</span><span class="sxs-lookup"><span data-stu-id="7746d-113">You can obtain a `_Host.cshtml` file from an app created from the Blazor Server template with the `dotnet new blazorserver -o BlazorServer` command in a command shell.</span></span> <span data-ttu-id="7746d-114">將檔案放 `Pages/_Host.cshtml` 入託管解決方案的伺服器應用程式之後 Blazor WebAssembly ，對檔案進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="7746d-114">After placing the `Pages/_Host.cshtml` file into the server app of the hosted Blazor WebAssembly solution, make the following changes to the file:</span></span>

   * <span data-ttu-id="7746d-115">將命名空間設定為伺服器應用程式的 `Pages` 資料夾 (例如 `@namespace BlazorHosted.Server.Pages`) 。</span><span class="sxs-lookup"><span data-stu-id="7746d-115">Set the namespace to the server app's `Pages` folder (for example, `@namespace BlazorHosted.Server.Pages`).</span></span>
   * <span data-ttu-id="7746d-116">設定 [`@using`](xref:mvc/views/razor#using) 用戶端專案的指示詞 (例如 `@using BlazorHosted.Client`) 。</span><span class="sxs-lookup"><span data-stu-id="7746d-116">Set an [`@using`](xref:mvc/views/razor#using) directive for the client project (for example, `@using BlazorHosted.Client`).</span></span>
   * <span data-ttu-id="7746d-117">更新樣式表單連結以指向 WebAssembly 應用程式的樣式表單。</span><span class="sxs-lookup"><span data-stu-id="7746d-117">Update the stylesheet links to point to the WebAssembly app's stylesheet.</span></span> <span data-ttu-id="7746d-118">在下列範例中，用戶端應用程式的命名空間為 `BlazorHosted.Client` ：</span><span class="sxs-lookup"><span data-stu-id="7746d-118">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * <span data-ttu-id="7746d-119">更新 `render-mode` [元件標記](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) 協助程式的，以呈現根 `App` 元件：</span><span class="sxs-lookup"><span data-stu-id="7746d-119">Update the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to prerender the root `App` component:</span></span>

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * <span data-ttu-id="7746d-120">更新 Blazor 腳本來源以使用用戶端 Blazor WebAssembly 腳本：</span><span class="sxs-lookup"><span data-stu-id="7746d-120">Update the Blazor script source to use the client-side Blazor WebAssembly script:</span></span>

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. <span data-ttu-id="7746d-121">在 `Startup.Configure` `Startup.cs` 伺服器專案的 () 中：</span><span class="sxs-lookup"><span data-stu-id="7746d-121">In `Startup.Configure` (`Startup.cs`) of the server project:</span></span>

   * <span data-ttu-id="7746d-122">在 `UseDeveloperExceptionPage` 開發環境中呼叫應用程式建立器。</span><span class="sxs-lookup"><span data-stu-id="7746d-122">Call `UseDeveloperExceptionPage` on the app builder in the Development environment.</span></span>
   * <span data-ttu-id="7746d-123">`UseBlazorFrameworkFiles`在 app builder 上呼叫。</span><span class="sxs-lookup"><span data-stu-id="7746d-123">Call `UseBlazorFrameworkFiles` on the app builder.</span></span>
   * <span data-ttu-id="7746d-124">將 () 的檔案切換回 `index.html` `endpoints.MapFallbackToFile("index.html");` `_Host.cshtml` 頁面： `endpoints.MapFallbackToPage("/_Host");` 。</span><span class="sxs-lookup"><span data-stu-id="7746d-124">Change the fallback from the `index.html` file (`endpoints.MapFallbackToFile("index.html");`) to the `_Host.cshtml` page: `endpoints.MapFallbackToPage("/_Host");`.</span></span>

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
           app.UseWebAssemblyDebugging();
       }
       else
       {
           app.UseExceptionHandler("/Error");
           app.UseHsts();
       }

       app.UseHttpsRedirection();
       app.UseBlazorFrameworkFiles();
       app.UseStaticFiles();

       app.UseRouting();

       app.UseEndpoints(endpoints =>
       {
           endpoints.MapRazorPages();
           endpoints.MapControllers();
           endpoints.MapFallbackToPage("/_Host");
       });
   }
   ```

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a><span data-ttu-id="7746d-125">使用元件標記協助程式在頁面或視圖中轉譯元件</span><span class="sxs-lookup"><span data-stu-id="7746d-125">Render components in a page or view with the Component Tag Helper</span></span>

<span data-ttu-id="7746d-126">元件標籤協助程式支援兩種轉譯模式，可從 Blazor WebAssembly 頁面或視圖中的應用程式呈現元件：</span><span class="sxs-lookup"><span data-stu-id="7746d-126">The Component Tag Helper supports two render modes for rendering a component from a Blazor WebAssembly app in a page or view:</span></span>

* <span data-ttu-id="7746d-127">`WebAssembly`：轉譯應用程式的標記，以在 Blazor WebAssembly 瀏覽器中載入時用來包含互動式元件。</span><span class="sxs-lookup"><span data-stu-id="7746d-127">`WebAssembly`: Renders a marker for a Blazor WebAssembly app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="7746d-128">元件不是資源清單。</span><span class="sxs-lookup"><span data-stu-id="7746d-128">The component isn't prerendered.</span></span> <span data-ttu-id="7746d-129">此選項可讓您更輕鬆地 Blazor WebAssembly 在不同的頁面上呈現不同的元件。</span><span class="sxs-lookup"><span data-stu-id="7746d-129">This option makes it easier to render different Blazor WebAssembly components on different pages.</span></span>
* <span data-ttu-id="7746d-130">`WebAssemblyPrerendered`：將元件 Prerenders 為靜態 HTML，並包含應用程式的標記，以 Blazor WebAssembly 供稍後用來在瀏覽器中載入元件時進行互動。</span><span class="sxs-lookup"><span data-stu-id="7746d-130">`WebAssemblyPrerendered`: Prerenders the component into static HTML and includes a marker for a Blazor WebAssembly app for later use to make the component interactive when loaded in the browser.</span></span>

<span data-ttu-id="7746d-131">在下列 Razor 頁面範例中， `Counter` 元件會在頁面中呈現。</span><span class="sxs-lookup"><span data-stu-id="7746d-131">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="7746d-132">若要使元件互動， Blazor WebAssembly 腳本會包含在頁面的 [轉譯] [區段](xref:mvc/views/layout#sections)中。</span><span class="sxs-lookup"><span data-stu-id="7746d-132">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections).</span></span> <span data-ttu-id="7746d-133">若要避免使用元件標記協助程式 () 的完整命名空間 `Counter` `{APP ASSEMBLY}.Pages.Counter` ，請為 [`@using`](xref:mvc/views/razor#using) 用戶端應用程式的 `Pages` 命名空間新增指示詞。</span><span class="sxs-lookup"><span data-stu-id="7746d-133">To avoid using the full namespace for the `Counter` component with the Component Tag Helper (`{APP ASSEMBLY}.Pages.Counter`), add an [`@using`](xref:mvc/views/razor#using) directive for the client app's `Pages` namespace.</span></span> <span data-ttu-id="7746d-134">在下列範例中，用戶端應用程式的命名空間為 `BlazorHosted.Client` ：</span><span class="sxs-lookup"><span data-stu-id="7746d-134">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="7746d-135"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 設定元件是否為：</span><span class="sxs-lookup"><span data-stu-id="7746d-135"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="7746d-136">會資源清單到頁面中。</span><span class="sxs-lookup"><span data-stu-id="7746d-136">Is prerendered into the page.</span></span>
* <span data-ttu-id="7746d-137">會在頁面上轉譯為靜態 HTML，或包含從使用者代理程式啟動應用程式所需的資訊 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="7746d-137">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

<span data-ttu-id="7746d-138">如需元件標記協助程式的詳細資訊，包括傳遞參數和設定 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> ，請參閱 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> 。</span><span class="sxs-lookup"><span data-stu-id="7746d-138">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

<span data-ttu-id="7746d-139">上述範例需要伺服器應用程式的版面配置 (`_Layout.cshtml`) 包含結束標記內腳本的轉譯 [區段](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) `</body>` ：</span><span class="sxs-lookup"><span data-stu-id="7746d-139">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="7746d-140">檔案 `_Layout.cshtml` 位於 MVC 應用程式中 `Pages/Shared` Razor 頁面應用程式或資料夾的資料夾中 `Views/Shared` 。</span><span class="sxs-lookup"><span data-stu-id="7746d-140">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="7746d-141">如果應用程式也應該將應用程式中的樣式作為元件樣式 Blazor WebAssembly ，請在檔案中包含應用程式的樣式 `_Layout.cshtml` 。</span><span class="sxs-lookup"><span data-stu-id="7746d-141">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="7746d-142">在下列範例中，用戶端應用程式的命名空間為 `BlazorHosted.Client` ：</span><span class="sxs-lookup"><span data-stu-id="7746d-142">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a><span data-ttu-id="7746d-143">使用 CSS 選取器呈現頁面或視圖中的元件</span><span class="sxs-lookup"><span data-stu-id="7746d-143">Render components in a page or view with a CSS selector</span></span>

<span data-ttu-id="7746d-144">在 () 中，將根元件新增至 *用戶端* 專案 `Program.Main` `Program.cs` 。</span><span class="sxs-lookup"><span data-stu-id="7746d-144">Add root components to the *Client* project in `Program.Main` (`Program.cs`).</span></span> <span data-ttu-id="7746d-145">在下列範例中， `Counter` 元件會宣告為根元件，其中包含的 CSS 選取器會使用 `id` 符合的來選取專案 `my-counter` 。</span><span class="sxs-lookup"><span data-stu-id="7746d-145">In the following example, the `Counter` component is declared as a root component with a CSS selector that selects the element with the `id` that matches `my-counter`.</span></span> <span data-ttu-id="7746d-146">在下列範例中，用戶端應用程式的命名空間為 `BlazorHosted.Client` ：</span><span class="sxs-lookup"><span data-stu-id="7746d-146">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

<span data-ttu-id="7746d-147">在下列 Razor 頁面範例中， `Counter` 元件會在頁面中呈現。</span><span class="sxs-lookup"><span data-stu-id="7746d-147">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="7746d-148">若要使元件互動， Blazor WebAssembly 腳本會包含在頁面的轉譯 [區段](xref:mvc/views/layout#sections)中：</span><span class="sxs-lookup"><span data-stu-id="7746d-148">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections):</span></span>

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="7746d-149">上述範例需要伺服器應用程式的版面配置 (`_Layout.cshtml`) 包含結束標記內腳本的轉譯 [區段](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) `</body>` ：</span><span class="sxs-lookup"><span data-stu-id="7746d-149">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="7746d-150">檔案 `_Layout.cshtml` 位於 MVC 應用程式中 `Pages/Shared` Razor 頁面應用程式或資料夾的資料夾中 `Views/Shared` 。</span><span class="sxs-lookup"><span data-stu-id="7746d-150">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="7746d-151">如果應用程式也應該將應用程式中的樣式作為元件樣式 Blazor WebAssembly ，請在檔案中包含應用程式的樣式 `_Layout.cshtml` 。</span><span class="sxs-lookup"><span data-stu-id="7746d-151">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="7746d-152">在下列範例中，用戶端應用程式的命名空間為 `BlazorHosted.Client` ：</span><span class="sxs-lookup"><span data-stu-id="7746d-152">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="7746d-153">Razor Razor Blazor WebAssembly .Net 5 或更新版本的 ASP.NET Core 支援將元件整合至裝載方案中的頁面和 MVC 應用程式。</span><span class="sxs-lookup"><span data-stu-id="7746d-153">Integrating Razor components into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution is supported in ASP.NET Core in .NET 5 or later.</span></span> <span data-ttu-id="7746d-154">請選取此文章的 .NET 5 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="7746d-154">Select a .NET 5 or later version of this article.</span></span>

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="7746d-155">Razor 元件可以整合至 Razor 應用程式中的頁面和 MVC 應用程式 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="7746d-155">Razor components can be integrated into Razor Pages and MVC apps in a Blazor Server app.</span></span> <span data-ttu-id="7746d-156">轉譯頁面或視圖時，可同時資源清單元件。</span><span class="sxs-lookup"><span data-stu-id="7746d-156">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="7746d-157">設定 [應用程式](#configuration)之後，請根據應用程式的需求，使用下列各節中的指導方針：</span><span class="sxs-lookup"><span data-stu-id="7746d-157">After [configuring the app](#configuration), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="7746d-158">可路由的元件：適用于直接從使用者要求路由傳送的元件。</span><span class="sxs-lookup"><span data-stu-id="7746d-158">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="7746d-159">當訪客應能在其瀏覽器中使用指示詞為元件發出 HTTP 要求時，請遵循此指導 [`@page`](xref:mvc/views/razor#page) 方針。</span><span class="sxs-lookup"><span data-stu-id="7746d-159">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="7746d-160">在頁面應用程式中使用可路由的元件 Razor</span><span class="sxs-lookup"><span data-stu-id="7746d-160">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="7746d-161">在 MVC 應用程式中使用可路由的元件</span><span class="sxs-lookup"><span data-stu-id="7746d-161">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="7746d-162">[從頁面或視圖呈現元件](#render-components-from-a-page-or-view)：適用于無法直接從使用者要求路由傳送的元件。</span><span class="sxs-lookup"><span data-stu-id="7746d-162">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="7746d-163">當應用程式使用元件標籤協助程式將元件內嵌至現有的頁面和流覽 [器](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)時，請遵循此指引。</span><span class="sxs-lookup"><span data-stu-id="7746d-163">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="configuration"></a><span data-ttu-id="7746d-164">組態</span><span class="sxs-lookup"><span data-stu-id="7746d-164">Configuration</span></span>

<span data-ttu-id="7746d-165">現有的 Razor 頁面或 MVC 應用程式可以將 Razor 元件整合至頁面和流覽中：</span><span class="sxs-lookup"><span data-stu-id="7746d-165">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="7746d-166">在應用程式的版面配置檔案中 (`_Layout.cshtml`) ：</span><span class="sxs-lookup"><span data-stu-id="7746d-166">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="7746d-167">將下列 `<base>` 標記新增至 `<head>` 元素：</span><span class="sxs-lookup"><span data-stu-id="7746d-167">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="7746d-168">`href`上述範例中的 *應用程式基底路徑*)  (值假設應用程式位於根 URL 路徑 (`/`) 。</span><span class="sxs-lookup"><span data-stu-id="7746d-168">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="7746d-169">如果應用程式是子應用程式，請遵循本文的「 *應用程式基底路徑* 」一節中的指導方針 <xref:blazor/host-and-deploy/index#app-base-path> 。</span><span class="sxs-lookup"><span data-stu-id="7746d-169">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="7746d-170">檔案 `_Layout.cshtml` 位於 MVC 應用程式中 `Pages/Shared` Razor 頁面應用程式或資料夾的資料夾中 `Views/Shared` 。</span><span class="sxs-lookup"><span data-stu-id="7746d-170">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

   * <span data-ttu-id="7746d-171">在轉譯 `<script>` `blazor.server.js` 區段之前，立即新增腳本的標記 `Scripts` ：</span><span class="sxs-lookup"><span data-stu-id="7746d-171">Add a `<script>` tag for the `blazor.server.js` script immediately before the `Scripts` render section:</span></span>

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     <span data-ttu-id="7746d-172">架構會將 `blazor.server.js` 腳本新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="7746d-172">The framework adds the `blazor.server.js` script to the app.</span></span> <span data-ttu-id="7746d-173">不需要手動將腳本新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="7746d-173">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="7746d-174">`_Imports.razor`使用下列內容將檔案新增至專案的根資料夾， (將最後一個命名空間變更 `MyAppNamespace` 為應用程式) 的命名空間：</span><span class="sxs-lookup"><span data-stu-id="7746d-174">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="7746d-175">在中 `Startup.ConfigureServices` ，註冊 Blazor Server 服務：</span><span class="sxs-lookup"><span data-stu-id="7746d-175">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="7746d-176">在中 `Startup.Configure` ，將 Blazor 中樞端點新增至 `app.UseEndpoints` ：</span><span class="sxs-lookup"><span data-stu-id="7746d-176">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="7746d-177">將元件整合至任何頁面或視圖中。</span><span class="sxs-lookup"><span data-stu-id="7746d-177">Integrate components into any page or view.</span></span> <span data-ttu-id="7746d-178">如需詳細資訊，請參閱 [從頁面或視圖區段呈現元件](#render-components-from-a-page-or-view) 。</span><span class="sxs-lookup"><span data-stu-id="7746d-178">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="7746d-179">在頁面應用程式中使用可路由的元件 Razor</span><span class="sxs-lookup"><span data-stu-id="7746d-179">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="7746d-180">*本節適用于新增直接可從使用者要求路由傳送的元件。*</span><span class="sxs-lookup"><span data-stu-id="7746d-180">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="7746d-181">支援 Razor 頁面應用程式中可路由的元件 Razor ：</span><span class="sxs-lookup"><span data-stu-id="7746d-181">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="7746d-182">遵循 [設定一節中的指導](#configuration) 方針。</span><span class="sxs-lookup"><span data-stu-id="7746d-182">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="7746d-183">`App.razor`使用下列內容將檔案新增至專案根目錄：</span><span class="sxs-lookup"><span data-stu-id="7746d-183">Add an `App.razor` file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. <span data-ttu-id="7746d-184">將檔案新增 `_Host.cshtml` 至 `Pages` 具有下列內容的資料夾：</span><span class="sxs-lookup"><span data-stu-id="7746d-184">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="7746d-185">元件會使用共用檔案 `_Layout.cshtml` 進行版面配置。</span><span class="sxs-lookup"><span data-stu-id="7746d-185">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="7746d-186"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 設定元件是否為 `App` ：</span><span class="sxs-lookup"><span data-stu-id="7746d-186"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="7746d-187">會資源清單到頁面中。</span><span class="sxs-lookup"><span data-stu-id="7746d-187">Is prerendered into the page.</span></span>
   * <span data-ttu-id="7746d-188">會在頁面上轉譯為靜態 HTML，或包含從使用者代理程式啟動應用程式所需的資訊 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="7746d-188">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="7746d-189">如需元件標記協助程式的詳細資訊，包括傳遞參數和設定 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> ，請參閱 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> 。</span><span class="sxs-lookup"><span data-stu-id="7746d-189">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="7746d-190">將頁面的低優先順序路由新增 `_Host.cshtml` 至中的端點設定 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="7746d-190">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="7746d-191">將可路由傳送的元件新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="7746d-191">Add routable components to the app.</span></span> <span data-ttu-id="7746d-192">例如：</span><span class="sxs-lookup"><span data-stu-id="7746d-192">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="7746d-193">如需命名空間的詳細資訊，請參閱 [元件命名空間](#component-namespaces) 一節。</span><span class="sxs-lookup"><span data-stu-id="7746d-193">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="7746d-194">在 MVC 應用程式中使用可路由的元件</span><span class="sxs-lookup"><span data-stu-id="7746d-194">Use routable components in an MVC app</span></span>

<span data-ttu-id="7746d-195">*本節適用于新增直接可從使用者要求路由傳送的元件。*</span><span class="sxs-lookup"><span data-stu-id="7746d-195">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="7746d-196">支援 Razor MVC 應用程式中的可路由元件：</span><span class="sxs-lookup"><span data-stu-id="7746d-196">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="7746d-197">遵循 [設定一節中的指導](#configuration) 方針。</span><span class="sxs-lookup"><span data-stu-id="7746d-197">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="7746d-198">`App.razor`使用下列內容將檔案新增至專案的根目錄：</span><span class="sxs-lookup"><span data-stu-id="7746d-198">Add an `App.razor` file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. <span data-ttu-id="7746d-199">將檔案新增 `_Host.cshtml` 至 `Views/Home` 具有下列內容的資料夾：</span><span class="sxs-lookup"><span data-stu-id="7746d-199">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="7746d-200">元件會使用共用檔案 `_Layout.cshtml` 進行版面配置。</span><span class="sxs-lookup"><span data-stu-id="7746d-200">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="7746d-201"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 設定元件是否為 `App` ：</span><span class="sxs-lookup"><span data-stu-id="7746d-201"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="7746d-202">會資源清單到頁面中。</span><span class="sxs-lookup"><span data-stu-id="7746d-202">Is prerendered into the page.</span></span>
   * <span data-ttu-id="7746d-203">會在頁面上轉譯為靜態 HTML，或包含從使用者代理程式啟動應用程式所需的資訊 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="7746d-203">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="7746d-204">如需元件標記協助程式的詳細資訊，包括傳遞參數和設定 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> ，請參閱 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> 。</span><span class="sxs-lookup"><span data-stu-id="7746d-204">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="7746d-205">將動作新增至 Home 控制器：</span><span class="sxs-lookup"><span data-stu-id="7746d-205">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="7746d-206">新增控制器動作的低優先順序路由，將 `_Host.cshtml` 視圖傳回至中的端點設定 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="7746d-206">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="7746d-207">建立 `Pages` 資料夾，並將可路由傳送的元件新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="7746d-207">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="7746d-208">例如：</span><span class="sxs-lookup"><span data-stu-id="7746d-208">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="7746d-209">如需命名空間的詳細資訊，請參閱 [元件命名空間](#component-namespaces) 一節。</span><span class="sxs-lookup"><span data-stu-id="7746d-209">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="7746d-210">從頁面或視圖呈現元件</span><span class="sxs-lookup"><span data-stu-id="7746d-210">Render components from a page or view</span></span>

<span data-ttu-id="7746d-211">*本節適用于將元件新增至頁面或視圖，其中元件無法直接從使用者要求路由傳送。*</span><span class="sxs-lookup"><span data-stu-id="7746d-211">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="7746d-212">若要從頁面或視圖轉譯元件，請使用 [元件標記](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)協助程式。</span><span class="sxs-lookup"><span data-stu-id="7746d-212">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="7746d-213">轉譯具狀態的互動式元件</span><span class="sxs-lookup"><span data-stu-id="7746d-213">Render stateful interactive components</span></span>

<span data-ttu-id="7746d-214">可設定狀態的互動式元件可以加入至 Razor 頁面或視圖中。</span><span class="sxs-lookup"><span data-stu-id="7746d-214">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="7746d-215">當頁面或視圖呈現時：</span><span class="sxs-lookup"><span data-stu-id="7746d-215">When the page or view renders:</span></span>

* <span data-ttu-id="7746d-216">此元件是使用頁面或視圖所資源清單。</span><span class="sxs-lookup"><span data-stu-id="7746d-216">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="7746d-217">用於進行可呈現的初始元件狀態會遺失。</span><span class="sxs-lookup"><span data-stu-id="7746d-217">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="7746d-218">建立連接時，會建立新的元件狀態 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="7746d-218">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="7746d-219">下列 Razor 頁面會呈現 `Counter` 元件：</span><span class="sxs-lookup"><span data-stu-id="7746d-219">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="7746d-220">如需詳細資訊，請參閱<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="7746d-220">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="7746d-221">轉譯非互動式元件</span><span class="sxs-lookup"><span data-stu-id="7746d-221">Render noninteractive components</span></span>

<span data-ttu-id="7746d-222">在下 Razor 一個頁面中， `Counter` 系統會使用表單所指定的初始值，以靜態方式呈現元件。</span><span class="sxs-lookup"><span data-stu-id="7746d-222">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="7746d-223">由於元件是以靜態方式轉譯，因此元件並非互動式：</span><span class="sxs-lookup"><span data-stu-id="7746d-223">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="7746d-224">如需詳細資訊，請參閱<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="7746d-224">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="7746d-225">元件命名空間</span><span class="sxs-lookup"><span data-stu-id="7746d-225">Component namespaces</span></span>

<span data-ttu-id="7746d-226">使用自訂資料夾來保存應用程式的元件時，請將代表資料夾的命名空間新增至頁面/視圖或檔案 `_ViewImports.cshtml` 。</span><span class="sxs-lookup"><span data-stu-id="7746d-226">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="7746d-227">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="7746d-227">In the following example:</span></span>

* <span data-ttu-id="7746d-228">變更 `MyAppNamespace` 為應用程式的命名空間。</span><span class="sxs-lookup"><span data-stu-id="7746d-228">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="7746d-229">如果未使用名為的資料夾 `Components` 來保存元件，請變更 `Components` 為元件所在的資料夾。</span><span class="sxs-lookup"><span data-stu-id="7746d-229">If a folder named `Components` isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="7746d-230">檔案 `_ViewImports.cshtml` 位於 `Pages` Razor 頁面應用程式的資料夾或 `Views` MVC 應用程式的資料夾中。</span><span class="sxs-lookup"><span data-stu-id="7746d-230">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="7746d-231">如需詳細資訊，請參閱<xref:blazor/components/index#namespaces>。</span><span class="sxs-lookup"><span data-stu-id="7746d-231">For more information, see <xref:blazor/components/index#namespaces>.</span></span>

::: zone-end
