---
title: 第2部分：將控制器新增至 ASP.NET Core MVC 應用程式
author: rick-anderson
description: ASP.NET Core MVC 之教學課程系列的第2部分。
ms.author: riande
ms.date: 01/23/2021
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
uid: tutorials/first-mvc-app/adding-controller
ms.custom: contperf-fy21q3
ms.openlocfilehash: 4afee8c09a3eb1030bf68e7591e1686b18d5f7e9
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394443"
---
# <a name="part-2-add-a-controller-to-an-aspnet-core-mvc-app"></a>第2部分：將控制器新增至 ASP.NET Core MVC 應用程式

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

模型檢視控制器 (MVC) 架構模式可將一個應用程式劃分成三個主要元件：模型 (**M**)、檢視 (**V**) 和控制器 (**C**)。 MVC 模式可協助您建立比傳統整合型應用程式更可測試且更易於更新的應用程式。

MVC 架構的應用程式包含：

* 模型 (**M**)：代表應用程式資料的類別。 模型類別使用驗證邏輯對該資料強制執行商務規則。 通常，模型物件會在資料庫中擷取並儲存模型狀態。 在本教學課程中，`Movie` 模型會從資料庫擷取電影資料，將其提供給檢視或更新它。 更新的資料會寫入資料庫。
* 檢視 (**V**)：檢視是顯示應用程式之使用者介面 (UI) 的元件。 一般而言，此 UI 會顯示模型資料。
* **C**) ：類別：
  * 處理瀏覽器要求。
  * 取出模型資料。
  * 呼叫會傳迴響應的視圖範本。

在 MVC 應用程式中，視圖只會顯示資訊。 控制器會處理和回應使用者輸入和互動。 例如，控制器會處理 URL 區段和查詢字串值，並將這些值傳遞至模型。 此模型可能會使用這些值來查詢資料庫。 例如：

* `https://localhost:5001/Home/Privacy`：指定 `Home` 控制器和 `Privacy` 動作。
* `https://localhost:5001/Movies/Edit/5`：這是使用 `Movies` 控制器和 `Edit` 動作（稍後在本教學課程中詳述）來編輯識別碼 = 5 之電影的要求。

本教學課程稍後會說明路由資料。

MVC 架構模式會將應用程式分成三個主要的元件群組：模型、視圖和控制器。 這種模式有助於達成關注點分離： UI 邏輯屬於視圖。 輸入邏輯位於控制器。 商務邏輯則位於模型。 建立應用程式時，這種區隔有助於管理複雜性，因為它可讓您一次在執行的某個層面上工作，而不會影響另一個的程式碼。 例如，您可以處理檢視程式碼，而不需要根據商務邏輯程式碼。

此教學課程系列將介紹這些概念，並在建立電影應用程式時加以示範。 MVC 專案包含｢控制器｣和｢檢視｣的資料夾。

## <a name="add-a-controller"></a>新增控制器

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在 [ **Solution Explorer**] 中，以滑鼠右鍵按一下 [ **控制器] > 新增 > 控制器**。

![方案 Explorer 中，以滑鼠右鍵按一下控制器 > 新增 > 控制器](~/tutorials/first-mvc-app/adding-controller/_static/add_controllercopyVS19v16.9.png)

在 [ **新增 Scaffold** ] 對話方塊中，選取 [ **MVC 控制器-空白**]。

![新增 MVC 控制器並將其命名](~/tutorials/first-mvc-app/adding-controller/_static/acCopyVS19v16.9.png)

在 [ **加入新專案-MvcMovie] 對話方塊** 中，輸入 **HelloWorldController.cs** ，然後選取 [ **加入**]。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

選取 **總管** 圖示，然後 Control+按一下 (按一下滑鼠右鍵) [控制器] > [新增檔案]，將新檔案命名為 *HelloWorldController.cs*。

![操作功能表](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_fileVSC1.51.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

在方案總管中，以滑鼠右鍵按一下 [控制器] > [新增] > [新增檔案]。

![操作功能表](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

選取 [ **ASP.NET 核心** 和 **控制器類別**]。

將控制器命名為 **HelloWorldController**。

![新增 MVC 控制器並將其命名](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

以下列內容取代 *Controllers/HelloWorldController.cs* 的內容：

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

控制器中的每個 `public` 方法可呼叫作為 HTTP 端點。 在上述範例中，這兩種方法都會傳回一個字串。 請注意每個方法之前的註解。

HTTP 端點：

* 是 web 應用程式中的目標設為 URL，例如 `https://localhost:5001/HelloWorld` 。
* 結合：
  * 使用的通訊協定： `HTTPS` 。
  * Web 服務器的網路位置，包括 TCP 埠： `localhost:5001` 。
  * 目標 URI： `HelloWorld` 。

第一個註解指出這是 [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) 方法，叫用方法是將 `/HelloWorld/` 附加至基底 URL。

第二個註解會指定 [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods) 方法，叫用方法是將 `/HelloWorld/Welcome/` 附加至 URL。 稍後在教學課程中，會使用樣板引擎來產生可 `HTTP POST` 更新資料的方法。

在沒有偵錯工具的情況下執行應用程式。

將 "HelloWorld" 附加至網址列中的路徑。 `Index` 方法會傳回一個字串。

![顯示應用程式回應的瀏覽器視窗是我的預設動作](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC 會根據傳入的 URL，叫用控制器類別以及它們內的動作方法。 MVC 使用的預設 [URL 路由邏輯](xref:mvc/controllers/routing) ，會使用如下的格式來判斷要叫用的程式碼：

`/[Controller]/[ActionName]/[Parameters]`

您可以在 *Startup.cs* 檔案的 `Configure` 方法中設定路由格式。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

當您瀏覽至應用程式而不提供任何 URL 區段時，則會預設為上方醒目提示之範本行中指定的 "Home" 控制器和 "Index" 方法。  在先前的 URL 區段中：

* 第一個 URL 區段決定要執行的控制器類別。 因此，`localhost:5001/HelloWorld` 會對應到 **HelloWorld** Controller 類別。
* URL 區段的第二部分則決定類別上的動作方法。 因此， `localhost:5001/HelloWorld/Index` 會讓 `Index` 類別的方法 `HelloWorldController` 執行。 請注意，您只需要瀏覽至 `localhost:5001/HelloWorld`，根據預設就會呼叫 `Index` 方法。 `Index` 這是未明確指定方法名稱時，將在控制器上呼叫的預設方法。
* URL 區段的第三個部分 (`id`) 是路由資料。 本教學課程稍後會說明路由資料。

流覽至： `https://localhost:{PORT}/HelloWorld/Welcome` 。 取代 `{PORT}` 為您的埠號碼。

`Welcome` 方法隨即執行，並傳回字串 `This is the Welcome action method...`。 在此 URL 中，控制器是 `HelloWorld`，而 `Welcome` 是動作方法。 您尚未使用 URL 的 `[Parameters]` 部分。

![顯示應用程式回應 "This is the Welcome action method" 的瀏覽器視窗](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

修改程式碼 ，將 URL 中的某些參數資訊傳遞到控制器。 例如： `/HelloWorld/Welcome?name=Rick&numtimes=4` 。

變更 `Welcome` 方法以包含兩個參數，如下列程式碼所示。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

上述程式碼：

* 使用 C# 選擇性參數功能來指出若未針對 `numTimes` 參數傳遞任何值時，該參數預設為 1。
* 用 `HtmlEncoder.Default.Encode` 來保護應用程式免于惡意輸入，例如透過 JavaScript。
* 在 `$"Hello {name}, NumTimes is: {numTimes}"` 中使用[字串插值](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)。

執行應用程式，並流覽至： `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4` 。 取代 `{PORT}` 為您的埠號碼。

`name`在 URL 中為和嘗試不同的值 `numtimes` 。 MVC [模型](xref:mvc/models/model-binding) 系結系統會自動將查詢字串中的具名引數對應至方法中的參數。 如需詳細資訊，請參閱[模型繫結](xref:mvc/models/model-binding)。

![顯示 Hello Rick 的應用程式回應的瀏覽器視窗，Numtimes is 是 \: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

在上圖中：

* `Parameters`未使用 URL 區段。
* `name`和 `numTimes` 參數會在[查詢字串](https://wikipedia.org/wiki/Query_string)中傳遞。
* `?`上述 URL 中的 (問號) 是分隔符號，而查詢字串如下所示。
* `&`字元會分隔欄位-值配對。

以下列程式碼取代 `Welcome` 方法：

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

執行應用程式，並輸入下列 URL：`https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

在上述 URL 中：

* 第三個 URL 區段符合路由參數 `id` 。 
* `Welcome` 方法包含符合 `MapControllerRoute` 方法中 URL 範本的 `id` 參數。
* 尾端會 `?` 開始 [查詢字串](https://wikipedia.org/wiki/Query_string)。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Startup.cs?name=snippet_route&highlight=5)]

在上述範例中：

* 第三個 URL 區段符合路由參數 `id` 。
* `Welcome` 方法包含符合 `MapControllerRoute` 方法中 URL 範本的 `id` 參數。
* 結尾的 `?` (在 `id?` 中) 表示 `id` 是選擇性參數。

> [!div class="step-by-step"]
> [上一步：開始](start-mvc.md) 
>  使用[下一步：新增視圖](adding-view.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

模型檢視控制器 (MVC) 架構模式可將一個應用程式劃分成三個主要元件：模型 (**M**)、檢視 (**V**) 和控制器 (**C**)。 MVC 模式可協助您建立比傳統整合型應用程式更可測試且更易於更新的應用程式。 MVC 架構的應用程式包含：

* 模型 (**M**)：代表應用程式資料的類別。 模型類別使用驗證邏輯對該資料強制執行商務規則。 通常，模型物件會在資料庫中擷取並儲存模型狀態。 在本教學課程中，`Movie` 模型會從資料庫擷取電影資料，將其提供給檢視或更新它。 更新的資料會寫入資料庫。

* 檢視 (**V**)：檢視是顯示應用程式之使用者介面 (UI) 的元件。 一般而言，此 UI 會顯示模型資料。

* 控制器 (**C**)：處理瀏覽器要求的類別。 它們會擷取模型資料，並呼叫傳回回應的檢視範本。 在 MVC 應用程式中，檢視只能顯示資訊；控制器則會處理和回應使用者輸入和互動。 例如，控制器會處理路由資料和查詢字串值，並將這些值傳遞至模型。 此模型可能會使用這些值來查詢資料庫。 例如，`https://localhost:5001/Home/About` 具有 `Home` (控制器) 和 `About` (在首頁控制器上呼叫的動作方法) 的路由資料。 `https://localhost:5001/Movies/Edit/5` 是要使用電影控制器編輯識別碼 = 5 之電影的要求。 本教學課程稍後會說明路由資料。

MVC 模式可協助您建立應用程式，用來隔離應用程的不同層面 (輸入邏輯、商務邏輯和 UI 邏輯)，同時提供這些項目之間的鬆散結合。 此模式指定每一種邏輯應該位於應用程式中的位置。 UI 邏輯位於檢視。 輸入邏輯位於控制器。 商務邏輯則位於模型。 這項隔離可協助您管理建置應用程式時的複雜度，因為它可讓您一次處理實作的其中一個層面，而不影響另一個層面的程式碼。 例如，您可以處理檢視程式碼，而不需要根據商務邏輯程式碼。

本教學課程系列中涵蓋了這些概念，並且顯示如何使用它們來建置電影應用程式。 MVC 專案包含｢控制器｣和｢檢視｣的資料夾。

## <a name="add-a-controller"></a>新增控制器

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在 [ **方案 Explorer**] 中，以滑鼠右鍵按一下 [ **控制器] > 新增 > 控制器**

  ![操作功能表](~/tutorials/first-mvc-app/adding-controller/_static/add_controller.png)

* 在 [新增 Scaffold] 對話方塊中，選取 [MVC 控制器 - 空白]

  ![新增 MVC 控制器並將其命名](~/tutorials/first-mvc-app/adding-controller/_static/ac.png)

* 在 [Add Empty MVC Controller] \(新增空白 MVC 控制器\) 對話方塊中，輸入 **HelloWorldController**，然後選取 [新增]。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

選取 **總管** 圖示，然後 Control+按一下 (按一下滑鼠右鍵) [控制器] > [新增檔案]，將新檔案命名為 *HelloWorldController.cs*。

  ![操作功能表](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

在方案總管中，以滑鼠右鍵按一下 [控制器] > [新增] > [新增檔案]。

![操作功能表](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

選取 [ASP.NET Core] 和 [MVC 控制器類別]。

將控制器命名為 **HelloWorldController**。

![新增 MVC 控制器並將其命名](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

以下列內容取代 *Controllers/HelloWorldController.cs* 的內容：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

控制器中的每個 `public` 方法可呼叫作為 HTTP 端點。 在上述範例中，這兩種方法都會傳回一個字串。 請注意每個方法之前的註解。

HTTP 端點是 Web 應用程式中的可設定目標 URL，例如 `https://localhost:5001/HelloWorld`，其結合使用的通訊協定：`HTTPS`、網頁伺服器的網路位置 (包括 TCP 連接埠)：`localhost:5001`，以及目標 URI `HelloWorld`。

第一個註解指出這是 [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) 方法，叫用方法是將 `/HelloWorld/` 附加至基底 URL。 第二個註解會指定 [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) 方法，叫用方法是將 `/HelloWorld/Welcome/` 附加至 URL。 稍後在本教學課程中，您將使用 Scaffolding 引擎產生 `HTTP POST` 方法來更新資料。

在非偵錯模式中執行應用程式，並將 "HelloWorld" 附加至網址列中的路徑。 `Index` 方法會傳回一個字串。

![顯示應用程式回應 "This is my default action" 的瀏覽器視窗](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC 會根據傳入 URL 叫用控制器類別 (和其中的動作方法)。 MVC 使用的預設 [URL 路由邏輯](xref:mvc/controllers/routing)使用像這樣的格式來判斷要叫用的程式碼：

`/[Controller]/[ActionName]/[Parameters]`

您可以在 *Startup.cs* 檔案的 `Configure` 方法中設定路由格式。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

當您瀏覽至應用程式而不提供任何 URL 區段時，則會預設為上方醒目提示之範本行中指定的 "Home" 控制器和 "Index" 方法。

第一個 URL 區段決定要執行的控制器類別。 因此，`localhost:{PORT}/HelloWorld` 會對應至 `HelloWorldController` 類別。 URL 區段的第二部分則決定類別上的動作方法。 因此，`localhost:{PORT}/HelloWorld/Index` 會導致 `HelloWorldController` 類別的 `Index` 方法執行。 請注意，您只需要瀏覽至 `localhost:{PORT}/HelloWorld`，根據預設就會呼叫 `Index` 方法。 這是因為 `Index` 是未明確指定方法名稱時，在控制器上呼叫的預設方法。 URL 區段的第三個部分 (`id`) 是路由資料。 本教學課程稍後會說明路由資料。

瀏覽至 `https://localhost:{PORT}/HelloWorld/Welcome`。 `Welcome` 方法隨即執行，並傳回字串 `This is the Welcome action method...`。 在此 URL 中，控制器是 `HelloWorld`，而 `Welcome` 是動作方法。 您尚未使用 URL 的 `[Parameters]` 部分。

![顯示應用程式回應 "This is the Welcome action method" 的瀏覽器視窗](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

修改程式碼 ，將 URL 中的某些參數資訊傳遞到控制器。 例如： `/HelloWorld/Welcome?name=Rick&numtimes=4` 。 變更 `Welcome` 方法以包含兩個參數，如下列程式碼所示。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

上述程式碼：

* 使用 C# 選擇性參數功能來指出若未針對 `numTimes` 參數傳遞任何值時，該參數預設為 1。 <!-- remove for simplified -->
* 使用 `HtmlEncoder.Default.Encode` 來保護應用程式免於遭受惡意輸入 (也就是 JavaScript) 攻擊。
* 在 `$"Hello {name}, NumTimes is: {numTimes}"` 中使用[字串插值](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)。 <!-- remove for simplified -->

執行應用程式，然後瀏覽至：

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

 (取代 `{PORT}` 為您的埠號碼。 ) 您可以 `name` `numtimes` 在 URL 中嘗試使用不同的值。 MVC [模型繫結](xref:mvc/models/model-binding)系統會自動將網址列上查詢字串中的具名參數對應至方法中的參數。 如需詳細資訊，請參閱[模型繫結](xref:mvc/models/model-binding)。

![顯示 Hello Rick 的應用程式回應的瀏覽器視窗，Numtimes is 是 \: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

在上圖中， `Parameters` 不會使用 () 的 URL 區段， `name` 而是 `numTimes` 在 [查詢字串](https://wikipedia.org/wiki/Query_string)中傳遞和參數。 `?`上述 URL 中的 (問號) 是分隔符號，而查詢字串如下所示。 `&`字元會分隔欄位-值配對。

以下列程式碼取代 `Welcome` 方法：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

執行應用程式，並輸入下列 URL：`https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

此時，第三個 URL 區段符合路由參數 `id`。 `Welcome` 方法包含符合 `MapRoute` 方法中 URL 範本的 `id` 參數。 結尾的 `?` (在 `id?` 中) 表示 `id` 是選擇性參數。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

在這些範例中，控制器已執行 MVC 的 "VC" 部分，也就是檢視和控制器工作。 控制器會直接傳回 HTML。 一般來說，您不希望控制器直接傳回 HTML，因為撰寫程式碼和維護會變得很麻煩。 相反地，您通常會使用個別的 Razor view 範本檔案來協助產生 HTML 回應。 您可在接下來的教學課程中這麼做。

> [!div class="step-by-step"]
> [上一個](start-mvc.md) 
> [下一步](adding-view.md)

::: moniker-end
