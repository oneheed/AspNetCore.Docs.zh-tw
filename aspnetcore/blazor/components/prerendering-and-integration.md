---
title: ASP.NET Core 元件的已呈現和整合 Razor
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
ms.openlocfilehash: affca6c9b585b91787f94a13144d07bedfefdd37
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431700"
---
# <a name="prerender-and-integrate-aspnet-core-no-locrazor-components"></a>ASP.NET Core 元件的已呈現和整合 Razor

依 [Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

Razor 元件可以整合到 Razor 託管解決方案中的頁面和 MVC 應用程式 Blazor WebAssembly 。 轉譯頁面或視圖時，可同時資源清單元件。

## <a name="configuration"></a>設定

若要設定應用程式的預先安裝 Blazor WebAssembly ：

1. Blazor WebAssembly在 ASP.NET Core 應用程式中裝載應用程式。 獨立 Blazor WebAssembly 應用程式可以新增至 ASP.NET Core 方案，或者您可以使用 Blazor WebAssembly 從裝載的專案範本建立的託管應用程式 Blazor 。

1. `wwwroot/index.html`從用戶端專案中移除預設靜態檔案 Blazor WebAssembly 。

1. 刪除用戶端專案中的下列程式 `Program.Main` 程式碼：

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. 將檔案加入 `Pages/_Host.cshtml` 至伺服器專案。 您可以 `_Host.cshtml` Blazor Server 使用命令 shell 中的命令，從範本建立的應用程式取得檔案 `dotnet new blazorserver -o BlazorServer` 。 將檔案放 `Pages/_Host.cshtml` 入託管解決方案的伺服器應用程式之後 Blazor WebAssembly ，對檔案進行下列變更：

   * 將命名空間設定為伺服器應用程式的 `Pages` 資料夾 (例如 `@namespace BlazorHosted.Server.Pages`) 。
   * 設定 [`@using`](xref:mvc/views/razor#using) 用戶端專案的指示詞 (例如 `@using BlazorHosted.Client`) 。
   * 更新樣式表單連結以指向 WebAssembly 應用程式的樣式表單。 在下列範例中，用戶端應用程式的命名空間為 `BlazorHosted.Client` ：

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * 更新 `render-mode` [元件標記](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) 協助程式的，以呈現根 `App` 元件：

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * 更新 Blazor 腳本來源以使用用戶端 Blazor WebAssembly 腳本：

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. 在 `Startup.Configure` `Startup.cs` 伺服器專案的 () 中：

   * 在 `UseDeveloperExceptionPage` 開發環境中呼叫應用程式建立器。
   * `UseBlazorFrameworkFiles`在 app builder 上呼叫。
   * 將 `index.html` 頁面 () 的回復變更 `endpoints.MapFallbackToFile("index.html");` 為 `_Host.cshtml` 頁面。

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

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a>使用元件標記協助程式在頁面或視圖中轉譯元件

元件標籤協助程式支援兩種轉譯模式，可從 Blazor WebAssembly 頁面或視圖中的應用程式呈現元件：

* `WebAssembly`：轉譯應用程式的標記，以在 Blazor WebAssembly 瀏覽器中載入時用來包含互動式元件。 元件不是資源清單。 此選項可讓您更輕鬆地 Blazor WebAssembly 在不同的頁面上呈現不同的元件。
* `WebAssemblyPrerendered`：將元件 Prerenders 為靜態 HTML，並包含應用程式的標記，以 Blazor WebAssembly 供稍後用來在瀏覽器中載入元件時進行互動。

在下列 Razor 頁面範例中， `Counter` 元件會在頁面中呈現。 若要使元件互動， Blazor WebAssembly 腳本會包含在頁面的 [轉譯] [區段](xref:mvc/views/layout#sections)中。 若要避免使用元件標記協助程式 () 的完整命名空間 `Counter` `{APP ASSEMBLY}.Pages.Counter` ，請為 [`@using`](xref:mvc/views/razor#using) 用戶端應用程式的 `Pages` 命名空間新增指示詞。 在下列範例中，用戶端應用程式的命名空間為 `BlazorHosted.Client` ：

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 設定元件是否為：

* 會資源清單到頁面中。
* 會在頁面上轉譯為靜態 HTML，或包含從使用者代理程式啟動應用程式所需的資訊 Blazor 。

如需元件標記協助程式的詳細資訊，包括傳遞參數和設定 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> ，請參閱 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> 。

上述範例需要伺服器應用程式的版面配置 (`_Layout.cshtml`) 包含結束標記內腳本的轉譯 [區段](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) `</body>` ：

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

檔案 `_Layout.cshtml` 位於 MVC 應用程式中 `Pages/Shared` Razor 頁面應用程式或資料夾的資料夾中 `Views/Shared` 。

如果應用程式也應該將應用程式中的樣式作為元件樣式 Blazor WebAssembly ，請在檔案中包含應用程式的樣式 `_Layout.cshtml` 。 在下列範例中，用戶端應用程式的命名空間為 `BlazorHosted.Client` ：

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a>使用 CSS 選取器呈現頁面或視圖中的元件

在 () 中，將根元件新增至 *用戶端* 專案 `Program.Main` `Program.cs` 。 在下列範例中， `Counter` 元件會宣告為根元件，其中包含的 CSS 選取器會使用 `id` 符合的來選取專案 `my-counter` 。 在下列範例中，用戶端應用程式的命名空間為 `BlazorHosted.Client` ：

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

在下列 Razor 頁面範例中， `Counter` 元件會在頁面中呈現。 若要使元件互動， Blazor WebAssembly 腳本會包含在頁面的轉譯 [區段](xref:mvc/views/layout#sections)中：

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

上述範例需要伺服器應用程式的版面配置 (`_Layout.cshtml`) 包含結束標記內腳本的轉譯 [區段](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) `</body>` ：

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

檔案 `_Layout.cshtml` 位於 MVC 應用程式中 `Pages/Shared` Razor 頁面應用程式或資料夾的資料夾中 `Views/Shared` 。

如果應用程式也應該將應用程式中的樣式作為元件樣式 Blazor WebAssembly ，請在檔案中包含應用程式的樣式 `_Layout.cshtml` 。 在下列範例中，用戶端應用程式的命名空間為 `BlazorHosted.Client` ：

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

在 Razor Razor Blazor WebAssembly .net 5 或更新版本的 ASP.NET Core 中，支援將元件整合至裝載方案中的頁面和 MVC 應用程式。 請選取此文章的 .NET 5 或更新版本。

::: moniker-end

::: zone-end

::: zone pivot="server"

Razor 元件可以整合至 Razor 應用程式中的頁面和 MVC 應用程式 Blazor Server 。 轉譯頁面或視圖時，可同時資源清單元件。

設定 [應用程式](#configuration)之後，請根據應用程式的需求，使用下列各節中的指導方針：

* 可路由的元件：適用于直接從使用者要求路由傳送的元件。 當訪客應能在其瀏覽器中使用指示詞為元件發出 HTTP 要求時，請遵循此指導 [`@page`](xref:mvc/views/razor#page) 方針。
  * [在頁面應用程式中使用可路由的元件 Razor](#use-routable-components-in-a-razor-pages-app)
  * [在 MVC 應用程式中使用可路由的元件](#use-routable-components-in-an-mvc-app)
* [從頁面或視圖呈現元件](#render-components-from-a-page-or-view)：適用于無法直接從使用者要求路由傳送的元件。 當應用程式使用元件標籤協助程式將元件內嵌至現有的頁面和流覽 [器](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)時，請遵循此指引。

## <a name="configuration"></a>設定

現有的 Razor 頁面或 MVC 應用程式可以將 Razor 元件整合至頁面和流覽中：

1. 在應用程式的版面配置檔案中 (`_Layout.cshtml`) ：

   * 將下列 `<base>` 標記新增至 `<head>` 元素：

     ```html
     <base href="~/" />
     ```

     `href`上述範例中的 *應用程式基底路徑* )  (值假設應用程式位於根 URL 路徑 (`/`) 。 如果應用程式是子應用程式，請遵循本文的「 *應用程式基底路徑* 」一節中的指導方針 <xref:blazor/host-and-deploy/index#app-base-path> 。

     檔案 `_Layout.cshtml` 位於 MVC 應用程式中 `Pages/Shared` Razor 頁面應用程式或資料夾的資料夾中 `Views/Shared` 。

   * 在轉譯 `<script>` `blazor.server.js` 區段之前，立即新增腳本的標記 `Scripts` ：

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     架構會將 `blazor.server.js` 腳本新增至應用程式。 不需要手動將腳本新增至應用程式。

1. `_Imports.razor`使用下列內容將檔案新增至專案的根資料夾， (將最後一個命名空間變更 `MyAppNamespace` 為應用程式) 的命名空間：

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

1. 在中 `Startup.ConfigureServices` ，註冊 Blazor Server 服務：

   ```csharp
   services.AddServerSideBlazor();
   ```

1. 在中 `Startup.Configure` ，將 Blazor 中樞端點新增至 `app.UseEndpoints` ：

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. 將元件整合至任何頁面或視圖中。 如需詳細資訊，請參閱 [從頁面或視圖區段呈現元件](#render-components-from-a-page-or-view) 。

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a>在頁面應用程式中使用可路由的元件 Razor

*本節適用于新增直接可從使用者要求路由傳送的元件。*

支援 Razor 頁面應用程式中可路由的元件 Razor ：

1. 遵循 [設定一節中的指導](#configuration) 方針。

1. `App.razor`使用下列內容將檔案新增至專案根目錄：

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

1. 將檔案新增 `_Host.cshtml` 至 `Pages` 具有下列內容的資料夾：

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   元件會使用共用檔案 `_Layout.cshtml` 進行版面配置。

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 設定元件是否為 `App` ：

   * 會資源清單到頁面中。
   * 會在頁面上轉譯為靜態 HTML，或包含從使用者代理程式啟動應用程式所需的資訊 Blazor 。

   如需元件標記協助程式的詳細資訊，包括傳遞參數和設定 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> ，請參閱 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> 。

1. 將頁面的低優先順序路由新增 `_Host.cshtml` 至中的端點設定 `Startup.Configure` ：

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. 將可路由傳送的元件新增至應用程式。 例如：

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

如需命名空間的詳細資訊，請參閱 [元件命名空間](#component-namespaces) 一節。

## <a name="use-routable-components-in-an-mvc-app"></a>在 MVC 應用程式中使用可路由的元件

*本節適用于新增直接可從使用者要求路由傳送的元件。*

支援 Razor MVC 應用程式中的可路由元件：

1. 遵循 [設定一節中的指導](#configuration) 方針。

1. `App.razor`使用下列內容將檔案新增至專案的根目錄：

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

1. 將檔案新增 `_Host.cshtml` 至 `Views/Home` 具有下列內容的資料夾：

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   元件會使用共用檔案 `_Layout.cshtml` 進行版面配置。

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 設定元件是否為 `App` ：

   * 會資源清單到頁面中。
   * 會在頁面上轉譯為靜態 HTML，或包含從使用者代理程式啟動應用程式所需的資訊 Blazor 。

   如需元件標記協助程式的詳細資訊，包括傳遞參數和設定 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> ，請參閱 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> 。

1. 將動作新增至 Home 控制器：

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. 新增控制器動作的低優先順序路由，將 `_Host.cshtml` 視圖傳回至中的端點設定 `Startup.Configure` ：

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. 建立 `Pages` 資料夾，並將可路由傳送的元件新增至應用程式。 例如：

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

如需命名空間的詳細資訊，請參閱 [元件命名空間](#component-namespaces) 一節。

## <a name="render-components-from-a-page-or-view"></a>從頁面或視圖呈現元件

*本節適用于將元件新增至頁面或視圖，其中元件無法直接從使用者要求路由傳送。*

若要從頁面或視圖轉譯元件，請使用 [元件標記](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)協助程式。

### <a name="render-stateful-interactive-components"></a>轉譯具狀態的互動式元件

可設定狀態的互動式元件可以加入至 Razor 頁面或視圖中。

當頁面或視圖呈現時：

* 此元件是使用頁面或視圖所資源清單。
* 用於進行可呈現的初始元件狀態會遺失。
* 建立連接時，會建立新的元件狀態 SignalR 。

下列 Razor 頁面會呈現 `Counter` 元件：

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

如需詳細資訊，請參閱<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。

### <a name="render-noninteractive-components"></a>轉譯非互動式元件

在下 Razor 一個頁面中， `Counter` 系統會使用表單所指定的初始值，以靜態方式呈現元件。 由於元件是以靜態方式轉譯，因此元件並非互動式：

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

如需詳細資訊，請參閱<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。

## <a name="component-namespaces"></a>元件命名空間

使用自訂資料夾來保存應用程式的元件時，請將代表資料夾的命名空間新增至頁面/視圖或檔案 `_ViewImports.cshtml` 。 在下例中︰

* 變更 `MyAppNamespace` 為應用程式的命名空間。
* 如果未使用名為的資料夾 `Components` 來保存元件，請變更 `Components` 為元件所在的資料夾。

```cshtml
@using MyAppNamespace.Components
```

檔案 `_ViewImports.cshtml` 位於 `Pages` Razor 頁面應用程式的資料夾或 `Views` MVC 應用程式的資料夾中。

如需詳細資訊，請參閱<xref:blazor/components/index#namespaces>。

::: zone-end
