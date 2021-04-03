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
# <a name="prerender-and-integrate-aspnet-core-razor-components"></a>ASP.NET Core 元件的已呈現和整合 Razor

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

Razor 元件可以整合到 Razor 託管解決方案中的頁面和 MVC 應用程式 Blazor WebAssembly 。 轉譯頁面或視圖時，可同時資源清單元件。

## <a name="solution-configuration"></a>解決方案設定

### <a name="prerendering-configuration"></a>已呈現的設定

若要為裝載的應用程式設定預先安裝 Blazor WebAssembly ：

1. Blazor WebAssembly在 ASP.NET Core 應用程式中裝載應用程式。 獨立 Blazor WebAssembly 應用程式可以新增至 ASP.NET Core 解決方案，或您可以使用 Blazor WebAssembly 從[ Blazor WebAssembly 專案範本](xref:blazor/tooling)建立的託管應用程式與裝載的選項：

   * Visual Studio：在建立應用程式時，選取 [ **Advanced**  >  **ASP.NET Core hosted** ] 核取方塊 Blazor WebAssembly 。 在本文的範例中，會命名方案 `BlazorHosted` 。
   * Visual Studio Code/.NET CLI 命令 shell： `dotnet new blazorwasm -ho` (使用 `-ho|--hosted`) 選項。 使用 `-o|--output {LOCATION}` 選項來建立方案的資料夾，並設定解決方案的專案命名空間。 在本文的範例中，會將方案命名為 `BlazorHosted` (`dotnet new blazorwasm -ho -o BlazorHosted`) 。

   針對本文中的範例，用戶端專案的命名空間是 `BlazorHosted.Client` ，而伺服器專案的命名空間是 `BlazorHosted.Server` 。

1.  `wwwroot/index.html` 從專案中刪除檔案 Blazor WebAssembly **`Client`** 。

1. 在 **`Client`** 專案中， **刪除** () 中的下列程式 `Program.Main` 程式碼 `Program.cs` ：

   ```diff
   - builder.RootComponents.Add<App>("#app");
   ```

1. 將檔案加入 `Pages/_Host.cshtml` **`Server`** 專案的 `Pages` 資料夾中。 您可以 `_Host.cshtml` 使用命令介面中的命令，從範本建立的專案中取得檔案， Blazor Server `dotnet new blazorserver -o BlazorServer` (選項會 `-o BlazorServer` 建立專案) 的資料夾。 將檔案放 `Pages/_Host.cshtml` 入 **`Server`** 託管解決方案的專案之後，對檔案 Blazor WebAssembly 進行下列變更：

   * 為專案提供指示詞 [`@using`](xref:mvc/views/razor#using) **`Client`** (例如 `@using BlazorHosted.Client`) 。
   * 更新樣式表單連結以指向 WebAssembly 專案的樣式表單。 在下列範例中，用戶端專案的命名空間為 `BlazorHosted.Client` ：

     ```diff
     - <link href="css/site.css" rel="stylesheet" />
     - <link href="_content/BlazorServer/_framework/scoped.styles.css" rel="stylesheet" />
     + <link href="css/app.css" rel="stylesheet" />
     + <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

     > [!NOTE]
     > 將 `<link>` 要求啟動增益集樣式的元素保留 (`css/bootstrap/bootstrap.min.css`) 。

   * 使用下列內容更新元件標籤協助程式的， `render-mode` 以呈現根[](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) `App` 元件 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered> ：

     ```diff
     - <component type="typeof(App)" render-mode="ServerPrerendered" />
     + <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * 更新 Blazor 腳本來源以使用用戶端 Blazor WebAssembly 腳本：

     ```diff
     - <script src="_framework/blazor.server.js"></script>
     + <script src="_framework/blazor.webassembly.js"></script>
     ```

1. 在 `Startup.Configure` 專案中 **`Server`** ，將檔案的回復變更 `index.html` 為 `_Host.cshtml` 頁面。

   `Startup.cs`:

   ```diff
   - endpoints.MapFallbackToFile("index.html");
   + endpoints.MapFallbackToPage("/_Host");
   ```

1. 執行 **`Server`** 專案。 裝載的 Blazor WebAssembly 應用程式是由 **`Server`** 用戶端的專案所資源清單。

### <a name="configuration-for-embedding-razor-components-into-pages-and-views"></a>將元件內嵌 Razor 至頁面和視圖的設定

本文中的下列各節和範例可將 Razor 用戶端應用程式的元件內嵌 Blazor WebAssembly 至伺服器應用程式的頁面和觀點，需要進行額外的設定。

使用 Razor 專案中的預設頁面或 MVC 版面配置檔案 **`Server`** 。 **`Server`** 專案必須具有下列檔案和資料夾。

Razor 頁面：

* `Pages/Shared/_Layout.cshtml`
* `Pages/_ViewImports.cshtml`
* `Pages/_ViewStart.cshtml`

Mvc：

* `Views/Shared/_Layout.cshtml`
* `Views/_ViewImports.cshtml`
* `Views/_ViewStart.cshtml`

從 Razor 頁面或 MVC 專案範本所建立的應用程式取得先前的檔案。 如需詳細資訊，請參閱 <xref:tutorials/razor-pages/razor-pages-start>或 <xref:tutorials/first-mvc-app/start-mvc>。

更新匯入檔案中的命名空間 `_ViewImports.cshtml` ，以符合接收檔案的專案所使用的命名空間 **`Server`** 。

更新匯入的版面配置檔案 (`_Layout.cshtml`) ，以包含 **`Client`** 專案的樣式。 在下列範例中， **`Client`** 專案的命名空間為 `BlazorHosted.Client` 。 您 `<title>` 可以同時更新元素。

`Pages/Shared/_Layout.cshtml` (Razor) 或 `Views/Shared/_Layout.cshtml` (MVC) 的頁面：

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

匯入的版面配置包含 `Home` 和 `Privacy` 導覽連結。 若要將 `Home` 連結指向託管 Blazor WebAssembly 應用程式，請變更超連結：

```diff
- <a class="nav-link text-dark" asp-area="" asp-page="/Index">Home</a>
+ <a class="nav-link text-dark" href="/">Home</a>
```

在 MVC 版面配置檔案中：

```diff
- <a class="nav-link text-dark" asp-area="" asp-controller="Home" 
-     asp-action="Index">Home</a>
+ <a class="nav-link text-dark" href="/">Home</a>
```

若要將 `Privacy` 連結導向至隱私權頁面，請將隱私權頁面新增至 **`Server`** 專案。

`Pages/Privacy.cshtml` 在 **`Server`** 專案中：

```cshtml
@page
@model BlazorHosted.Server.Pages.PrivacyModel
@{
}

<h1>Privacy Policy</h1>
```

如果偏好以 MVC 為基礎的隱私視圖，請在專案中建立隱私權視圖 **`Server`** 。

`View/Home/Privacy.cshtml`:

```cshtml
@{
    ViewData["Title"] = "Privacy Policy";
}

<h1>@ViewData["Title"]</h1>
```

在 `Home` 控制器中，返回 [view]。

`Controllers/HomeController.cs`:

```diff
+ public IActionResult Privacy()
+ {
+     return View();
+ }
```

**`Server`** 從贊助商專案的資料夾將靜態資產匯入至專案 `wwwroot` ：

* `wwwroot/css` 資料夾和內容
* `wwwroot/js` 資料夾和內容
* `wwwroot/lib` 資料夾和內容

如果從 ASP.NET Core 專案範本建立「捐贈者」專案，而且未修改這些檔案，您可以從「 `wwwroot` 贊助商」專案將整個資料夾複製到專案中， **`Server`** 並移除 `favicon.ico` 圖示檔。

> [!NOTE]
> 如果 **`Client`** 和 **`Server`** 專案在其資料夾中包含相同的靜態資產 `wwwroot` (例如) ，則會擲回例外狀況 `favicon.ico` ，因為每個資料夾中的靜態資產都會共用相同的 web 根目錄路徑：
>
> > 靜態 web 資產 '. ..\favicon.ico ' 有衝突的 web 根路徑 '/wwwroot/favicon.ico ' 與專案檔 ' wwwroot\favicon.ico '。
>
> 因此，請在任一資料夾中裝載靜態資產 `wwwroot` ，而非兩者。

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a>使用元件標記協助程式在頁面或視圖中轉譯元件

在設定 [解決方案](#solution-configuration)（包括 [其他](#configuration-for-embedding-razor-components-into-pages-and-views)設定）之後， [元件](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) 標籤協助程式支援兩種轉譯模式，可從 Blazor WebAssembly 頁面或視圖中的應用程式轉譯元件：

* <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssembly>
* <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered>

在下列 Razor 頁面範例中， `Counter` 元件會在頁面中呈現。 若要使元件互動， Blazor WebAssembly 腳本會包含在頁面的 [轉譯] [區段](xref:mvc/views/layout#sections)中。 若要避免使用元件標記協助程式 `Counter` () 的[](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)完整命名空間 `{APP ASSEMBLY}.Pages.Counter` ，請加入 [`@using`](xref:mvc/views/razor#using) 用戶端專案 `Pages` 命名空間的指示詞。 在下列範例中， **`Client`** 專案的命名空間為 `BlazorHosted.Client` 。

在 **`Server`** 專案中 `Pages/RazorPagesCounter1.cshtml` ：

```cshtml
@page
@using BlazorHosted.Client.Pages

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

執行 **`Server`** 專案。 流覽至中的 Razor 頁面 `/razorpagescounter1` 。 資源清單 `Counter` 元件內嵌在頁面中。

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 設定元件是否為：

* 會資源清單到頁面中。
* 會在頁面上轉譯為靜態 HTML，或包含從使用者代理程式啟動應用程式所需的資訊 Blazor 。

如需元件標記協助程式的詳細資訊，包括傳遞參數和設定 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> ，請參閱 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> 。

根據元件所使用的靜態資源，以及在應用程式中組織版面配置頁面的方式，可能需要額外的工作。 一般而言，腳本會加入至頁面或視圖的轉譯 `Scripts` 區段，並將樣式表單加入至版面配置的 `<head>` 元素內容中。

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a>使用 CSS 選取器呈現頁面或視圖中的元件

在設定 [解決方案](#solution-configuration)（包括 [其他](#configuration-for-embedding-razor-components-into-pages-and-views)設定）之後，請在中將根元件新增至裝載 **`Client`** 方案的專案 Blazor WebAssembly `Program.Main` 。 在下列範例中， `Counter` 元件會宣告為根元件，其中包含的 CSS 選取器會使用 `id` 符合的來選取專案 `counter-component` 。 在下列範例中， **`Client`** 專案的命名空間為 `BlazorHosted.Client` 。

在 `Program.cs` 專案中 **`Client`** ，將專案元件的命名空間新增 Razor 至檔案頂端：

```diff
+ using BlazorHosted.Client.Pages;
```

`builder`在中建立之後 `Program.Main` ，請將 `Counter` 元件新增為根元件：

```diff
+ builder.RootComponents.Add<Counter>("#counter-component");
```

在下列 Razor 頁面範例中， `Counter` 元件會在頁面中呈現。 若要使元件互動， Blazor WebAssembly 腳本會包含在頁面的 [轉譯] [區段](xref:mvc/views/layout#sections)中。

在 **`Server`** 專案中 `Pages/RazorPagesCounter2.cshtml` ：

```cshtml
@page

<div id="counter-component">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

執行 **`Server`** 專案。 流覽至中的 Razor 頁面 `/razorpagescounter2` 。 資源清單 `Counter` 元件內嵌在頁面中。

根據元件所使用的靜態資源，以及在應用程式中組織版面配置頁面的方式，可能需要額外的工作。 一般而言，腳本會加入至頁面或視圖的轉譯 `Scripts` 區段，並將樣式表單加入至版面配置的 `<head>` 元素內容中。

> [!NOTE]
> 上述範例 <xref:Microsoft.JSInterop.JSException> Blazor WebAssembly 會在應用程式 Razor 使用 CSS 選取器 **同時** 資源清單和整合至頁面或 MVC 應用程式時擲回。 流覽至其中一個專案元件時，會擲回 **`Client`** Razor 下列例外狀況：
>
> > Microsoft.JSInterop.JS例外狀況：找不到任何符合選取器 ' #counter 元件的元素。
>
> 這是正常行為，因為 Blazor WebAssembly 使用可路由傳送的元件來呈現和整合應用程式與 Razor 使用 CSS 選取器不相容。

## <a name="additional-blazor-webassembly-resources"></a>其他 Blazor WebAssembly 資源

* [狀態管理：處理已呈現](xref:blazor/state-management?pivot=webassembly#handle-prerendering)
* [元件消極式載入的預呈現支援](xref:blazor/webassembly-lazy-load-assemblies#assembly-load-logic-in-onnavigateasync)
* Razor 與可呈現的相關的元件生命週期主題
  * [元件初始化 (`OnInitialized{Async}`) ](xref:blazor/components/lifecycle#component-initialization-oninitializedasync)
  * [元件轉譯 (之後 `OnAfterRender{Async}`) ](xref:blazor/components/lifecycle#after-component-render-onafterrenderasync)
  * 可設定的可設定[狀態重新連接](xref:blazor/components/lifecycle#stateful-reconnection-after-prerendering)：雖然區段中的內容著重于和可設定狀態的重新連線，但 Blazor Server 在裝載 SignalR 的應用程式 () 的情況下，會 Blazor WebAssembly <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered> 包含類似的條件和方法來防止執行開發人員程式碼兩次 已針對 ASP.NET Core 6.0 版本規劃 *新的狀態保留功能* ，可改善在預進行期間的初始化程式碼執行管理。
  * [偵測應用程式何時進行呈現](xref:blazor/components/lifecycle#detect-when-the-app-is-prerendering)
* 與可呈現的相關的驗證與授權主題
  * [一般層面](xref:blazor/security/index#aspnet-core-blazor-authentication-and-authorization)
  * [支援使用驗證進行預進行](xref:blazor/security/webassembly/additional-scenarios#support-prerendering-with-authentication)
* [裝載和部署： Blazor WebAssembly](xref:blazor/host-and-deploy/webassembly)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

在 Razor Razor Blazor WebAssembly .net 5 或更新版本的 ASP.NET Core 中，支援將元件整合至裝載方案中的頁面和 MVC 應用程式。 請選取此文章的 .NET 5 或更新版本。

::: moniker-end

::: zone-end

::: zone pivot="server"

Razor 元件可以整合至 Razor 應用程式中的頁面和 MVC 應用程式 Blazor Server 。 轉譯頁面或視圖時，可同時資源清單元件。

設定 [專案](#configuration)之後，請根據專案的需求，使用下列各節中的指導方針：

* 可路由的元件：適用于直接從使用者要求路由傳送的元件。 當訪客應能在其瀏覽器中使用指示詞為元件發出 HTTP 要求時，請遵循此指導 [`@page`](xref:mvc/views/razor#page) 方針。
  * [在頁面應用程式中使用可路由的元件 Razor](#use-routable-components-in-a-razor-pages-app)
  * [在 MVC 應用程式中使用可路由的元件](#use-routable-components-in-an-mvc-app)
* [從頁面或視圖呈現元件](#render-components-from-a-page-or-view)：適用于無法直接從使用者要求路由傳送的元件。 當應用程式使用元件標籤協助程式將元件內嵌至現有的頁面和流覽 [器](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)時，請遵循此指引。

## <a name="configuration"></a>組態

現有的 Razor 頁面或 MVC 應用程式可以將 Razor 元件整合至頁面和流覽中：

1. 在專案的版面配置檔案中：

   * 將下列 `<base>` 標記新增至 `<head>` `Pages/Shared/_Layout.cshtml` (Razor 頁面) 或 `Views/Shared/_Layout.cshtml` (MVC) 中的元素：

     ```diff
     + <base href="~/" />
     ```

     `href`上述範例中的 *應用程式基底路徑*)  (值假設應用程式位於根 URL 路徑 (`/`) 。 如果應用程式是子應用程式，請遵循本文的「 *應用程式基底路徑* 」一節中的指導方針 <xref:blazor/host-and-deploy/index#app-base-path> 。

   * 在轉譯 `<script>` `blazor.server.js` 區段之前，立即新增腳本的標記 `Scripts` 。

     `Pages/Shared/_Layout.cshtml` (Razor) 或 `Views/Shared/_Layout.cshtml` (MVC) 的頁面：

     ```diff
         ...
     +   <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     架構會將 `blazor.server.js` 腳本新增至應用程式。 不需要手動將腳本檔案新增 `blazor.server.js` 至應用程式。

1. 使用下列內容將匯入檔案新增至專案的根資料夾。 將 `{APP NAMESPACE}` 預留位置變更為專案的命名空間。

   `_Imports.razor`:

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

1. Blazor Server在中註冊服務 `Startup.ConfigureServices` 。

   `Startup.cs`:

   ```diff
   + services.AddServerSideBlazor();
   ```

1. 將 Blazor 中樞端點新增至 (`app.UseEndpoints`) 的端點 `Startup.Configure` 。

   `Startup.cs`:

   ```diff
   + endpoints.MapBlazorHub();
   ```

1. 將元件整合至任何頁面或視圖中。 例如，將元件新增 `Counter` 至專案的 `Shared` 資料夾。

   `Pages/Shared/Counter.razor` (Razor) 或 `Views/Shared/Counter.razor` (MVC) 的頁面：

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

   **Razor 頁面**：

   在 `Index` 頁面應用程式的專案頁面 Razor 上，新增 `Counter` 元件的命名空間，並將元件內嵌到頁面中。 當 `Index` 頁面載入時， `Counter` 會在頁面中資源清單元件。 在下列範例中，請將 `{APP NAMESPACE}` 預留位置取代為專案的命名空間。

   `Pages/Index.cshtml`:

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

   在上述範例中，請將 `{APP NAMESPACE}` 預留位置取代為應用程式的命名空間。

   **MVC**：

   在專案的 `Index` MVC 應用程式視圖中，新增 `Counter` 元件的命名空間，並將元件內嵌到視圖中。 當 `Index` view 載入時， `Counter` 會在頁面中資源清單元件。 在下列範例中，請將 `{APP NAMESPACE}` 預留位置取代為專案的命名空間。

   `Views/Home/Index.cshtml`:

   ```cshtml
   @using {APP NAMESPACE}.Views.Shared
   @{
       ViewData["Title"] = "Home Page";
   }

   <div>
       <component type="typeof(Counter)" render-mode="ServerPrerendered" />
   </div>
   ```

如需詳細資訊，請參閱 [從頁面或視圖區段呈現元件](#render-components-from-a-page-or-view) 。

## <a name="use-routable-components-in-a-razor-pages-app"></a>在頁面應用程式中使用可路由的元件 Razor

*本節適用于新增直接可從使用者要求路由傳送的元件。*

支援 Razor 頁面應用程式中可路由的元件 Razor ：

1. 遵循 [設定一節中的指導](#configuration) 方針。

1. `App`使用下列內容將元件新增至專案根目錄。

   `App.razor`:

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

1. `_Host`使用下列內容將頁面加入至專案。

   `Pages/_Host.cshtml`:

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

1. 在的 `Startup.Configure` 端點中 `Startup.cs` ，將頁面的低優先順序路由新增 `_Host` 為最後一個端點：

   ```diff
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapRazorPages();
       endpoints.MapBlazorHub();
   +   endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. 將可路由傳送的元件加入至專案。

   `Pages/RoutableCounter.razor`:

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

1. 執行專案，然後流覽至中可路由傳送的 `RoutableCounter` 元件 `/routable-counter` 。

如需命名空間的詳細資訊，請參閱 [元件命名空間](#component-namespaces) 一節。

## <a name="use-routable-components-in-an-mvc-app"></a>在 MVC 應用程式中使用可路由的元件

*本節適用于新增直接可從使用者要求路由傳送的元件。*

支援 Razor MVC 應用程式中的可路由元件：

1. 遵循 [設定一節中的指導](#configuration) 方針。

1. `App`使用下列內容將元件新增至專案根目錄。

   `App.razor`:

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

1. `_Host`使用下列內容將視圖新增至專案。

   `Views/Home/_Host.cshtml`:

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

1. 將動作新增至首頁控制器。

   `Controllers/HomeController.cs`:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. 在的 [端點] 中 `Startup.Configure` `Startup.cs` ，為傳回 view 的控制器動作新增低優先順序的路由 `_Host` ：

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

1. 將可路由傳送的元件加入至專案。

   `Pages/RoutableCounter.razor`:

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

1. 執行專案，然後流覽至中可路由傳送的 `RoutableCounter` 元件 `/routable-counter` 。

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

使用自訂資料夾來保存專案的元件時 Razor ，請將代表資料夾的命名空間新增至頁面/視圖或檔案 `_ViewImports.cshtml` 。 在下例中︰

* 元件會儲存在 `Components` 專案的資料夾中。
* `{APP NAMESPACE}`預留位置是專案的命名空間。 `Components` 代表資料夾的名稱。

```cshtml
@using {APP NAMESPACE}.Components
```

檔案 `_ViewImports.cshtml` 位於 `Pages` Razor 頁面應用程式的資料夾或 `Views` MVC 應用程式的資料夾中。

如需詳細資訊，請參閱<xref:blazor/components/index#namespaces>。

## <a name="additional-blazor-server-resources"></a>其他 Blazor Server 資源

* [狀態管理：處理已呈現](xref:blazor/state-management?pivot=server#handle-prerendering)
* Razor 與可呈現的相關的元件生命週期主題
  * [元件初始化 (`OnInitialized{Async}`) ](xref:blazor/components/lifecycle#component-initialization-oninitializedasync)
  * [元件轉譯 (之後 `OnAfterRender{Async}`) ](xref:blazor/components/lifecycle#after-component-render-onafterrenderasync)
  * [以具狀態重新連接後重新連線](xref:blazor/components/lifecycle#stateful-reconnection-after-prerendering)
  * [偵測應用程式何時進行呈現](xref:blazor/components/lifecycle#detect-when-the-app-is-prerendering)
* [驗證和授權：一般層面](xref:blazor/security/index#aspnet-core-blazor-authentication-and-authorization)
* [Blazor Server 轉譯資料流程](xref:blazor/fundamentals/handle-errors?pivot=server#blazor-server-prerendering-server)
* [裝載和部署： Blazor Server](xref:blazor/host-and-deploy/server)
* [威脅緩和：跨網站腳本 (XSS) ](xref:blazor/security/server/threat-mitigation#cross-site-scripting-xss)

::: zone-end
