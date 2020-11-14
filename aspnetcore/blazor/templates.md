---
title: 'ASP.NET Core Blazor 範本'
author: guardrex
description: '瞭解 ASP.NET Core Blazor 應用程式範本和 Blazor 專案結構。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/04/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/templates
ms.openlocfilehash: fc2e81cf130732d515fb871227031493e297cf9f
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507768"
---
# <a name="aspnet-core-no-locblazor-templates"></a><span data-ttu-id="a5d9c-103">ASP.NET Core Blazor 範本</span><span class="sxs-lookup"><span data-stu-id="a5d9c-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="a5d9c-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a5d9c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a5d9c-105">Blazor架構會提供範本，以針對每個裝載模型開發應用程式 Blazor ：</span><span class="sxs-lookup"><span data-stu-id="a5d9c-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* <span data-ttu-id="a5d9c-106">Blazor WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="a5d9c-106">Blazor WebAssembly (`blazorwasm`)</span></span>
* <span data-ttu-id="a5d9c-107">Blazor Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="a5d9c-107">Blazor Server (`blazorserver`)</span></span>

<span data-ttu-id="a5d9c-108">如需裝載模型的詳細資訊 Blazor ，請參閱 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="a5d9c-109">您可以藉由將選項傳遞 `--help` 至 CLI 命令，來取得範本選項 [`dotnet new`](/dotnet/core/tools/dotnet-new) ：</span><span class="sxs-lookup"><span data-stu-id="a5d9c-109">Template options are available by passing the `--help` option to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a><span data-ttu-id="a5d9c-110">Blazor 專案結構</span><span class="sxs-lookup"><span data-stu-id="a5d9c-110">Blazor project structure</span></span>

<span data-ttu-id="a5d9c-111">下列檔案和資料夾構成 Blazor 從專案範本產生的應用程式 Blazor ：</span><span class="sxs-lookup"><span data-stu-id="a5d9c-111">The following files and folders make up a Blazor app generated from a Blazor project template:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="a5d9c-112">`Program.cs`：應用程式的進入點，其設定：</span><span class="sxs-lookup"><span data-stu-id="a5d9c-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="a5d9c-113">ASP.NET Core [主機](xref:fundamentals/host/generic-host) (Blazor Server) </span><span class="sxs-lookup"><span data-stu-id="a5d9c-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="a5d9c-114">WebAssembly 主機 (Blazor WebAssembly) ：此檔案中的程式碼對從 () 的範本建立的應用程式而言是唯一的 Blazor WebAssembly `blazorwasm` 。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-114">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="a5d9c-115">`App`元件是應用程式的根元件。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="a5d9c-116">`App`元件會指定為 `app` DOM 元素， (`<div id="app">Loading...</div>` 在 `wwwroot/index.html`)  () 的根元件集合中 `builder.RootComponents.Add<App>("#app")` 。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-116">The `App` component is specified as the `app` DOM element (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
    * <span data-ttu-id="a5d9c-117">系統會新增並設定[服務](xref:blazor/fundamentals/dependency-injection) (例如 `builder.Services.AddSingleton<IMyDependency, MyDependency>()`) 。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="a5d9c-118">`Program.cs`：應用程式的進入點，其設定：</span><span class="sxs-lookup"><span data-stu-id="a5d9c-118">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="a5d9c-119">ASP.NET Core [主機](xref:fundamentals/host/generic-host) (Blazor Server) </span><span class="sxs-lookup"><span data-stu-id="a5d9c-119">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="a5d9c-120">WebAssembly 主機 (Blazor WebAssembly) ：此檔案中的程式碼對從 () 的範本建立的應用程式而言是唯一的 Blazor WebAssembly `blazorwasm` 。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-120">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="a5d9c-121">`App`元件是應用程式的根元件。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-121">The `App` component is the root component of the app.</span></span> <span data-ttu-id="a5d9c-122">`App`元件會指定為 `app` DOM 元素， (`<app>Loading...</app>` 在 `wwwroot/index.html`)  () 的根元件集合中 `builder.RootComponents.Add<App>("app")` 。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-122">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="a5d9c-123">系統會新增並設定[服務](xref:blazor/fundamentals/dependency-injection) (例如 `builder.Services.AddSingleton<IMyDependency, MyDependency>()`) 。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-123">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

* <span data-ttu-id="a5d9c-124">`Startup.cs` (Blazor Server) ：包含應用程式的啟動邏輯。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-124">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="a5d9c-125">`Startup`類別會定義兩種方法：</span><span class="sxs-lookup"><span data-stu-id="a5d9c-125">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="a5d9c-126">`ConfigureServices`：設定應用程式的相依性 [插入 (DI) ](xref:fundamentals/dependency-injection) 服務。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-126">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="a5d9c-127">在 Blazor Server 應用程式中，會藉由呼叫來新增服務， <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> 並且將 `WeatherForecastService` 加入至服務容器以供範例 `FetchData` 元件使用。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-127">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="a5d9c-128">`Configure`：設定應用程式的要求處理管線：</span><span class="sxs-lookup"><span data-stu-id="a5d9c-128">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="a5d9c-129"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 呼叫以設定與瀏覽器的即時連線端點。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-129"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="a5d9c-130">使用建立連線 [SignalR](xref:signalr/introduction) ，這是將即時 web 功能新增至應用程式的架構。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-130">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="a5d9c-131">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) 呼叫以設定應用程式的根頁面 (`Pages/_Host.cshtml`) 並啟用導覽。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-131">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

* <span data-ttu-id="a5d9c-132">`wwwroot/index.html` (Blazor WebAssembly) ：實作為 HTML 網頁的應用程式根頁面：</span><span class="sxs-lookup"><span data-stu-id="a5d9c-132">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="a5d9c-133">最初要求應用程式的任何頁面時，會轉譯此頁面，並在回應中傳回。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-133">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="a5d9c-134">頁面會指定呈現根元件的位置 `App` 。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-134">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="a5d9c-135">元件會轉譯在 `app` DOM 元素 () 的位置 `<app>...</app>` 。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-135">The component is rendered at the location of the `app` DOM element (`<app>...</app>`).</span></span>
  * <span data-ttu-id="a5d9c-136">`_framework/blazor.webassembly.js`載入 JavaScript 檔案，其：</span><span class="sxs-lookup"><span data-stu-id="a5d9c-136">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="a5d9c-137">下載 .NET 執行時間、應用程式和應用程式的相依性。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-137">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="a5d9c-138">初始化執行時間以執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-138">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="a5d9c-139">`App.razor`：應用程式的根元件，它會使用元件來設定用戶端路由 <xref:Microsoft.AspNetCore.Components.Routing.Router> 。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-139">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="a5d9c-140"><xref:Microsoft.AspNetCore.Components.Routing.Router>元件會攔截瀏覽器導覽，並轉譯符合所要求位址的頁面。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-140">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="a5d9c-141">`Pages` 資料夾：包含組成應用程式的可路由元件/頁面 (`.razor`) ， Blazor 以及 Razor 應用程式的根頁面 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-141">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="a5d9c-142">每個頁面的路由都是使用指示詞來指定 [`@page`](xref:mvc/views/razor#page) 。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-142">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="a5d9c-143">範本包含下列各項：</span><span class="sxs-lookup"><span data-stu-id="a5d9c-143">The template includes the following:</span></span>
  * <span data-ttu-id="a5d9c-144">`_Host.cshtml` (Blazor Server) ：作為頁面執行之應用程式的根頁面 Razor ：</span><span class="sxs-lookup"><span data-stu-id="a5d9c-144">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="a5d9c-145">最初要求應用程式的任何頁面時，會轉譯此頁面，並在回應中傳回。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-145">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="a5d9c-146">`_framework/blazor.server.js`JavaScript 檔案會載入，這會設定 SignalR 瀏覽器與伺服器之間的即時連接。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-146">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="a5d9c-147">[主機] 頁面 `App` 會指定呈現根元件 () 的位置 `App.razor` 。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-147">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="a5d9c-148">`Counter` (`Pages/Counter.razor`) ：實行計數器頁面。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-148">`Counter` (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="a5d9c-149">`Error` (`Error.razor` ， Blazor Server 僅限應用程式) ：當應用程式中發生未處理的例外狀況時轉譯。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-149">`Error` (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="a5d9c-150">`FetchData` (`Pages/FetchData.razor`) ：實行提取資料頁面。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-150">`FetchData` (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="a5d9c-151">`Index` (`Pages/Index.razor`) ：實行首頁。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-151">`Index` (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="a5d9c-152">`Properties/launchSettings.json`：保留 [開發環境](xref:fundamentals/environments#development-and-launchsettingsjson)設定。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-152">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

* <span data-ttu-id="a5d9c-153">`Shared` 資料夾：包含 `.razor` 應用程式所使用 () 的其他 UI 元件：</span><span class="sxs-lookup"><span data-stu-id="a5d9c-153">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="a5d9c-154">`MainLayout` (`MainLayout.razor`) ：應用程式的 [版面配置元件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-154">`MainLayout` (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="a5d9c-155">`NavMenu` (`NavMenu.razor`) ：實行提要欄位導覽。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-155">`NavMenu` (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="a5d9c-156">包含 () 的[ `NavLink` 元件](xref:blazor/fundamentals/routing#navlink-component) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ，其會呈現其他元件的導覽連結 Razor 。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-156">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="a5d9c-157">元件會在 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 載入元件時自動表示選取的狀態，這可協助使用者瞭解目前顯示的元件。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-157">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="a5d9c-158">`_Imports.razor`：包含 Razor 要包含在應用程式元件 () 的一般指示詞 `.razor` ，例如 [`@using`](xref:mvc/views/razor#using) 命名空間的指示詞。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-158">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="a5d9c-159">`Data` 資料夾 (Blazor Server) ：包含 `WeatherForecast` `WeatherForecastService` 提供範例天氣資料給應用程式元件的類別和執行 `FetchData` 。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-159">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="a5d9c-160">`wwwroot`：應用程式的 [Web 根](xref:fundamentals/index#web-root) 資料夾，其中包含應用程式的公用靜態資產。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-160">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="a5d9c-161">`appsettings.json`：保留應用程式的 [設定](xref:blazor/fundamentals/configuration) 。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-161">`appsettings.json`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="a5d9c-162">在 Blazor WebAssembly 應用程式中，應用程式佈建檔案位於 `wwwroot` 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-162">In a Blazor WebAssembly app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="a5d9c-163">在 Blazor Server 應用程式中，應用程式佈建檔案位於專案根目錄。</span><span class="sxs-lookup"><span data-stu-id="a5d9c-163">In a Blazor Server app, the app settings file is located at the project root.</span></span>
