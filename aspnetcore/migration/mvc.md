---
title: 瞭解如何從 ASP.NET MVC 遷移至 ASP.NET Core MVC
author: wadepickett
description: 瞭解如何開始將 ASP.NET MVC 專案遷移至 ASP.NET Core MVC。
ms.author: wpickett
ms.date: 06/18/2020
no-loc:
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
uid: migration/mvc
ms.openlocfilehash: 51228e59284b5edf0554e9929b16deafe08ea31e
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326632"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>從 ASP.NET MVC 移轉至 ASP.NET Core MVC

::: moniker range=">= aspnetcore-3.0"

本文說明如何開始將 ASP.NET MVC 專案遷移至 [ASP.NET CORE mvc](xref:mvc/overview)。 在此程式中，它會反白顯示 ASP.NET MVC 的相關變更。

從 ASP.NET MVC 進行遷移是一個多步驟的程式。 本文將說明：

* 初始設定。
* 基本控制器和 views。
* 靜態內容。
* 用戶端相依性。

如需遷移設定和程式 Identity 代碼，請參閱 [將設定遷移至 ASP.NET Core](xref:migration/configuration) 以及 [將驗證遷移至 Identity ASP.NET Core](xref:migration/identity)。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a>建立 starter ASP.NET MVC 專案

在 Visual Studio 中建立 MVC 專案的範例以進行遷移：

1. 從 [ **檔案** ] 功能表選取 [ **新增** > **專案**]。
1. 選取 [ **ASP.NET Web 應用程式 (] .NET Framework) ** 然後選取 **[下一步]**。
1. 將專案命名為 *WebApp1* ，讓命名空間符合下一個步驟中所建立的 ASP.NET Core 專案。 選取 [建立]。
1. 選取 [ **MVC**]，然後選取 [ **建立**]。

## <a name="create-the-aspnet-core-project"></a>建立 ASP.NET Core 專案

使用新的 ASP.NET Core 專案來建立新的方案，以遷移至：

1. 啟動 Visual Studio 的第二個實例。
1. 從 [ **檔案** ] 功能表選取 [ **新增** > **專案**]。
1. 依序選取 [ASP.NET Core Web 應用程式]**** 和 [下一步]****。
1. 在 [ **設定您的新專案** ] 對話方塊中，將專案命名為 *WebApp1*。
1. 將位置設定為與上一個專案不同的目錄，以使用相同的專案名稱。 使用相同的命名空間，可讓您更輕鬆地在兩個專案之間複製程式碼。 選取 [建立]。
1. 在 [ **建立新的 ASP.NET Core Web 應用程式** ] 對話方塊中，確認已選取 [ **.net Core** ] 和 [ **ASP.NET Core 3.1** ]。 選取 **Web 應用程式 (模型-視圖控制器) ** 專案範本，然後選取 [ **建立**]。

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a>將 ASP.NET Core 網站設定為使用 MVC

在 ASP.NET Core 3.0 和更新版本的專案中，.NET Framework 不再是支援的目標 Framework。 您的專案必須以 .NET Core 為目標。 ASP.NET Core 共用架構（包括 MVC）是 .NET Core 執行時間安裝的一部分。 在專案檔中使用 SDK 時，會自動參考共用架構 `Microsoft.NET.Sdk.Web` ：

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

如需詳細資訊，請參閱 [架構參考](xref:migration/22-to-30#framework-reference)。

在 ASP.NET Core 中， `Startup` 類別：

* 取代 *global.asax*。
* 處理所有應用程式啟動工作。

如需詳細資訊，請參閱<xref:fundamentals/startup>。

在 ASP.NET Core 專案中，開啟 *Startup.cs* 檔案：

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

ASP.NET Core apps 必須選擇使用中介軟體的架構功能。 先前範本產生的程式碼會新增下列服務和中介軟體：

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A>擴充方法會註冊控制器、API 相關功能和 views 的 MVC 服務支援。 如需 MVC 服務註冊選項的詳細資訊，請參閱 [mvc 服務註冊](xref:migration/22-to-30#mvc-service-registration)
* <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>擴充方法會新增靜態檔案處理常式 `Microsoft.AspNetCore.StaticFiles` 。 `UseStaticFiles`必須先呼叫擴充方法 `UseRouting` 。 如需詳細資訊，請參閱<xref:fundamentals/static-files>。
* <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>擴充方法會新增路由。 如需詳細資訊，請參閱<xref:fundamentals/routing>。

此現有設定包含遷移範例 ASP.NET MVC 專案所需的專案。 如需 ASP.NET Core 中介軟體選項的詳細資訊，請參閱 <xref:fundamentals/startup> 。

## <a name="migrate-controllers-and-views"></a>遷移控制器和視圖

在 ASP.NET Core 專案中，會加入新的空白控制器類別和 view 類別做為預留位置，其使用的名稱與 ASP.NET MVC 專案中的控制器和 view 類別相同，可從中進行遷移。

ASP.NET Core 的 *WebApp1* 專案已經包含最基本的範例控制器，並以與 ASP.NET MVC 專案相同的名稱來查看。 因此，這些專案將作為 ASP.NET MVC 控制器的預留位置，以及要從 ASP.NET MVC *WebApp1* 專案中遷移的視圖。

1. 從 ASP.NET MVC 複製方法 `HomeController` ，以取代新的 ASP.NET Core `HomeController` 方法。 不需要變更動作方法的傳回型別。 ASP.NET MVC 內建範本的控制器動作方法傳回型別為 <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; 在 ASP.NET CORE mvc 中，動作方法會改為傳回 `IActionResult` 。 `ActionResult` 會實作 `IActionResult`。
1. 在 ASP.NET Core 專案中，以滑鼠右鍵按一下 *Views/Home* 目錄，然後選取 [ **加入** > **現有專案**]。
1. 在 [ **加入現有專案** ] 對話方塊中，流覽至 ASP.NET MVC *WebApp1* 專案的 *Views/Home* 目錄。
1. 選取 [ *About*]、[ *Contact*] 和 [ *Index* ] Razor view 檔案，然後選取 [ **加入**]，取代現有的檔案。

如需詳細資訊，請參閱 <xref:mvc/controllers/actions> 和 <xref:mvc/views/overview>。

## <a name="test-each-method"></a>測試每個方法

您可以測試每個控制器端點，不過，配置和樣式稍後會在檔中涵蓋。

1. 執行 ASP.NET Core 應用程式。
1. 將目前的埠號碼取代為 ASP.NET Core 專案中使用的埠號碼，以在執行 ASP.NET Core 應用程式的瀏覽器中叫用轉譯的視圖。 例如： `https://localhost:44375/home/about` 。

## <a name="migrate-static-content"></a>遷移靜態內容

在 ASP.NET MVC 5 及更早版本中，靜態內容是由 Web 專案的根目錄所裝載，並與伺服器端檔案混合。 在 ASP.NET Core 中，靜態檔案會儲存在專案的 [web 根目錄](xref:fundamentals/index#web-root) 中。 預設目錄是 *{content root}/wwwroot*，但可以變更。 如需詳細資訊，請參閱 [ASP.NET Core 中的靜態檔案](xref:fundamentals/static-files#serve-static-files)。

將 ASP.NET MVC *WebApp1*專案中的靜態內容複寫到 ASP.NET Core *WebApp1*專案中的*wwwroot*目錄：

1. 在 ASP.NET Core 專案中，以滑鼠右鍵按一下 [ *wwwroot* ] 目錄，然後選取 [ **加入** > **現有專案**]。
1. 在 [ **加入現有專案** ] 對話方塊中，流覽至 [ASP.NET MVC *WebApp1* ] 專案。
1. 選取 *favicon .ico* 檔案，然後選取 [ **加入**]，取代現有的檔案。

## <a name="migrate-the-layout-files"></a>遷移版面配置檔案

將 ASP.NET MVC 專案版面配置檔案複製到 ASP.NET Core 專案：

1. 在 ASP.NET Core 專案中，以滑鼠右鍵按一下 *Views* 目錄，然後選取 [ **加入** > **現有專案**]。
1. 在 [ **加入現有專案** ] 對話方塊中，流覽至 ASP.NET MVC *WebApp1* 專案的 *Views* 目錄。
1. 選取 *_ViewStart 的 cshtml* 檔案，然後選取 [ **新增**]。

將 ASP.NET MVC 專案共用設定檔案複製到 ASP.NET Core 專案：

1. 在 ASP.NET Core 專案中，以滑鼠右鍵按一下 *Views/Shared* 目錄，然後選取 [ **加入** > **現有專案**]。
1. 在 [ **加入現有專案** ] 對話方塊中，流覽至 ASP.NET MVC *WebApp1* 專案的 *Views/Shared* 目錄。
1. 選取 *_Layout 的 cshtml* 檔案，然後選取 [ **新增**]，取代現有的檔案。

在 ASP.NET Core 專案中，開啟 *_Layout 的 cshtml* 檔案。 進行下列變更，以符合如下所示的完整程式碼：

更新啟動載入 CSS 包含的程式碼，以符合下列完成的程式碼：

1. 取代為 `@Styles.Render("~/Content/css")` 用 `<link>` 來載入開機載入程式的元素 *。 css* (請參閱下面) 。
1. 移除 `@Scripts.Render("~/bundles/modernizr")`。

啟動載入 CSS 的完整取代標記包含：

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

更新 jQuery 和啟動程式 JavaScript 包含，以符合下列完成的程式碼：

1. `@Scripts.Render("~/bundles/jquery")`以元素取代 `<script>` (請參閱以下) 。
1. `@Scripts.Render("~/bundles/bootstrap")`以元素取代 `<script>` (請參閱以下) 。

JQuery 和啟動程式 JavaScript 的完整取代標記包含：

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

更新的 *_Layout cshtml* 檔案如下所示：

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

在瀏覽器中查看網站。 它應該會以預期的樣式來呈現。

## <a name="configure-bundling-and-minification"></a>設定捆綁和縮制

ASP.NET Core 與數個開放原始碼的包裝和縮制解決方案（例如 [WebOptimizer](https://github.com/ligershark/WebOptimizer) 和其他類似的程式庫）相容。 ASP.NET Core 不提供原生包裝和縮制解決方案。 如需設定包裝和縮制的相關資訊，請參閱 [捆綁和縮制](xref:client-side/bundling-and-minification)。

## <a name="solve-http-500-errors"></a>解決 HTTP 500 錯誤

有許多問題可能會導致 HTTP 500 錯誤訊息，而不包含問題來源的相關資訊。 例如，如果 *Views/_ViewImports cshtml* 檔案包含不存在於專案中的命名空間，則會產生 HTTP 500 錯誤。 根據預設，在 ASP.NET Core apps 中， `UseDeveloperExceptionPage` 擴充功能會新增至， `IApplicationBuilder` 並在環境為 *開發*環境時執行。 下列程式碼會詳細說明這一點：

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

ASP.NET Core 將未處理的例外狀況轉換成 HTTP 500 錯誤回應。 一般來說，這些回應中不會包含錯誤詳細資料，以防止洩漏有關伺服器的潛在敏感性資訊。 如需詳細資訊，請參閱 [開發人員例外狀況頁面](xref:fundamentals/error-handling#developer-exception-page)。

## <a name="next-steps"></a>後續步驟

* <xref:migration/identity>

## <a name="additional-resources"></a>其他資源

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

本文說明如何開始將 ASP.NET MVC 專案遷移至 [ASP.NET CORE mvc](xref:mvc/overview) 2.2。 在此程式中，它會反白顯示已從 ASP.NET MVC 變更的許多專案。 從 ASP.NET MVC 進行遷移是一個多步驟的程式。 本文將說明：

* 初始設定
* 基本控制器與視圖
* 靜態內容
* 用戶端相依性。

如需遷移設定和程式 Identity 代碼，請參閱 <xref:migration/configuration> 和 <xref:migration/identity> 。

> [!NOTE]
> 範例中的版本號碼可能不是最新版本，請據以更新專案。

## <a name="create-the-starter-aspnet-mvc-project"></a>建立 starter ASP.NET MVC 專案

為了示範升級，我們將從建立 ASP.NET MVC 應用程式開始。 使用名稱 *WebApp1* 加以建立，讓命名空間符合下一個步驟中所建立的 ASP.NET Core 專案。

![Visual Studio 的 [新增專案] 對話方塊](mvc/_static/new-project.png)

![新增 Web 應用程式對話方塊： ASP.NET 範本面板中選取的 MVC 專案範本](mvc/_static/new-project-select-mvc-template.png)

*選用：* 將方案名稱從 *WebApp1* 變更為 *Mvc5*。 Visual Studio 會顯示新的方案名稱 (*Mvc5*) ，讓您更輕鬆地從下一個專案告訴這個專案。

## <a name="create-the-aspnet-core-project"></a>建立 ASP.NET Core 專案

使用與上一個專案相同的名稱來建立新的 *空白* ASP.NET Core web 應用程式 (*WebApp1*) 讓兩個專案中的命名空間相符。 擁有相同的命名空間可讓您更輕鬆地在兩個專案之間複製程式碼。 在與前一個專案不同的目錄中建立此專案，以使用相同的名稱。

![[新增專案] 對話方塊](mvc/_static/new_core.png)

![新的 ASP.NET Web 應用程式對話方塊： ASP.NET Core 範本面板中選取了空白專案範本](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *選用：* 使用 *Web 應用程式* 專案範本建立新的 ASP.NET Core 應用程式。 將專案命名為 *WebApp1*，然後選取 **個別使用者帳戶**的驗證選項。 將此應用程式重新命名為 *FullAspNetCore*。 建立此專案可節省轉換的時間。 您可以在範本產生的程式碼中查看最終結果、將程式碼複製到轉換專案，或與範本產生的專案比較。

## <a name="configure-the-site-to-use-mvc"></a>將網站設定為使用 MVC

* 以 .NET Core 為目標時，預設會參考 [AspNetCore 應用程式中繼套件](xref:fundamentals/metapackage-app) 。 此套件包含 MVC 應用程式經常使用的套件。 如果目標為 .NET Framework，則套件參考必須個別列在專案檔中。

`Microsoft.AspNetCore.Mvc` 是 ASP.NET Core 的 MVC 架構。 `Microsoft.AspNetCore.StaticFiles` 這是靜態檔案處理常式。 ASP.NET Core apps 明確加入宣告中介軟體，例如提供靜態檔案。 如需詳細資訊，請參閱[靜態檔案](xref:fundamentals/static-files)。

* 開啟 *Startup.cs* 檔案，並變更程式碼以符合下列各項：

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>擴充方法會新增靜態檔案處理常式。 如需詳細資訊，請參閱 [應用程式啟動](xref:fundamentals/startup) 和 [路由](xref:fundamentals/routing)。

## <a name="add-a-controller-and-view"></a>新增控制器和查看

在本節中，會新增最基本的控制器和視圖，作為 ASP.NET MVC 控制器的預留位置，以及下一節中所遷移的視圖。

* 新增 *控制器* 目錄。

* 將名為*HomeController.cs*的**控制器類別**新增至*控制器*目錄。

![[新增項目] 對話方塊](mvc/_static/add_mvc_ctl.png)

* 新增 *Views* 目錄。

* 新增 *Views/Home* 目錄。

* 將名為*Index. cshtml*的** Razor View**新增至*Views/Home*目錄。

![[新增項目] 對話方塊](mvc/_static/view.png)

專案結構如下所示：

![方案總管顯示 WebApp1 的檔案和目錄](mvc/_static/project-structure-controller-view.png)

將 *Views/Home/Index.cshtml* 檔案的內容取代為下列標記：

```html
<h1>Hello world!</h1>
```

執行應用程式。

![Web 應用程式在 Microsoft Edge 中開啟](mvc/_static/hello-world.png)

如需詳細資訊，請參閱 [控制器](xref:mvc/controllers/actions) 和 [Views](xref:mvc/views/overview)。

下列功能需要從範例 ASP.NET MVC 專案遷移至 ASP.NET Core 專案：

* 用戶端內容 (CSS、字型和腳本) 

* controllers

* 檢視

* 模型

* 捆綁

* filters

* 登入/登出， Identity (在下一個教學課程中完成這項操作。 ) 

## <a name="controllers-and-views"></a>控制器與視圖

* 將 ASP.NET MVC 中的每個方法複製 `HomeController` 到新的 `HomeController` 。 在 ASP.NET MVC 中，內建範本的控制器動作方法傳回型別為 <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; 在 ASP.NET CORE mvc 中，動作方法會 `IActionResult` 改為傳回。 `ActionResult` implements `IActionResult` ，因此不需要變更動作方法的傳回型別。

* 將 ASP.NET MVC 專案中的 *About*、 *Contact*和 *Index.* cshtml view 檔案複製 Razor 到 ASP.NET Core 專案。

## <a name="test-each-method"></a>測試每個方法

版面配置檔案和樣式尚未遷移，所以轉譯的視圖只會包含視圖檔案中的內容。 和 views 的版面配置檔案產生的連結 `About` `Contact` 將無法使用。

將目前的埠號碼取代為 ASP.NET core 專案中使用的埠號碼，以在執行 ASP.NET core 應用程式的瀏覽器中叫用轉譯的視圖。 例如： `https://localhost:44375/home/about` 。

![連絡人頁面](mvc/_static/contact-page.png)

請注意缺少樣式和功能表項目。 下一節將會修正樣式。

## <a name="static-content"></a>靜態內容

在 ASP.NET MVC 5 及更早版本中，靜態內容是由 Web 專案的根目錄所裝載，並與伺服器端檔案混合。 在 ASP.NET Core 中，靜態內容裝載于 *wwwroot* 目錄。 將靜態內容從 ASP.NET MVC 應用程式複製到 ASP.NET Core 專案中的 *wwwroot* 目錄。 在此範例轉換中：

* 將 *favicon .ico* 檔案從 ASP.NET MVC 專案複製到 ASP.NET Core 專案中的 *wwwroot* 目錄。

ASP.NET MVC 專案會使用 [啟動](https://getbootstrap.com/) 程式進行其樣式，並將啟動程式檔案儲存在 *內容* 和 *腳本* 目錄中。 產生 ASP.NET MVC 專案的範本會參考設定檔案中的啟動程式 (*Views/Shared/_Layout. cshtml*) 。 *bootstrap.js*和*啟動程式 .css*檔案可以從 ASP.NET MVC 專案複製到新專案中的*wwwroot*目錄。 相反地，本檔會在下一節中使用 Cdn 來新增啟動載入 (和其他用戶端程式庫的支援) 。

## <a name="migrate-the-layout-file"></a>遷移版面配置檔案

* 將 ASP.NET MVC 專案*views*目錄中的 *_ViewStart cshtml*檔案複製到 ASP.NET Core 專案的*views*目錄。 在 ASP.NET Core MVC 中， *_ViewStart 的 cshtml* 檔案尚未變更。

* 建立 *Views/Shared* 目錄。

* *選用：* 從*FullAspNetCore* MVC 專案的*views*目錄將 *_ViewImports. cshtml*複製到 ASP.NET Core 專案的*views*目錄。 移除 *_ViewImports cshtml* 檔案中的任何命名空間宣告。 *_ViewImports cshtml*檔案提供所有 view 檔案的命名空間，並引進標籤協助[程式。](xref:mvc/views/tag-helpers/intro) 標籤協助程式會在新的版面配置檔案中使用。 *_ViewImports 的 cshtml*檔案是 ASP.NET Core 的新功能。

* 將 ASP.NET MVC 專案的*views/shared*目錄中的 *_Layout cshtml*檔案複製到 ASP.NET Core 專案的*views/shared*目錄中。

開啟 *_Layout cshtml* 檔案，並進行下列變更 (完成的程式碼如下所示) ：

* 取代為 `@Styles.Render("~/Content/css")` 用 `<link>` 來載入開機載入程式的元素 *。 css* (請參閱下面) 。

* 移除 `@Scripts.Render("~/bundles/modernizr")`。

* `@Html.Partial("_LoginPartial")`使用) 將行批註 (程式程式碼 `@*...*@` 。 如需詳細資訊，請參閱 [遷移驗證和 Identity ASP.NET Core](xref:migration/identity)

* `@Scripts.Render("~/bundles/jquery")`以元素取代 `<script>` (請參閱以下) 。

* `@Scripts.Render("~/bundles/bootstrap")`以元素取代 `<script>` (請參閱以下) 。

啟動載入 CSS 的取代標記包含：

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

JQuery 和啟動程式 JavaScript 的取代標記包含：

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

更新的 *_Layout cshtml* 檔案如下所示：

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

在瀏覽器中查看網站。 它現在應該會正確載入，且需要適當的樣式。

* *選用：* 請嘗試使用新的版面配置檔案。 從 *FullAspNetCore* 專案複製版面配置檔案。 新的版面配置檔案 [使用卷](xref:mvc/views/tag-helpers/intro) 標協助程式，並有其他改良功能。

## <a name="configure-bundling-and-minification"></a>設定捆綁和縮制

如需有關如何設定包裝和縮制的詳細資訊，請參閱 [捆綁和縮制](xref:client-side/bundling-and-minification)。

## <a name="solve-http-500-errors"></a>解決 HTTP 500 錯誤

有許多問題可能會導致 HTTP 500 錯誤訊息，而不包含問題來源的相關資訊。 例如，如果 *Views/_ViewImports cshtml* 檔案包含不存在於專案中的命名空間，則會產生 HTTP 500 錯誤。 根據預設，在 ASP.NET Core apps 中， `UseDeveloperExceptionPage` 擴充功能會新增至，並在設定 `IApplicationBuilder` 為 *開發*時執行。 請參閱下列程式碼中的範例：

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core 將未處理的例外狀況轉換成 HTTP 500 錯誤回應。 一般來說，這些回應中不會包含錯誤詳細資料，以防止洩漏有關伺服器的潛在敏感性資訊。 如需詳細資訊，請參閱 [開發人員例外狀況頁面](xref:fundamentals/error-handling#developer-exception-page)。

## <a name="additional-resources"></a>其他資源

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

本文說明如何開始將 ASP.NET MVC 專案遷移至 [ASP.NET CORE mvc](xref:mvc/overview) 2.1。 在此程式中，它會反白顯示已從 ASP.NET MVC 變更的許多專案。 從 ASP.NET MVC 進行遷移是一個多步驟的程式。 本文將說明：

* 初始設定
* 基本控制器與視圖
* 靜態內容
* 用戶端相依性。

如需遷移設定和程式 Identity 代碼，請參閱 [將設定遷移至 ASP.NET Core](xref:migration/configuration) 以及 [將驗證遷移至 Identity ASP.NET Core](xref:migration/identity)。

> [!NOTE]
> 範例中的版本號碼可能不是最新版本，請據以更新專案。

## <a name="create-the-starter-aspnet-mvc-project"></a>建立 starter ASP.NET MVC 專案

為了示範升級，我們將從建立 ASP.NET MVC 應用程式開始。 使用名稱 *WebApp1* 加以建立，讓命名空間符合下一個步驟中所建立的 ASP.NET Core 專案。

![Visual Studio 的 [新增專案] 對話方塊](mvc/_static/new-project.png)

![新增 Web 應用程式對話方塊： ASP.NET 範本面板中選取的 MVC 專案範本](mvc/_static/new-project-select-mvc-template.png)

*選用：* 將方案名稱從 *WebApp1* 變更為 *Mvc5*。 Visual Studio 會顯示新的方案名稱 (*Mvc5*) ，讓您更輕鬆地從下一個專案告訴這個專案。

## <a name="create-the-aspnet-core-project"></a>建立 ASP.NET Core 專案

使用與上一個專案相同的名稱來建立新的 *空白* ASP.NET Core web 應用程式 (*WebApp1*) 讓兩個專案中的命名空間相符。 擁有相同的命名空間可讓您更輕鬆地在兩個專案之間複製程式碼。 在與前一個專案不同的目錄中建立此專案，以使用相同的名稱。

![[新增專案] 對話方塊](mvc/_static/new_core.png)

![新的 ASP.NET Web 應用程式對話方塊： ASP.NET Core 範本面板中選取了空白專案範本](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *選用：* 使用 *Web 應用程式* 專案範本建立新的 ASP.NET Core 應用程式。 將專案命名為 *WebApp1*，然後選取 **個別使用者帳戶**的驗證選項。 將此應用程式重新命名為 *FullAspNetCore*。 建立此專案可節省轉換的時間。 您可以在範本產生的程式碼中查看最終結果、將程式碼複製到轉換專案，或與範本產生的專案比較。

## <a name="configure-the-site-to-use-mvc"></a>將網站設定為使用 MVC

* 以 .NET Core 為目標時，預設會參考 [AspNetCore 應用程式中繼套件](xref:fundamentals/metapackage-app) 。 此套件包含 MVC 應用程式經常使用的套件。 如果目標為 .NET Framework，則套件參考必須個別列在專案檔中。

`Microsoft.AspNetCore.Mvc` 是 ASP.NET Core 的 MVC 架構。 `Microsoft.AspNetCore.StaticFiles` 這是靜態檔案處理常式。 ASP.NET Core apps 明確加入宣告中介軟體，例如提供靜態檔案。 如需詳細資訊，請參閱[靜態檔案](xref:fundamentals/static-files)。

* 開啟 *Startup.cs* 檔案，並變更程式碼以符合下列各項：

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>擴充方法會新增靜態檔案處理常式。 `UseMvc`擴充方法會新增路由。 如需詳細資訊，請參閱 [應用程式啟動](xref:fundamentals/startup) 和 [路由](xref:fundamentals/routing)。

## <a name="add-a-controller-and-view"></a>新增控制器和查看

在本節中，會新增最基本的控制器和視圖，作為 ASP.NET MVC 控制器的預留位置，以及下一節中所遷移的視圖。

* 新增 *控制器* 目錄。

* 將名為*HomeController.cs*的**控制器類別**新增至*控制器*目錄。

![[新增項目] 對話方塊](mvc/_static/add_mvc_ctl.png)

* 新增 *Views* 目錄。

* 新增 *Views/Home* 目錄。

* 將名為*Index. cshtml*的** Razor View**新增至*Views/Home*目錄。

![[新增項目] 對話方塊](mvc/_static/view.png)

專案結構如下所示：

![方案總管顯示 WebApp1 的檔案和目錄](mvc/_static/project-structure-controller-view.png)

將 *Views/Home/Index.cshtml* 檔案的內容取代為下列標記：

```html
<h1>Hello world!</h1>
```

執行應用程式。

![Web 應用程式在 Microsoft Edge 中開啟](mvc/_static/hello-world.png)

如需詳細資訊，請參閱 [控制器](xref:mvc/controllers/actions) 和 [Views](xref:mvc/views/overview)。

下列功能需要從範例 ASP.NET MVC 專案遷移至 ASP.NET Core 專案：

* 用戶端內容 (CSS、字型和腳本) 

* controllers

* 檢視

* 模型

* 捆綁

* filters

* 登入/登出， Identity (在下一個教學課程中完成這項操作。 ) 

## <a name="controllers-and-views"></a>控制器與視圖

* 將 ASP.NET MVC 中的每個方法複製 `HomeController` 到新的 `HomeController` 。 在 ASP.NET MVC 中，內建範本的控制器動作方法傳回型別為 <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; 在 ASP.NET CORE mvc 中，動作方法會 `IActionResult` 改為傳回。 `ActionResult` implements `IActionResult` ，因此不需要變更動作方法的傳回型別。

* 將 ASP.NET MVC 專案中的 *About*、 *Contact*和 *Index.* cshtml view 檔案複製 Razor 到 ASP.NET Core 專案。

## <a name="test-each-method"></a>測試每個方法

版面配置檔案和樣式尚未遷移，所以轉譯的視圖只會包含視圖檔案中的內容。 和 views 的版面配置檔案產生的連結 `About` `Contact` 將無法使用。

* 將目前的埠號碼取代為 ASP.NET core 專案中使用的埠號碼，以在執行 ASP.NET core 應用程式的瀏覽器中叫用轉譯的視圖。 例如： `https://localhost:44375/home/about` 。

![連絡人頁面](mvc/_static/contact-page.png)

請注意缺少樣式和功能表項目。 下一節將會修正樣式。

## <a name="static-content"></a>靜態內容

在 ASP.NET MVC 5 及更早版本中，靜態內容是由 Web 專案的根目錄所裝載，並與伺服器端檔案混合。 在 ASP.NET Core 中，靜態內容裝載于 *wwwroot* 目錄。 將靜態內容從 ASP.NET MVC 應用程式複製到 ASP.NET Core 專案中的 *wwwroot* 目錄。 在此範例轉換中：

* 將 *favicon .ico* 檔案從 ASP.NET MVC 專案複製到 ASP.NET Core 專案中的 *wwwroot* 目錄。

ASP.NET MVC 專案會使用 [啟動](https://getbootstrap.com/) 程式進行其樣式，並將啟動程式檔案儲存在 *內容* 和 *腳本* 目錄中。 產生 ASP.NET MVC 專案的範本會參考設定檔案中的啟動程式 (*Views/Shared/_Layout. cshtml*) 。 *bootstrap.js*和*啟動程式 .css*檔案可以從 ASP.NET MVC 專案複製到新專案中的*wwwroot*目錄。 相反地，本檔會在下一節中使用 Cdn 來新增啟動載入 (和其他用戶端程式庫的支援) 。

## <a name="migrate-the-layout-file"></a>遷移版面配置檔案

* 將 ASP.NET MVC 專案*views*目錄中的 *_ViewStart cshtml*檔案複製到 ASP.NET Core 專案的*views*目錄。 在 ASP.NET Core MVC 中， *_ViewStart 的 cshtml* 檔案尚未變更。

* 建立 *Views/Shared* 目錄。

* *選用：* 從*FullAspNetCore* MVC 專案的*views*目錄將 *_ViewImports. cshtml*複製到 ASP.NET Core 專案的*views*目錄。 移除 *_ViewImports cshtml* 檔案中的任何命名空間宣告。 *_ViewImports cshtml*檔案提供所有 view 檔案的命名空間，並引進標籤協助[程式。](xref:mvc/views/tag-helpers/intro) 標籤協助程式會在新的版面配置檔案中使用。 *_ViewImports 的 cshtml*檔案是 ASP.NET Core 的新功能。

* 將 ASP.NET MVC 專案的*views/shared*目錄中的 *_Layout cshtml*檔案複製到 ASP.NET Core 專案的*views/shared*目錄中。

開啟 *_Layout cshtml* 檔案，並進行下列變更 (完成的程式碼如下所示) ：

* 取代為 `@Styles.Render("~/Content/css")` 用 `<link>` 來載入開機載入程式的元素 *。 css* (請參閱下面) 。

* 移除 `@Scripts.Render("~/bundles/modernizr")`。

* `@Html.Partial("_LoginPartial")`使用) 將行批註 (程式程式碼 `@*...*@` 。 如需詳細資訊，請參閱 [遷移驗證和 Identity ASP.NET Core](xref:migration/identity)

* `@Scripts.Render("~/bundles/jquery")`以元素取代 `<script>` (請參閱以下) 。

* `@Scripts.Render("~/bundles/bootstrap")`以元素取代 `<script>` (請參閱以下) 。

啟動載入 CSS 的取代標記包含：

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

JQuery 和啟動程式 JavaScript 的取代標記包含：

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

更新的 *_Layout cshtml* 檔案如下所示：

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

在瀏覽器中查看網站。 它現在應該會正確載入，且需要適當的樣式。

* *選用：* 請嘗試使用新的版面配置檔案。 從 *FullAspNetCore* 專案複製版面配置檔案。 新的版面配置檔案 [使用卷](xref:mvc/views/tag-helpers/intro) 標協助程式，並有其他改良功能。

## <a name="configure-bundling-and-minification"></a>設定捆綁和縮制

如需有關如何設定包裝和縮制的詳細資訊，請參閱 [捆綁和縮制](xref:client-side/bundling-and-minification)。

## <a name="solve-http-500-errors"></a>解決 HTTP 500 錯誤

有許多問題可能會導致 HTTP 500 錯誤訊息，而不包含問題來源的相關資訊。 例如，如果 *Views/_ViewImports cshtml* 檔案包含不存在於專案中的命名空間，則會產生 HTTP 500 錯誤。 根據預設，在 ASP.NET Core apps 中， `UseDeveloperExceptionPage` 擴充功能會新增至，並在設定 `IApplicationBuilder` 為 *開發*時執行。 請參閱下列程式碼中的範例：

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core 將未處理的例外狀況轉換成 HTTP 500 錯誤回應。 一般來說，這些回應中不會包含錯誤詳細資料，以防止洩漏有關伺服器的潛在敏感性資訊。 如需詳細資訊，請參閱 [開發人員例外狀況頁面](xref:fundamentals/error-handling#developer-exception-page)。

## <a name="additional-resources"></a>其他資源

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
