---
title: '將 ASP.NET Core :::no-loc(Razor)::: 元件整合至 :::no-loc(Razor)::: 頁面和 MVC 應用程式'
author: guardrex
description: '瞭解應用程式中元件和 DOM 元素的資料系結案例 :::no-loc(Blazor)::: 。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
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
uid: blazor/components/integrate-components-into-razor-pages-and-mvc-apps
ms.openlocfilehash: e56a08be082cef4ba3a0a58fdfa9d3800d244f75
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056253"
---
# <a name="integrate-aspnet-core-no-locrazor-components-into-no-locrazor-pages-and-mvc-apps"></a><span data-ttu-id="b6f25-103">將 ASP.NET Core :::no-loc(Razor)::: 元件整合至 :::no-loc(Razor)::: 頁面和 MVC 應用程式</span><span class="sxs-lookup"><span data-stu-id="b6f25-103">Integrate ASP.NET Core :::no-loc(Razor)::: components into :::no-loc(Razor)::: Pages and MVC apps</span></span>

<span data-ttu-id="b6f25-104">依 [Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b6f25-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="b6f25-105">:::no-loc(Razor)::: 元件可以整合至 :::no-loc(Razor)::: 頁面和 MVC 應用程式。</span><span class="sxs-lookup"><span data-stu-id="b6f25-105">:::no-loc(Razor)::: components can be integrated into :::no-loc(Razor)::: Pages and MVC apps.</span></span> <span data-ttu-id="b6f25-106">轉譯頁面或視圖時，可同時資源清單元件。</span><span class="sxs-lookup"><span data-stu-id="b6f25-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="b6f25-107">[準備好應用程式](#prepare-the-app)之後，請根據應用程式的需求，使用下列各節中的指導方針：</span><span class="sxs-lookup"><span data-stu-id="b6f25-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="b6f25-108">可路由的元件：適用于直接從使用者要求路由傳送的元件。</span><span class="sxs-lookup"><span data-stu-id="b6f25-108">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="b6f25-109">當訪客應能在其瀏覽器中使用指示詞為元件發出 HTTP 要求時，請遵循此指導 [`@page`](xref:mvc/views/razor#page) 方針。</span><span class="sxs-lookup"><span data-stu-id="b6f25-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="b6f25-110">在頁面應用程式中使用可路由的元件 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="b6f25-110">Use routable components in a :::no-loc(Razor)::: Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="b6f25-111">在 MVC 應用程式中使用可路由的元件</span><span class="sxs-lookup"><span data-stu-id="b6f25-111">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="b6f25-112">[從頁面或視圖呈現元件](#render-components-from-a-page-or-view)：適用于無法直接從使用者要求路由傳送的元件。</span><span class="sxs-lookup"><span data-stu-id="b6f25-112">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="b6f25-113">當應用程式使用元件標籤協助程式將元件內嵌至現有的頁面和流覽 [器](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)時，請遵循此指引。</span><span class="sxs-lookup"><span data-stu-id="b6f25-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="b6f25-114">準備應用程式</span><span class="sxs-lookup"><span data-stu-id="b6f25-114">Prepare the app</span></span>

<span data-ttu-id="b6f25-115">現有的 :::no-loc(Razor)::: 頁面或 MVC 應用程式可以將 :::no-loc(Razor)::: 元件整合至頁面和流覽中：</span><span class="sxs-lookup"><span data-stu-id="b6f25-115">An existing :::no-loc(Razor)::: Pages or MVC app can integrate :::no-loc(Razor)::: components into pages and views:</span></span>

1. <span data-ttu-id="b6f25-116">在應用程式的版面配置檔案中 (`_Layout.cshtml`) ：</span><span class="sxs-lookup"><span data-stu-id="b6f25-116">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="b6f25-117">將下列 `<base>` 標記新增至 `<head>` 元素：</span><span class="sxs-lookup"><span data-stu-id="b6f25-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="b6f25-118">`href`上述範例中的 *應用程式基底路徑* )  (值假設應用程式位於根 URL 路徑 (`/`) 。</span><span class="sxs-lookup"><span data-stu-id="b6f25-118">The `href` value (the *app base path* ) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="b6f25-119">如果應用程式是子應用程式，請遵循本文的「 *應用程式基底路徑* 」一節中的指導方針 <xref:blazor/host-and-deploy/index#app-base-path> 。</span><span class="sxs-lookup"><span data-stu-id="b6f25-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="b6f25-120">檔案 `_Layout.cshtml` 位於 MVC 應用程式中的 pages */shared* 資料夾 :::no-loc(Razor)::: 或 MVC 應用程式中的 *Views/shared* 資料夾內。</span><span class="sxs-lookup"><span data-stu-id="b6f25-120">The `_Layout.cshtml` file is located in the *Pages/Shared* folder in a :::no-loc(Razor)::: Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="b6f25-121">將 `<script>` *blazor.server.js* 腳本的標記新增至結尾 `</body>` 標記之前：</span><span class="sxs-lookup"><span data-stu-id="b6f25-121">Add a `<script>` tag for the *blazor.server.js* script immediately before the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="b6f25-122">架構會將 *blazor.server.js* 腳本新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="b6f25-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="b6f25-123">不需要手動將腳本新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="b6f25-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="b6f25-124">`_Imports.razor`使用下列內容將檔案新增至專案的根資料夾， (將最後一個命名空間變更 `MyAppNamespace` 為應用程式) 的命名空間：</span><span class="sxs-lookup"><span data-stu-id="b6f25-124">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="b6f25-125">在中 `Startup.ConfigureServices` ，註冊 :::no-loc(Blazor Server)::: 服務：</span><span class="sxs-lookup"><span data-stu-id="b6f25-125">In `Startup.ConfigureServices`, register the :::no-loc(Blazor Server)::: service:</span></span>

   ```csharp
   services.AddServerSide:::no-loc(Blazor):::();
   ```

1. <span data-ttu-id="b6f25-126">在中 `Startup.Configure` ，將 :::no-loc(Blazor)::: 中樞端點新增至 `app.UseEndpoints` ：</span><span class="sxs-lookup"><span data-stu-id="b6f25-126">In `Startup.Configure`, add the :::no-loc(Blazor)::: Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.Map:::no-loc(Blazor):::Hub();
   ```

1. <span data-ttu-id="b6f25-127">將元件整合至任何頁面或視圖中。</span><span class="sxs-lookup"><span data-stu-id="b6f25-127">Integrate components into any page or view.</span></span> <span data-ttu-id="b6f25-128">如需詳細資訊，請參閱 [從頁面或視圖區段呈現元件](#render-components-from-a-page-or-view) 。</span><span class="sxs-lookup"><span data-stu-id="b6f25-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a><span data-ttu-id="b6f25-129">在頁面應用程式中使用可路由的元件 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="b6f25-129">Use routable components in a :::no-loc(Razor)::: Pages app</span></span>

<span data-ttu-id="b6f25-130">*本節適用于新增直接可從使用者要求路由傳送的元件。*</span><span class="sxs-lookup"><span data-stu-id="b6f25-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="b6f25-131">支援 :::no-loc(Razor)::: 頁面應用程式中可路由的元件 :::no-loc(Razor)::: ：</span><span class="sxs-lookup"><span data-stu-id="b6f25-131">To support routable :::no-loc(Razor)::: components in :::no-loc(Razor)::: Pages apps:</span></span>

1. <span data-ttu-id="b6f25-132">遵循「 [準備應用程式](#prepare-the-app) 」一節中的指導方針。</span><span class="sxs-lookup"><span data-stu-id="b6f25-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="b6f25-133">`App.razor`使用下列內容將檔案新增至專案根目錄：</span><span class="sxs-lookup"><span data-stu-id="b6f25-133">Add an `App.razor` file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="b6f25-134">將檔案新增 `_Host.cshtml` 至 `Pages` 具有下列內容的資料夾：</span><span class="sxs-lookup"><span data-stu-id="b6f25-134">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="b6f25-135">元件會使用共用檔案 `_Layout.cshtml` 進行版面配置。</span><span class="sxs-lookup"><span data-stu-id="b6f25-135">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="b6f25-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 設定元件是否為 `App` ：</span><span class="sxs-lookup"><span data-stu-id="b6f25-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="b6f25-137">會資源清單到頁面中。</span><span class="sxs-lookup"><span data-stu-id="b6f25-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="b6f25-138">會在頁面上轉譯為靜態 HTML，或包含從使用者代理程式啟動應用程式所需的資訊 :::no-loc(Blazor)::: 。</span><span class="sxs-lookup"><span data-stu-id="b6f25-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

   | <span data-ttu-id="b6f25-139">轉譯模式</span><span class="sxs-lookup"><span data-stu-id="b6f25-139">Render Mode</span></span> | <span data-ttu-id="b6f25-140">描述</span><span class="sxs-lookup"><span data-stu-id="b6f25-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="b6f25-141">將 `App` 元件轉譯為靜態 HTML，並包含 :::no-loc(Blazor Server)::: 應用程式的標記。</span><span class="sxs-lookup"><span data-stu-id="b6f25-141">Renders the `App` component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="b6f25-142">當使用者代理程式啟動時，會使用此標記來啟動 :::no-loc(Blazor)::: 應用程式。</span><span class="sxs-lookup"><span data-stu-id="b6f25-142">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="b6f25-143">轉譯應用程式的標記 :::no-loc(Blazor Server)::: 。</span><span class="sxs-lookup"><span data-stu-id="b6f25-143">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="b6f25-144">`App`不包含元件的輸出。</span><span class="sxs-lookup"><span data-stu-id="b6f25-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="b6f25-145">當使用者代理程式啟動時，會使用此標記來啟動 :::no-loc(Blazor)::: 應用程式。</span><span class="sxs-lookup"><span data-stu-id="b6f25-145">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="b6f25-146">將 `App` 元件轉譯為靜態 HTML。</span><span class="sxs-lookup"><span data-stu-id="b6f25-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="b6f25-147">如需元件標記協助程式的詳細資訊，請參閱 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> 。</span><span class="sxs-lookup"><span data-stu-id="b6f25-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="b6f25-148">將頁面的低優先順序路由新增 `_Host.cshtml` 至中的端點設定 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="b6f25-148">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="b6f25-149">將可路由傳送的元件新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="b6f25-149">Add routable components to the app.</span></span> <span data-ttu-id="b6f25-150">例如：</span><span class="sxs-lookup"><span data-stu-id="b6f25-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="b6f25-151">如需命名空間的詳細資訊，請參閱 [元件命名空間](#component-namespaces) 一節。</span><span class="sxs-lookup"><span data-stu-id="b6f25-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="b6f25-152">在 MVC 應用程式中使用可路由的元件</span><span class="sxs-lookup"><span data-stu-id="b6f25-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="b6f25-153">*本節適用于新增直接可從使用者要求路由傳送的元件。*</span><span class="sxs-lookup"><span data-stu-id="b6f25-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="b6f25-154">支援 :::no-loc(Razor)::: MVC 應用程式中的可路由元件：</span><span class="sxs-lookup"><span data-stu-id="b6f25-154">To support routable :::no-loc(Razor)::: components in MVC apps:</span></span>

1. <span data-ttu-id="b6f25-155">遵循「 [準備應用程式](#prepare-the-app) 」一節中的指導方針。</span><span class="sxs-lookup"><span data-stu-id="b6f25-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="b6f25-156">`App.razor`使用下列內容將檔案新增至專案的根目錄：</span><span class="sxs-lookup"><span data-stu-id="b6f25-156">Add an `App.razor` file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="b6f25-157">將檔案新增 `_Host.cshtml` 至 `Views/Home` 具有下列內容的資料夾：</span><span class="sxs-lookup"><span data-stu-id="b6f25-157">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="b6f25-158">元件會使用共用檔案 `_Layout.cshtml` 進行版面配置。</span><span class="sxs-lookup"><span data-stu-id="b6f25-158">Components use the shared `_Layout.cshtml` file for their layout.</span></span>
   
   <span data-ttu-id="b6f25-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 設定元件是否為 `App` ：</span><span class="sxs-lookup"><span data-stu-id="b6f25-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="b6f25-160">會資源清單到頁面中。</span><span class="sxs-lookup"><span data-stu-id="b6f25-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="b6f25-161">會在頁面上轉譯為靜態 HTML，或包含從使用者代理程式啟動應用程式所需的資訊 :::no-loc(Blazor)::: 。</span><span class="sxs-lookup"><span data-stu-id="b6f25-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

   | <span data-ttu-id="b6f25-162">轉譯模式</span><span class="sxs-lookup"><span data-stu-id="b6f25-162">Render Mode</span></span> | <span data-ttu-id="b6f25-163">描述</span><span class="sxs-lookup"><span data-stu-id="b6f25-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="b6f25-164">將 `App` 元件轉譯為靜態 HTML，並包含 :::no-loc(Blazor Server)::: 應用程式的標記。</span><span class="sxs-lookup"><span data-stu-id="b6f25-164">Renders the `App` component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="b6f25-165">當使用者代理程式啟動時，會使用此標記來啟動 :::no-loc(Blazor)::: 應用程式。</span><span class="sxs-lookup"><span data-stu-id="b6f25-165">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="b6f25-166">轉譯應用程式的標記 :::no-loc(Blazor Server)::: 。</span><span class="sxs-lookup"><span data-stu-id="b6f25-166">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="b6f25-167">`App`不包含元件的輸出。</span><span class="sxs-lookup"><span data-stu-id="b6f25-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="b6f25-168">當使用者代理程式啟動時，會使用此標記來啟動 :::no-loc(Blazor)::: 應用程式。</span><span class="sxs-lookup"><span data-stu-id="b6f25-168">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="b6f25-169">將 `App` 元件轉譯為靜態 HTML。</span><span class="sxs-lookup"><span data-stu-id="b6f25-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="b6f25-170">如需元件標記協助程式的詳細資訊，請參閱 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> 。</span><span class="sxs-lookup"><span data-stu-id="b6f25-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="b6f25-171">將動作新增至 Home 控制器：</span><span class="sxs-lookup"><span data-stu-id="b6f25-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult :::no-loc(Blazor):::()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="b6f25-172">新增控制器動作的低優先順序路由，將 `_Host.cshtml` 視圖傳回至中的端點設定 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="b6f25-172">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController(":::no-loc(Blazor):::", "Home");
   });
   ```

1. <span data-ttu-id="b6f25-173">建立 `Pages` 資料夾，並將可路由傳送的元件新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="b6f25-173">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="b6f25-174">例如：</span><span class="sxs-lookup"><span data-stu-id="b6f25-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="b6f25-175">如需命名空間的詳細資訊，請參閱 [元件命名空間](#component-namespaces) 一節。</span><span class="sxs-lookup"><span data-stu-id="b6f25-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="b6f25-176">從頁面或視圖呈現元件</span><span class="sxs-lookup"><span data-stu-id="b6f25-176">Render components from a page or view</span></span>

<span data-ttu-id="b6f25-177">*本節適用于將元件新增至頁面或視圖，其中元件無法直接從使用者要求路由傳送。*</span><span class="sxs-lookup"><span data-stu-id="b6f25-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="b6f25-178">若要從頁面或視圖轉譯元件，請使用 [元件標記](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)協助程式。</span><span class="sxs-lookup"><span data-stu-id="b6f25-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="b6f25-179">轉譯具狀態的互動式元件</span><span class="sxs-lookup"><span data-stu-id="b6f25-179">Render stateful interactive components</span></span>

<span data-ttu-id="b6f25-180">可設定狀態的互動式元件可以加入至 :::no-loc(Razor)::: 頁面或視圖中。</span><span class="sxs-lookup"><span data-stu-id="b6f25-180">Stateful interactive components can be added to a :::no-loc(Razor)::: page or view.</span></span>

<span data-ttu-id="b6f25-181">當頁面或視圖呈現時：</span><span class="sxs-lookup"><span data-stu-id="b6f25-181">When the page or view renders:</span></span>

* <span data-ttu-id="b6f25-182">此元件是使用頁面或視圖所資源清單。</span><span class="sxs-lookup"><span data-stu-id="b6f25-182">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="b6f25-183">用於進行可呈現的初始元件狀態會遺失。</span><span class="sxs-lookup"><span data-stu-id="b6f25-183">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="b6f25-184">建立連接時，會建立新的元件狀態 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="b6f25-184">New component state is created when the :::no-loc(SignalR)::: connection is established.</span></span>

<span data-ttu-id="b6f25-185">下列 :::no-loc(Razor)::: 頁面會呈現 `Counter` 元件：</span><span class="sxs-lookup"><span data-stu-id="b6f25-185">The following :::no-loc(Razor)::: page renders a `Counter` component:</span></span>

```cshtml
<h1>My :::no-loc(Razor)::: Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="b6f25-186">如需詳細資訊，請參閱<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="b6f25-186">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="b6f25-187">轉譯非互動式元件</span><span class="sxs-lookup"><span data-stu-id="b6f25-187">Render noninteractive components</span></span>

<span data-ttu-id="b6f25-188">在下 :::no-loc(Razor)::: 一個頁面中， `Counter` 系統會使用表單所指定的初始值，以靜態方式呈現元件。</span><span class="sxs-lookup"><span data-stu-id="b6f25-188">In the following :::no-loc(Razor)::: page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="b6f25-189">由於元件是以靜態方式轉譯，因此元件並非互動式：</span><span class="sxs-lookup"><span data-stu-id="b6f25-189">Since the component is statically rendered, the component isn't interactive:</span></span>

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

<span data-ttu-id="b6f25-190">如需詳細資訊，請參閱<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="b6f25-190">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="b6f25-191">元件命名空間</span><span class="sxs-lookup"><span data-stu-id="b6f25-191">Component namespaces</span></span>

<span data-ttu-id="b6f25-192">使用自訂資料夾來保存應用程式的元件時，請將代表資料夾的命名空間新增至頁面/視圖或檔案 `_ViewImports.cshtml` 。</span><span class="sxs-lookup"><span data-stu-id="b6f25-192">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="b6f25-193">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="b6f25-193">In the following example:</span></span>

* <span data-ttu-id="b6f25-194">變更 `MyAppNamespace` 為應用程式的命名空間。</span><span class="sxs-lookup"><span data-stu-id="b6f25-194">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="b6f25-195">如果未使用名為 *元件* 的資料夾來保存元件，請變更 `Components` 為元件所在的資料夾。</span><span class="sxs-lookup"><span data-stu-id="b6f25-195">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="b6f25-196">檔案 `_ViewImports.cshtml` 位於 `Pages` :::no-loc(Razor)::: 頁面應用程式的資料夾或 `Views` MVC 應用程式的資料夾中。</span><span class="sxs-lookup"><span data-stu-id="b6f25-196">The `_ViewImports.cshtml` file is located in the `Pages` folder of a :::no-loc(Razor)::: Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="b6f25-197">如需詳細資訊，請參閱<xref:blazor/components/index#namespaces>。</span><span class="sxs-lookup"><span data-stu-id="b6f25-197">For more information, see <xref:blazor/components/index#namespaces>.</span></span>
