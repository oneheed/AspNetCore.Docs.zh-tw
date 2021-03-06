---
title: ASP.NET 核心 Blazor 專案結構
author: guardrex
description: 瞭解 ASP.NET Core Blazor 應用程式專案結構。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/19/2021
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
uid: blazor/project-structure
ms.openlocfilehash: fe42c2d43b79ea959bb0ba8e5b96e6c865b2a416
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394872"
---
# <a name="aspnet-core-blazor-project-structure"></a><span data-ttu-id="e6772-103">ASP.NET 核心 Blazor 專案結構</span><span class="sxs-lookup"><span data-stu-id="e6772-103">ASP.NET Core Blazor project structure</span></span>

<span data-ttu-id="e6772-104">本文說明組成應用程式的檔案和資料夾，其中 Blazor 一個 Blazor 架構的專案範本會產生應用程式。</span><span class="sxs-lookup"><span data-stu-id="e6772-104">This article describes the files and folders that make up a Blazor app generated from one of the Blazor framework's project templates.</span></span> <span data-ttu-id="e6772-105">如需如何使用工具 Blazor 從專案範本建立應用程式的詳細資訊 Blazor ，請參閱 <xref:blazor/tooling> 。</span><span class="sxs-lookup"><span data-stu-id="e6772-105">For information on how to use tooling to create a Blazor app from a Blazor project template, see <xref:blazor/tooling>.</span></span> <span data-ttu-id="e6772-106">如需裝載模型和的詳細資訊，請 Blazor Blazor WebAssembly Blazor Server 參閱 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="e6772-106">For information on Blazor's hosting models, Blazor WebAssembly and Blazor Server, see <xref:blazor/hosting-models>.</span></span>

## Blazor WebAssembly

<span data-ttu-id="e6772-107">Blazor WebAssembly 專案範本： `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="e6772-107">Blazor WebAssembly project template: `blazorwasm`</span></span>

<span data-ttu-id="e6772-108">此 Blazor WebAssembly 範本會建立應用程式的初始檔案和目錄結構 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="e6772-108">The Blazor WebAssembly template creates the initial files and directory structure for a Blazor WebAssembly app.</span></span> <span data-ttu-id="e6772-109">應用程式會填入元件的示範程式碼 `FetchData` ，該元件會從靜態資產載入資料、以及 `weather.json` 使用者與元件的互動 `Counter` 。</span><span class="sxs-lookup"><span data-stu-id="e6772-109">The app is populated with demonstration code for a `FetchData` component that loads data from a static asset, `weather.json`, and user interaction with a `Counter` component.</span></span>

* <span data-ttu-id="e6772-110">`Pages` 資料夾：包含 `.razor` 組成應用程式 () 的可路由元件/頁面 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="e6772-110">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app.</span></span> <span data-ttu-id="e6772-111">每個頁面的路由都是使用指示詞來指定 [`@page`](xref:mvc/views/razor#page) 。</span><span class="sxs-lookup"><span data-stu-id="e6772-111">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="e6772-112">範本包含下列元件：</span><span class="sxs-lookup"><span data-stu-id="e6772-112">The template includes the following components:</span></span>
  * <span data-ttu-id="e6772-113">`Counter` 元件 (`Counter.razor`) ：實行計數器頁面。</span><span class="sxs-lookup"><span data-stu-id="e6772-113">`Counter` component (`Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="e6772-114">`FetchData` 元件 (`FetchData.razor`) ：實行提取資料頁面。</span><span class="sxs-lookup"><span data-stu-id="e6772-114">`FetchData` component (`FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="e6772-115">`Index` 元件 (`Index.razor`) ：實行首頁。</span><span class="sxs-lookup"><span data-stu-id="e6772-115">`Index` component (`Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="e6772-116">`Properties/launchSettings.json`：保留 [開發環境](xref:fundamentals/environments#development-and-launchsettingsjson)設定。</span><span class="sxs-lookup"><span data-stu-id="e6772-116">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="e6772-117">`Shared` 資料夾：包含下列共用元件和樣式表單：</span><span class="sxs-lookup"><span data-stu-id="e6772-117">`Shared` folder: Contains the following shared components and stylesheets:</span></span>
  * <span data-ttu-id="e6772-118">`MainLayout` 元件 (`MainLayout.razor`) ：應用程式的 [版面配置元件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="e6772-118">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="e6772-119">`MainLayout.razor.css`：應用程式主要版面配置的樣式表單。</span><span class="sxs-lookup"><span data-stu-id="e6772-119">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="e6772-120">`NavMenu` 元件 (`NavMenu.razor`) ：實行提要欄位導覽。</span><span class="sxs-lookup"><span data-stu-id="e6772-120">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="e6772-121">包含 () 的[ `NavLink` 元件](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ，其會呈現其他元件的導覽連結 Razor 。</span><span class="sxs-lookup"><span data-stu-id="e6772-121">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="e6772-122">元件會在 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 載入元件時自動表示選取的狀態，這可協助使用者瞭解目前顯示的元件。</span><span class="sxs-lookup"><span data-stu-id="e6772-122">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="e6772-123">`NavMenu.razor.css`：應用程式導覽功能表的樣式表單。</span><span class="sxs-lookup"><span data-stu-id="e6772-123">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>
  * <span data-ttu-id="e6772-124">`SurveyPrompt` 元件 (`SurveyPrompt.razor`) ： Blazor 問卷元件。</span><span class="sxs-lookup"><span data-stu-id="e6772-124">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="e6772-125">`Shared` 資料夾：包含下列共用元件：</span><span class="sxs-lookup"><span data-stu-id="e6772-125">`Shared` folder: Contains the following shared components:</span></span>
  * <span data-ttu-id="e6772-126">`MainLayout` 元件 (`MainLayout.razor`) ：應用程式的 [版面配置元件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="e6772-126">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="e6772-127">`NavMenu` 元件 (`NavMenu.razor`) ：實行提要欄位導覽。</span><span class="sxs-lookup"><span data-stu-id="e6772-127">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="e6772-128">包含 () 的[ `NavLink` 元件](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ，其會呈現其他元件的導覽連結 Razor 。</span><span class="sxs-lookup"><span data-stu-id="e6772-128">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="e6772-129">元件會在 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 載入元件時自動表示選取的狀態，這可協助使用者瞭解目前顯示的元件。</span><span class="sxs-lookup"><span data-stu-id="e6772-129">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="e6772-130">`SurveyPrompt` 元件 (`SurveyPrompt.razor`) ： Blazor 問卷元件。</span><span class="sxs-lookup"><span data-stu-id="e6772-130">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="e6772-131">`wwwroot`：應用程式的 [Web 根](xref:fundamentals/index#web-root) 資料夾，其中包含應用程式的公用靜態資產，包括 `appsettings.json` 設定的環境應用程式 [配置](xref:blazor/fundamentals/configuration)檔案。</span><span class="sxs-lookup"><span data-stu-id="e6772-131">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets, including `appsettings.json` and environmental app settings files for [configuration settings](xref:blazor/fundamentals/configuration).</span></span> <span data-ttu-id="e6772-132">`index.html`網頁是實作為 HTML 網頁的應用程式的根頁面：</span><span class="sxs-lookup"><span data-stu-id="e6772-132">The `index.html` webpage is the root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="e6772-133">最初要求應用程式的任何頁面時，會轉譯此頁面，並在回應中傳回。</span><span class="sxs-lookup"><span data-stu-id="e6772-133">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="e6772-134">頁面會指定呈現根元件的位置 `App` 。</span><span class="sxs-lookup"><span data-stu-id="e6772-134">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="e6772-135">元件是以 `div` `id` () 的 DOM 元素位置轉譯 `app` `<div id="app">Loading...</div>` 。</span><span class="sxs-lookup"><span data-stu-id="e6772-135">The component is rendered at the location of the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="e6772-136">`wwwroot`：應用程式的 [Web 根](xref:fundamentals/index#web-root) 資料夾，其中包含應用程式的公用靜態資產，包括 `appsettings.json` 設定的環境應用程式 [配置](xref:blazor/fundamentals/configuration)檔案。</span><span class="sxs-lookup"><span data-stu-id="e6772-136">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets, including `appsettings.json` and environmental app settings files for [configuration settings](xref:blazor/fundamentals/configuration).</span></span> <span data-ttu-id="e6772-137">`index.html`網頁是實作為 HTML 網頁的應用程式的根頁面：</span><span class="sxs-lookup"><span data-stu-id="e6772-137">The `index.html` webpage is the root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="e6772-138">最初要求應用程式的任何頁面時，會轉譯此頁面，並在回應中傳回。</span><span class="sxs-lookup"><span data-stu-id="e6772-138">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="e6772-139">頁面會指定呈現根元件的位置 `App` 。</span><span class="sxs-lookup"><span data-stu-id="e6772-139">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="e6772-140">元件會轉譯在 `app` DOM 元素 () 的位置 `<app>Loading...</app>` 。</span><span class="sxs-lookup"><span data-stu-id="e6772-140">The component is rendered at the location of the `app` DOM element (`<app>Loading...</app>`).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="e6772-141">新增至檔案的 JavaScript (JS) 檔案 `wwwroot/index.html` 應該會出現在結尾 `</body>` 標記之前。</span><span class="sxs-lookup"><span data-stu-id="e6772-141">JavaScript (JS) files added to the `wwwroot/index.html` file should appear before the closing `</body>` tag.</span></span> <span data-ttu-id="e6772-142">在某些情況下，從 JS 檔案載入自訂 JS 程式碼的順序很重要。</span><span class="sxs-lookup"><span data-stu-id="e6772-142">The order that custom JS code is loaded from JS files is important in some scenarios.</span></span> <span data-ttu-id="e6772-143">例如，請確定具有 interop 方法的 JS 檔案包含在 Blazor FRAMEWORK JS 檔案之前。</span><span class="sxs-lookup"><span data-stu-id="e6772-143">For example, ensure that JS files with interop methods are included before Blazor framework JS files.</span></span>

* <span data-ttu-id="e6772-144">`_Imports.razor`：包含 Razor 要包含在應用程式元件 () 的一般指示詞 `.razor` ，例如 [`@using`](xref:mvc/views/razor#using) 命名空間的指示詞。</span><span class="sxs-lookup"><span data-stu-id="e6772-144">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="e6772-145">`App.razor`：應用程式的根元件，它會使用元件來設定用戶端路由 <xref:Microsoft.AspNetCore.Components.Routing.Router> 。</span><span class="sxs-lookup"><span data-stu-id="e6772-145">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="e6772-146"><xref:Microsoft.AspNetCore.Components.Routing.Router>元件會攔截瀏覽器導覽，並轉譯符合所要求位址的頁面。</span><span class="sxs-lookup"><span data-stu-id="e6772-146">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="e6772-147">`Program.cs`：應用程式的進入點，可設定 WebAssembly 主機：</span><span class="sxs-lookup"><span data-stu-id="e6772-147">`Program.cs`: The app's entry point that sets up the WebAssembly host:</span></span>
  
  * <span data-ttu-id="e6772-148">`App`元件是應用程式的根元件。</span><span class="sxs-lookup"><span data-stu-id="e6772-148">The `App` component is the root component of the app.</span></span> <span data-ttu-id="e6772-149">`App`元件會指定為 `div` DOM 元素，其中 `id` `app` (的 `<div id="app">Loading...</div>` `wwwroot/index.html`)  () 的根元件集合 `builder.RootComponents.Add<App>("#app")` 。</span><span class="sxs-lookup"><span data-stu-id="e6772-149">The `App` component is specified as the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
  * <span data-ttu-id="e6772-150">系統會新增並設定[服務](xref:blazor/fundamentals/dependency-injection) (例如 `builder.Services.AddSingleton<IMyDependency, MyDependency>()`) 。</span><span class="sxs-lookup"><span data-stu-id="e6772-150">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="e6772-151">`Program.cs`：應用程式的進入點，可設定 WebAssembly 主機：</span><span class="sxs-lookup"><span data-stu-id="e6772-151">`Program.cs`: The app's entry point that sets up the WebAssembly host:</span></span>

  * <span data-ttu-id="e6772-152">`App`元件是應用程式的根元件。</span><span class="sxs-lookup"><span data-stu-id="e6772-152">The `App` component is the root component of the app.</span></span> <span data-ttu-id="e6772-153">`App`元件會指定為 `app` DOM 元素， (`<app>Loading...</app>` 在 `wwwroot/index.html`)  () 的根元件集合中 `builder.RootComponents.Add<App>("app")` 。</span><span class="sxs-lookup"><span data-stu-id="e6772-153">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
  * <span data-ttu-id="e6772-154">系統會新增並設定[服務](xref:blazor/fundamentals/dependency-injection) (例如 `builder.Services.AddSingleton<IMyDependency, MyDependency>()`) 。</span><span class="sxs-lookup"><span data-stu-id="e6772-154">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

## Blazor Server

<span data-ttu-id="e6772-155">Blazor Server 專案範本： `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="e6772-155">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="e6772-156">此 Blazor Server 範本會建立應用程式的初始檔案和目錄結構 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="e6772-156">The Blazor Server template creates the initial files and directory structure for a Blazor Server app.</span></span> <span data-ttu-id="e6772-157">應用程式會填入元件的示範程式碼， `FetchData` 該元件會從已註冊的服務載入資料，以及 `WeatherForecastService` 使用者與 `Counter` 元件的互動。</span><span class="sxs-lookup"><span data-stu-id="e6772-157">The app is populated with demonstration code for a `FetchData` component that loads data from a registered service, `WeatherForecastService`, and user interaction with a `Counter` component.</span></span>

* <span data-ttu-id="e6772-158">`Data` 資料夾：包含 `WeatherForecast` `WeatherForecastService` 提供範例天氣資料給應用程式元件的類別和執行 `FetchData` 。</span><span class="sxs-lookup"><span data-stu-id="e6772-158">`Data` folder: Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provides example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="e6772-159">`Pages` 資料夾：包含組成應用程式的可路由元件/頁面 (`.razor`) ， Blazor 以及 Razor 應用程式的根頁面 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="e6772-159">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="e6772-160">每個頁面的路由都是使用指示詞來指定 [`@page`](xref:mvc/views/razor#page) 。</span><span class="sxs-lookup"><span data-stu-id="e6772-160">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="e6772-161">範本包含下列各項：</span><span class="sxs-lookup"><span data-stu-id="e6772-161">The template includes the following:</span></span>
  * <span data-ttu-id="e6772-162">`_Host.cshtml`：應用程式的根頁面會實作為 Razor 頁面：</span><span class="sxs-lookup"><span data-stu-id="e6772-162">`_Host.cshtml`: The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="e6772-163">最初要求應用程式的任何頁面時，會轉譯此頁面，並在回應中傳回。</span><span class="sxs-lookup"><span data-stu-id="e6772-163">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="e6772-164">[主機] 頁面 `App` 會指定呈現根元件 () 的位置 `App.razor` 。</span><span class="sxs-lookup"><span data-stu-id="e6772-164">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="e6772-165">`Counter` 元件 (`Counter.razor`) ：實行計數器頁面。</span><span class="sxs-lookup"><span data-stu-id="e6772-165">`Counter` component (`Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="e6772-166">`Error` 元件 (`Error.razor`) ：當應用程式中發生未處理的例外狀況時，即會呈現。</span><span class="sxs-lookup"><span data-stu-id="e6772-166">`Error` component (`Error.razor`): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="e6772-167">`FetchData` 元件 (`FetchData.razor`) ：實行提取資料頁面。</span><span class="sxs-lookup"><span data-stu-id="e6772-167">`FetchData` component (`FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="e6772-168">`Index` 元件 (`Index.razor`) ：實行首頁。</span><span class="sxs-lookup"><span data-stu-id="e6772-168">`Index` component (`Index.razor`): Implements the Home page.</span></span>

> [!NOTE]
> <span data-ttu-id="e6772-169">新增至檔案的 JavaScript (JS) 檔案 `Pages/_Host.cshtml` 應該會出現在結尾 `</body>` 標記之前。</span><span class="sxs-lookup"><span data-stu-id="e6772-169">JavaScript (JS) files added to the `Pages/_Host.cshtml` file should appear before the closing `</body>` tag.</span></span> <span data-ttu-id="e6772-170">在某些情況下，從 JS 檔案載入自訂 JS 程式碼的順序很重要。</span><span class="sxs-lookup"><span data-stu-id="e6772-170">The order that custom JS code is loaded from JS files is important in some scenarios.</span></span> <span data-ttu-id="e6772-171">例如，請確定具有 interop 方法的 JS 檔案包含在 Blazor FRAMEWORK JS 檔案之前。</span><span class="sxs-lookup"><span data-stu-id="e6772-171">For example, ensure that JS files with interop methods are included before Blazor framework JS files.</span></span>

* <span data-ttu-id="e6772-172">`Properties/launchSettings.json`：保留 [開發環境](xref:fundamentals/environments#development-and-launchsettingsjson)設定。</span><span class="sxs-lookup"><span data-stu-id="e6772-172">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="e6772-173">`Shared` 資料夾：包含下列共用元件和樣式表單：</span><span class="sxs-lookup"><span data-stu-id="e6772-173">`Shared` folder: Contains the following shared components and stylesheets:</span></span>
  * <span data-ttu-id="e6772-174">`MainLayout` 元件 (`MainLayout.razor`) ：應用程式的 [版面配置元件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="e6772-174">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="e6772-175">`MainLayout.razor.css`：應用程式主要版面配置的樣式表單。</span><span class="sxs-lookup"><span data-stu-id="e6772-175">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="e6772-176">`NavMenu` 元件 (`NavMenu.razor`) ：實行提要欄位導覽。</span><span class="sxs-lookup"><span data-stu-id="e6772-176">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="e6772-177">包含 () 的[ `NavLink` 元件](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ，其會呈現其他元件的導覽連結 Razor 。</span><span class="sxs-lookup"><span data-stu-id="e6772-177">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="e6772-178">元件會在 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 載入元件時自動表示選取的狀態，這可協助使用者瞭解目前顯示的元件。</span><span class="sxs-lookup"><span data-stu-id="e6772-178">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="e6772-179">`NavMenu.razor.css`：應用程式導覽功能表的樣式表單。</span><span class="sxs-lookup"><span data-stu-id="e6772-179">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>
  * <span data-ttu-id="e6772-180">`SurveyPrompt` 元件 (`SurveyPrompt.razor`) ： Blazor 問卷元件。</span><span class="sxs-lookup"><span data-stu-id="e6772-180">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="e6772-181">`Shared` 資料夾：包含下列共用元件：</span><span class="sxs-lookup"><span data-stu-id="e6772-181">`Shared` folder: Contains the following shared components:</span></span>
  * <span data-ttu-id="e6772-182">`MainLayout` 元件 (`MainLayout.razor`) ：應用程式的 [版面配置元件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="e6772-182">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="e6772-183">`NavMenu` 元件 (`NavMenu.razor`) ：實行提要欄位導覽。</span><span class="sxs-lookup"><span data-stu-id="e6772-183">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="e6772-184">包含 () 的[ `NavLink` 元件](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ，其會呈現其他元件的導覽連結 Razor 。</span><span class="sxs-lookup"><span data-stu-id="e6772-184">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="e6772-185">元件會在 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 載入元件時自動表示選取的狀態，這可協助使用者瞭解目前顯示的元件。</span><span class="sxs-lookup"><span data-stu-id="e6772-185">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="e6772-186">`SurveyPrompt` 元件 (`SurveyPrompt.razor`) ： Blazor 問卷元件。</span><span class="sxs-lookup"><span data-stu-id="e6772-186">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>
  
::: moniker-end

* <span data-ttu-id="e6772-187">`wwwroot`：應用程式的 [Web 根](xref:fundamentals/index#web-root) 資料夾，其中包含應用程式的公用靜態資產。</span><span class="sxs-lookup"><span data-stu-id="e6772-187">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="e6772-188">`_Imports.razor`：包含 Razor 要包含在應用程式元件 () 的一般指示詞 `.razor` ，例如 [`@using`](xref:mvc/views/razor#using) 命名空間的指示詞。</span><span class="sxs-lookup"><span data-stu-id="e6772-188">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="e6772-189">`App.razor`：應用程式的根元件，它會使用元件來設定用戶端路由 <xref:Microsoft.AspNetCore.Components.Routing.Router> 。</span><span class="sxs-lookup"><span data-stu-id="e6772-189">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="e6772-190"><xref:Microsoft.AspNetCore.Components.Routing.Router>元件會攔截瀏覽器導覽，並轉譯符合所要求位址的頁面。</span><span class="sxs-lookup"><span data-stu-id="e6772-190">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="e6772-191">`appsettings.json` 和環境應用程式佈建檔案：提供應用程式的 [設定](xref:blazor/fundamentals/configuration) 。</span><span class="sxs-lookup"><span data-stu-id="e6772-191">`appsettings.json` and environmental app settings files: Provide [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span>

* <span data-ttu-id="e6772-192">`Program.cs`：設定 ASP.NET 核心 [主機](xref:fundamentals/host/generic-host)的應用程式進入點。</span><span class="sxs-lookup"><span data-stu-id="e6772-192">`Program.cs`: The app's entry point that sets up the ASP.NET Core [host](xref:fundamentals/host/generic-host).</span></span>

* <span data-ttu-id="e6772-193">`Startup.cs`：包含應用程式的啟動邏輯。</span><span class="sxs-lookup"><span data-stu-id="e6772-193">`Startup.cs`: Contains the app's startup logic.</span></span> <span data-ttu-id="e6772-194">`Startup`類別會定義兩種方法：</span><span class="sxs-lookup"><span data-stu-id="e6772-194">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="e6772-195">`ConfigureServices`：設定應用程式的相依性 [插入 (DI) ](xref:fundamentals/dependency-injection) 服務。</span><span class="sxs-lookup"><span data-stu-id="e6772-195">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="e6772-196">藉由呼叫來新增服務 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> ，並將 `WeatherForecastService` 加入至服務容器，以供範例元件使用 `FetchData` 。</span><span class="sxs-lookup"><span data-stu-id="e6772-196">Services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="e6772-197">`Configure`：設定應用程式的要求處理管線：</span><span class="sxs-lookup"><span data-stu-id="e6772-197">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="e6772-198"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 呼叫以設定與瀏覽器的即時連線端點。</span><span class="sxs-lookup"><span data-stu-id="e6772-198"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="e6772-199">使用建立連線 [SignalR](xref:signalr/introduction) ，這是將即時 web 功能新增至應用程式的架構。</span><span class="sxs-lookup"><span data-stu-id="e6772-199">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="e6772-200">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) 呼叫以設定應用程式的根頁面 (`Pages/_Host.cshtml`) 並啟用導覽。</span><span class="sxs-lookup"><span data-stu-id="e6772-200">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e6772-201">其他資源</span><span class="sxs-lookup"><span data-stu-id="e6772-201">Additional resources</span></span>

* <xref:blazor/tooling>
* <xref:blazor/hosting-models>
