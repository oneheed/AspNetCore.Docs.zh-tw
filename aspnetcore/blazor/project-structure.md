---
title: ASP.NET Core Blazor 專案結構
author: guardrex
description: 瞭解 ASP.NET Core Blazor 應用程式專案結構。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2020
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
ms.openlocfilehash: 6df84366dc3fc99d31a8a0e614ca860a50df7f5c
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513544"
---
# <a name="aspnet-core-no-locblazor-project-structure"></a><span data-ttu-id="b2944-103">ASP.NET Core Blazor 專案結構</span><span class="sxs-lookup"><span data-stu-id="b2944-103">ASP.NET Core Blazor project structure</span></span>

<span data-ttu-id="b2944-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b2944-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b2944-105">下列檔案和資料夾構成 Blazor 從專案範本產生的應用程式 Blazor ：</span><span class="sxs-lookup"><span data-stu-id="b2944-105">The following files and folders make up a Blazor app generated from a Blazor project template:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="b2944-106">`Program.cs`：應用程式的進入點，其設定：</span><span class="sxs-lookup"><span data-stu-id="b2944-106">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="b2944-107">ASP.NET Core [主機](xref:fundamentals/host/generic-host) (Blazor Server) </span><span class="sxs-lookup"><span data-stu-id="b2944-107">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="b2944-108">WebAssembly 主機 (Blazor WebAssembly) ：此檔案中的程式碼對從 () 的範本建立的應用程式而言是唯一的 Blazor WebAssembly `blazorwasm` 。</span><span class="sxs-lookup"><span data-stu-id="b2944-108">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="b2944-109">`App`元件是應用程式的根元件。</span><span class="sxs-lookup"><span data-stu-id="b2944-109">The `App` component is the root component of the app.</span></span> <span data-ttu-id="b2944-110">`App`元件會指定為 `div` DOM 元素，其中 `id` `app` (的 `<div id="app">Loading...</div>` `wwwroot/index.html`)  () 的根元件集合 `builder.RootComponents.Add<App>("#app")` 。</span><span class="sxs-lookup"><span data-stu-id="b2944-110">The `App` component is specified as the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
    * <span data-ttu-id="b2944-111">系統會新增並設定[服務](xref:blazor/fundamentals/dependency-injection) (例如 `builder.Services.AddSingleton<IMyDependency, MyDependency>()`) 。</span><span class="sxs-lookup"><span data-stu-id="b2944-111">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="b2944-112">`Program.cs`：應用程式的進入點，其設定：</span><span class="sxs-lookup"><span data-stu-id="b2944-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="b2944-113">ASP.NET Core [主機](xref:fundamentals/host/generic-host) (Blazor Server) </span><span class="sxs-lookup"><span data-stu-id="b2944-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="b2944-114">WebAssembly 主機 (Blazor WebAssembly) ：此檔案中的程式碼對從 () 的範本建立的應用程式而言是唯一的 Blazor WebAssembly `blazorwasm` 。</span><span class="sxs-lookup"><span data-stu-id="b2944-114">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="b2944-115">`App`元件是應用程式的根元件。</span><span class="sxs-lookup"><span data-stu-id="b2944-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="b2944-116">`App`元件會指定為 `app` DOM 元素， (`<app>Loading...</app>` 在 `wwwroot/index.html`)  () 的根元件集合中 `builder.RootComponents.Add<App>("app")` 。</span><span class="sxs-lookup"><span data-stu-id="b2944-116">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="b2944-117">系統會新增並設定[服務](xref:blazor/fundamentals/dependency-injection) (例如 `builder.Services.AddSingleton<IMyDependency, MyDependency>()`) 。</span><span class="sxs-lookup"><span data-stu-id="b2944-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

* <span data-ttu-id="b2944-118">`Startup.cs` (Blazor Server) ：包含應用程式的啟動邏輯。</span><span class="sxs-lookup"><span data-stu-id="b2944-118">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="b2944-119">`Startup`類別會定義兩種方法：</span><span class="sxs-lookup"><span data-stu-id="b2944-119">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="b2944-120">`ConfigureServices`：設定應用程式的相依性 [插入 (DI) ](xref:fundamentals/dependency-injection) 服務。</span><span class="sxs-lookup"><span data-stu-id="b2944-120">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="b2944-121">在 Blazor Server 應用程式中，會藉由呼叫來新增服務， <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> 並且將 `WeatherForecastService` 加入至服務容器以供範例 `FetchData` 元件使用。</span><span class="sxs-lookup"><span data-stu-id="b2944-121">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="b2944-122">`Configure`：設定應用程式的要求處理管線：</span><span class="sxs-lookup"><span data-stu-id="b2944-122">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="b2944-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 呼叫以設定與瀏覽器的即時連線端點。</span><span class="sxs-lookup"><span data-stu-id="b2944-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="b2944-124">使用建立連線 [SignalR](xref:signalr/introduction) ，這是將即時 web 功能新增至應用程式的架構。</span><span class="sxs-lookup"><span data-stu-id="b2944-124">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="b2944-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) 呼叫以設定應用程式的根頁面 (`Pages/_Host.cshtml`) 並啟用導覽。</span><span class="sxs-lookup"><span data-stu-id="b2944-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="b2944-126">`wwwroot/index.html` (Blazor WebAssembly) ：實作為 HTML 網頁的應用程式根頁面：</span><span class="sxs-lookup"><span data-stu-id="b2944-126">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="b2944-127">最初要求應用程式的任何頁面時，會轉譯此頁面，並在回應中傳回。</span><span class="sxs-lookup"><span data-stu-id="b2944-127">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="b2944-128">頁面會指定呈現根元件的位置 `App` 。</span><span class="sxs-lookup"><span data-stu-id="b2944-128">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="b2944-129">元件是以 `div` `id` () 的 DOM 元素位置轉譯 `app` `<div id="app">Loading...</div>` 。</span><span class="sxs-lookup"><span data-stu-id="b2944-129">The component is rendered at the location of the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>`).</span></span>
  * <span data-ttu-id="b2944-130">`_framework/blazor.webassembly.js`載入 JavaScript 檔案，其：</span><span class="sxs-lookup"><span data-stu-id="b2944-130">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="b2944-131">下載 .NET 執行時間、應用程式和應用程式的相依性。</span><span class="sxs-lookup"><span data-stu-id="b2944-131">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="b2944-132">初始化執行時間以執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b2944-132">Initializes the runtime to run the app.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="b2944-133">`wwwroot/index.html` (Blazor WebAssembly) ：實作為 HTML 網頁的應用程式根頁面：</span><span class="sxs-lookup"><span data-stu-id="b2944-133">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="b2944-134">最初要求應用程式的任何頁面時，會轉譯此頁面，並在回應中傳回。</span><span class="sxs-lookup"><span data-stu-id="b2944-134">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="b2944-135">頁面會指定呈現根元件的位置 `App` 。</span><span class="sxs-lookup"><span data-stu-id="b2944-135">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="b2944-136">元件會轉譯在 `app` DOM 元素 () 的位置 `<app>Loading...</app>` 。</span><span class="sxs-lookup"><span data-stu-id="b2944-136">The component is rendered at the location of the `app` DOM element (`<app>Loading...</app>`).</span></span>
  * <span data-ttu-id="b2944-137">`_framework/blazor.webassembly.js`載入 JavaScript 檔案，其：</span><span class="sxs-lookup"><span data-stu-id="b2944-137">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="b2944-138">下載 .NET 執行時間、應用程式和應用程式的相依性。</span><span class="sxs-lookup"><span data-stu-id="b2944-138">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="b2944-139">初始化執行時間以執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b2944-139">Initializes the runtime to run the app.</span></span>

::: moniker-end

* <span data-ttu-id="b2944-140">`App.razor`：應用程式的根元件，它會使用元件來設定用戶端路由 <xref:Microsoft.AspNetCore.Components.Routing.Router> 。</span><span class="sxs-lookup"><span data-stu-id="b2944-140">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="b2944-141"><xref:Microsoft.AspNetCore.Components.Routing.Router>元件會攔截瀏覽器導覽，並轉譯符合所要求位址的頁面。</span><span class="sxs-lookup"><span data-stu-id="b2944-141">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="b2944-142">`Pages` 資料夾：包含組成應用程式的可路由元件/頁面 (`.razor`) ， Blazor 以及 Razor 應用程式的根頁面 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="b2944-142">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="b2944-143">每個頁面的路由都是使用指示詞來指定 [`@page`](xref:mvc/views/razor#page) 。</span><span class="sxs-lookup"><span data-stu-id="b2944-143">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="b2944-144">範本包含下列各項：</span><span class="sxs-lookup"><span data-stu-id="b2944-144">The template includes the following:</span></span>
  * <span data-ttu-id="b2944-145">`_Host.cshtml` (Blazor Server) ：作為頁面執行之應用程式的根頁面 Razor ：</span><span class="sxs-lookup"><span data-stu-id="b2944-145">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="b2944-146">最初要求應用程式的任何頁面時，會轉譯此頁面，並在回應中傳回。</span><span class="sxs-lookup"><span data-stu-id="b2944-146">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="b2944-147">`_framework/blazor.server.js`JavaScript 檔案會載入，這會設定 SignalR 瀏覽器與伺服器之間的即時連接。</span><span class="sxs-lookup"><span data-stu-id="b2944-147">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="b2944-148">[主機] 頁面 `App` 會指定呈現根元件 () 的位置 `App.razor` 。</span><span class="sxs-lookup"><span data-stu-id="b2944-148">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="b2944-149">`Counter` 元件 (`Pages/Counter.razor`) ：實行計數器頁面。</span><span class="sxs-lookup"><span data-stu-id="b2944-149">`Counter` component (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="b2944-150">`Error` 元件 (`Error.razor` ， Blazor Server 僅限應用程式) ：當應用程式中發生未處理的例外狀況時轉譯。</span><span class="sxs-lookup"><span data-stu-id="b2944-150">`Error` component (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="b2944-151">`FetchData` 元件 (`Pages/FetchData.razor`) ：實行提取資料頁面。</span><span class="sxs-lookup"><span data-stu-id="b2944-151">`FetchData` component (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="b2944-152">`Index` 元件 (`Pages/Index.razor`) ：實行首頁。</span><span class="sxs-lookup"><span data-stu-id="b2944-152">`Index` component (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="b2944-153">`Properties/launchSettings.json`：保留 [開發環境](xref:fundamentals/environments#development-and-launchsettingsjson)設定。</span><span class="sxs-lookup"><span data-stu-id="b2944-153">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="b2944-154">`Shared` 資料夾：包含 `.razor` 應用程式所使用 () 的其他 UI 元件：</span><span class="sxs-lookup"><span data-stu-id="b2944-154">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="b2944-155">`MainLayout` 元件 (`MainLayout.razor`) ：應用程式的 [版面配置元件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="b2944-155">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="b2944-156">`MainLayout.razor.css`：應用程式主要版面配置的樣式表單。</span><span class="sxs-lookup"><span data-stu-id="b2944-156">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="b2944-157">`NavMenu` 元件 (`NavMenu.razor`) ：實行提要欄位導覽。</span><span class="sxs-lookup"><span data-stu-id="b2944-157">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="b2944-158">包含 () 的[ `NavLink` 元件](xref:blazor/fundamentals/routing#navlink-component) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ，其會呈現其他元件的導覽連結 Razor 。</span><span class="sxs-lookup"><span data-stu-id="b2944-158">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="b2944-159">元件會在 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 載入元件時自動表示選取的狀態，這可協助使用者瞭解目前顯示的元件。</span><span class="sxs-lookup"><span data-stu-id="b2944-159">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="b2944-160">`NavMenu.razor.css`：應用程式導覽功能表的樣式表單。</span><span class="sxs-lookup"><span data-stu-id="b2944-160">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="b2944-161">`Shared` 資料夾：包含 `.razor` 應用程式所使用 () 的其他 UI 元件：</span><span class="sxs-lookup"><span data-stu-id="b2944-161">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="b2944-162">`MainLayout` 元件 (`MainLayout.razor`) ：應用程式的 [版面配置元件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="b2944-162">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="b2944-163">`NavMenu` 元件 (`NavMenu.razor`) ：實行提要欄位導覽。</span><span class="sxs-lookup"><span data-stu-id="b2944-163">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="b2944-164">包含 () 的[ `NavLink` 元件](xref:blazor/fundamentals/routing#navlink-component) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ，其會呈現其他元件的導覽連結 Razor 。</span><span class="sxs-lookup"><span data-stu-id="b2944-164">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="b2944-165">元件會在 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 載入元件時自動表示選取的狀態，這可協助使用者瞭解目前顯示的元件。</span><span class="sxs-lookup"><span data-stu-id="b2944-165">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  
::: moniker-end

* <span data-ttu-id="b2944-166">`_Imports.razor`：包含 Razor 要包含在應用程式元件 () 的一般指示詞 `.razor` ，例如 [`@using`](xref:mvc/views/razor#using) 命名空間的指示詞。</span><span class="sxs-lookup"><span data-stu-id="b2944-166">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="b2944-167">`Data` 資料夾 (Blazor Server) ：包含 `WeatherForecast` `WeatherForecastService` 提供範例天氣資料給應用程式元件的類別和執行 `FetchData` 。</span><span class="sxs-lookup"><span data-stu-id="b2944-167">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="b2944-168">`wwwroot`：應用程式的 [Web 根](xref:fundamentals/index#web-root) 資料夾，其中包含應用程式的公用靜態資產。</span><span class="sxs-lookup"><span data-stu-id="b2944-168">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="b2944-169">`appsettings.json`：保留應用程式的 [設定](xref:blazor/fundamentals/configuration) 。</span><span class="sxs-lookup"><span data-stu-id="b2944-169">`appsettings.json`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="b2944-170">在 Blazor WebAssembly 應用程式中，應用程式佈建檔案位於 `wwwroot` 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="b2944-170">In a Blazor WebAssembly app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="b2944-171">在 Blazor Server 應用程式中，應用程式佈建檔案位於專案根目錄。</span><span class="sxs-lookup"><span data-stu-id="b2944-171">In a Blazor Server app, the app settings file is located at the project root.</span></span>
