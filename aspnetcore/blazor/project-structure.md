---
title: ASP.NET Core Blazor 專案結構
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
ms.openlocfilehash: ae41d096c50d350b7fcde52da59382614e62c109
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658621"
---
# <a name="aspnet-core-no-locblazor-project-structure"></a><span data-ttu-id="e1a66-103">ASP.NET Core Blazor 專案結構</span><span class="sxs-lookup"><span data-stu-id="e1a66-103">ASP.NET Core Blazor project structure</span></span>

<span data-ttu-id="e1a66-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e1a66-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e1a66-105">本文說明組成專案範本所產生之應用程式的檔案和資料夾 Blazor Blazor 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-105">This article describes the files and folders that make up a Blazor app generated from the Blazor project templates.</span></span>

## Blazor WebAssembly

<span data-ttu-id="e1a66-106">Blazor WebAssembly範本 (`blazorwasm`) 會建立應用程式的初始檔案和目錄結構 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-106">The Blazor WebAssembly template (`blazorwasm`) creates the initial files and directory structure for a Blazor WebAssembly app.</span></span> <span data-ttu-id="e1a66-107">應用程式會填入元件的示範程式碼 `FetchData` ，該元件會從靜態資產載入資料、以及 `weather.json` 使用者與元件的互動 `Counter` 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-107">The app is populated with demonstration code for a `FetchData` component that loads data from a static asset, `weather.json`, and user interaction with a `Counter` component.</span></span>

* <span data-ttu-id="e1a66-108">`Pages` 資料夾：包含 `.razor` 組成應用程式 () 的可路由元件/頁面 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-108">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app.</span></span> <span data-ttu-id="e1a66-109">每個頁面的路由都是使用指示詞來指定 [`@page`](xref:mvc/views/razor#page) 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-109">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="e1a66-110">範本包含下列元件：</span><span class="sxs-lookup"><span data-stu-id="e1a66-110">The template includes the following components:</span></span>
  * <span data-ttu-id="e1a66-111">`Counter` 元件 (`Counter.razor`) ：實行計數器頁面。</span><span class="sxs-lookup"><span data-stu-id="e1a66-111">`Counter` component (`Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="e1a66-112">`FetchData` 元件 (`FetchData.razor`) ：實行提取資料頁面。</span><span class="sxs-lookup"><span data-stu-id="e1a66-112">`FetchData` component (`FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="e1a66-113">`Index` 元件 (`Index.razor`) ：實行首頁。</span><span class="sxs-lookup"><span data-stu-id="e1a66-113">`Index` component (`Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="e1a66-114">`Properties/launchSettings.json`：保留 [開發環境](xref:fundamentals/environments#development-and-launchsettingsjson)設定。</span><span class="sxs-lookup"><span data-stu-id="e1a66-114">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="e1a66-115">`Shared` 資料夾：包含下列共用元件和樣式表單：</span><span class="sxs-lookup"><span data-stu-id="e1a66-115">`Shared` folder: Contains the following shared components and stylesheets:</span></span>
  * <span data-ttu-id="e1a66-116">`MainLayout` 元件 (`MainLayout.razor`) ：應用程式的 [版面配置元件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="e1a66-116">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="e1a66-117">`MainLayout.razor.css`：應用程式主要版面配置的樣式表單。</span><span class="sxs-lookup"><span data-stu-id="e1a66-117">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="e1a66-118">`NavMenu` 元件 (`NavMenu.razor`) ：實行提要欄位導覽。</span><span class="sxs-lookup"><span data-stu-id="e1a66-118">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="e1a66-119">包含 () 的[ `NavLink` 元件](xref:blazor/fundamentals/routing#navlink-component) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ，其會呈現其他元件的導覽連結 Razor 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-119">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="e1a66-120">元件會在 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 載入元件時自動表示選取的狀態，這可協助使用者瞭解目前顯示的元件。</span><span class="sxs-lookup"><span data-stu-id="e1a66-120">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="e1a66-121">`NavMenu.razor.css`：應用程式導覽功能表的樣式表單。</span><span class="sxs-lookup"><span data-stu-id="e1a66-121">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>
  * <span data-ttu-id="e1a66-122">`SurveyPrompt` 元件 (`SurveyPrompt.razor`) ： Blazor 問卷元件。</span><span class="sxs-lookup"><span data-stu-id="e1a66-122">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="e1a66-123">`Shared` 資料夾：包含下列共用元件：</span><span class="sxs-lookup"><span data-stu-id="e1a66-123">`Shared` folder: Contains the following shared components:</span></span>
  * <span data-ttu-id="e1a66-124">`MainLayout` 元件 (`MainLayout.razor`) ：應用程式的 [版面配置元件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="e1a66-124">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="e1a66-125">`NavMenu` 元件 (`NavMenu.razor`) ：實行提要欄位導覽。</span><span class="sxs-lookup"><span data-stu-id="e1a66-125">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="e1a66-126">包含 () 的[ `NavLink` 元件](xref:blazor/fundamentals/routing#navlink-component) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ，其會呈現其他元件的導覽連結 Razor 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-126">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="e1a66-127">元件會在 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 載入元件時自動表示選取的狀態，這可協助使用者瞭解目前顯示的元件。</span><span class="sxs-lookup"><span data-stu-id="e1a66-127">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="e1a66-128">`SurveyPrompt` 元件 (`SurveyPrompt.razor`) ： Blazor 問卷元件。</span><span class="sxs-lookup"><span data-stu-id="e1a66-128">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="e1a66-129">`wwwroot`：應用程式的 [Web 根](xref:fundamentals/index#web-root) 資料夾，其中包含應用程式的公用靜態資產，包括 `appsettings.json` 設定的環境應用程式 [配置](xref:blazor/fundamentals/configuration)檔案。</span><span class="sxs-lookup"><span data-stu-id="e1a66-129">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets, including `appsettings.json` and environmental app settings files for [configuration settings](xref:blazor/fundamentals/configuration).</span></span> <span data-ttu-id="e1a66-130">`index.html`網頁是實作為 HTML 網頁的應用程式的根頁面：</span><span class="sxs-lookup"><span data-stu-id="e1a66-130">The `index.html` webpage is the root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="e1a66-131">最初要求應用程式的任何頁面時，會轉譯此頁面，並在回應中傳回。</span><span class="sxs-lookup"><span data-stu-id="e1a66-131">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="e1a66-132">頁面會指定呈現根元件的位置 `App` 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-132">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="e1a66-133">元件是以 `div` `id` () 的 DOM 元素位置轉譯 `app` `<div id="app">Loading...</div>` 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-133">The component is rendered at the location of the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="e1a66-134">`wwwroot`：應用程式的 [Web 根](xref:fundamentals/index#web-root) 資料夾，其中包含應用程式的公用靜態資產，包括 `appsettings.json` 設定的環境應用程式 [配置](xref:blazor/fundamentals/configuration)檔案。</span><span class="sxs-lookup"><span data-stu-id="e1a66-134">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets, including `appsettings.json` and environmental app settings files for [configuration settings](xref:blazor/fundamentals/configuration).</span></span> <span data-ttu-id="e1a66-135">`index.html`網頁是實作為 HTML 網頁的應用程式的根頁面：</span><span class="sxs-lookup"><span data-stu-id="e1a66-135">The `index.html` webpage is the root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="e1a66-136">最初要求應用程式的任何頁面時，會轉譯此頁面，並在回應中傳回。</span><span class="sxs-lookup"><span data-stu-id="e1a66-136">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="e1a66-137">頁面會指定呈現根元件的位置 `App` 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-137">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="e1a66-138">元件會轉譯在 `app` DOM 元素 () 的位置 `<app>Loading...</app>` 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-138">The component is rendered at the location of the `app` DOM element (`<app>Loading...</app>`).</span></span>

::: moniker-end

* <span data-ttu-id="e1a66-139">`_Imports.razor`：包含 Razor 要包含在應用程式元件 () 的一般指示詞 `.razor` ，例如 [`@using`](xref:mvc/views/razor#using) 命名空間的指示詞。</span><span class="sxs-lookup"><span data-stu-id="e1a66-139">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="e1a66-140">`App.razor`：應用程式的根元件，它會使用元件來設定用戶端路由 <xref:Microsoft.AspNetCore.Components.Routing.Router> 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-140">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="e1a66-141"><xref:Microsoft.AspNetCore.Components.Routing.Router>元件會攔截瀏覽器導覽，並轉譯符合所要求位址的頁面。</span><span class="sxs-lookup"><span data-stu-id="e1a66-141">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="e1a66-142">`Program.cs`：應用程式的進入點，可設定 WebAssembly 主機：</span><span class="sxs-lookup"><span data-stu-id="e1a66-142">`Program.cs`: The app's entry point that sets up the WebAssembly host:</span></span>
  
  * <span data-ttu-id="e1a66-143">`App`元件是應用程式的根元件。</span><span class="sxs-lookup"><span data-stu-id="e1a66-143">The `App` component is the root component of the app.</span></span> <span data-ttu-id="e1a66-144">`App`元件會指定為 `div` DOM 元素，其中 `id` `app` (的 `<div id="app">Loading...</div>` `wwwroot/index.html`)  () 的根元件集合 `builder.RootComponents.Add<App>("#app")` 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-144">The `App` component is specified as the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
  * <span data-ttu-id="e1a66-145">系統會新增並設定[服務](xref:blazor/fundamentals/dependency-injection) (例如 `builder.Services.AddSingleton<IMyDependency, MyDependency>()`) 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-145">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="e1a66-146">`Program.cs`：應用程式的進入點，可設定 WebAssembly 主機：</span><span class="sxs-lookup"><span data-stu-id="e1a66-146">`Program.cs`: The app's entry point that sets up the WebAssembly host:</span></span>

  * <span data-ttu-id="e1a66-147">`App`元件是應用程式的根元件。</span><span class="sxs-lookup"><span data-stu-id="e1a66-147">The `App` component is the root component of the app.</span></span> <span data-ttu-id="e1a66-148">`App`元件會指定為 `app` DOM 元素， (`<app>Loading...</app>` 在 `wwwroot/index.html`)  () 的根元件集合中 `builder.RootComponents.Add<App>("app")` 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-148">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
  * <span data-ttu-id="e1a66-149">系統會新增並設定[服務](xref:blazor/fundamentals/dependency-injection) (例如 `builder.Services.AddSingleton<IMyDependency, MyDependency>()`) 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-149">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

## Blazor Server

<span data-ttu-id="e1a66-150">Blazor Server範本 (`blazorserver`) 會建立應用程式的初始檔案和目錄結構 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-150">The Blazor Server template (`blazorserver`) creates the initial files and directory structure for a Blazor Server app.</span></span> <span data-ttu-id="e1a66-151">應用程式會填入元件的示範程式碼， `FetchData` 該元件會從已註冊的服務載入資料，以及 `WeatherForecastService` 使用者與 `Counter` 元件的互動。</span><span class="sxs-lookup"><span data-stu-id="e1a66-151">The app is populated with demonstration code for a `FetchData` component that loads data from a registered service, `WeatherForecastService`, and user interaction with a `Counter` component.</span></span>

* <span data-ttu-id="e1a66-152">`Data` 資料夾：包含 `WeatherForecast` `WeatherForecastService` 提供範例天氣資料給應用程式元件的類別和執行 `FetchData` 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-152">`Data` folder: Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provides example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="e1a66-153">`Pages` 資料夾：包含組成應用程式的可路由元件/頁面 (`.razor`) ， Blazor 以及 Razor 應用程式的根頁面 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-153">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="e1a66-154">每個頁面的路由都是使用指示詞來指定 [`@page`](xref:mvc/views/razor#page) 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-154">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="e1a66-155">範本包含下列各項：</span><span class="sxs-lookup"><span data-stu-id="e1a66-155">The template includes the following:</span></span>
  * <span data-ttu-id="e1a66-156">`_Host.cshtml`：應用程式的根頁面會實作為 Razor 頁面：</span><span class="sxs-lookup"><span data-stu-id="e1a66-156">`_Host.cshtml`: The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="e1a66-157">最初要求應用程式的任何頁面時，會轉譯此頁面，並在回應中傳回。</span><span class="sxs-lookup"><span data-stu-id="e1a66-157">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="e1a66-158">[主機] 頁面 `App` 會指定呈現根元件 () 的位置 `App.razor` 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-158">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="e1a66-159">`Counter` 元件 (`Counter.razor`) ：實行計數器頁面。</span><span class="sxs-lookup"><span data-stu-id="e1a66-159">`Counter` component (`Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="e1a66-160">`Error` 元件 (`Error.razor`) ：當應用程式中發生未處理的例外狀況時，即會呈現。</span><span class="sxs-lookup"><span data-stu-id="e1a66-160">`Error` component (`Error.razor`): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="e1a66-161">`FetchData` 元件 (`FetchData.razor`) ：實行提取資料頁面。</span><span class="sxs-lookup"><span data-stu-id="e1a66-161">`FetchData` component (`FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="e1a66-162">`Index` 元件 (`Index.razor`) ：實行首頁。</span><span class="sxs-lookup"><span data-stu-id="e1a66-162">`Index` component (`Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="e1a66-163">`Properties/launchSettings.json`：保留 [開發環境](xref:fundamentals/environments#development-and-launchsettingsjson)設定。</span><span class="sxs-lookup"><span data-stu-id="e1a66-163">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="e1a66-164">`Shared` 資料夾：包含下列共用元件和樣式表單：</span><span class="sxs-lookup"><span data-stu-id="e1a66-164">`Shared` folder: Contains the following shared components and stylesheets:</span></span>
  * <span data-ttu-id="e1a66-165">`MainLayout` 元件 (`MainLayout.razor`) ：應用程式的 [版面配置元件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="e1a66-165">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="e1a66-166">`MainLayout.razor.css`：應用程式主要版面配置的樣式表單。</span><span class="sxs-lookup"><span data-stu-id="e1a66-166">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="e1a66-167">`NavMenu` 元件 (`NavMenu.razor`) ：實行提要欄位導覽。</span><span class="sxs-lookup"><span data-stu-id="e1a66-167">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="e1a66-168">包含 () 的[ `NavLink` 元件](xref:blazor/fundamentals/routing#navlink-component) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ，其會呈現其他元件的導覽連結 Razor 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-168">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="e1a66-169">元件會在 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 載入元件時自動表示選取的狀態，這可協助使用者瞭解目前顯示的元件。</span><span class="sxs-lookup"><span data-stu-id="e1a66-169">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="e1a66-170">`NavMenu.razor.css`：應用程式導覽功能表的樣式表單。</span><span class="sxs-lookup"><span data-stu-id="e1a66-170">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>
  * <span data-ttu-id="e1a66-171">`SurveyPrompt` 元件 (`SurveyPrompt.razor`) ： Blazor 問卷元件。</span><span class="sxs-lookup"><span data-stu-id="e1a66-171">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="e1a66-172">`Shared` 資料夾：包含下列共用元件：</span><span class="sxs-lookup"><span data-stu-id="e1a66-172">`Shared` folder: Contains the following shared components:</span></span>
  * <span data-ttu-id="e1a66-173">`MainLayout` 元件 (`MainLayout.razor`) ：應用程式的 [版面配置元件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="e1a66-173">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="e1a66-174">`NavMenu` 元件 (`NavMenu.razor`) ：實行提要欄位導覽。</span><span class="sxs-lookup"><span data-stu-id="e1a66-174">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="e1a66-175">包含 () 的[ `NavLink` 元件](xref:blazor/fundamentals/routing#navlink-component) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ，其會呈現其他元件的導覽連結 Razor 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-175">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="e1a66-176">元件會在 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 載入元件時自動表示選取的狀態，這可協助使用者瞭解目前顯示的元件。</span><span class="sxs-lookup"><span data-stu-id="e1a66-176">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="e1a66-177">`SurveyPrompt` 元件 (`SurveyPrompt.razor`) ： Blazor 問卷元件。</span><span class="sxs-lookup"><span data-stu-id="e1a66-177">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>
  
::: moniker-end

* <span data-ttu-id="e1a66-178">`wwwroot`：應用程式的 [Web 根](xref:fundamentals/index#web-root) 資料夾，其中包含應用程式的公用靜態資產。</span><span class="sxs-lookup"><span data-stu-id="e1a66-178">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="e1a66-179">`_Imports.razor`：包含 Razor 要包含在應用程式元件 () 的一般指示詞 `.razor` ，例如 [`@using`](xref:mvc/views/razor#using) 命名空間的指示詞。</span><span class="sxs-lookup"><span data-stu-id="e1a66-179">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="e1a66-180">`App.razor`：應用程式的根元件，它會使用元件來設定用戶端路由 <xref:Microsoft.AspNetCore.Components.Routing.Router> 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-180">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="e1a66-181"><xref:Microsoft.AspNetCore.Components.Routing.Router>元件會攔截瀏覽器導覽，並轉譯符合所要求位址的頁面。</span><span class="sxs-lookup"><span data-stu-id="e1a66-181">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="e1a66-182">`appsettings.json` 和環境應用程式佈建檔案：提供應用程式的 [設定](xref:blazor/fundamentals/configuration) 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-182">`appsettings.json` and environmental app settings files: Provide [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span>

* <span data-ttu-id="e1a66-183">`Program.cs`：設定 ASP.NET Core [主機](xref:fundamentals/host/generic-host)的應用程式進入點。</span><span class="sxs-lookup"><span data-stu-id="e1a66-183">`Program.cs`: The app's entry point that sets up the ASP.NET Core [host](xref:fundamentals/host/generic-host).</span></span>

* <span data-ttu-id="e1a66-184">`Startup.cs`：包含應用程式的啟動邏輯。</span><span class="sxs-lookup"><span data-stu-id="e1a66-184">`Startup.cs`: Contains the app's startup logic.</span></span> <span data-ttu-id="e1a66-185">`Startup`類別會定義兩種方法：</span><span class="sxs-lookup"><span data-stu-id="e1a66-185">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="e1a66-186">`ConfigureServices`：設定應用程式的相依性 [插入 (DI) ](xref:fundamentals/dependency-injection) 服務。</span><span class="sxs-lookup"><span data-stu-id="e1a66-186">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="e1a66-187">藉由呼叫來新增服務 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> ，並將 `WeatherForecastService` 加入至服務容器，以供範例元件使用 `FetchData` 。</span><span class="sxs-lookup"><span data-stu-id="e1a66-187">Services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="e1a66-188">`Configure`：設定應用程式的要求處理管線：</span><span class="sxs-lookup"><span data-stu-id="e1a66-188">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="e1a66-189"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 呼叫以設定與瀏覽器的即時連線端點。</span><span class="sxs-lookup"><span data-stu-id="e1a66-189"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="e1a66-190">使用建立連線 [SignalR](xref:signalr/introduction) ，這是將即時 web 功能新增至應用程式的架構。</span><span class="sxs-lookup"><span data-stu-id="e1a66-190">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="e1a66-191">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) 呼叫以設定應用程式的根頁面 (`Pages/_Host.cshtml`) 並啟用導覽。</span><span class="sxs-lookup"><span data-stu-id="e1a66-191">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>
