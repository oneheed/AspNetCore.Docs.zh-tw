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
ms.openlocfilehash: 958fa23a1befac3696d850d5409d4021dd109c22
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751538"
---
# <a name="aspnet-core-no-locblazor-project-structure"></a>ASP.NET Core Blazor 專案結構

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

本文說明組成專案範本所產生之應用程式的檔案和資料夾 Blazor Blazor 。

## Blazor WebAssembly

Blazor WebAssembly範本 (`blazorwasm`) 會建立應用程式的初始檔案和目錄結構 Blazor WebAssembly 。 應用程式會填入元件的示範程式碼 `FetchData` ，該元件會從靜態資產載入資料、以及 `weather.json` 使用者與元件的互動 `Counter` 。

* `Pages` 資料夾：包含 `.razor` 組成應用程式 () 的可路由元件/頁面 Blazor 。 每個頁面的路由都是使用指示詞來指定 [`@page`](xref:mvc/views/razor#page) 。 範本包含下列元件：
  * `Counter` 元件 (`Counter.razor`) ：實行計數器頁面。
  * `FetchData` 元件 (`FetchData.razor`) ：實行提取資料頁面。
  * `Index` 元件 (`Index.razor`) ：實行首頁。
  
* `Properties/launchSettings.json`：保留 [開發環境](xref:fundamentals/environments#development-and-launchsettingsjson)設定。

::: moniker range=">= aspnetcore-5.0"

* `Shared` 資料夾：包含下列共用元件和樣式表單：
  * `MainLayout` 元件 (`MainLayout.razor`) ：應用程式的 [版面配置元件](xref:blazor/layouts)。
  * `MainLayout.razor.css`：應用程式主要版面配置的樣式表單。
  * `NavMenu` 元件 (`NavMenu.razor`) ：實行提要欄位導覽。 包含 () 的[ `NavLink` 元件](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ，其會呈現其他元件的導覽連結 Razor 。 元件會在 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 載入元件時自動表示選取的狀態，這可協助使用者瞭解目前顯示的元件。
  * `NavMenu.razor.css`：應用程式導覽功能表的樣式表單。
  * `SurveyPrompt` 元件 (`SurveyPrompt.razor`) ： Blazor 問卷元件。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared` 資料夾：包含下列共用元件：
  * `MainLayout` 元件 (`MainLayout.razor`) ：應用程式的 [版面配置元件](xref:blazor/layouts)。
  * `NavMenu` 元件 (`NavMenu.razor`) ：實行提要欄位導覽。 包含 () 的[ `NavLink` 元件](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ，其會呈現其他元件的導覽連結 Razor 。 元件會在 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 載入元件時自動表示選取的狀態，這可協助使用者瞭解目前顯示的元件。
  * `SurveyPrompt` 元件 (`SurveyPrompt.razor`) ： Blazor 問卷元件。
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `wwwroot`：應用程式的 [Web 根](xref:fundamentals/index#web-root) 資料夾，其中包含應用程式的公用靜態資產，包括 `appsettings.json` 設定的環境應用程式 [配置](xref:blazor/fundamentals/configuration)檔案。 `index.html`網頁是實作為 HTML 網頁的應用程式的根頁面：
  * 最初要求應用程式的任何頁面時，會轉譯此頁面，並在回應中傳回。
  * 頁面會指定呈現根元件的位置 `App` 。 元件是以 `div` `id` () 的 DOM 元素位置轉譯 `app` `<div id="app">Loading...</div>` 。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `wwwroot`：應用程式的 [Web 根](xref:fundamentals/index#web-root) 資料夾，其中包含應用程式的公用靜態資產，包括 `appsettings.json` 設定的環境應用程式 [配置](xref:blazor/fundamentals/configuration)檔案。 `index.html`網頁是實作為 HTML 網頁的應用程式的根頁面：
  * 最初要求應用程式的任何頁面時，會轉譯此頁面，並在回應中傳回。
  * 頁面會指定呈現根元件的位置 `App` 。 元件會轉譯在 `app` DOM 元素 () 的位置 `<app>Loading...</app>` 。

::: moniker-end

> [!NOTE]
> 新增至檔案的 JavaScript (JS) 檔案 `wwwroot/index.html` 應該會出現在結尾 `</body>` 標記之前。 在某些情況下，從 JS 檔案載入自訂 JS 程式碼的順序很重要。 例如，請確定具有 interop 方法的 JS 檔案包含在 Blazor FRAMEWORK JS 檔案之前。

* `_Imports.razor`：包含 Razor 要包含在應用程式元件 () 的一般指示詞 `.razor` ，例如 [`@using`](xref:mvc/views/razor#using) 命名空間的指示詞。

* `App.razor`：應用程式的根元件，它會使用元件來設定用戶端路由 <xref:Microsoft.AspNetCore.Components.Routing.Router> 。 <xref:Microsoft.AspNetCore.Components.Routing.Router>元件會攔截瀏覽器導覽，並轉譯符合所要求位址的頁面。

::: moniker range=">= aspnetcore-5.0"

* `Program.cs`：應用程式的進入點，可設定 WebAssembly 主機：
  
  * `App`元件是應用程式的根元件。 `App`元件會指定為 `div` DOM 元素，其中 `id` `app` (的 `<div id="app">Loading...</div>` `wwwroot/index.html`)  () 的根元件集合 `builder.RootComponents.Add<App>("#app")` 。
  * 系統會新增並設定[服務](xref:blazor/fundamentals/dependency-injection) (例如 `builder.Services.AddSingleton<IMyDependency, MyDependency>()`) 。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Program.cs`：應用程式的進入點，可設定 WebAssembly 主機：

  * `App`元件是應用程式的根元件。 `App`元件會指定為 `app` DOM 元素， (`<app>Loading...</app>` 在 `wwwroot/index.html`)  () 的根元件集合中 `builder.RootComponents.Add<App>("app")` 。
  * 系統會新增並設定[服務](xref:blazor/fundamentals/dependency-injection) (例如 `builder.Services.AddSingleton<IMyDependency, MyDependency>()`) 。

::: moniker-end

## Blazor Server

Blazor Server範本 (`blazorserver`) 會建立應用程式的初始檔案和目錄結構 Blazor Server 。 應用程式會填入元件的示範程式碼， `FetchData` 該元件會從已註冊的服務載入資料，以及 `WeatherForecastService` 使用者與 `Counter` 元件的互動。

* `Data` 資料夾：包含 `WeatherForecast` `WeatherForecastService` 提供範例天氣資料給應用程式元件的類別和執行 `FetchData` 。

* `Pages` 資料夾：包含組成應用程式的可路由元件/頁面 (`.razor`) ， Blazor 以及 Razor 應用程式的根頁面 Blazor Server 。 每個頁面的路由都是使用指示詞來指定 [`@page`](xref:mvc/views/razor#page) 。 範本包含下列各項：
  * `_Host.cshtml`：應用程式的根頁面會實作為 Razor 頁面：
    * 最初要求應用程式的任何頁面時，會轉譯此頁面，並在回應中傳回。
    * [主機] 頁面 `App` 會指定呈現根元件 () 的位置 `App.razor` 。
  * `Counter` 元件 (`Counter.razor`) ：實行計數器頁面。
  * `Error` 元件 (`Error.razor`) ：當應用程式中發生未處理的例外狀況時，即會呈現。
  * `FetchData` 元件 (`FetchData.razor`) ：實行提取資料頁面。
  * `Index` 元件 (`Index.razor`) ：實行首頁。

> [!NOTE]
> 新增至檔案的 JavaScript (JS) 檔案 `Pages/_Host.cshtml` 應該會出現在結尾 `</body>` 標記之前。 在某些情況下，從 JS 檔案載入自訂 JS 程式碼的順序很重要。 例如，請確定具有 interop 方法的 JS 檔案包含在 Blazor FRAMEWORK JS 檔案之前。

* `Properties/launchSettings.json`：保留 [開發環境](xref:fundamentals/environments#development-and-launchsettingsjson)設定。

::: moniker range=">= aspnetcore-5.0"

* `Shared` 資料夾：包含下列共用元件和樣式表單：
  * `MainLayout` 元件 (`MainLayout.razor`) ：應用程式的 [版面配置元件](xref:blazor/layouts)。
  * `MainLayout.razor.css`：應用程式主要版面配置的樣式表單。
  * `NavMenu` 元件 (`NavMenu.razor`) ：實行提要欄位導覽。 包含 () 的[ `NavLink` 元件](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ，其會呈現其他元件的導覽連結 Razor 。 元件會在 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 載入元件時自動表示選取的狀態，這可協助使用者瞭解目前顯示的元件。
  * `NavMenu.razor.css`：應用程式導覽功能表的樣式表單。
  * `SurveyPrompt` 元件 (`SurveyPrompt.razor`) ： Blazor 問卷元件。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared` 資料夾：包含下列共用元件：
  * `MainLayout` 元件 (`MainLayout.razor`) ：應用程式的 [版面配置元件](xref:blazor/layouts)。
  * `NavMenu` 元件 (`NavMenu.razor`) ：實行提要欄位導覽。 包含 () 的[ `NavLink` 元件](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ，其會呈現其他元件的導覽連結 Razor 。 元件會在 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 載入元件時自動表示選取的狀態，這可協助使用者瞭解目前顯示的元件。
  * `SurveyPrompt` 元件 (`SurveyPrompt.razor`) ： Blazor 問卷元件。
  
::: moniker-end

* `wwwroot`：應用程式的 [Web 根](xref:fundamentals/index#web-root) 資料夾，其中包含應用程式的公用靜態資產。

* `_Imports.razor`：包含 Razor 要包含在應用程式元件 () 的一般指示詞 `.razor` ，例如 [`@using`](xref:mvc/views/razor#using) 命名空間的指示詞。

* `App.razor`：應用程式的根元件，它會使用元件來設定用戶端路由 <xref:Microsoft.AspNetCore.Components.Routing.Router> 。 <xref:Microsoft.AspNetCore.Components.Routing.Router>元件會攔截瀏覽器導覽，並轉譯符合所要求位址的頁面。

* `appsettings.json` 和環境應用程式佈建檔案：提供應用程式的 [設定](xref:blazor/fundamentals/configuration) 。

* `Program.cs`：設定 ASP.NET Core [主機](xref:fundamentals/host/generic-host)的應用程式進入點。

* `Startup.cs`：包含應用程式的啟動邏輯。 `Startup`類別會定義兩種方法：

  * `ConfigureServices`：設定應用程式的相依性 [插入 (DI) ](xref:fundamentals/dependency-injection) 服務。 藉由呼叫來新增服務 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> ，並將 `WeatherForecastService` 加入至服務容器，以供範例元件使用 `FetchData` 。
  * `Configure`：設定應用程式的要求處理管線：
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 呼叫以設定與瀏覽器的即時連線端點。 使用建立連線 [SignalR](xref:signalr/introduction) ，這是將即時 web 功能新增至應用程式的架構。
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) 呼叫以設定應用程式的根頁面 (`Pages/_Host.cshtml`) 並啟用導覽。
