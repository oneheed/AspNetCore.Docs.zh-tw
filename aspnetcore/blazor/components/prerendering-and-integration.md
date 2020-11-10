---
title: 'ASP.NET Core 元件的已呈現和整合 :::no-loc(Razor):::'
author: guardrex
description: '深入瞭解 :::no-loc(Razor)::: 應用程式的元件整合案例 :::no-loc(Blazor)::: ，包括伺服器上的元件的可呈現 :::no-loc(Razor)::: 。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: affca6c9b585b91787f94a13144d07bedfefdd37
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431700"
---
# <a name="prerender-and-integrate-aspnet-core-no-locrazor-components"></a><span data-ttu-id="71f11-103">ASP.NET Core 元件的已呈現和整合 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="71f11-103">Prerender and integrate ASP.NET Core :::no-loc(Razor)::: components</span></span>

<span data-ttu-id="71f11-104">依 [Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="71f11-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="71f11-105">:::no-loc(Razor)::: 元件可以整合到 :::no-loc(Razor)::: 託管解決方案中的頁面和 MVC 應用程式 :::no-loc(Blazor WebAssembly)::: 。</span><span class="sxs-lookup"><span data-stu-id="71f11-105">:::no-loc(Razor)::: components can be integrated into :::no-loc(Razor)::: Pages and MVC apps in a hosted :::no-loc(Blazor WebAssembly)::: solution.</span></span> <span data-ttu-id="71f11-106">轉譯頁面或視圖時，可同時資源清單元件。</span><span class="sxs-lookup"><span data-stu-id="71f11-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="configuration"></a><span data-ttu-id="71f11-107">設定</span><span class="sxs-lookup"><span data-stu-id="71f11-107">Configuration</span></span>

<span data-ttu-id="71f11-108">若要設定應用程式的預先安裝 :::no-loc(Blazor WebAssembly)::: ：</span><span class="sxs-lookup"><span data-stu-id="71f11-108">To set up prerendering for a :::no-loc(Blazor WebAssembly)::: app:</span></span>

1. <span data-ttu-id="71f11-109">:::no-loc(Blazor WebAssembly):::在 ASP.NET Core 應用程式中裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="71f11-109">Host the :::no-loc(Blazor WebAssembly)::: app in an ASP.NET Core app.</span></span> <span data-ttu-id="71f11-110">獨立 :::no-loc(Blazor WebAssembly)::: 應用程式可以新增至 ASP.NET Core 方案，或者您可以使用 :::no-loc(Blazor WebAssembly)::: 從裝載的專案範本建立的託管應用程式 :::no-loc(Blazor)::: 。</span><span class="sxs-lookup"><span data-stu-id="71f11-110">A standalone :::no-loc(Blazor WebAssembly)::: app can be added to an ASP.NET Core solution, or you can use a hosted :::no-loc(Blazor WebAssembly)::: app created from the :::no-loc(Blazor)::: Hosted project template.</span></span>

1. <span data-ttu-id="71f11-111">`wwwroot/index.html`從用戶端專案中移除預設靜態檔案 :::no-loc(Blazor WebAssembly)::: 。</span><span class="sxs-lookup"><span data-stu-id="71f11-111">Remove the default static `wwwroot/index.html` file from the :::no-loc(Blazor WebAssembly)::: client project.</span></span>

1. <span data-ttu-id="71f11-112">刪除用戶端專案中的下列程式 `Program.Main` 程式碼：</span><span class="sxs-lookup"><span data-stu-id="71f11-112">Delete the following line in `Program.Main` in the client project:</span></span>

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. <span data-ttu-id="71f11-113">將檔案加入 `Pages/_Host.cshtml` 至伺服器專案。</span><span class="sxs-lookup"><span data-stu-id="71f11-113">Add a `Pages/_Host.cshtml` file to the server project.</span></span> <span data-ttu-id="71f11-114">您可以 `_Host.cshtml` :::no-loc(Blazor Server)::: 使用命令 shell 中的命令，從範本建立的應用程式取得檔案 `dotnet new blazorserver -o :::no-loc(Blazor):::Server` 。</span><span class="sxs-lookup"><span data-stu-id="71f11-114">You can obtain a `_Host.cshtml` file from an app created from the :::no-loc(Blazor Server)::: template with the `dotnet new blazorserver -o :::no-loc(Blazor):::Server` command in a command shell.</span></span> <span data-ttu-id="71f11-115">將檔案放 `Pages/_Host.cshtml` 入託管解決方案的伺服器應用程式之後 :::no-loc(Blazor WebAssembly)::: ，對檔案進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="71f11-115">After placing the `Pages/_Host.cshtml` file into the server app of the hosted :::no-loc(Blazor WebAssembly)::: solution, make the following changes to the file:</span></span>

   * <span data-ttu-id="71f11-116">將命名空間設定為伺服器應用程式的 `Pages` 資料夾 (例如 `@namespace :::no-loc(Blazor):::Hosted.Server.Pages`) 。</span><span class="sxs-lookup"><span data-stu-id="71f11-116">Set the namespace to the server app's `Pages` folder (for example, `@namespace :::no-loc(Blazor):::Hosted.Server.Pages`).</span></span>
   * <span data-ttu-id="71f11-117">設定 [`@using`](xref:mvc/views/razor#using) 用戶端專案的指示詞 (例如 `@using :::no-loc(Blazor):::Hosted.Client`) 。</span><span class="sxs-lookup"><span data-stu-id="71f11-117">Set an [`@using`](xref:mvc/views/razor#using) directive for the client project (for example, `@using :::no-loc(Blazor):::Hosted.Client`).</span></span>
   * <span data-ttu-id="71f11-118">更新樣式表單連結以指向 WebAssembly 應用程式的樣式表單。</span><span class="sxs-lookup"><span data-stu-id="71f11-118">Update the stylesheet links to point to the WebAssembly app's stylesheet.</span></span> <span data-ttu-id="71f11-119">在下列範例中，用戶端應用程式的命名空間為 `:::no-loc(Blazor):::Hosted.Client` ：</span><span class="sxs-lookup"><span data-stu-id="71f11-119">In the following example, the client app's namespace is `:::no-loc(Blazor):::Hosted.Client`:</span></span>

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href=":::no-loc(Blazor):::Hosted.Client.styles.css" rel="stylesheet" />
     ```

   * <span data-ttu-id="71f11-120">更新 `render-mode` [元件標記](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) 協助程式的，以呈現根 `App` 元件：</span><span class="sxs-lookup"><span data-stu-id="71f11-120">Update the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to prerender the root `App` component:</span></span>

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * <span data-ttu-id="71f11-121">更新 :::no-loc(Blazor)::: 腳本來源以使用用戶端 :::no-loc(Blazor WebAssembly)::: 腳本：</span><span class="sxs-lookup"><span data-stu-id="71f11-121">Update the :::no-loc(Blazor)::: script source to use the client-side :::no-loc(Blazor WebAssembly)::: script:</span></span>

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. <span data-ttu-id="71f11-122">在 `Startup.Configure` `Startup.cs` 伺服器專案的 () 中：</span><span class="sxs-lookup"><span data-stu-id="71f11-122">In `Startup.Configure` (`Startup.cs`) of the server project:</span></span>

   * <span data-ttu-id="71f11-123">在 `UseDeveloperExceptionPage` 開發環境中呼叫應用程式建立器。</span><span class="sxs-lookup"><span data-stu-id="71f11-123">Call `UseDeveloperExceptionPage` on the app builder in the Development environment.</span></span>
   * <span data-ttu-id="71f11-124">`Use:::no-loc(Blazor):::FrameworkFiles`在 app builder 上呼叫。</span><span class="sxs-lookup"><span data-stu-id="71f11-124">Call `Use:::no-loc(Blazor):::FrameworkFiles` on the app builder.</span></span>
   * <span data-ttu-id="71f11-125">將 `index.html` 頁面 () 的回復變更 `endpoints.MapFallbackToFile("index.html");` 為 `_Host.cshtml` 頁面。</span><span class="sxs-lookup"><span data-stu-id="71f11-125">Change the fallback from the `index.html` page (`endpoints.MapFallbackToFile("index.html");`) to the `_Host.cshtml` page.</span></span>

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
       app.Use:::no-loc(Blazor):::FrameworkFiles();
       app.UseStaticFiles();

       app.UseRouting();

       app.UseEndpoints(endpoints =>
       {
           endpoints.Map:::no-loc(Razor):::Pages();
           endpoints.MapControllers();
           endpoints.MapFallbackToPage("/_Host");
       });
   }
   ```

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a><span data-ttu-id="71f11-126">使用元件標記協助程式在頁面或視圖中轉譯元件</span><span class="sxs-lookup"><span data-stu-id="71f11-126">Render components in a page or view with the Component Tag Helper</span></span>

<span data-ttu-id="71f11-127">元件標籤協助程式支援兩種轉譯模式，可從 :::no-loc(Blazor WebAssembly)::: 頁面或視圖中的應用程式呈現元件：</span><span class="sxs-lookup"><span data-stu-id="71f11-127">The Component Tag Helper supports two render modes for rendering a component from a :::no-loc(Blazor WebAssembly)::: app in a page or view:</span></span>

* <span data-ttu-id="71f11-128">`WebAssembly`：轉譯應用程式的標記，以在 :::no-loc(Blazor WebAssembly)::: 瀏覽器中載入時用來包含互動式元件。</span><span class="sxs-lookup"><span data-stu-id="71f11-128">`WebAssembly`: Renders a marker for a :::no-loc(Blazor WebAssembly)::: app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="71f11-129">元件不是資源清單。</span><span class="sxs-lookup"><span data-stu-id="71f11-129">The component isn't prerendered.</span></span> <span data-ttu-id="71f11-130">此選項可讓您更輕鬆地 :::no-loc(Blazor WebAssembly)::: 在不同的頁面上呈現不同的元件。</span><span class="sxs-lookup"><span data-stu-id="71f11-130">This option makes it easier to render different :::no-loc(Blazor WebAssembly)::: components on different pages.</span></span>
* <span data-ttu-id="71f11-131">`WebAssemblyPrerendered`：將元件 Prerenders 為靜態 HTML，並包含應用程式的標記，以 :::no-loc(Blazor WebAssembly)::: 供稍後用來在瀏覽器中載入元件時進行互動。</span><span class="sxs-lookup"><span data-stu-id="71f11-131">`WebAssemblyPrerendered`: Prerenders the component into static HTML and includes a marker for a :::no-loc(Blazor WebAssembly)::: app for later use to make the component interactive when loaded in the browser.</span></span>

<span data-ttu-id="71f11-132">在下列 :::no-loc(Razor)::: 頁面範例中， `Counter` 元件會在頁面中呈現。</span><span class="sxs-lookup"><span data-stu-id="71f11-132">In the following :::no-loc(Razor)::: Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="71f11-133">若要使元件互動， :::no-loc(Blazor WebAssembly)::: 腳本會包含在頁面的 [轉譯] [區段](xref:mvc/views/layout#sections)中。</span><span class="sxs-lookup"><span data-stu-id="71f11-133">To make the component interactive, the :::no-loc(Blazor WebAssembly)::: script is included in the page's [render section](xref:mvc/views/layout#sections).</span></span> <span data-ttu-id="71f11-134">若要避免使用元件標記協助程式 () 的完整命名空間 `Counter` `{APP ASSEMBLY}.Pages.Counter` ，請為 [`@using`](xref:mvc/views/razor#using) 用戶端應用程式的 `Pages` 命名空間新增指示詞。</span><span class="sxs-lookup"><span data-stu-id="71f11-134">To avoid using the full namespace for the `Counter` component with the Component Tag Helper (`{APP ASSEMBLY}.Pages.Counter`), add an [`@using`](xref:mvc/views/razor#using) directive for the client app's `Pages` namespace.</span></span> <span data-ttu-id="71f11-135">在下列範例中，用戶端應用程式的命名空間為 `:::no-loc(Blazor):::Hosted.Client` ：</span><span class="sxs-lookup"><span data-stu-id="71f11-135">In the following example, the client app's namespace is `:::no-loc(Blazor):::Hosted.Client`:</span></span>

```cshtml
...
@using :::no-loc(Blazor):::Hosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="71f11-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 設定元件是否為：</span><span class="sxs-lookup"><span data-stu-id="71f11-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="71f11-137">會資源清單到頁面中。</span><span class="sxs-lookup"><span data-stu-id="71f11-137">Is prerendered into the page.</span></span>
* <span data-ttu-id="71f11-138">會在頁面上轉譯為靜態 HTML，或包含從使用者代理程式啟動應用程式所需的資訊 :::no-loc(Blazor)::: 。</span><span class="sxs-lookup"><span data-stu-id="71f11-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

<span data-ttu-id="71f11-139">如需元件標記協助程式的詳細資訊，包括傳遞參數和設定 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> ，請參閱 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> 。</span><span class="sxs-lookup"><span data-stu-id="71f11-139">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

<span data-ttu-id="71f11-140">上述範例需要伺服器應用程式的版面配置 (`_Layout.cshtml`) 包含結束標記內腳本的轉譯 [區段](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.RenderSection%2A>) `</body>` ：</span><span class="sxs-lookup"><span data-stu-id="71f11-140">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="71f11-141">檔案 `_Layout.cshtml` 位於 MVC 應用程式中 `Pages/Shared` :::no-loc(Razor)::: 頁面應用程式或資料夾的資料夾中 `Views/Shared` 。</span><span class="sxs-lookup"><span data-stu-id="71f11-141">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a :::no-loc(Razor)::: Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="71f11-142">如果應用程式也應該將應用程式中的樣式作為元件樣式 :::no-loc(Blazor WebAssembly)::: ，請在檔案中包含應用程式的樣式 `_Layout.cshtml` 。</span><span class="sxs-lookup"><span data-stu-id="71f11-142">If the app should also style components with the styles in the :::no-loc(Blazor WebAssembly)::: app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="71f11-143">在下列範例中，用戶端應用程式的命名空間為 `:::no-loc(Blazor):::Hosted.Client` ：</span><span class="sxs-lookup"><span data-stu-id="71f11-143">In the following example, the client app's namespace is `:::no-loc(Blazor):::Hosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href=":::no-loc(Blazor):::Hosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a><span data-ttu-id="71f11-144">使用 CSS 選取器呈現頁面或視圖中的元件</span><span class="sxs-lookup"><span data-stu-id="71f11-144">Render components in a page or view with a CSS selector</span></span>

<span data-ttu-id="71f11-145">在 () 中，將根元件新增至 *用戶端* 專案 `Program.Main` `Program.cs` 。</span><span class="sxs-lookup"><span data-stu-id="71f11-145">Add root components to the *Client* project in `Program.Main` (`Program.cs`).</span></span> <span data-ttu-id="71f11-146">在下列範例中， `Counter` 元件會宣告為根元件，其中包含的 CSS 選取器會使用 `id` 符合的來選取專案 `my-counter` 。</span><span class="sxs-lookup"><span data-stu-id="71f11-146">In the following example, the `Counter` component is declared as a root component with a CSS selector that selects the element with the `id` that matches `my-counter`.</span></span> <span data-ttu-id="71f11-147">在下列範例中，用戶端應用程式的命名空間為 `:::no-loc(Blazor):::Hosted.Client` ：</span><span class="sxs-lookup"><span data-stu-id="71f11-147">In the following example, the client app's namespace is `:::no-loc(Blazor):::Hosted.Client`:</span></span>

```csharp
using :::no-loc(Blazor):::Hosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

<span data-ttu-id="71f11-148">在下列 :::no-loc(Razor)::: 頁面範例中， `Counter` 元件會在頁面中呈現。</span><span class="sxs-lookup"><span data-stu-id="71f11-148">In the following :::no-loc(Razor)::: Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="71f11-149">若要使元件互動， :::no-loc(Blazor WebAssembly)::: 腳本會包含在頁面的轉譯 [區段](xref:mvc/views/layout#sections)中：</span><span class="sxs-lookup"><span data-stu-id="71f11-149">To make the component interactive, the :::no-loc(Blazor WebAssembly)::: script is included in the page's [render section](xref:mvc/views/layout#sections):</span></span>

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="71f11-150">上述範例需要伺服器應用程式的版面配置 (`_Layout.cshtml`) 包含結束標記內腳本的轉譯 [區段](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.RenderSection%2A>) `</body>` ：</span><span class="sxs-lookup"><span data-stu-id="71f11-150">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="71f11-151">檔案 `_Layout.cshtml` 位於 MVC 應用程式中 `Pages/Shared` :::no-loc(Razor)::: 頁面應用程式或資料夾的資料夾中 `Views/Shared` 。</span><span class="sxs-lookup"><span data-stu-id="71f11-151">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a :::no-loc(Razor)::: Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="71f11-152">如果應用程式也應該將應用程式中的樣式作為元件樣式 :::no-loc(Blazor WebAssembly)::: ，請在檔案中包含應用程式的樣式 `_Layout.cshtml` 。</span><span class="sxs-lookup"><span data-stu-id="71f11-152">If the app should also style components with the styles in the :::no-loc(Blazor WebAssembly)::: app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="71f11-153">在下列範例中，用戶端應用程式的命名空間為 `:::no-loc(Blazor):::Hosted.Client` ：</span><span class="sxs-lookup"><span data-stu-id="71f11-153">In the following example, the client app's namespace is `:::no-loc(Blazor):::Hosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href=":::no-loc(Blazor):::Hosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="71f11-154">在 :::no-loc(Razor)::: :::no-loc(Razor)::: :::no-loc(Blazor WebAssembly)::: .net 5 或更新版本的 ASP.NET Core 中，支援將元件整合至裝載方案中的頁面和 MVC 應用程式。</span><span class="sxs-lookup"><span data-stu-id="71f11-154">Integrating :::no-loc(Razor)::: components into :::no-loc(Razor)::: Pages and MVC apps in a hosted :::no-loc(Blazor WebAssembly)::: solution is supported in ASP.NET Core in .NET 5 or later.</span></span> <span data-ttu-id="71f11-155">請選取此文章的 .NET 5 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="71f11-155">Select a .NET 5 or later version of this article.</span></span>

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="71f11-156">:::no-loc(Razor)::: 元件可以整合至 :::no-loc(Razor)::: 應用程式中的頁面和 MVC 應用程式 :::no-loc(Blazor Server)::: 。</span><span class="sxs-lookup"><span data-stu-id="71f11-156">:::no-loc(Razor)::: components can be integrated into :::no-loc(Razor)::: Pages and MVC apps in a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="71f11-157">轉譯頁面或視圖時，可同時資源清單元件。</span><span class="sxs-lookup"><span data-stu-id="71f11-157">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="71f11-158">設定 [應用程式](#configuration)之後，請根據應用程式的需求，使用下列各節中的指導方針：</span><span class="sxs-lookup"><span data-stu-id="71f11-158">After [configuring the app](#configuration), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="71f11-159">可路由的元件：適用于直接從使用者要求路由傳送的元件。</span><span class="sxs-lookup"><span data-stu-id="71f11-159">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="71f11-160">當訪客應能在其瀏覽器中使用指示詞為元件發出 HTTP 要求時，請遵循此指導 [`@page`](xref:mvc/views/razor#page) 方針。</span><span class="sxs-lookup"><span data-stu-id="71f11-160">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="71f11-161">在頁面應用程式中使用可路由的元件 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="71f11-161">Use routable components in a :::no-loc(Razor)::: Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="71f11-162">在 MVC 應用程式中使用可路由的元件</span><span class="sxs-lookup"><span data-stu-id="71f11-162">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="71f11-163">[從頁面或視圖呈現元件](#render-components-from-a-page-or-view)：適用于無法直接從使用者要求路由傳送的元件。</span><span class="sxs-lookup"><span data-stu-id="71f11-163">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="71f11-164">當應用程式使用元件標籤協助程式將元件內嵌至現有的頁面和流覽 [器](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)時，請遵循此指引。</span><span class="sxs-lookup"><span data-stu-id="71f11-164">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="configuration"></a><span data-ttu-id="71f11-165">設定</span><span class="sxs-lookup"><span data-stu-id="71f11-165">Configuration</span></span>

<span data-ttu-id="71f11-166">現有的 :::no-loc(Razor)::: 頁面或 MVC 應用程式可以將 :::no-loc(Razor)::: 元件整合至頁面和流覽中：</span><span class="sxs-lookup"><span data-stu-id="71f11-166">An existing :::no-loc(Razor)::: Pages or MVC app can integrate :::no-loc(Razor)::: components into pages and views:</span></span>

1. <span data-ttu-id="71f11-167">在應用程式的版面配置檔案中 (`_Layout.cshtml`) ：</span><span class="sxs-lookup"><span data-stu-id="71f11-167">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="71f11-168">將下列 `<base>` 標記新增至 `<head>` 元素：</span><span class="sxs-lookup"><span data-stu-id="71f11-168">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="71f11-169">`href`上述範例中的 *應用程式基底路徑* )  (值假設應用程式位於根 URL 路徑 (`/`) 。</span><span class="sxs-lookup"><span data-stu-id="71f11-169">The `href` value (the *app base path* ) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="71f11-170">如果應用程式是子應用程式，請遵循本文的「 *應用程式基底路徑* 」一節中的指導方針 <xref:blazor/host-and-deploy/index#app-base-path> 。</span><span class="sxs-lookup"><span data-stu-id="71f11-170">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="71f11-171">檔案 `_Layout.cshtml` 位於 MVC 應用程式中 `Pages/Shared` :::no-loc(Razor)::: 頁面應用程式或資料夾的資料夾中 `Views/Shared` 。</span><span class="sxs-lookup"><span data-stu-id="71f11-171">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a :::no-loc(Razor)::: Pages app or `Views/Shared` folder in an MVC app.</span></span>

   * <span data-ttu-id="71f11-172">在轉譯 `<script>` `blazor.server.js` 區段之前，立即新增腳本的標記 `Scripts` ：</span><span class="sxs-lookup"><span data-stu-id="71f11-172">Add a `<script>` tag for the `blazor.server.js` script immediately before the `Scripts` render section:</span></span>

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     <span data-ttu-id="71f11-173">架構會將 `blazor.server.js` 腳本新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="71f11-173">The framework adds the `blazor.server.js` script to the app.</span></span> <span data-ttu-id="71f11-174">不需要手動將腳本新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="71f11-174">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="71f11-175">`_Imports.razor`使用下列內容將檔案新增至專案的根資料夾， (將最後一個命名空間變更 `MyAppNamespace` 為應用程式) 的命名空間：</span><span class="sxs-lookup"><span data-stu-id="71f11-175">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="71f11-176">在中 `Startup.ConfigureServices` ，註冊 :::no-loc(Blazor Server)::: 服務：</span><span class="sxs-lookup"><span data-stu-id="71f11-176">In `Startup.ConfigureServices`, register the :::no-loc(Blazor Server)::: service:</span></span>

   ```csharp
   services.AddServerSide:::no-loc(Blazor):::();
   ```

1. <span data-ttu-id="71f11-177">在中 `Startup.Configure` ，將 :::no-loc(Blazor)::: 中樞端點新增至 `app.UseEndpoints` ：</span><span class="sxs-lookup"><span data-stu-id="71f11-177">In `Startup.Configure`, add the :::no-loc(Blazor)::: Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.Map:::no-loc(Blazor):::Hub();
   ```

1. <span data-ttu-id="71f11-178">將元件整合至任何頁面或視圖中。</span><span class="sxs-lookup"><span data-stu-id="71f11-178">Integrate components into any page or view.</span></span> <span data-ttu-id="71f11-179">如需詳細資訊，請參閱 [從頁面或視圖區段呈現元件](#render-components-from-a-page-or-view) 。</span><span class="sxs-lookup"><span data-stu-id="71f11-179">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a><span data-ttu-id="71f11-180">在頁面應用程式中使用可路由的元件 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="71f11-180">Use routable components in a :::no-loc(Razor)::: Pages app</span></span>

<span data-ttu-id="71f11-181">*本節適用于新增直接可從使用者要求路由傳送的元件。*</span><span class="sxs-lookup"><span data-stu-id="71f11-181">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="71f11-182">支援 :::no-loc(Razor)::: 頁面應用程式中可路由的元件 :::no-loc(Razor)::: ：</span><span class="sxs-lookup"><span data-stu-id="71f11-182">To support routable :::no-loc(Razor)::: components in :::no-loc(Razor)::: Pages apps:</span></span>

1. <span data-ttu-id="71f11-183">遵循 [設定一節中的指導](#configuration) 方針。</span><span class="sxs-lookup"><span data-stu-id="71f11-183">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="71f11-184">`App.razor`使用下列內容將檔案新增至專案根目錄：</span><span class="sxs-lookup"><span data-stu-id="71f11-184">Add an `App.razor` file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="71f11-185">將檔案新增 `_Host.cshtml` 至 `Pages` 具有下列內容的資料夾：</span><span class="sxs-lookup"><span data-stu-id="71f11-185">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="71f11-186">元件會使用共用檔案 `_Layout.cshtml` 進行版面配置。</span><span class="sxs-lookup"><span data-stu-id="71f11-186">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="71f11-187"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 設定元件是否為 `App` ：</span><span class="sxs-lookup"><span data-stu-id="71f11-187"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="71f11-188">會資源清單到頁面中。</span><span class="sxs-lookup"><span data-stu-id="71f11-188">Is prerendered into the page.</span></span>
   * <span data-ttu-id="71f11-189">會在頁面上轉譯為靜態 HTML，或包含從使用者代理程式啟動應用程式所需的資訊 :::no-loc(Blazor)::: 。</span><span class="sxs-lookup"><span data-stu-id="71f11-189">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

   <span data-ttu-id="71f11-190">如需元件標記協助程式的詳細資訊，包括傳遞參數和設定 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> ，請參閱 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> 。</span><span class="sxs-lookup"><span data-stu-id="71f11-190">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="71f11-191">將頁面的低優先順序路由新增 `_Host.cshtml` 至中的端點設定 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="71f11-191">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="71f11-192">將可路由傳送的元件新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="71f11-192">Add routable components to the app.</span></span> <span data-ttu-id="71f11-193">例如：</span><span class="sxs-lookup"><span data-stu-id="71f11-193">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="71f11-194">如需命名空間的詳細資訊，請參閱 [元件命名空間](#component-namespaces) 一節。</span><span class="sxs-lookup"><span data-stu-id="71f11-194">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="71f11-195">在 MVC 應用程式中使用可路由的元件</span><span class="sxs-lookup"><span data-stu-id="71f11-195">Use routable components in an MVC app</span></span>

<span data-ttu-id="71f11-196">*本節適用于新增直接可從使用者要求路由傳送的元件。*</span><span class="sxs-lookup"><span data-stu-id="71f11-196">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="71f11-197">支援 :::no-loc(Razor)::: MVC 應用程式中的可路由元件：</span><span class="sxs-lookup"><span data-stu-id="71f11-197">To support routable :::no-loc(Razor)::: components in MVC apps:</span></span>

1. <span data-ttu-id="71f11-198">遵循 [設定一節中的指導](#configuration) 方針。</span><span class="sxs-lookup"><span data-stu-id="71f11-198">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="71f11-199">`App.razor`使用下列內容將檔案新增至專案的根目錄：</span><span class="sxs-lookup"><span data-stu-id="71f11-199">Add an `App.razor` file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="71f11-200">將檔案新增 `_Host.cshtml` 至 `Views/Home` 具有下列內容的資料夾：</span><span class="sxs-lookup"><span data-stu-id="71f11-200">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="71f11-201">元件會使用共用檔案 `_Layout.cshtml` 進行版面配置。</span><span class="sxs-lookup"><span data-stu-id="71f11-201">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="71f11-202"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 設定元件是否為 `App` ：</span><span class="sxs-lookup"><span data-stu-id="71f11-202"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="71f11-203">會資源清單到頁面中。</span><span class="sxs-lookup"><span data-stu-id="71f11-203">Is prerendered into the page.</span></span>
   * <span data-ttu-id="71f11-204">會在頁面上轉譯為靜態 HTML，或包含從使用者代理程式啟動應用程式所需的資訊 :::no-loc(Blazor)::: 。</span><span class="sxs-lookup"><span data-stu-id="71f11-204">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

   <span data-ttu-id="71f11-205">如需元件標記協助程式的詳細資訊，包括傳遞參數和設定 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> ，請參閱 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> 。</span><span class="sxs-lookup"><span data-stu-id="71f11-205">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="71f11-206">將動作新增至 Home 控制器：</span><span class="sxs-lookup"><span data-stu-id="71f11-206">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult :::no-loc(Blazor):::()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="71f11-207">新增控制器動作的低優先順序路由，將 `_Host.cshtml` 視圖傳回至中的端點設定 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="71f11-207">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController(":::no-loc(Blazor):::", "Home");
   });
   ```

1. <span data-ttu-id="71f11-208">建立 `Pages` 資料夾，並將可路由傳送的元件新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="71f11-208">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="71f11-209">例如：</span><span class="sxs-lookup"><span data-stu-id="71f11-209">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="71f11-210">如需命名空間的詳細資訊，請參閱 [元件命名空間](#component-namespaces) 一節。</span><span class="sxs-lookup"><span data-stu-id="71f11-210">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="71f11-211">從頁面或視圖呈現元件</span><span class="sxs-lookup"><span data-stu-id="71f11-211">Render components from a page or view</span></span>

<span data-ttu-id="71f11-212">*本節適用于將元件新增至頁面或視圖，其中元件無法直接從使用者要求路由傳送。*</span><span class="sxs-lookup"><span data-stu-id="71f11-212">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="71f11-213">若要從頁面或視圖轉譯元件，請使用 [元件標記](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)協助程式。</span><span class="sxs-lookup"><span data-stu-id="71f11-213">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="71f11-214">轉譯具狀態的互動式元件</span><span class="sxs-lookup"><span data-stu-id="71f11-214">Render stateful interactive components</span></span>

<span data-ttu-id="71f11-215">可設定狀態的互動式元件可以加入至 :::no-loc(Razor)::: 頁面或視圖中。</span><span class="sxs-lookup"><span data-stu-id="71f11-215">Stateful interactive components can be added to a :::no-loc(Razor)::: page or view.</span></span>

<span data-ttu-id="71f11-216">當頁面或視圖呈現時：</span><span class="sxs-lookup"><span data-stu-id="71f11-216">When the page or view renders:</span></span>

* <span data-ttu-id="71f11-217">此元件是使用頁面或視圖所資源清單。</span><span class="sxs-lookup"><span data-stu-id="71f11-217">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="71f11-218">用於進行可呈現的初始元件狀態會遺失。</span><span class="sxs-lookup"><span data-stu-id="71f11-218">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="71f11-219">建立連接時，會建立新的元件狀態 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="71f11-219">New component state is created when the :::no-loc(SignalR)::: connection is established.</span></span>

<span data-ttu-id="71f11-220">下列 :::no-loc(Razor)::: 頁面會呈現 `Counter` 元件：</span><span class="sxs-lookup"><span data-stu-id="71f11-220">The following :::no-loc(Razor)::: page renders a `Counter` component:</span></span>

```cshtml
<h1>My :::no-loc(Razor)::: Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="71f11-221">如需詳細資訊，請參閱<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="71f11-221">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="71f11-222">轉譯非互動式元件</span><span class="sxs-lookup"><span data-stu-id="71f11-222">Render noninteractive components</span></span>

<span data-ttu-id="71f11-223">在下 :::no-loc(Razor)::: 一個頁面中， `Counter` 系統會使用表單所指定的初始值，以靜態方式呈現元件。</span><span class="sxs-lookup"><span data-stu-id="71f11-223">In the following :::no-loc(Razor)::: page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="71f11-224">由於元件是以靜態方式轉譯，因此元件並非互動式：</span><span class="sxs-lookup"><span data-stu-id="71f11-224">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My :::no-loc(Razor)::: Page</h1>

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

<span data-ttu-id="71f11-225">如需詳細資訊，請參閱<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="71f11-225">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="71f11-226">元件命名空間</span><span class="sxs-lookup"><span data-stu-id="71f11-226">Component namespaces</span></span>

<span data-ttu-id="71f11-227">使用自訂資料夾來保存應用程式的元件時，請將代表資料夾的命名空間新增至頁面/視圖或檔案 `_ViewImports.cshtml` 。</span><span class="sxs-lookup"><span data-stu-id="71f11-227">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="71f11-228">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="71f11-228">In the following example:</span></span>

* <span data-ttu-id="71f11-229">變更 `MyAppNamespace` 為應用程式的命名空間。</span><span class="sxs-lookup"><span data-stu-id="71f11-229">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="71f11-230">如果未使用名為的資料夾 `Components` 來保存元件，請變更 `Components` 為元件所在的資料夾。</span><span class="sxs-lookup"><span data-stu-id="71f11-230">If a folder named `Components` isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="71f11-231">檔案 `_ViewImports.cshtml` 位於 `Pages` :::no-loc(Razor)::: 頁面應用程式的資料夾或 `Views` MVC 應用程式的資料夾中。</span><span class="sxs-lookup"><span data-stu-id="71f11-231">The `_ViewImports.cshtml` file is located in the `Pages` folder of a :::no-loc(Razor)::: Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="71f11-232">如需詳細資訊，請參閱<xref:blazor/components/index#namespaces>。</span><span class="sxs-lookup"><span data-stu-id="71f11-232">For more information, see <xref:blazor/components/index#namespaces>.</span></span>

::: zone-end
