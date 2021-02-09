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
ms.openlocfilehash: 47bb9b96bd5565a3a67f3cbdf9a4b6bc1f987447
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975257"
---
# <a name="part-2-add-a-controller-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="0d256-103">第2部分：將控制器新增至 ASP.NET Core MVC 應用程式</span><span class="sxs-lookup"><span data-stu-id="0d256-103">Part 2, add a controller to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="0d256-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0d256-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0d256-105">模型檢視控制器 (MVC) 架構模式可將一個應用程式劃分成三個主要元件：模型 (**M**)、檢視 (**V**) 和控制器 (**C**)。</span><span class="sxs-lookup"><span data-stu-id="0d256-105">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="0d256-106">MVC 模式可協助您建立比傳統整合型應用程式更可測試且更易於更新的應用程式。</span><span class="sxs-lookup"><span data-stu-id="0d256-106">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span>

<span data-ttu-id="0d256-107">MVC 架構的應用程式包含：</span><span class="sxs-lookup"><span data-stu-id="0d256-107">MVC-based apps contain:</span></span>

* <span data-ttu-id="0d256-108">模型 (**M**)：代表應用程式資料的類別。</span><span class="sxs-lookup"><span data-stu-id="0d256-108">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="0d256-109">模型類別使用驗證邏輯對該資料強制執行商務規則。</span><span class="sxs-lookup"><span data-stu-id="0d256-109">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="0d256-110">通常，模型物件會在資料庫中擷取並儲存模型狀態。</span><span class="sxs-lookup"><span data-stu-id="0d256-110">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="0d256-111">在本教學課程中，`Movie` 模型會從資料庫擷取電影資料，將其提供給檢視或更新它。</span><span class="sxs-lookup"><span data-stu-id="0d256-111">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="0d256-112">更新的資料會寫入資料庫。</span><span class="sxs-lookup"><span data-stu-id="0d256-112">Updated data is written to a database.</span></span>
* <span data-ttu-id="0d256-113">檢視 (**V**)：檢視是顯示應用程式之使用者介面 (UI) 的元件。</span><span class="sxs-lookup"><span data-stu-id="0d256-113">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="0d256-114">一般而言，此 UI 會顯示模型資料。</span><span class="sxs-lookup"><span data-stu-id="0d256-114">Generally, this UI displays the model data.</span></span>
* <span data-ttu-id="0d256-115">**C**) ：類別：</span><span class="sxs-lookup"><span data-stu-id="0d256-115">**C** ontrollers: Classes that:</span></span>
  * <span data-ttu-id="0d256-116">處理瀏覽器要求。</span><span class="sxs-lookup"><span data-stu-id="0d256-116">Handle browser requests.</span></span>
  * <span data-ttu-id="0d256-117">取出模型資料。</span><span class="sxs-lookup"><span data-stu-id="0d256-117">Retrieve model data.</span></span>
  * <span data-ttu-id="0d256-118">呼叫會傳迴響應的視圖範本。</span><span class="sxs-lookup"><span data-stu-id="0d256-118">Call view templates that return a response.</span></span>

<span data-ttu-id="0d256-119">在 MVC 應用程式中，視圖只會顯示資訊。</span><span class="sxs-lookup"><span data-stu-id="0d256-119">In an MVC app, the view only displays information.</span></span> <span data-ttu-id="0d256-120">控制器會處理和回應使用者輸入和互動。</span><span class="sxs-lookup"><span data-stu-id="0d256-120">The controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="0d256-121">例如，控制器會處理 URL 區段和查詢字串值，並將這些值傳遞至模型。</span><span class="sxs-lookup"><span data-stu-id="0d256-121">For example, the controller handles URL segments and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="0d256-122">此模型可能會使用這些值來查詢資料庫。</span><span class="sxs-lookup"><span data-stu-id="0d256-122">The model might use these values to query the database.</span></span> <span data-ttu-id="0d256-123">例如：</span><span class="sxs-lookup"><span data-stu-id="0d256-123">For example:</span></span>

* <span data-ttu-id="0d256-124">`Https://localhost:5001/Home/Privacy`：指定 `Home`  控制器和 `Privacy` 動作。</span><span class="sxs-lookup"><span data-stu-id="0d256-124">`Https://localhost:5001/Home/Privacy`: specifies the `Home`  controller  and the `Privacy` action.</span></span>
* <span data-ttu-id="0d256-125">`Https://localhost:5001/Movies/Edit/5`：這是使用 `Movies` 控制器和 `Edit` 動作（稍後在本教學課程中詳述）來編輯識別碼 = 5 之電影的要求。</span><span class="sxs-lookup"><span data-stu-id="0d256-125">`Https://localhost:5001/Movies/Edit/5`: is a request to edit the movie with ID=5 using the `Movies` controller and the `Edit` action, which are detailed later in the tutorial.</span></span>

<span data-ttu-id="0d256-126">本教學課程稍後會說明路由資料。</span><span class="sxs-lookup"><span data-stu-id="0d256-126">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="0d256-127">MVC 架構模式會將應用程式分成三個主要的元件群組：模型、視圖和控制器。</span><span class="sxs-lookup"><span data-stu-id="0d256-127">The MVC architectural pattern separates an app into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="0d256-128">這種模式有助於達成關注點分離： UI 邏輯屬於視圖。</span><span class="sxs-lookup"><span data-stu-id="0d256-128">This pattern helps to achieve separation of concerns: The UI logic belongs in the view.</span></span> <span data-ttu-id="0d256-129">輸入邏輯位於控制器。</span><span class="sxs-lookup"><span data-stu-id="0d256-129">Input logic belongs in the controller.</span></span> <span data-ttu-id="0d256-130">商務邏輯則位於模型。</span><span class="sxs-lookup"><span data-stu-id="0d256-130">Business logic belongs in the model.</span></span> <span data-ttu-id="0d256-131">建立應用程式時，這種區隔有助於管理複雜性，因為它可讓您一次在執行的某個層面上工作，而不會影響另一個的程式碼。</span><span class="sxs-lookup"><span data-stu-id="0d256-131">This separation helps manage complexity when building an app, because it enables work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="0d256-132">例如，您可以處理檢視程式碼，而不需要根據商務邏輯程式碼。</span><span class="sxs-lookup"><span data-stu-id="0d256-132">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="0d256-133">此教學課程系列將介紹這些概念，並在建立電影應用程式時加以示範。</span><span class="sxs-lookup"><span data-stu-id="0d256-133">These concepts are introduced and demonstrated in this tutorial series while building a movie app.</span></span> <span data-ttu-id="0d256-134">MVC 專案包含｢控制器｣和｢檢視｣的資料夾。</span><span class="sxs-lookup"><span data-stu-id="0d256-134">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="0d256-135">新增控制器</span><span class="sxs-lookup"><span data-stu-id="0d256-135">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d256-136">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d256-136">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0d256-137">在 **方案總管** 中，以滑鼠右鍵按一下 [ **控制器] > 新增 > 控制器**。</span><span class="sxs-lookup"><span data-stu-id="0d256-137">In the **Solution Explorer**, right-click **Controllers > Add > Controller**.</span></span>

![方案總管中，以滑鼠右鍵按一下 [控制器] > 新增 > 控制器](~/tutorials/first-mvc-app/adding-controller/_static/add_controllercopyVS19v16.9.png)

<span data-ttu-id="0d256-139">在 [ **新增 Scaffold** ] 對話方塊中，選取 [ **MVC 控制器-空白**]。</span><span class="sxs-lookup"><span data-stu-id="0d256-139">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**.</span></span>

![新增 MVC 控制器並將其命名](~/tutorials/first-mvc-app/adding-controller/_static/acCopyVS19v16.9.png)

<span data-ttu-id="0d256-141">在 [ **加入新專案-MvcMovie] 對話方塊** 中，輸入 **HelloWorldController.cs** ，然後選取 [ **加入**]。</span><span class="sxs-lookup"><span data-stu-id="0d256-141">In the **Add New Item - MvcMovie dialog**, enter **HelloWorldController.cs** and select **Add**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0d256-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d256-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0d256-143">選取 **總管** 圖示，然後 Control+按一下 (按一下滑鼠右鍵) [控制器] > [新增檔案]，將新檔案命名為 *HelloWorldController.cs*。</span><span class="sxs-lookup"><span data-stu-id="0d256-143">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

![操作功能表](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_fileVSC1.51.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d256-145">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0d256-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0d256-146">在方案總管中，以滑鼠右鍵按一下 [控制器] > [新增] > [新增檔案]。</span><span class="sxs-lookup"><span data-stu-id="0d256-146">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>

![操作功能表](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

<span data-ttu-id="0d256-148">選取 **ASP.NET Core** 和 **控制器類別**。</span><span class="sxs-lookup"><span data-stu-id="0d256-148">Select **ASP.NET Core** and **Controller Class**.</span></span>

<span data-ttu-id="0d256-149">將控制器命名為 **HelloWorldController**。</span><span class="sxs-lookup"><span data-stu-id="0d256-149">Name the controller **HelloWorldController**.</span></span>

![新增 MVC 控制器並將其命名](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="0d256-151">以下列內容取代 *Controllers/HelloWorldController.cs* 的內容：</span><span class="sxs-lookup"><span data-stu-id="0d256-151">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="0d256-152">控制器中的每個 `public` 方法可呼叫作為 HTTP 端點。</span><span class="sxs-lookup"><span data-stu-id="0d256-152">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="0d256-153">在上述範例中，這兩種方法都會傳回一個字串。</span><span class="sxs-lookup"><span data-stu-id="0d256-153">In the sample above, both methods return a string.</span></span> <span data-ttu-id="0d256-154">請注意每個方法之前的註解。</span><span class="sxs-lookup"><span data-stu-id="0d256-154">Note the comments preceding each method.</span></span>

<span data-ttu-id="0d256-155">HTTP 端點：</span><span class="sxs-lookup"><span data-stu-id="0d256-155">An HTTP endpoint:</span></span>

* <span data-ttu-id="0d256-156">是 web 應用程式中的目標設為 URL，例如 `https://localhost:5001/HelloWorld` 。</span><span class="sxs-lookup"><span data-stu-id="0d256-156">Is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`.</span></span>
* <span data-ttu-id="0d256-157">結合：</span><span class="sxs-lookup"><span data-stu-id="0d256-157">Combines:</span></span>
  * <span data-ttu-id="0d256-158">使用的通訊協定： `HTTPS` 。</span><span class="sxs-lookup"><span data-stu-id="0d256-158">The protocol used: `HTTPS`.</span></span>
  * <span data-ttu-id="0d256-159">Web 服務器的網路位置，包括 TCP 埠： `localhost:5001` 。</span><span class="sxs-lookup"><span data-stu-id="0d256-159">The network location of the web server, including the TCP port: `localhost:5001`.</span></span>
  * <span data-ttu-id="0d256-160">目標 URI： `HelloWorld` 。</span><span class="sxs-lookup"><span data-stu-id="0d256-160">The target URI: `HelloWorld`.</span></span>

<span data-ttu-id="0d256-161">第一個註解指出這是 [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) 方法，叫用方法是將 `/HelloWorld/` 附加至基底 URL。</span><span class="sxs-lookup"><span data-stu-id="0d256-161">The first comment states this is an [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span>

<span data-ttu-id="0d256-162">第二個註解會指定 [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods) 方法，叫用方法是將 `/HelloWorld/Welcome/` 附加至 URL。</span><span class="sxs-lookup"><span data-stu-id="0d256-162">The second comment specifies an [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="0d256-163">稍後在教學課程中，會使用樣板引擎來產生可 `HTTP POST` 更新資料的方法。</span><span class="sxs-lookup"><span data-stu-id="0d256-163">Later on in the tutorial, the scaffolding engine is used to generate `HTTP POST` methods, which update data.</span></span>

<span data-ttu-id="0d256-164">在沒有偵錯工具的情況下執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="0d256-164">Run the app without the debugger.</span></span>

<span data-ttu-id="0d256-165">將 "HelloWorld" 附加至網址列中的路徑。</span><span class="sxs-lookup"><span data-stu-id="0d256-165">Append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="0d256-166">`Index` 方法會傳回一個字串。</span><span class="sxs-lookup"><span data-stu-id="0d256-166">The `Index` method returns a string.</span></span>

![顯示應用程式回應的瀏覽器視窗是我的預設動作](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="0d256-168">MVC 會根據傳入的 URL，叫用控制器類別以及它們內的動作方法。</span><span class="sxs-lookup"><span data-stu-id="0d256-168">MVC invokes controller classes, and the action methods within them, depending on the incoming URL.</span></span> <span data-ttu-id="0d256-169">MVC 使用的預設 [URL 路由邏輯](xref:mvc/controllers/routing) ，會使用如下的格式來判斷要叫用的程式碼：</span><span class="sxs-lookup"><span data-stu-id="0d256-169">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC, uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="0d256-170">您可以在 *Startup.cs* 檔案的 `Configure` 方法中設定路由格式。</span><span class="sxs-lookup"><span data-stu-id="0d256-170">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="0d256-171">當您瀏覽至應用程式而不提供任何 URL 區段時，則會預設為上方醒目提示之範本行中指定的 "Home" 控制器和 "Index" 方法。</span><span class="sxs-lookup"><span data-stu-id="0d256-171">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>  <span data-ttu-id="0d256-172">在先前的 URL 區段中：</span><span class="sxs-lookup"><span data-stu-id="0d256-172">In the preceding URL segments:</span></span>

* <span data-ttu-id="0d256-173">第一個 URL 區段決定要執行的控制器類別。</span><span class="sxs-lookup"><span data-stu-id="0d256-173">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="0d256-174">因此，`localhost:5001/HelloWorld` 會對應到 **HelloWorld** Controller 類別。</span><span class="sxs-lookup"><span data-stu-id="0d256-174">So `localhost:5001/HelloWorld` maps to the **HelloWorld** Controller class.</span></span>
* <span data-ttu-id="0d256-175">URL 區段的第二部分則決定類別上的動作方法。</span><span class="sxs-lookup"><span data-stu-id="0d256-175">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="0d256-176">因此， `localhost:5001/HelloWorld/Index` 會讓 `Index` 類別的方法 `HelloWorldController` 執行。</span><span class="sxs-lookup"><span data-stu-id="0d256-176">So `localhost:5001/HelloWorld/Index` causes the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="0d256-177">請注意，您只需要瀏覽至 `localhost:5001/HelloWorld`，根據預設就會呼叫 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="0d256-177">Notice that you only had to browse to `localhost:5001/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="0d256-178">`Index` 這是未明確指定方法名稱時，將在控制器上呼叫的預設方法。</span><span class="sxs-lookup"><span data-stu-id="0d256-178">`Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span>
* <span data-ttu-id="0d256-179">URL 區段的第三個部分 (`id`) 是路由資料。</span><span class="sxs-lookup"><span data-stu-id="0d256-179">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="0d256-180">本教學課程稍後會說明路由資料。</span><span class="sxs-lookup"><span data-stu-id="0d256-180">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="0d256-181">流覽至： `https://localhost:{PORT}/HelloWorld/Welcome` 。</span><span class="sxs-lookup"><span data-stu-id="0d256-181">Browse to: `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="0d256-182">取代 `{PORT}` 為您的埠號碼。</span><span class="sxs-lookup"><span data-stu-id="0d256-182">Replace `{PORT}` with your port number.</span></span>

<span data-ttu-id="0d256-183">`Welcome` 方法隨即執行，並傳回字串 `This is the Welcome action method...`。</span><span class="sxs-lookup"><span data-stu-id="0d256-183">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="0d256-184">在此 URL 中，控制器是 `HelloWorld`，而 `Welcome` 是動作方法。</span><span class="sxs-lookup"><span data-stu-id="0d256-184">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="0d256-185">您尚未使用 URL 的 `[Parameters]` 部分。</span><span class="sxs-lookup"><span data-stu-id="0d256-185">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![顯示應用程式回應 "This is the Welcome action method" 的瀏覽器視窗](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="0d256-187">修改程式碼 ，將 URL 中的某些參數資訊傳遞到控制器。</span><span class="sxs-lookup"><span data-stu-id="0d256-187">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="0d256-188">例如： `/HelloWorld/Welcome?name=Rick&numtimes=4` 。</span><span class="sxs-lookup"><span data-stu-id="0d256-188">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span>

<span data-ttu-id="0d256-189">變更 `Welcome` 方法以包含兩個參數，如下列程式碼所示。</span><span class="sxs-lookup"><span data-stu-id="0d256-189">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="0d256-190">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="0d256-190">The preceding code:</span></span>

* <span data-ttu-id="0d256-191">使用 C# 選擇性參數功能來指出若未針對 `numTimes` 參數傳遞任何值時，該參數預設為 1。</span><span class="sxs-lookup"><span data-stu-id="0d256-191">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span>
* <span data-ttu-id="0d256-192">用 `HtmlEncoder.Default.Encode` 來保護應用程式免于惡意輸入，例如透過 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="0d256-192">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input, such as through JavaScript.</span></span>
* <span data-ttu-id="0d256-193">在 `$"Hello {name}, NumTimes is: {numTimes}"` 中使用[字串插值](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)。</span><span class="sxs-lookup"><span data-stu-id="0d256-193">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span>

<span data-ttu-id="0d256-194">執行應用程式，並流覽至： `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4` 。</span><span class="sxs-lookup"><span data-stu-id="0d256-194">Run the app and browse to: `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="0d256-195">取代 `{PORT}` 為您的埠號碼。</span><span class="sxs-lookup"><span data-stu-id="0d256-195">Replace `{PORT}` with your port number.</span></span>

<span data-ttu-id="0d256-196">`name`在 URL 中為和嘗試不同的值 `numtimes` 。</span><span class="sxs-lookup"><span data-stu-id="0d256-196">Try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="0d256-197">MVC [模型](xref:mvc/models/model-binding) 系結系統會自動將查詢字串中的具名引數對應至方法中的參數。</span><span class="sxs-lookup"><span data-stu-id="0d256-197">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string to parameters in the method.</span></span> <span data-ttu-id="0d256-198">如需詳細資訊，請參閱[模型繫結](xref:mvc/models/model-binding)。</span><span class="sxs-lookup"><span data-stu-id="0d256-198">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![顯示 Hello Rick 的應用程式回應的瀏覽器視窗，Numtimes is 是 \: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="0d256-200">在上圖中：</span><span class="sxs-lookup"><span data-stu-id="0d256-200">In the previous image:</span></span>

* <span data-ttu-id="0d256-201">`Parameters`未使用 URL 區段。</span><span class="sxs-lookup"><span data-stu-id="0d256-201">The URL segment `Parameters` isn't used.</span></span>
* <span data-ttu-id="0d256-202">`name`和 `numTimes` 參數會在[查詢字串](https://wikipedia.org/wiki/Query_string)中傳遞。</span><span class="sxs-lookup"><span data-stu-id="0d256-202">The `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span>
* <span data-ttu-id="0d256-203">`?`上述 URL 中的 (問號) 是分隔符號，而查詢字串如下所示。</span><span class="sxs-lookup"><span data-stu-id="0d256-203">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span>
* <span data-ttu-id="0d256-204">`&`字元會分隔欄位-值配對。</span><span class="sxs-lookup"><span data-stu-id="0d256-204">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="0d256-205">以下列程式碼取代 `Welcome` 方法：</span><span class="sxs-lookup"><span data-stu-id="0d256-205">Replace the `Welcome` method with the following code:</span></span>

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="0d256-206">執行應用程式，並輸入下列 URL：`https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="0d256-206">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="0d256-207">在上述 URL 中：</span><span class="sxs-lookup"><span data-stu-id="0d256-207">In the preceding URL:</span></span>

* <span data-ttu-id="0d256-208">第三個 URL 區段符合路由參數 `id` 。</span><span class="sxs-lookup"><span data-stu-id="0d256-208">The third URL segment matched the route parameter `id`.</span></span> 
* <span data-ttu-id="0d256-209">`Welcome` 方法包含符合 `MapControllerRoute` 方法中 URL 範本的 `id` 參數。</span><span class="sxs-lookup"><span data-stu-id="0d256-209">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span>
* <span data-ttu-id="0d256-210">結尾的 `?` (在 `id?` 中) 表示 `id` 是選擇性參數。</span><span class="sxs-lookup"><span data-stu-id="0d256-210">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Startup.cs?name=snippet_route&highlight=5)]

<span data-ttu-id="0d256-211">在上述範例中：</span><span class="sxs-lookup"><span data-stu-id="0d256-211">In the preceding example:</span></span>

* <span data-ttu-id="0d256-212">第三個 URL 區段符合路由參數 `id` 。</span><span class="sxs-lookup"><span data-stu-id="0d256-212">The third URL segment matched the route parameter `id`.</span></span>
* <span data-ttu-id="0d256-213">`Welcome` 方法包含符合 `MapControllerRoute` 方法中 URL 範本的 `id` 參數。</span><span class="sxs-lookup"><span data-stu-id="0d256-213">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span>
* <span data-ttu-id="0d256-214">結尾的 `?` (在 `id?` 中) 表示 `id` 是選擇性參數。</span><span class="sxs-lookup"><span data-stu-id="0d256-214">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="0d256-215">[上一步：開始](start-mvc.md) 
> [下一步：新增視圖](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="0d256-215">[Previous: Get Started](start-mvc.md)
[Next: Add a View](adding-view.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0d256-216">模型檢視控制器 (MVC) 架構模式可將一個應用程式劃分成三個主要元件：模型 (**M**)、檢視 (**V**) 和控制器 (**C**)。</span><span class="sxs-lookup"><span data-stu-id="0d256-216">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="0d256-217">MVC 模式可協助您建立比傳統整合型應用程式更可測試且更易於更新的應用程式。</span><span class="sxs-lookup"><span data-stu-id="0d256-217">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span> <span data-ttu-id="0d256-218">MVC 架構的應用程式包含：</span><span class="sxs-lookup"><span data-stu-id="0d256-218">MVC-based apps contain:</span></span>

* <span data-ttu-id="0d256-219">模型 (**M**)：代表應用程式資料的類別。</span><span class="sxs-lookup"><span data-stu-id="0d256-219">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="0d256-220">模型類別使用驗證邏輯對該資料強制執行商務規則。</span><span class="sxs-lookup"><span data-stu-id="0d256-220">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="0d256-221">通常，模型物件會在資料庫中擷取並儲存模型狀態。</span><span class="sxs-lookup"><span data-stu-id="0d256-221">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="0d256-222">在本教學課程中，`Movie` 模型會從資料庫擷取電影資料，將其提供給檢視或更新它。</span><span class="sxs-lookup"><span data-stu-id="0d256-222">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="0d256-223">更新的資料會寫入資料庫。</span><span class="sxs-lookup"><span data-stu-id="0d256-223">Updated data is written to a database.</span></span>

* <span data-ttu-id="0d256-224">檢視 (**V**)：檢視是顯示應用程式之使用者介面 (UI) 的元件。</span><span class="sxs-lookup"><span data-stu-id="0d256-224">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="0d256-225">一般而言，此 UI 會顯示模型資料。</span><span class="sxs-lookup"><span data-stu-id="0d256-225">Generally, this UI displays the model data.</span></span>

* <span data-ttu-id="0d256-226">控制器 (**C**)：處理瀏覽器要求的類別。</span><span class="sxs-lookup"><span data-stu-id="0d256-226">**C** ontrollers: Classes that handle browser requests.</span></span> <span data-ttu-id="0d256-227">它們會擷取模型資料，並呼叫傳回回應的檢視範本。</span><span class="sxs-lookup"><span data-stu-id="0d256-227">They retrieve model data and call view templates that return a response.</span></span> <span data-ttu-id="0d256-228">在 MVC 應用程式中，檢視只能顯示資訊；控制器則會處理和回應使用者輸入和互動。</span><span class="sxs-lookup"><span data-stu-id="0d256-228">In an MVC app, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="0d256-229">例如，控制器會處理路由資料和查詢字串值，並將這些值傳遞至模型。</span><span class="sxs-lookup"><span data-stu-id="0d256-229">For example, the controller handles route data and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="0d256-230">此模型可能會使用這些值來查詢資料庫。</span><span class="sxs-lookup"><span data-stu-id="0d256-230">The model might use these values to query the database.</span></span> <span data-ttu-id="0d256-231">例如，`https://localhost:5001/Home/About` 具有 `Home` (控制器) 和 `About` (在首頁控制器上呼叫的動作方法) 的路由資料。</span><span class="sxs-lookup"><span data-stu-id="0d256-231">For example, `https://localhost:5001/Home/About` has route data of `Home` (the controller) and `About` (the action method to call on the home controller).</span></span> <span data-ttu-id="0d256-232">`https://localhost:5001/Movies/Edit/5` 是要使用電影控制器編輯識別碼 = 5 之電影的要求。</span><span class="sxs-lookup"><span data-stu-id="0d256-232">`https://localhost:5001/Movies/Edit/5` is a request to edit the movie with ID=5 using the movie controller.</span></span> <span data-ttu-id="0d256-233">本教學課程稍後會說明路由資料。</span><span class="sxs-lookup"><span data-stu-id="0d256-233">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="0d256-234">MVC 模式可協助您建立應用程式，用來隔離應用程的不同層面 (輸入邏輯、商務邏輯和 UI 邏輯)，同時提供這些項目之間的鬆散結合。</span><span class="sxs-lookup"><span data-stu-id="0d256-234">The MVC pattern helps you create apps that separate the different aspects of the app (input logic, business logic, and UI logic), while providing a loose coupling between these elements.</span></span> <span data-ttu-id="0d256-235">此模式指定每一種邏輯應該位於應用程式中的位置。</span><span class="sxs-lookup"><span data-stu-id="0d256-235">The pattern specifies where each kind of logic should be located in the app.</span></span> <span data-ttu-id="0d256-236">UI 邏輯位於檢視。</span><span class="sxs-lookup"><span data-stu-id="0d256-236">The UI logic belongs in the view.</span></span> <span data-ttu-id="0d256-237">輸入邏輯位於控制器。</span><span class="sxs-lookup"><span data-stu-id="0d256-237">Input logic belongs in the controller.</span></span> <span data-ttu-id="0d256-238">商務邏輯則位於模型。</span><span class="sxs-lookup"><span data-stu-id="0d256-238">Business logic belongs in the model.</span></span> <span data-ttu-id="0d256-239">這項隔離可協助您管理建置應用程式時的複雜度，因為它可讓您一次處理實作的其中一個層面，而不影響另一個層面的程式碼。</span><span class="sxs-lookup"><span data-stu-id="0d256-239">This separation helps you manage complexity when you build an app, because it enables you to work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="0d256-240">例如，您可以處理檢視程式碼，而不需要根據商務邏輯程式碼。</span><span class="sxs-lookup"><span data-stu-id="0d256-240">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="0d256-241">本教學課程系列中涵蓋了這些概念，並且顯示如何使用它們來建置電影應用程式。</span><span class="sxs-lookup"><span data-stu-id="0d256-241">We cover these concepts in this tutorial series and show you how to use them to build a movie app.</span></span> <span data-ttu-id="0d256-242">MVC 專案包含｢控制器｣和｢檢視｣的資料夾。</span><span class="sxs-lookup"><span data-stu-id="0d256-242">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="0d256-243">新增控制器</span><span class="sxs-lookup"><span data-stu-id="0d256-243">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d256-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d256-244">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0d256-245">在 **方案總管** 中，以滑鼠右鍵按一下 [ **控制器] > 新增 > 控制器**</span><span class="sxs-lookup"><span data-stu-id="0d256-245">In **Solution Explorer**, right-click **Controllers > Add > Controller**</span></span>

  ![操作功能表](~/tutorials/first-mvc-app/adding-controller/_static/add_controller.png)

* <span data-ttu-id="0d256-247">在 [新增 Scaffold] 對話方塊中，選取 [MVC 控制器 - 空白]</span><span class="sxs-lookup"><span data-stu-id="0d256-247">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**</span></span>

  ![新增 MVC 控制器並將其命名](~/tutorials/first-mvc-app/adding-controller/_static/ac.png)

* <span data-ttu-id="0d256-249">在 [Add Empty MVC Controller] \(新增空白 MVC 控制器\) 對話方塊中，輸入 **HelloWorldController**，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="0d256-249">In the **Add Empty MVC Controller dialog**, enter **HelloWorldController** and select **ADD**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0d256-250">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d256-250">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0d256-251">選取 **總管** 圖示，然後 Control+按一下 (按一下滑鼠右鍵) [控制器] > [新增檔案]，將新檔案命名為 *HelloWorldController.cs*。</span><span class="sxs-lookup"><span data-stu-id="0d256-251">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

  ![操作功能表](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d256-253">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0d256-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0d256-254">在方案總管中，以滑鼠右鍵按一下 [控制器] > [新增] > [新增檔案]。</span><span class="sxs-lookup"><span data-stu-id="0d256-254">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>

![操作功能表](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

<span data-ttu-id="0d256-256">選取 [ASP.NET Core] 和 [MVC 控制器類別]。</span><span class="sxs-lookup"><span data-stu-id="0d256-256">Select **ASP.NET Core** and **MVC Controller Class**.</span></span>

<span data-ttu-id="0d256-257">將控制器命名為 **HelloWorldController**。</span><span class="sxs-lookup"><span data-stu-id="0d256-257">Name the controller **HelloWorldController**.</span></span>

![新增 MVC 控制器並將其命名](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="0d256-259">以下列內容取代 *Controllers/HelloWorldController.cs* 的內容：</span><span class="sxs-lookup"><span data-stu-id="0d256-259">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="0d256-260">控制器中的每個 `public` 方法可呼叫作為 HTTP 端點。</span><span class="sxs-lookup"><span data-stu-id="0d256-260">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="0d256-261">在上述範例中，這兩種方法都會傳回一個字串。</span><span class="sxs-lookup"><span data-stu-id="0d256-261">In the sample above, both methods return a string.</span></span> <span data-ttu-id="0d256-262">請注意每個方法之前的註解。</span><span class="sxs-lookup"><span data-stu-id="0d256-262">Note the comments preceding each method.</span></span>

<span data-ttu-id="0d256-263">HTTP 端點是 Web 應用程式中的可設定目標 URL，例如 `https://localhost:5001/HelloWorld`，其結合使用的通訊協定：`HTTPS`、網頁伺服器的網路位置 (包括 TCP 連接埠)：`localhost:5001`，以及目標 URI `HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="0d256-263">An HTTP endpoint is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`, and combines the protocol used: `HTTPS`, the network location of the web server (including the TCP port): `localhost:5001` and the target URI `HelloWorld`.</span></span>

<span data-ttu-id="0d256-264">第一個註解指出這是 [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) 方法，叫用方法是將 `/HelloWorld/` 附加至基底 URL。</span><span class="sxs-lookup"><span data-stu-id="0d256-264">The first comment states this is an [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span> <span data-ttu-id="0d256-265">第二個註解會指定 [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) 方法，叫用方法是將 `/HelloWorld/Welcome/` 附加至 URL。</span><span class="sxs-lookup"><span data-stu-id="0d256-265">The second comment specifies an [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="0d256-266">稍後在本教學課程中，您將使用 Scaffolding 引擎產生 `HTTP POST` 方法來更新資料。</span><span class="sxs-lookup"><span data-stu-id="0d256-266">Later on in the tutorial the scaffolding engine is used to generate `HTTP POST` methods which update data.</span></span>

<span data-ttu-id="0d256-267">在非偵錯模式中執行應用程式，並將 "HelloWorld" 附加至網址列中的路徑。</span><span class="sxs-lookup"><span data-stu-id="0d256-267">Run the app in non-debug mode and append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="0d256-268">`Index` 方法會傳回一個字串。</span><span class="sxs-lookup"><span data-stu-id="0d256-268">The `Index` method returns a string.</span></span>

![顯示應用程式回應 "This is my default action" 的瀏覽器視窗](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="0d256-270">MVC 會根據傳入 URL 叫用控制器類別 (和其中的動作方法)。</span><span class="sxs-lookup"><span data-stu-id="0d256-270">MVC invokes controller classes (and the action methods within them) depending on the incoming URL.</span></span> <span data-ttu-id="0d256-271">MVC 使用的預設 [URL 路由邏輯](xref:mvc/controllers/routing)使用像這樣的格式來判斷要叫用的程式碼：</span><span class="sxs-lookup"><span data-stu-id="0d256-271">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="0d256-272">您可以在 *Startup.cs* 檔案的 `Configure` 方法中設定路由格式。</span><span class="sxs-lookup"><span data-stu-id="0d256-272">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

<span data-ttu-id="0d256-273">當您瀏覽至應用程式而不提供任何 URL 區段時，則會預設為上方醒目提示之範本行中指定的 "Home" 控制器和 "Index" 方法。</span><span class="sxs-lookup"><span data-stu-id="0d256-273">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>

<span data-ttu-id="0d256-274">第一個 URL 區段決定要執行的控制器類別。</span><span class="sxs-lookup"><span data-stu-id="0d256-274">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="0d256-275">因此，`localhost:{PORT}/HelloWorld` 會對應至 `HelloWorldController` 類別。</span><span class="sxs-lookup"><span data-stu-id="0d256-275">So `localhost:{PORT}/HelloWorld` maps to the `HelloWorldController` class.</span></span> <span data-ttu-id="0d256-276">URL 區段的第二部分則決定類別上的動作方法。</span><span class="sxs-lookup"><span data-stu-id="0d256-276">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="0d256-277">因此，`localhost:{PORT}/HelloWorld/Index` 會導致 `HelloWorldController` 類別的 `Index` 方法執行。</span><span class="sxs-lookup"><span data-stu-id="0d256-277">So `localhost:{PORT}/HelloWorld/Index` would cause the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="0d256-278">請注意，您只需要瀏覽至 `localhost:{PORT}/HelloWorld`，根據預設就會呼叫 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="0d256-278">Notice that you only had to browse to `localhost:{PORT}/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="0d256-279">這是因為 `Index` 是未明確指定方法名稱時，在控制器上呼叫的預設方法。</span><span class="sxs-lookup"><span data-stu-id="0d256-279">This is because `Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span> <span data-ttu-id="0d256-280">URL 區段的第三個部分 (`id`) 是路由資料。</span><span class="sxs-lookup"><span data-stu-id="0d256-280">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="0d256-281">本教學課程稍後會說明路由資料。</span><span class="sxs-lookup"><span data-stu-id="0d256-281">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="0d256-282">瀏覽至 `https://localhost:{PORT}/HelloWorld/Welcome`。</span><span class="sxs-lookup"><span data-stu-id="0d256-282">Browse to `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="0d256-283">`Welcome` 方法隨即執行，並傳回字串 `This is the Welcome action method...`。</span><span class="sxs-lookup"><span data-stu-id="0d256-283">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="0d256-284">在此 URL 中，控制器是 `HelloWorld`，而 `Welcome` 是動作方法。</span><span class="sxs-lookup"><span data-stu-id="0d256-284">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="0d256-285">您尚未使用 URL 的 `[Parameters]` 部分。</span><span class="sxs-lookup"><span data-stu-id="0d256-285">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![顯示應用程式回應 "This is the Welcome action method" 的瀏覽器視窗](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="0d256-287">修改程式碼 ，將 URL 中的某些參數資訊傳遞到控制器。</span><span class="sxs-lookup"><span data-stu-id="0d256-287">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="0d256-288">例如： `/HelloWorld/Welcome?name=Rick&numtimes=4` 。</span><span class="sxs-lookup"><span data-stu-id="0d256-288">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="0d256-289">變更 `Welcome` 方法以包含兩個參數，如下列程式碼所示。</span><span class="sxs-lookup"><span data-stu-id="0d256-289">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="0d256-290">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="0d256-290">The preceding code:</span></span>

* <span data-ttu-id="0d256-291">使用 C# 選擇性參數功能來指出若未針對 `numTimes` 參數傳遞任何值時，該參數預設為 1。</span><span class="sxs-lookup"><span data-stu-id="0d256-291">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span> <!-- remove for simplified -->
* <span data-ttu-id="0d256-292">使用 `HtmlEncoder.Default.Encode` 來保護應用程式免於遭受惡意輸入 (也就是 JavaScript) 攻擊。</span><span class="sxs-lookup"><span data-stu-id="0d256-292">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input (namely JavaScript).</span></span>
* <span data-ttu-id="0d256-293">在 `$"Hello {name}, NumTimes is: {numTimes}"` 中使用[字串插值](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)。</span><span class="sxs-lookup"><span data-stu-id="0d256-293">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span> <!-- remove for simplified -->

<span data-ttu-id="0d256-294">執行應用程式，然後瀏覽至：</span><span class="sxs-lookup"><span data-stu-id="0d256-294">Run the app and browse to:</span></span>

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="0d256-295"> (取代 `{PORT}` 為您的埠號碼。 ) 您可以 `name` `numtimes` 在 URL 中嘗試使用不同的值。</span><span class="sxs-lookup"><span data-stu-id="0d256-295">(Replace `{PORT}` with your port number.) You can try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="0d256-296">MVC [模型繫結](xref:mvc/models/model-binding)系統會自動將網址列上查詢字串中的具名參數對應至方法中的參數。</span><span class="sxs-lookup"><span data-stu-id="0d256-296">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="0d256-297">如需詳細資訊，請參閱[模型繫結](xref:mvc/models/model-binding)。</span><span class="sxs-lookup"><span data-stu-id="0d256-297">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![顯示 Hello Rick 的應用程式回應的瀏覽器視窗，Numtimes is 是 \: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="0d256-299">在上圖中， `Parameters` 不會使用 () 的 URL 區段， `name` 而是 `numTimes` 在 [查詢字串](https://wikipedia.org/wiki/Query_string)中傳遞和參數。</span><span class="sxs-lookup"><span data-stu-id="0d256-299">In the image above, the URL segment (`Parameters`) isn't used, the `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span> <span data-ttu-id="0d256-300">`?`上述 URL 中的 (問號) 是分隔符號，而查詢字串如下所示。</span><span class="sxs-lookup"><span data-stu-id="0d256-300">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span> <span data-ttu-id="0d256-301">`&`字元會分隔欄位-值配對。</span><span class="sxs-lookup"><span data-stu-id="0d256-301">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="0d256-302">以下列程式碼取代 `Welcome` 方法：</span><span class="sxs-lookup"><span data-stu-id="0d256-302">Replace the `Welcome` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="0d256-303">執行應用程式，並輸入下列 URL：`https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="0d256-303">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="0d256-304">此時，第三個 URL 區段符合路由參數 `id`。</span><span class="sxs-lookup"><span data-stu-id="0d256-304">This time the third URL segment matched the route parameter `id`.</span></span> <span data-ttu-id="0d256-305">`Welcome` 方法包含符合 `MapRoute` 方法中 URL 範本的 `id` 參數。</span><span class="sxs-lookup"><span data-stu-id="0d256-305">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapRoute` method.</span></span> <span data-ttu-id="0d256-306">結尾的 `?` (在 `id?` 中) 表示 `id` 是選擇性參數。</span><span class="sxs-lookup"><span data-stu-id="0d256-306">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="0d256-307">在這些範例中，控制器已執行 MVC 的 "VC" 部分，也就是檢視和控制器工作。</span><span class="sxs-lookup"><span data-stu-id="0d256-307">In these examples the controller has been doing the "VC" portion of MVC - that is, the view and controller work.</span></span> <span data-ttu-id="0d256-308">控制器會直接傳回 HTML。</span><span class="sxs-lookup"><span data-stu-id="0d256-308">The controller is returning HTML directly.</span></span> <span data-ttu-id="0d256-309">一般來說，您不希望控制器直接傳回 HTML，因為撰寫程式碼和維護會變得很麻煩。</span><span class="sxs-lookup"><span data-stu-id="0d256-309">Generally you don't want controllers returning HTML directly, since that becomes very cumbersome to code and maintain.</span></span> <span data-ttu-id="0d256-310">相反地，您通常會使用個別的 Razor view 範本檔案來協助產生 HTML 回應。</span><span class="sxs-lookup"><span data-stu-id="0d256-310">Instead you typically use a separate Razor view template file to help generate the HTML response.</span></span> <span data-ttu-id="0d256-311">您可在接下來的教學課程中這麼做。</span><span class="sxs-lookup"><span data-stu-id="0d256-311">You do that in the next tutorial.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="0d256-312">[上一個](start-mvc.md) 
> [下一步](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="0d256-312">[Previous](start-mvc.md)
[Next](adding-view.md)</span></span>

::: moniker-end
