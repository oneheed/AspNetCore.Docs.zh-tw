---
title: ASP.NET Core 元件的已呈現和整合 Razor
author: guardrex
description: 深入瞭解 Razor 應用程式的元件整合案例 Blazor ，包括伺服器上的元件的可呈現 Razor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2021
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
ms.openlocfilehash: d0be30e14c67119f0aadaaab415cf0ae53e82915
ms.sourcegitcommit: 7923a9ec594690f01e0c9c6df3416c239e6745fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081516"
---
# <a name="prerender-and-integrate-aspnet-core-razor-components"></a><span data-ttu-id="402fc-103">ASP.NET Core 元件的已呈現和整合 Razor</span><span class="sxs-lookup"><span data-stu-id="402fc-103">Prerender and integrate ASP.NET Core Razor components</span></span>

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="402fc-104">Razor 元件可以整合到 Razor 託管解決方案中的頁面和 MVC 應用程式 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="402fc-104">Razor components can be integrated into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="402fc-105">轉譯頁面或視圖時，可同時資源清單元件。</span><span class="sxs-lookup"><span data-stu-id="402fc-105">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="solution-configuration"></a><span data-ttu-id="402fc-106">解決方案設定</span><span class="sxs-lookup"><span data-stu-id="402fc-106">Solution configuration</span></span>

### <a name="prerendering-configuration"></a><span data-ttu-id="402fc-107">已呈現的設定</span><span class="sxs-lookup"><span data-stu-id="402fc-107">Prerendering configuration</span></span>

<span data-ttu-id="402fc-108">若要為裝載的應用程式設定預先安裝 Blazor WebAssembly ：</span><span class="sxs-lookup"><span data-stu-id="402fc-108">To set up prerendering for a hosted Blazor WebAssembly app:</span></span>

1. <span data-ttu-id="402fc-109">Blazor WebAssembly在 ASP.NET Core 應用程式中裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="402fc-109">Host the Blazor WebAssembly app in an ASP.NET Core app.</span></span> <span data-ttu-id="402fc-110">獨立 Blazor WebAssembly 應用程式可以新增至 ASP.NET Core 解決方案，或您可以使用 Blazor WebAssembly 從[ Blazor WebAssembly 專案範本](xref:blazor/tooling)建立的託管應用程式與裝載的選項：</span><span class="sxs-lookup"><span data-stu-id="402fc-110">A standalone Blazor WebAssembly app can be added to an ASP.NET Core solution, or you can use a hosted Blazor WebAssembly app created from the [Blazor WebAssembly project template](xref:blazor/tooling) with the hosted option:</span></span>

   * <span data-ttu-id="402fc-111">Visual Studio：在建立應用程式時，選取 [ **Advanced**  >  **ASP.NET Core hosted** ] 核取方塊 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="402fc-111">Visual Studio: Select the **Advanced** > **ASP.NET Core hosted** check box when creating the Blazor WebAssembly app.</span></span> <span data-ttu-id="402fc-112">在本文的範例中，會命名方案 `BlazorHosted` 。</span><span class="sxs-lookup"><span data-stu-id="402fc-112">In this article's examples, the solution is named `BlazorHosted`.</span></span>
   * <span data-ttu-id="402fc-113">Visual Studio Code/.NET CLI 命令 shell： `dotnet new blazorwasm -ho` (使用 `-ho|--hosted`) 選項。</span><span class="sxs-lookup"><span data-stu-id="402fc-113">Visual Studio Code/.NET CLI command shell: `dotnet new blazorwasm -ho` (use the `-ho|--hosted` option).</span></span> <span data-ttu-id="402fc-114">使用 `-o|--output {LOCATION}` 選項來建立方案的資料夾，並設定解決方案的專案命名空間。</span><span class="sxs-lookup"><span data-stu-id="402fc-114">Use the `-o|--output {LOCATION}` option to create a folder for the solution and set the solution's project namespaces.</span></span> <span data-ttu-id="402fc-115">在本文的範例中，會將方案命名為 `BlazorHosted` (`dotnet new blazorwasm -ho -o BlazorHosted`) 。</span><span class="sxs-lookup"><span data-stu-id="402fc-115">In this article's examples, the solution is named `BlazorHosted` (`dotnet new blazorwasm -ho -o BlazorHosted`).</span></span>

   <span data-ttu-id="402fc-116">針對本文中的範例，用戶端專案的命名空間是 `BlazorHosted.Client` ，而伺服器專案的命名空間是 `BlazorHosted.Server` 。</span><span class="sxs-lookup"><span data-stu-id="402fc-116">For the examples in this article, the client project's namespace is `BlazorHosted.Client`, and the server project's namespace is `BlazorHosted.Server`.</span></span>

1. <span data-ttu-id="402fc-117"> `wwwroot/index.html` 從專案中刪除檔案 Blazor WebAssembly **`Client`** 。</span><span class="sxs-lookup"><span data-stu-id="402fc-117">**Delete** the `wwwroot/index.html` file from the Blazor WebAssembly **`Client`** project.</span></span>

1. <span data-ttu-id="402fc-118">在 **`Client`** 專案中， **刪除** () 中的下列程式 `Program.Main` 程式碼 `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="402fc-118">In the **`Client`** project, **delete** the following line in `Program.Main` (`Program.cs`):</span></span>

   ```diff
   - builder.RootComponents.Add<App>("#app");
   ```

1. <span data-ttu-id="402fc-119">將檔案加入 `Pages/_Host.cshtml` **`Server`** 專案的 `Pages` 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="402fc-119">Add a `Pages/_Host.cshtml` file to the **`Server`** project's `Pages` folder.</span></span> <span data-ttu-id="402fc-120">您可以 `_Host.cshtml` 使用命令介面中的命令，從範本建立的專案中取得檔案， Blazor Server `dotnet new blazorserver -o BlazorServer` (選項會 `-o BlazorServer` 建立專案) 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="402fc-120">You can obtain a `_Host.cshtml` file from a project created from the Blazor Server template with the `dotnet new blazorserver -o BlazorServer` command in a command shell (the `-o BlazorServer` option creates a folder for the project).</span></span> <span data-ttu-id="402fc-121">將檔案放 `Pages/_Host.cshtml` 入 **`Server`** 託管解決方案的專案之後，對檔案 Blazor WebAssembly 進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="402fc-121">After placing the `Pages/_Host.cshtml` file into the **`Server`** project of the hosted Blazor WebAssembly solution, make the following changes to the file:</span></span>

   * <span data-ttu-id="402fc-122">為專案提供指示詞 [`@using`](xref:mvc/views/razor#using) **`Client`** (例如 `@using BlazorHosted.Client`) 。</span><span class="sxs-lookup"><span data-stu-id="402fc-122">Provide an [`@using`](xref:mvc/views/razor#using) directive for the **`Client`** project (for example, `@using BlazorHosted.Client`).</span></span>
   * <span data-ttu-id="402fc-123">更新樣式表單連結以指向 WebAssembly 專案的樣式表單。</span><span class="sxs-lookup"><span data-stu-id="402fc-123">Update the stylesheet links to point to the WebAssembly project's stylesheets.</span></span> <span data-ttu-id="402fc-124">在下列範例中，用戶端專案的命名空間為 `BlazorHosted.Client` ：</span><span class="sxs-lookup"><span data-stu-id="402fc-124">In the following example, the client project's namespace is `BlazorHosted.Client`:</span></span>

     ```diff
     - <link href="css/site.css" rel="stylesheet" />
     - <link href="_content/BlazorServer/_framework/scoped.styles.css" rel="stylesheet" />
     + <link href="css/app.css" rel="stylesheet" />
     + <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

     > [!NOTE]
     > <span data-ttu-id="402fc-125">將 `<link>` 要求啟動增益集樣式的元素保留 (`css/bootstrap/bootstrap.min.css`) 。</span><span class="sxs-lookup"><span data-stu-id="402fc-125">Leave the `<link>` element that requests the Bootstrap stylesheet (`css/bootstrap/bootstrap.min.css`) in place.</span></span>

   * <span data-ttu-id="402fc-126">使用下列內容更新元件標籤協助程式的， `render-mode` 以呈現根[](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) `App` 元件 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered> ：</span><span class="sxs-lookup"><span data-stu-id="402fc-126">Update the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to prerender the root `App` component with <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered>:</span></span>

     ```diff
     - <component type="typeof(App)" render-mode="ServerPrerendered" />
     + <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * <span data-ttu-id="402fc-127">更新 Blazor 腳本來源以使用用戶端 Blazor WebAssembly 腳本：</span><span class="sxs-lookup"><span data-stu-id="402fc-127">Update the Blazor script source to use the client-side Blazor WebAssembly script:</span></span>

     ```diff
     - <script src="_framework/blazor.server.js"></script>
     + <script src="_framework/blazor.webassembly.js"></script>
     ```

1. <span data-ttu-id="402fc-128">在 `Startup.Configure` 專案中 **`Server`** ，將檔案的回復變更 `index.html` 為 `_Host.cshtml` 頁面。</span><span class="sxs-lookup"><span data-stu-id="402fc-128">In `Startup.Configure` of the **`Server`** project, change the fallback from the `index.html` file to the `_Host.cshtml` page.</span></span>

   <span data-ttu-id="402fc-129">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="402fc-129">`Startup.cs`:</span></span>

   ```diff
   - endpoints.MapFallbackToFile("index.html");
   + endpoints.MapFallbackToPage("/_Host");
   ```

1. <span data-ttu-id="402fc-130">執行 **`Server`** 專案。</span><span class="sxs-lookup"><span data-stu-id="402fc-130">Run the **`Server`** project.</span></span> <span data-ttu-id="402fc-131">裝載的 Blazor WebAssembly 應用程式是由 **`Server`** 用戶端的專案所資源清單。</span><span class="sxs-lookup"><span data-stu-id="402fc-131">The hosted Blazor WebAssembly app is prerendered by the **`Server`** project for clients.</span></span>

### <a name="configuration-for-embedding-razor-components-into-pages-and-views"></a><span data-ttu-id="402fc-132">將元件內嵌 Razor 至頁面和視圖的設定</span><span class="sxs-lookup"><span data-stu-id="402fc-132">Configuration for embedding Razor components into pages and views</span></span>

<span data-ttu-id="402fc-133">本文中的下列各節和範例可將 Razor 用戶端應用程式的元件內嵌 Blazor WebAssembly 至伺服器應用程式的頁面和觀點，需要進行額外的設定。</span><span class="sxs-lookup"><span data-stu-id="402fc-133">The following sections and examples in this article for embedding Razor components of the client Blazor WebAssembly app into pages and views of the server app require additional configuration.</span></span>

<span data-ttu-id="402fc-134">使用 Razor 專案中的預設頁面或 MVC 版面配置檔案 **`Server`** 。</span><span class="sxs-lookup"><span data-stu-id="402fc-134">Use a default Razor Pages or MVC layout file in the **`Server`** project.</span></span> <span data-ttu-id="402fc-135">**`Server`** 專案必須具有下列檔案和資料夾。</span><span class="sxs-lookup"><span data-stu-id="402fc-135">The **`Server`** project must have the following files and folders.</span></span>

<span data-ttu-id="402fc-136">Razor 頁面：</span><span class="sxs-lookup"><span data-stu-id="402fc-136">Razor Pages:</span></span>

* `Pages/Shared/_Layout.cshtml`
* `Pages/_ViewImports.cshtml`
* `Pages/_ViewStart.cshtml`

<span data-ttu-id="402fc-137">Mvc：</span><span class="sxs-lookup"><span data-stu-id="402fc-137">MVC:</span></span>

* `Views/Shared/_Layout.cshtml`
* `Views/_ViewImports.cshtml`
* `Views/_ViewStart.cshtml`

<span data-ttu-id="402fc-138">從 Razor 頁面或 MVC 專案範本所建立的應用程式取得先前的檔案。</span><span class="sxs-lookup"><span data-stu-id="402fc-138">Obtain the preceding files from an app created from the Razor Pages or MVC project template.</span></span> <span data-ttu-id="402fc-139">如需詳細資訊，請參閱 <xref:tutorials/razor-pages/razor-pages-start>或 <xref:tutorials/first-mvc-app/start-mvc>。</span><span class="sxs-lookup"><span data-stu-id="402fc-139">For more information, see <xref:tutorials/razor-pages/razor-pages-start> or <xref:tutorials/first-mvc-app/start-mvc>.</span></span>

<span data-ttu-id="402fc-140">更新匯入檔案中的命名空間 `_ViewImports.cshtml` ，以符合接收檔案的專案所使用的命名空間 **`Server`** 。</span><span class="sxs-lookup"><span data-stu-id="402fc-140">Update the namespaces in the imported `_ViewImports.cshtml` file to match those in use by the **`Server`** project receiving the files.</span></span>

<span data-ttu-id="402fc-141">更新匯入的版面配置檔案 (`_Layout.cshtml`) ，以包含 **`Client`** 專案的樣式。</span><span class="sxs-lookup"><span data-stu-id="402fc-141">Update the imported layout file (`_Layout.cshtml`) to include the **`Client`** project's styles.</span></span> <span data-ttu-id="402fc-142">在下列範例中， **`Client`** 專案的命名空間為 `BlazorHosted.Client` 。</span><span class="sxs-lookup"><span data-stu-id="402fc-142">In the following example, the **`Client`** project's namespace is `BlazorHosted.Client`.</span></span> <span data-ttu-id="402fc-143">您 `<title>` 可以同時更新元素。</span><span class="sxs-lookup"><span data-stu-id="402fc-143">The `<title>` element can be updated at the same time.</span></span>

<span data-ttu-id="402fc-144">`Pages/Shared/_Layout.cshtml` (Razor) 或 `Views/Shared/_Layout.cshtml` (MVC) 的頁面：</span><span class="sxs-lookup"><span data-stu-id="402fc-144">`Pages/Shared/_Layout.cshtml` (Razor Pages) or `Views/Shared/_Layout.cshtml` (MVC):</span></span>

```diff
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
-   <title>@ViewData["Title"] - DonorProject</title>
+   <title>@ViewData["Title"] - BlazorHosted</title>
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.min.css" />
    <link rel="stylesheet" href="~/css/site.css" />
+   <link href="css/app.css" rel="stylesheet" />
+   <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

<span data-ttu-id="402fc-145">匯入的版面配置包含 `Home` 和 `Privacy` 導覽連結。</span><span class="sxs-lookup"><span data-stu-id="402fc-145">The imported layout contains `Home` and `Privacy` navigation links.</span></span> <span data-ttu-id="402fc-146">若要將 `Home` 連結指向託管 Blazor WebAssembly 應用程式，請變更超連結：</span><span class="sxs-lookup"><span data-stu-id="402fc-146">To make the `Home` link point to the hosted Blazor WebAssembly app, change the hyperlink:</span></span>

```diff
- <a class="nav-link text-dark" asp-area="" asp-page="/Index">Home</a>
+ <a class="nav-link text-dark" href="/">Home</a>
```

<span data-ttu-id="402fc-147">在 MVC 版面配置檔案中：</span><span class="sxs-lookup"><span data-stu-id="402fc-147">In an MVC layout file:</span></span>

```diff
- <a class="nav-link text-dark" asp-area="" asp-controller="Home" 
-     asp-action="Index">Home</a>
+ <a class="nav-link text-dark" href="/">Home</a>
```

<span data-ttu-id="402fc-148">若要將 `Privacy` 連結導向至隱私權頁面，請將隱私權頁面新增至 **`Server`** 專案。</span><span class="sxs-lookup"><span data-stu-id="402fc-148">To make the `Privacy` link lead to a privacy page, add a privacy page to the **`Server`** project.</span></span>

<span data-ttu-id="402fc-149">`Pages/Privacy.cshtml` 在 **`Server`** 專案中：</span><span class="sxs-lookup"><span data-stu-id="402fc-149">`Pages/Privacy.cshtml` in the **`Server`** project:</span></span>

```cshtml
@page
@model BlazorHosted.Server.Pages.PrivacyModel
@{
}

<h1>Privacy Policy</h1>
```

<span data-ttu-id="402fc-150">如果偏好以 MVC 為基礎的隱私視圖，請在專案中建立隱私權視圖 **`Server`** 。</span><span class="sxs-lookup"><span data-stu-id="402fc-150">If an MVC-based privacy view is preferred, create a privacy view in the **`Server`** project.</span></span>

<span data-ttu-id="402fc-151">`View/Home/Privacy.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="402fc-151">`View/Home/Privacy.cshtml`:</span></span>

```cshtml
@{
    ViewData["Title"] = "Privacy Policy";
}

<h1>@ViewData["Title"]</h1>
```

<span data-ttu-id="402fc-152">在 `Home` 控制器中，返回 [view]。</span><span class="sxs-lookup"><span data-stu-id="402fc-152">In the `Home` controller, return the view.</span></span>

<span data-ttu-id="402fc-153">`Controllers/HomeController.cs`:</span><span class="sxs-lookup"><span data-stu-id="402fc-153">`Controllers/HomeController.cs`:</span></span>

```diff
+ public IActionResult Privacy()
+ {
+     return View();
+ }
```

<span data-ttu-id="402fc-154">**`Server`** 從贊助商專案的資料夾將靜態資產匯入至專案 `wwwroot` ：</span><span class="sxs-lookup"><span data-stu-id="402fc-154">Import static assets to the **`Server`** project from the donor project's `wwwroot` folder:</span></span>

* <span data-ttu-id="402fc-155">`wwwroot/css` 資料夾和內容</span><span class="sxs-lookup"><span data-stu-id="402fc-155">`wwwroot/css` folder and contents</span></span>
* <span data-ttu-id="402fc-156">`wwwroot/js` 資料夾和內容</span><span class="sxs-lookup"><span data-stu-id="402fc-156">`wwwroot/js` folder and contents</span></span>
* <span data-ttu-id="402fc-157">`wwwroot/lib` 資料夾和內容</span><span class="sxs-lookup"><span data-stu-id="402fc-157">`wwwroot/lib` folder and contents</span></span>

<span data-ttu-id="402fc-158">如果從 ASP.NET Core 專案範本建立「捐贈者」專案，而且未修改這些檔案，您可以從「 `wwwroot` 贊助商」專案將整個資料夾複製到專案中， **`Server`** 並移除 `favicon.ico` 圖示檔。</span><span class="sxs-lookup"><span data-stu-id="402fc-158">If the donor project is created from an ASP.NET Core project template and the files aren't modified, you can copy the entire `wwwroot` folder from the donor project into the **`Server`** project and remove the `favicon.ico` icon file.</span></span>

> [!NOTE]
> <span data-ttu-id="402fc-159">如果 **`Client`** 和 **`Server`** 專案在其資料夾中包含相同的靜態資產 `wwwroot` (例如) ，則會擲回例外狀況 `favicon.ico` ，因為每個資料夾中的靜態資產都會共用相同的 web 根目錄路徑：</span><span class="sxs-lookup"><span data-stu-id="402fc-159">If the **`Client`** and **`Server`** projects contain the same static asset in their `wwwroot` folders (for example, `favicon.ico`), an exception is thrown because the static asset in each folder shares the same web root path:</span></span>
>
> > <span data-ttu-id="402fc-160">靜態 web 資產 '. ..\favicon.ico ' 有衝突的 web 根路徑 '/wwwroot/favicon.ico ' 與專案檔 ' wwwroot\favicon.ico '。</span><span class="sxs-lookup"><span data-stu-id="402fc-160">The static web asset '...\favicon.ico' has a conflicting web root path '/wwwroot/favicon.ico' with the project file 'wwwroot\favicon.ico'.</span></span>
>
> <span data-ttu-id="402fc-161">因此，請在任一資料夾中裝載靜態資產 `wwwroot` ，而非兩者。</span><span class="sxs-lookup"><span data-stu-id="402fc-161">Therefore, host a static asset in either `wwwroot` folder, not both.</span></span>

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a><span data-ttu-id="402fc-162">使用元件標記協助程式在頁面或視圖中轉譯元件</span><span class="sxs-lookup"><span data-stu-id="402fc-162">Render components in a page or view with the Component Tag Helper</span></span>

<span data-ttu-id="402fc-163">在設定 [解決方案](#solution-configuration)（包括 [其他](#configuration-for-embedding-razor-components-into-pages-and-views)設定）之後， [元件](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) 標籤協助程式支援兩種轉譯模式，可從 Blazor WebAssembly 頁面或視圖中的應用程式轉譯元件：</span><span class="sxs-lookup"><span data-stu-id="402fc-163">After [configuring the solution](#solution-configuration), including the [additional configuration](#configuration-for-embedding-razor-components-into-pages-and-views), the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) supports two render modes for rendering a component from a Blazor WebAssembly app in a page or view:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssembly>
* <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered>

<span data-ttu-id="402fc-164">在下列 Razor 頁面範例中， `Counter` 元件會在頁面中呈現。</span><span class="sxs-lookup"><span data-stu-id="402fc-164">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="402fc-165">若要使元件互動， Blazor WebAssembly 腳本會包含在頁面的 [轉譯] [區段](xref:mvc/views/layout#sections)中。</span><span class="sxs-lookup"><span data-stu-id="402fc-165">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections).</span></span> <span data-ttu-id="402fc-166">若要避免使用元件標記協助程式 `Counter` () 的[](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)完整命名空間 `{APP ASSEMBLY}.Pages.Counter` ，請加入 [`@using`](xref:mvc/views/razor#using) 用戶端專案 `Pages` 命名空間的指示詞。</span><span class="sxs-lookup"><span data-stu-id="402fc-166">To avoid using the full namespace for the `Counter` component with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) (`{APP ASSEMBLY}.Pages.Counter`), add an [`@using`](xref:mvc/views/razor#using) directive for the client project's `Pages` namespace.</span></span> <span data-ttu-id="402fc-167">在下列範例中， **`Client`** 專案的命名空間為 `BlazorHosted.Client` 。</span><span class="sxs-lookup"><span data-stu-id="402fc-167">In the following example, the **`Client`** project's namespace is `BlazorHosted.Client`.</span></span>

<span data-ttu-id="402fc-168">在 **`Server`** 專案中 `Pages/RazorPagesCounter1.cshtml` ：</span><span class="sxs-lookup"><span data-stu-id="402fc-168">In the **`Server`** project, `Pages/RazorPagesCounter1.cshtml`:</span></span>

```cshtml
@page
@using BlazorHosted.Client.Pages

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="402fc-169">執行 **`Server`** 專案。</span><span class="sxs-lookup"><span data-stu-id="402fc-169">Run the **`Server`** project.</span></span> <span data-ttu-id="402fc-170">流覽至中的 Razor 頁面 `/razorpagescounter1` 。</span><span class="sxs-lookup"><span data-stu-id="402fc-170">Navigate to the Razor page at `/razorpagescounter1`.</span></span> <span data-ttu-id="402fc-171">資源清單 `Counter` 元件內嵌在頁面中。</span><span class="sxs-lookup"><span data-stu-id="402fc-171">The prerendered `Counter` component is embedded in the page.</span></span>

<span data-ttu-id="402fc-172"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 設定元件是否為：</span><span class="sxs-lookup"><span data-stu-id="402fc-172"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="402fc-173">會資源清單到頁面中。</span><span class="sxs-lookup"><span data-stu-id="402fc-173">Is prerendered into the page.</span></span>
* <span data-ttu-id="402fc-174">會在頁面上轉譯為靜態 HTML，或包含從使用者代理程式啟動應用程式所需的資訊 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="402fc-174">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

<span data-ttu-id="402fc-175">如需元件標記協助程式的詳細資訊，包括傳遞參數和設定 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> ，請參閱 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> 。</span><span class="sxs-lookup"><span data-stu-id="402fc-175">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

<span data-ttu-id="402fc-176">根據元件所使用的靜態資源，以及在應用程式中組織版面配置頁面的方式，可能需要額外的工作。</span><span class="sxs-lookup"><span data-stu-id="402fc-176">Additional work might be required depending on the static resources that components use and how layout pages are organized in an app.</span></span> <span data-ttu-id="402fc-177">一般而言，腳本會加入至頁面或視圖的轉譯 `Scripts` 區段，並將樣式表單加入至版面配置的 `<head>` 元素內容中。</span><span class="sxs-lookup"><span data-stu-id="402fc-177">Typically, scripts are added to a page or view's `Scripts` render section and stylesheets are added to the layout's `<head>` element content.</span></span>

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a><span data-ttu-id="402fc-178">使用 CSS 選取器呈現頁面或視圖中的元件</span><span class="sxs-lookup"><span data-stu-id="402fc-178">Render components in a page or view with a CSS selector</span></span>

<span data-ttu-id="402fc-179">在設定 [解決方案](#solution-configuration)（包括 [其他](#configuration-for-embedding-razor-components-into-pages-and-views)設定）之後，請在中將根元件新增至裝載 **`Client`** 方案的專案 Blazor WebAssembly `Program.Main` 。</span><span class="sxs-lookup"><span data-stu-id="402fc-179">After [configuring the solution](#solution-configuration), including the [additional configuration](#configuration-for-embedding-razor-components-into-pages-and-views), add root components to the **`Client`** project of a hosted Blazor WebAssembly solution in `Program.Main`.</span></span> <span data-ttu-id="402fc-180">在下列範例中， `Counter` 元件會宣告為根元件，其中包含的 CSS 選取器會使用 `id` 符合的來選取專案 `counter-component` 。</span><span class="sxs-lookup"><span data-stu-id="402fc-180">In the following example, the `Counter` component is declared as a root component with a CSS selector that selects the element with the `id` that matches `counter-component`.</span></span> <span data-ttu-id="402fc-181">在下列範例中， **`Client`** 專案的命名空間為 `BlazorHosted.Client` 。</span><span class="sxs-lookup"><span data-stu-id="402fc-181">In the following example, the **`Client`** project's namespace is `BlazorHosted.Client`.</span></span>

<span data-ttu-id="402fc-182">在 `Program.cs` 專案中 **`Client`** ，將專案元件的命名空間新增 Razor 至檔案頂端：</span><span class="sxs-lookup"><span data-stu-id="402fc-182">In `Program.cs` of the **`Client`** project, add the namespace for the project's Razor components to the top of the file:</span></span>

```diff
+ using BlazorHosted.Client.Pages;
```

<span data-ttu-id="402fc-183">`builder`在中建立之後 `Program.Main` ，請將 `Counter` 元件新增為根元件：</span><span class="sxs-lookup"><span data-stu-id="402fc-183">After the `builder` is established in `Program.Main`, add the `Counter` component as a root component:</span></span>

```diff
+ builder.RootComponents.Add<Counter>("#counter-component");
```

<span data-ttu-id="402fc-184">在下列 Razor 頁面範例中， `Counter` 元件會在頁面中呈現。</span><span class="sxs-lookup"><span data-stu-id="402fc-184">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="402fc-185">若要使元件互動， Blazor WebAssembly 腳本會包含在頁面的 [轉譯] [區段](xref:mvc/views/layout#sections)中。</span><span class="sxs-lookup"><span data-stu-id="402fc-185">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections).</span></span>

<span data-ttu-id="402fc-186">在 **`Server`** 專案中 `Pages/RazorPagesCounter2.cshtml` ：</span><span class="sxs-lookup"><span data-stu-id="402fc-186">In the **`Server`** project, `Pages/RazorPagesCounter2.cshtml`:</span></span>

```cshtml
@page

<div id="counter-component">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="402fc-187">執行 **`Server`** 專案。</span><span class="sxs-lookup"><span data-stu-id="402fc-187">Run the **`Server`** project.</span></span> <span data-ttu-id="402fc-188">流覽至中的 Razor 頁面 `/razorpagescounter2` 。</span><span class="sxs-lookup"><span data-stu-id="402fc-188">Navigate to the Razor page at `/razorpagescounter2`.</span></span> <span data-ttu-id="402fc-189">資源清單 `Counter` 元件內嵌在頁面中。</span><span class="sxs-lookup"><span data-stu-id="402fc-189">The prerendered `Counter` component is embedded in the page.</span></span>

<span data-ttu-id="402fc-190">根據元件所使用的靜態資源，以及在應用程式中組織版面配置頁面的方式，可能需要額外的工作。</span><span class="sxs-lookup"><span data-stu-id="402fc-190">Additional work might be required depending on the static resources that components use and how layout pages are organized in an app.</span></span> <span data-ttu-id="402fc-191">一般而言，腳本會加入至頁面或視圖的轉譯 `Scripts` 區段，並將樣式表單加入至版面配置的 `<head>` 元素內容中。</span><span class="sxs-lookup"><span data-stu-id="402fc-191">Typically, scripts are added to a page or view's `Scripts` render section and stylesheets are added to the layout's `<head>` element content.</span></span>

> [!NOTE]
> <span data-ttu-id="402fc-192">上述範例 <xref:Microsoft.JSInterop.JSException> Blazor WebAssembly 會在應用程式 Razor 使用 CSS 選取器 **同時** 資源清單和整合至頁面或 MVC 應用程式時擲回。</span><span class="sxs-lookup"><span data-stu-id="402fc-192">The preceding example throws a <xref:Microsoft.JSInterop.JSException> if a Blazor WebAssembly app is prerendered and integrated into a Razor Pages or MVC app **simultaneously** with a CSS selector.</span></span> <span data-ttu-id="402fc-193">流覽至其中一個專案元件時，會擲回 **`Client`** Razor 下列例外狀況：</span><span class="sxs-lookup"><span data-stu-id="402fc-193">Navigating to one of the **`Client`** project's Razor components throws the following exception:</span></span>
>
> > <span data-ttu-id="402fc-194">Microsoft.JSInterop.JS例外狀況：找不到任何符合選取器 ' #counter 元件的元素。</span><span class="sxs-lookup"><span data-stu-id="402fc-194">Microsoft.JSInterop.JSException: Could not find any element matching selector '#counter-component'.</span></span>
>
> <span data-ttu-id="402fc-195">這是正常行為，因為 Blazor WebAssembly 使用可路由傳送的元件來呈現和整合應用程式與 Razor 使用 CSS 選取器不相容。</span><span class="sxs-lookup"><span data-stu-id="402fc-195">This is normal behavior because prerendering and integrating a Blazor WebAssembly app with routable Razor components is incompatible with the use of CSS selectors.</span></span>

## <a name="additional-blazor-webassembly-resources"></a><span data-ttu-id="402fc-196">其他 Blazor WebAssembly 資源</span><span class="sxs-lookup"><span data-stu-id="402fc-196">Additional Blazor WebAssembly resources</span></span>

* [<span data-ttu-id="402fc-197">狀態管理：處理已呈現</span><span class="sxs-lookup"><span data-stu-id="402fc-197">State management: Handle prerendering</span></span>](xref:blazor/state-management?pivot=webassembly#handle-prerendering)
* [<span data-ttu-id="402fc-198">元件消極式載入的預呈現支援</span><span class="sxs-lookup"><span data-stu-id="402fc-198">Prerendering support with assembly lazy loading</span></span>](xref:blazor/webassembly-lazy-load-assemblies#assembly-load-logic-in-onnavigateasync)
* <span data-ttu-id="402fc-199">Razor 與可呈現的相關的元件生命週期主題</span><span class="sxs-lookup"><span data-stu-id="402fc-199">Razor component lifecycle subjects that pertain to prerendering</span></span>
  * [<span data-ttu-id="402fc-200">元件初始化 (`OnInitialized{Async}`) </span><span class="sxs-lookup"><span data-stu-id="402fc-200">Component initialization (`OnInitialized{Async}`)</span></span>](xref:blazor/components/lifecycle#component-initialization-oninitializedasync)
  * [<span data-ttu-id="402fc-201">元件轉譯 (之後 `OnAfterRender{Async}`) </span><span class="sxs-lookup"><span data-stu-id="402fc-201">After component render (`OnAfterRender{Async}`)</span></span>](xref:blazor/components/lifecycle#after-component-render-onafterrenderasync)
  * <span data-ttu-id="402fc-202">可設定的可設定[狀態重新連接](xref:blazor/components/lifecycle#stateful-reconnection-after-prerendering)：雖然區段中的內容著重于和可設定狀態的重新連線，但 Blazor Server 在裝載 SignalR 的應用程式 () 的情況下，會 Blazor WebAssembly <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered> 包含類似的條件和方法來防止執行開發人員程式碼兩次</span><span class="sxs-lookup"><span data-stu-id="402fc-202">[Stateful reconnection after prerendering](xref:blazor/components/lifecycle#stateful-reconnection-after-prerendering): Although the content in the section focuses on Blazor Server and stateful SignalR *reconnection*, the scenario for prerendering in hosted Blazor WebAssembly apps (<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered>) involves similar conditions and approaches to prevent executing developer code twice.</span></span> <span data-ttu-id="402fc-203">已針對 ASP.NET Core 6.0 版本規劃 *新的狀態保留功能* ，可改善在預進行期間的初始化程式碼執行管理。</span><span class="sxs-lookup"><span data-stu-id="402fc-203">A *new state preservation feature* is planned for the ASP.NET Core 6.0 release that will improve the management of initialization code execution during prerendering.</span></span>
  * [<span data-ttu-id="402fc-204">偵測應用程式何時進行呈現</span><span class="sxs-lookup"><span data-stu-id="402fc-204">Detect when the app is prerendering</span></span>](xref:blazor/components/lifecycle#detect-when-the-app-is-prerendering)
* <span data-ttu-id="402fc-205">與可呈現的相關的驗證與授權主題</span><span class="sxs-lookup"><span data-stu-id="402fc-205">Authentication and authorization subjects that pertain to prerendering</span></span>
  * [<span data-ttu-id="402fc-206">一般層面</span><span class="sxs-lookup"><span data-stu-id="402fc-206">General aspects</span></span>](xref:blazor/security/index#aspnet-core-blazor-authentication-and-authorization)
  * [<span data-ttu-id="402fc-207">支援使用驗證進行預進行</span><span class="sxs-lookup"><span data-stu-id="402fc-207">Support prerendering with authentication</span></span>](xref:blazor/security/webassembly/additional-scenarios#support-prerendering-with-authentication)
* [<span data-ttu-id="402fc-208">裝載和部署： Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="402fc-208">Host and deploy: Blazor WebAssembly</span></span>](xref:blazor/host-and-deploy/webassembly)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="402fc-209">在 Razor Razor Blazor WebAssembly .net 5 或更新版本的 ASP.NET Core 中，支援將元件整合至裝載方案中的頁面和 MVC 應用程式。</span><span class="sxs-lookup"><span data-stu-id="402fc-209">Integrating Razor components into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution is supported in ASP.NET Core in .NET 5 or later.</span></span> <span data-ttu-id="402fc-210">請選取此文章的 .NET 5 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="402fc-210">Select a .NET 5 or later version of this article.</span></span>

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="402fc-211">Razor 元件可以整合至 Razor 應用程式中的頁面和 MVC 應用程式 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="402fc-211">Razor components can be integrated into Razor Pages and MVC apps in a Blazor Server app.</span></span> <span data-ttu-id="402fc-212">轉譯頁面或視圖時，可同時資源清單元件。</span><span class="sxs-lookup"><span data-stu-id="402fc-212">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="402fc-213">設定 [專案](#configuration)之後，請根據專案的需求，使用下列各節中的指導方針：</span><span class="sxs-lookup"><span data-stu-id="402fc-213">After [configuring the project](#configuration), use the guidance in the following sections depending on the project's requirements:</span></span>

* <span data-ttu-id="402fc-214">可路由的元件：適用于直接從使用者要求路由傳送的元件。</span><span class="sxs-lookup"><span data-stu-id="402fc-214">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="402fc-215">當訪客應能在其瀏覽器中使用指示詞為元件發出 HTTP 要求時，請遵循此指導 [`@page`](xref:mvc/views/razor#page) 方針。</span><span class="sxs-lookup"><span data-stu-id="402fc-215">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="402fc-216">在頁面應用程式中使用可路由的元件 Razor</span><span class="sxs-lookup"><span data-stu-id="402fc-216">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="402fc-217">在 MVC 應用程式中使用可路由的元件</span><span class="sxs-lookup"><span data-stu-id="402fc-217">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="402fc-218">[從頁面或視圖呈現元件](#render-components-from-a-page-or-view)：適用于無法直接從使用者要求路由傳送的元件。</span><span class="sxs-lookup"><span data-stu-id="402fc-218">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="402fc-219">當應用程式使用元件標籤協助程式將元件內嵌至現有的頁面和流覽 [器](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)時，請遵循此指引。</span><span class="sxs-lookup"><span data-stu-id="402fc-219">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="configuration"></a><span data-ttu-id="402fc-220">組態</span><span class="sxs-lookup"><span data-stu-id="402fc-220">Configuration</span></span>

<span data-ttu-id="402fc-221">現有的 Razor 頁面或 MVC 應用程式可以將 Razor 元件整合至頁面和流覽中：</span><span class="sxs-lookup"><span data-stu-id="402fc-221">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="402fc-222">在專案的版面配置檔案中：</span><span class="sxs-lookup"><span data-stu-id="402fc-222">In the project's layout file:</span></span>

   * <span data-ttu-id="402fc-223">將下列 `<base>` 標記新增至 `<head>` `Pages/Shared/_Layout.cshtml` (Razor 頁面) 或 `Views/Shared/_Layout.cshtml` (MVC) 中的元素：</span><span class="sxs-lookup"><span data-stu-id="402fc-223">Add the following `<base>` tag to the `<head>` element in `Pages/Shared/_Layout.cshtml` (Razor Pages) or `Views/Shared/_Layout.cshtml` (MVC):</span></span>

     ```diff
     + <base href="~/" />
     ```

     <span data-ttu-id="402fc-224">`href`上述範例中的 *應用程式基底路徑*)  (值假設應用程式位於根 URL 路徑 (`/`) 。</span><span class="sxs-lookup"><span data-stu-id="402fc-224">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="402fc-225">如果應用程式是子應用程式，請遵循本文的「 *應用程式基底路徑* 」一節中的指導方針 <xref:blazor/host-and-deploy/index#app-base-path> 。</span><span class="sxs-lookup"><span data-stu-id="402fc-225">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

   * <span data-ttu-id="402fc-226">在轉譯 `<script>` `blazor.server.js` 區段之前，立即新增腳本的標記 `Scripts` 。</span><span class="sxs-lookup"><span data-stu-id="402fc-226">Add a `<script>` tag for the `blazor.server.js` script immediately before the `Scripts` render section.</span></span>

     <span data-ttu-id="402fc-227">`Pages/Shared/_Layout.cshtml` (Razor) 或 `Views/Shared/_Layout.cshtml` (MVC) 的頁面：</span><span class="sxs-lookup"><span data-stu-id="402fc-227">`Pages/Shared/_Layout.cshtml` (Razor Pages) or `Views/Shared/_Layout.cshtml` (MVC):</span></span>

     ```diff
         ...
     +   <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     <span data-ttu-id="402fc-228">架構會將 `blazor.server.js` 腳本新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="402fc-228">The framework adds the `blazor.server.js` script to the app.</span></span> <span data-ttu-id="402fc-229">不需要手動將腳本檔案新增 `blazor.server.js` 至應用程式。</span><span class="sxs-lookup"><span data-stu-id="402fc-229">There's no need to manually add a `blazor.server.js` script file to the app.</span></span>

1. <span data-ttu-id="402fc-230">使用下列內容將匯入檔案新增至專案的根資料夾。</span><span class="sxs-lookup"><span data-stu-id="402fc-230">Add an imports file to the root folder of the project with the following content.</span></span> <span data-ttu-id="402fc-231">將 `{APP NAMESPACE}` 預留位置變更為專案的命名空間。</span><span class="sxs-lookup"><span data-stu-id="402fc-231">Change the `{APP NAMESPACE}` placeholder to the namespace of the project.</span></span>

   <span data-ttu-id="402fc-232">`_Imports.razor`:</span><span class="sxs-lookup"><span data-stu-id="402fc-232">`_Imports.razor`:</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using {APP NAMESPACE}
   ```

1. <span data-ttu-id="402fc-233">Blazor Server在中註冊服務 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="402fc-233">Register the Blazor Server service in `Startup.ConfigureServices`.</span></span>

   <span data-ttu-id="402fc-234">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="402fc-234">`Startup.cs`:</span></span>

   ```diff
   + services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="402fc-235">將 Blazor 中樞端點新增至 (`app.UseEndpoints`) 的端點 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="402fc-235">Add the Blazor Hub endpoint to the endpoints (`app.UseEndpoints`) of `Startup.Configure`.</span></span>

   <span data-ttu-id="402fc-236">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="402fc-236">`Startup.cs`:</span></span>

   ```diff
   + endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="402fc-237">將元件整合至任何頁面或視圖中。</span><span class="sxs-lookup"><span data-stu-id="402fc-237">Integrate components into any page or view.</span></span> <span data-ttu-id="402fc-238">例如，將元件新增 `Counter` 至專案的 `Shared` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="402fc-238">For example, add a `Counter` component to the project's `Shared` folder.</span></span>

   <span data-ttu-id="402fc-239">`Pages/Shared/Counter.razor` (Razor) 或 `Views/Shared/Counter.razor` (MVC) 的頁面：</span><span class="sxs-lookup"><span data-stu-id="402fc-239">`Pages/Shared/Counter.razor` (Razor Pages) or `Views/Shared/Counter.razor` (MVC):</span></span>

   ```razor
   <h1>Counter</h1>

   <p>Current count: @currentCount</p>

   <button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

   @code {
       private int currentCount = 0;

       private void IncrementCount()
       {
           currentCount++;
       }
   }
   ```

   <span data-ttu-id="402fc-240">**Razor 頁面**：</span><span class="sxs-lookup"><span data-stu-id="402fc-240">**Razor Pages**:</span></span>

   <span data-ttu-id="402fc-241">在 `Index` 頁面應用程式的專案頁面 Razor 上，新增 `Counter` 元件的命名空間，並將元件內嵌到頁面中。</span><span class="sxs-lookup"><span data-stu-id="402fc-241">In the project's `Index` page of a Razor Pages app, add the `Counter` component's namespace and embed the component into the page.</span></span> <span data-ttu-id="402fc-242">當 `Index` 頁面載入時， `Counter` 會在頁面中資源清單元件。</span><span class="sxs-lookup"><span data-stu-id="402fc-242">When the `Index` page loads, the `Counter` component is prerendered in the page.</span></span> <span data-ttu-id="402fc-243">在下列範例中，請將 `{APP NAMESPACE}` 預留位置取代為專案的命名空間。</span><span class="sxs-lookup"><span data-stu-id="402fc-243">In the following example, replace the `{APP NAMESPACE}` placeholder with the project's namespace.</span></span>

   <span data-ttu-id="402fc-244">`Pages/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="402fc-244">`Pages/Index.cshtml`:</span></span>

   ```cshtml
   @page
   @using {APP NAMESPACE}.Pages.Shared
   @model IndexModel
   @{
       ViewData["Title"] = "Home page";
   }

   <div>
       <component type="typeof(Counter)" render-mode="ServerPrerendered" />
   </div>
   ```

   <span data-ttu-id="402fc-245">在上述範例中，請將 `{APP NAMESPACE}` 預留位置取代為應用程式的命名空間。</span><span class="sxs-lookup"><span data-stu-id="402fc-245">In the preceding example, replace the `{APP NAMESPACE}` placeholder with the app's namespace.</span></span>

   <span data-ttu-id="402fc-246">**MVC**：</span><span class="sxs-lookup"><span data-stu-id="402fc-246">**MVC**:</span></span>

   <span data-ttu-id="402fc-247">在專案的 `Index` MVC 應用程式視圖中，新增 `Counter` 元件的命名空間，並將元件內嵌到視圖中。</span><span class="sxs-lookup"><span data-stu-id="402fc-247">In the project's `Index` view of an MVC app, add the `Counter` component's namespace and embed the component into the view.</span></span> <span data-ttu-id="402fc-248">當 `Index` view 載入時， `Counter` 會在頁面中資源清單元件。</span><span class="sxs-lookup"><span data-stu-id="402fc-248">When the `Index` view loads, the `Counter` component is prerendered in the page.</span></span> <span data-ttu-id="402fc-249">在下列範例中，請將 `{APP NAMESPACE}` 預留位置取代為專案的命名空間。</span><span class="sxs-lookup"><span data-stu-id="402fc-249">In the following example, replace the `{APP NAMESPACE}` placeholder with the project's namespace.</span></span>

   <span data-ttu-id="402fc-250">`Views/Home/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="402fc-250">`Views/Home/Index.cshtml`:</span></span>

   ```cshtml
   @using {APP NAMESPACE}.Views.Shared
   @{
       ViewData["Title"] = "Home Page";
   }

   <div>
       <component type="typeof(Counter)" render-mode="ServerPrerendered" />
   </div>
   ```

<span data-ttu-id="402fc-251">如需詳細資訊，請參閱 [從頁面或視圖區段呈現元件](#render-components-from-a-page-or-view) 。</span><span class="sxs-lookup"><span data-stu-id="402fc-251">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="402fc-252">在頁面應用程式中使用可路由的元件 Razor</span><span class="sxs-lookup"><span data-stu-id="402fc-252">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="402fc-253">*本節適用于新增直接可從使用者要求路由傳送的元件。*</span><span class="sxs-lookup"><span data-stu-id="402fc-253">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="402fc-254">支援 Razor 頁面應用程式中可路由的元件 Razor ：</span><span class="sxs-lookup"><span data-stu-id="402fc-254">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="402fc-255">遵循 [設定一節中的指導](#configuration) 方針。</span><span class="sxs-lookup"><span data-stu-id="402fc-255">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="402fc-256">`App`使用下列內容將元件新增至專案根目錄。</span><span class="sxs-lookup"><span data-stu-id="402fc-256">Add an `App` component to the project root with the following content.</span></span>

   <span data-ttu-id="402fc-257">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="402fc-257">`App.razor`:</span></span>

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

1. <span data-ttu-id="402fc-258">`_Host`使用下列內容將頁面加入至專案。</span><span class="sxs-lookup"><span data-stu-id="402fc-258">Add a `_Host` page to the project with the following content.</span></span>

   <span data-ttu-id="402fc-259">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="402fc-259">`Pages/_Host.cshtml`:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="402fc-260">元件會使用共用檔案 `_Layout.cshtml` 進行版面配置。</span><span class="sxs-lookup"><span data-stu-id="402fc-260">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="402fc-261"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 設定元件是否為 `App` ：</span><span class="sxs-lookup"><span data-stu-id="402fc-261"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="402fc-262">會資源清單到頁面中。</span><span class="sxs-lookup"><span data-stu-id="402fc-262">Is prerendered into the page.</span></span>
   * <span data-ttu-id="402fc-263">會在頁面上轉譯為靜態 HTML，或包含從使用者代理程式啟動應用程式所需的資訊 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="402fc-263">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="402fc-264">如需元件標記協助程式的詳細資訊，包括傳遞參數和設定 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> ，請參閱 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> 。</span><span class="sxs-lookup"><span data-stu-id="402fc-264">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="402fc-265">在的 `Startup.Configure` 端點中 `Startup.cs` ，將頁面的低優先順序路由新增 `_Host` 為最後一個端點：</span><span class="sxs-lookup"><span data-stu-id="402fc-265">In the `Startup.Configure` endpoints of `Startup.cs`, add a low-priority route for the `_Host` page as the last endpoint:</span></span>

   ```diff
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapRazorPages();
       endpoints.MapBlazorHub();
   +   endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="402fc-266">將可路由傳送的元件加入至專案。</span><span class="sxs-lookup"><span data-stu-id="402fc-266">Add routable components to the project.</span></span>

   <span data-ttu-id="402fc-267">`Pages/RoutableCounter.razor`:</span><span class="sxs-lookup"><span data-stu-id="402fc-267">`Pages/RoutableCounter.razor`:</span></span>

   ```razor
   @page "/routable-counter"

   <h1>Routable Counter</h1>

   <p>Current count: @currentCount</p>

   <button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

   @code {
       private int currentCount = 0;

       private void IncrementCount()
       {
           currentCount++;
       }
   }
   ```

1. <span data-ttu-id="402fc-268">執行專案，然後流覽至中可路由傳送的 `RoutableCounter` 元件 `/routable-counter` 。</span><span class="sxs-lookup"><span data-stu-id="402fc-268">Run the project and navigate to the routable `RoutableCounter` component at `/routable-counter`.</span></span>

<span data-ttu-id="402fc-269">如需命名空間的詳細資訊，請參閱 [元件命名空間](#component-namespaces) 一節。</span><span class="sxs-lookup"><span data-stu-id="402fc-269">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="402fc-270">在 MVC 應用程式中使用可路由的元件</span><span class="sxs-lookup"><span data-stu-id="402fc-270">Use routable components in an MVC app</span></span>

<span data-ttu-id="402fc-271">*本節適用于新增直接可從使用者要求路由傳送的元件。*</span><span class="sxs-lookup"><span data-stu-id="402fc-271">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="402fc-272">支援 Razor MVC 應用程式中的可路由元件：</span><span class="sxs-lookup"><span data-stu-id="402fc-272">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="402fc-273">遵循 [設定一節中的指導](#configuration) 方針。</span><span class="sxs-lookup"><span data-stu-id="402fc-273">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="402fc-274">`App`使用下列內容將元件新增至專案根目錄。</span><span class="sxs-lookup"><span data-stu-id="402fc-274">Add an `App` component to the project root with the following content.</span></span>

   <span data-ttu-id="402fc-275">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="402fc-275">`App.razor`:</span></span>

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

1. <span data-ttu-id="402fc-276">`_Host`使用下列內容將視圖新增至專案。</span><span class="sxs-lookup"><span data-stu-id="402fc-276">Add a `_Host` view to the project with the following content.</span></span>

   <span data-ttu-id="402fc-277">`Views/Home/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="402fc-277">`Views/Home/_Host.cshtml`:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="402fc-278">元件會使用共用檔案 `_Layout.cshtml` 進行版面配置。</span><span class="sxs-lookup"><span data-stu-id="402fc-278">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="402fc-279"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 設定元件是否為 `App` ：</span><span class="sxs-lookup"><span data-stu-id="402fc-279"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="402fc-280">會資源清單到頁面中。</span><span class="sxs-lookup"><span data-stu-id="402fc-280">Is prerendered into the page.</span></span>
   * <span data-ttu-id="402fc-281">會在頁面上轉譯為靜態 HTML，或包含從使用者代理程式啟動應用程式所需的資訊 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="402fc-281">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="402fc-282">如需元件標記協助程式的詳細資訊，包括傳遞參數和設定 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> ，請參閱 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> 。</span><span class="sxs-lookup"><span data-stu-id="402fc-282">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="402fc-283">將動作新增至首頁控制器。</span><span class="sxs-lookup"><span data-stu-id="402fc-283">Add an action to the Home controller.</span></span>

   <span data-ttu-id="402fc-284">`Controllers/HomeController.cs`:</span><span class="sxs-lookup"><span data-stu-id="402fc-284">`Controllers/HomeController.cs`:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="402fc-285">在的 [端點] 中 `Startup.Configure` `Startup.cs` ，為傳回 view 的控制器動作新增低優先順序的路由 `_Host` ：</span><span class="sxs-lookup"><span data-stu-id="402fc-285">In the `Startup.Configure` endpoints of `Startup.cs`, add a low-priority route for the controller action that returns the `_Host` view:</span></span>

   ```diff
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapControllerRoute(
           name: "default",
           pattern: "{controller=Home}/{action=Index}/{id?}");
       endpoints.MapBlazorHub();
   +   endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="402fc-286">將可路由傳送的元件加入至專案。</span><span class="sxs-lookup"><span data-stu-id="402fc-286">Add routable components to the project.</span></span>

   <span data-ttu-id="402fc-287">`Pages/RoutableCounter.razor`:</span><span class="sxs-lookup"><span data-stu-id="402fc-287">`Pages/RoutableCounter.razor`:</span></span>

   ```razor
   @page "/routable-counter"

   <h1>Routable Counter</h1>

   <p>Current count: @currentCount</p>

   <button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

   @code {
       private int currentCount = 0;

       private void IncrementCount()
       {
           currentCount++;
       }
   }
   ```

1. <span data-ttu-id="402fc-288">執行專案，然後流覽至中可路由傳送的 `RoutableCounter` 元件 `/routable-counter` 。</span><span class="sxs-lookup"><span data-stu-id="402fc-288">Run the project and navigate to the routable `RoutableCounter` component at `/routable-counter`.</span></span>

<span data-ttu-id="402fc-289">如需命名空間的詳細資訊，請參閱 [元件命名空間](#component-namespaces) 一節。</span><span class="sxs-lookup"><span data-stu-id="402fc-289">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="402fc-290">從頁面或視圖呈現元件</span><span class="sxs-lookup"><span data-stu-id="402fc-290">Render components from a page or view</span></span>

<span data-ttu-id="402fc-291">*本節適用于將元件新增至頁面或視圖，其中元件無法直接從使用者要求路由傳送。*</span><span class="sxs-lookup"><span data-stu-id="402fc-291">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="402fc-292">若要從頁面或視圖轉譯元件，請使用 [元件標記](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)協助程式。</span><span class="sxs-lookup"><span data-stu-id="402fc-292">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="402fc-293">轉譯具狀態的互動式元件</span><span class="sxs-lookup"><span data-stu-id="402fc-293">Render stateful interactive components</span></span>

<span data-ttu-id="402fc-294">可設定狀態的互動式元件可以加入至 Razor 頁面或視圖中。</span><span class="sxs-lookup"><span data-stu-id="402fc-294">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="402fc-295">當頁面或視圖呈現時：</span><span class="sxs-lookup"><span data-stu-id="402fc-295">When the page or view renders:</span></span>

* <span data-ttu-id="402fc-296">此元件是使用頁面或視圖所資源清單。</span><span class="sxs-lookup"><span data-stu-id="402fc-296">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="402fc-297">用於進行可呈現的初始元件狀態會遺失。</span><span class="sxs-lookup"><span data-stu-id="402fc-297">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="402fc-298">建立連接時，會建立新的元件狀態 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="402fc-298">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="402fc-299">下列 Razor 頁面會呈現 `Counter` 元件：</span><span class="sxs-lookup"><span data-stu-id="402fc-299">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="402fc-300">如需詳細資訊，請參閱<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="402fc-300">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="402fc-301">轉譯非互動式元件</span><span class="sxs-lookup"><span data-stu-id="402fc-301">Render noninteractive components</span></span>

<span data-ttu-id="402fc-302">在下 Razor 一個頁面中， `Counter` 系統會使用表單所指定的初始值，以靜態方式呈現元件。</span><span class="sxs-lookup"><span data-stu-id="402fc-302">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="402fc-303">由於元件是以靜態方式轉譯，因此元件並非互動式：</span><span class="sxs-lookup"><span data-stu-id="402fc-303">Since the component is statically rendered, the component isn't interactive:</span></span>

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

<span data-ttu-id="402fc-304">如需詳細資訊，請參閱<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="402fc-304">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="402fc-305">元件命名空間</span><span class="sxs-lookup"><span data-stu-id="402fc-305">Component namespaces</span></span>

<span data-ttu-id="402fc-306">使用自訂資料夾來保存專案的元件時 Razor ，請將代表資料夾的命名空間新增至頁面/視圖或檔案 `_ViewImports.cshtml` 。</span><span class="sxs-lookup"><span data-stu-id="402fc-306">When using a custom folder to hold the project's Razor components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="402fc-307">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="402fc-307">In the following example:</span></span>

* <span data-ttu-id="402fc-308">元件會儲存在 `Components` 專案的資料夾中。</span><span class="sxs-lookup"><span data-stu-id="402fc-308">Components are stored in the `Components` folder of the project.</span></span>
* <span data-ttu-id="402fc-309">`{APP NAMESPACE}`預留位置是專案的命名空間。</span><span class="sxs-lookup"><span data-stu-id="402fc-309">The `{APP NAMESPACE}` placeholder is the project's namespace.</span></span> <span data-ttu-id="402fc-310">`Components` 代表資料夾的名稱。</span><span class="sxs-lookup"><span data-stu-id="402fc-310">`Components` represents the name of the folder.</span></span>

```cshtml
@using {APP NAMESPACE}.Components
```

<span data-ttu-id="402fc-311">檔案 `_ViewImports.cshtml` 位於 `Pages` Razor 頁面應用程式的資料夾或 `Views` MVC 應用程式的資料夾中。</span><span class="sxs-lookup"><span data-stu-id="402fc-311">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="402fc-312">如需詳細資訊，請參閱<xref:blazor/components/index#namespaces>。</span><span class="sxs-lookup"><span data-stu-id="402fc-312">For more information, see <xref:blazor/components/index#namespaces>.</span></span>

## <a name="additional-blazor-server-resources"></a><span data-ttu-id="402fc-313">其他 Blazor Server 資源</span><span class="sxs-lookup"><span data-stu-id="402fc-313">Additional Blazor Server resources</span></span>

* [<span data-ttu-id="402fc-314">狀態管理：處理已呈現</span><span class="sxs-lookup"><span data-stu-id="402fc-314">State management: Handle prerendering</span></span>](xref:blazor/state-management?pivot=server#handle-prerendering)
* <span data-ttu-id="402fc-315">Razor 與可呈現的相關的元件生命週期主題</span><span class="sxs-lookup"><span data-stu-id="402fc-315">Razor component lifecycle subjects that pertain to prerendering</span></span>
  * [<span data-ttu-id="402fc-316">元件初始化 (`OnInitialized{Async}`) </span><span class="sxs-lookup"><span data-stu-id="402fc-316">Component initialization (`OnInitialized{Async}`)</span></span>](xref:blazor/components/lifecycle#component-initialization-oninitializedasync)
  * [<span data-ttu-id="402fc-317">元件轉譯 (之後 `OnAfterRender{Async}`) </span><span class="sxs-lookup"><span data-stu-id="402fc-317">After component render (`OnAfterRender{Async}`)</span></span>](xref:blazor/components/lifecycle#after-component-render-onafterrenderasync)
  * [<span data-ttu-id="402fc-318">以具狀態重新連接後重新連線</span><span class="sxs-lookup"><span data-stu-id="402fc-318">Stateful reconnection after prerendering</span></span>](xref:blazor/components/lifecycle#stateful-reconnection-after-prerendering)
  * [<span data-ttu-id="402fc-319">偵測應用程式何時進行呈現</span><span class="sxs-lookup"><span data-stu-id="402fc-319">Detect when the app is prerendering</span></span>](xref:blazor/components/lifecycle#detect-when-the-app-is-prerendering)
* [<span data-ttu-id="402fc-320">驗證和授權：一般層面</span><span class="sxs-lookup"><span data-stu-id="402fc-320">Authentication and authorization: General aspects</span></span>](xref:blazor/security/index#aspnet-core-blazor-authentication-and-authorization)
* [<span data-ttu-id="402fc-321">Blazor Server 轉譯資料流程</span><span class="sxs-lookup"><span data-stu-id="402fc-321">Blazor Server rerendering</span></span>](xref:blazor/fundamentals/handle-errors?pivot=server#blazor-server-prerendering-server)
* [<span data-ttu-id="402fc-322">裝載和部署： Blazor Server</span><span class="sxs-lookup"><span data-stu-id="402fc-322">Host and deploy: Blazor Server</span></span>](xref:blazor/host-and-deploy/server)
* [<span data-ttu-id="402fc-323">威脅緩和：跨網站腳本 (XSS) </span><span class="sxs-lookup"><span data-stu-id="402fc-323">Threat mitigation: Cross-site scripting (XSS)</span></span>](xref:blazor/security/server/threat-mitigation#cross-site-scripting-xss)

::: zone-end
