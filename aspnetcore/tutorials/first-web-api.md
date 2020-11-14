---
title: 教學課程：使用 ASP.NET Core 建立 web API
author: rick-anderson
description: 了解如何使用 ASP.NET Core 建置 Web API。
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
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
- 'Models'
uid: tutorials/first-web-api
ms.openlocfilehash: 569744b2cbec062ec9abab9db1c94960f1f912e7
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570246"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="b2ad0-103">教學課程：使用 ASP.NET Core 建立 web API</span><span class="sxs-lookup"><span data-stu-id="b2ad0-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="b2ad0-104">由 [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Kirk Larkin](https://twitter.com/serpent5)和 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="b2ad0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="b2ad0-105">本教學課程將教導您使用 ASP.NET Core 建立 Web API 的基本概念。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b2ad0-106">在本教學課程中，您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b2ad0-107">建立 Web API 專案。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-107">Create a web API project.</span></span>
> * <span data-ttu-id="b2ad0-108">新增模型類別和資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="b2ad0-109">使用 CRUD 方法 Scaffold 控制器。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="b2ad0-110">設定路由、URL 路徑和傳回值。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="b2ad0-111">使用 Postman 呼叫 Web API。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-111">Call the web API with Postman.</span></span>

<span data-ttu-id="b2ad0-112">結束時，您會有一個 Web API，可以管理儲存在資料庫中的「待辦事項」。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="b2ad0-113">概觀</span><span class="sxs-lookup"><span data-stu-id="b2ad0-113">Overview</span></span>

<span data-ttu-id="b2ad0-114">本教學課程會建立以下 API：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="b2ad0-115">API</span><span class="sxs-lookup"><span data-stu-id="b2ad0-115">API</span></span> | <span data-ttu-id="b2ad0-116">描述</span><span class="sxs-lookup"><span data-stu-id="b2ad0-116">Description</span></span> | <span data-ttu-id="b2ad0-117">Request body</span><span class="sxs-lookup"><span data-stu-id="b2ad0-117">Request body</span></span> | <span data-ttu-id="b2ad0-118">回應本文</span><span class="sxs-lookup"><span data-stu-id="b2ad0-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="b2ad0-119">取得所有待辦事項</span><span class="sxs-lookup"><span data-stu-id="b2ad0-119">Get all to-do items</span></span> | <span data-ttu-id="b2ad0-120">無</span><span class="sxs-lookup"><span data-stu-id="b2ad0-120">None</span></span> | <span data-ttu-id="b2ad0-121">待辦事項的陣列</span><span class="sxs-lookup"><span data-stu-id="b2ad0-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="b2ad0-122">依識別碼取得項目</span><span class="sxs-lookup"><span data-stu-id="b2ad0-122">Get an item by ID</span></span> | <span data-ttu-id="b2ad0-123">無</span><span class="sxs-lookup"><span data-stu-id="b2ad0-123">None</span></span> | <span data-ttu-id="b2ad0-124">待辦事項</span><span class="sxs-lookup"><span data-stu-id="b2ad0-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="b2ad0-125">新增記錄</span><span class="sxs-lookup"><span data-stu-id="b2ad0-125">Add a new item</span></span> | <span data-ttu-id="b2ad0-126">待辦事項</span><span class="sxs-lookup"><span data-stu-id="b2ad0-126">To-do item</span></span> | <span data-ttu-id="b2ad0-127">待辦事項</span><span class="sxs-lookup"><span data-stu-id="b2ad0-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="b2ad0-128">更新現有的項目 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b2ad0-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="b2ad0-129">待辦事項</span><span class="sxs-lookup"><span data-stu-id="b2ad0-129">To-do item</span></span> | <span data-ttu-id="b2ad0-130">無</span><span class="sxs-lookup"><span data-stu-id="b2ad0-130">None</span></span> |
|<span data-ttu-id="b2ad0-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b2ad0-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="b2ad0-132">刪除專案 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="b2ad0-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="b2ad0-133">無</span><span class="sxs-lookup"><span data-stu-id="b2ad0-133">None</span></span> | <span data-ttu-id="b2ad0-134">無</span><span class="sxs-lookup"><span data-stu-id="b2ad0-134">None</span></span>|

<span data-ttu-id="b2ad0-135">下圖顯示應用程式的設計。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-135">The following diagram shows the design of the app.</span></span>

![左側方塊代表用戶端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="b2ad0-141">必要條件</span><span class="sxs-lookup"><span data-stu-id="b2ad0-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2ad0-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ad0-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2ad0-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2ad0-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2ad0-144">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2ad0-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="b2ad0-145">建立 Web 專案</span><span class="sxs-lookup"><span data-stu-id="b2ad0-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2ad0-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ad0-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b2ad0-147">從 [ **檔案** ] 功能表選取 [ **新增** > **專案** ]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b2ad0-148">選取 **ASP.NET Core Web 應用程式** 範本，然後按一下 [下一步]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="b2ad0-149">將專案命名為 *TodoApi* ，然後按一下 [建立]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="b2ad0-150">在 [ **建立新的 ASP.NET Core Web 應用程式** ] 對話方塊中，確認已選取 [ **.net Core** ] 和 [ **ASP.NET Core 5.0** ]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="b2ad0-151">選取 **API** 範本，然後按一下 [建立]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-151">Select the **API** template and click **Create**.</span></span>

![VS 新增專案對話方塊](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2ad0-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2ad0-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b2ad0-154">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b2ad0-155">將目錄 (`cd`) 變更為包含專案資料夾的資料夾。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="b2ad0-156">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="b2ad0-157">當出現對話方塊詢問您是否要將所需的資產新增至專案時，選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="b2ad0-158">上述命令：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-158">The preceding commands:</span></span>

  * <span data-ttu-id="b2ad0-159">建立新的 Web API 專案，然後在 Visual Studio Code 中予以開啟。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="b2ad0-160">新增下一節需要的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2ad0-161">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2ad0-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b2ad0-162">選取 [檔案] **[新增解決方案]** > 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-162">Select **File** > **New Solution**.</span></span>

  ![macOS 新增方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="b2ad0-164">在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用程式**  >  **API**  >  **]** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="b2ad0-165">在8.6 版或更新版本中，選取 [ **Web] 和 [主控台**  >  **應用程式**  >  **API**  >  **]** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 範本選取專案](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="b2ad0-167">在 [ **設定新的 ASP.NET Core WEB API** ] 對話方塊中，選取最新的 .net Core 5.X **目標 Framework** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="b2ad0-168">選取 [下一步]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-168">Select **Next**.</span></span>

* <span data-ttu-id="b2ad0-169">針對 [專案名稱] 輸入 *TodoApi* ，然後選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![設定對話方塊](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="b2ad0-171">在專案資料夾中開啟命令終端機，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="b2ad0-172">測試專案</span><span class="sxs-lookup"><span data-stu-id="b2ad0-172">Test the project</span></span>

<span data-ttu-id="b2ad0-173">專案範本會建立 `WeatherForecast` 支援 [Swagger](xref:tutorials/web-api-help-pages-using-swagger)的 API。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2ad0-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ad0-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b2ad0-175">按 Ctrl+F5 即可執行而不使用偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="b2ad0-176">Visual Studio 啟動：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-176">Visual Studio launches:</span></span>

* <span data-ttu-id="b2ad0-177">IIS Express web 伺服器。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-177">The IIS Express web server.</span></span>
* <span data-ttu-id="b2ad0-178">預設瀏覽器並流覽至 `https://localhost:<port>/https://localhost:5001/swagger/index.html` ，其中 `<port>` 是隨機播放的埠號碼。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-178">The default browser and navigates to `https://localhost:<port>/https://localhost:5001/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2ad0-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2ad0-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="b2ad0-180">按 Ctrl+F5 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b2ad0-181">在瀏覽器中，移至下列 URL： [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="b2ad0-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2ad0-182">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2ad0-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b2ad0-183">選取 [ **執行**  >  **開始調試** 程式] 以啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="b2ad0-184">Visual Studio for Mac 會啟動瀏覽器並巡覽至 `https://localhost:<port>`，其中 `<port>` 是隨機選擇的連接埠號碼。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="b2ad0-185">傳回 HTTP 404 (找不到) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="b2ad0-186">將 `/swagger` 附加至 URL (將 URL 變更為 `https://localhost:<port>/swagger`)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="b2ad0-187">Swagger 頁面隨即 `/swagger/index.html` 顯示。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="b2ad0-188">選取 [ **立即**  >  **試用** ]  >  **執行** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="b2ad0-189">頁面會顯示：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-189">The page displays:</span></span>

* <span data-ttu-id="b2ad0-190">用來測試 WeatherForecast API 的 [捲曲](https://curl.haxx.se/) 命令。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="b2ad0-191">用來測試 WeatherForecast API 的 URL。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="b2ad0-192">回應碼、主體和標頭。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="b2ad0-193">具有媒體類型的下拉式清單方塊，以及範例值和架構。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="b2ad0-194">Swagger 可用來產生 web Api 的實用檔和說明頁面。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="b2ad0-195">本教學課程著重于建立 web API。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="b2ad0-196">如需 Swagger 的詳細資訊，請參閱 <xref:tutorials/web-api-help-pages-using-swagger> 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="b2ad0-197">複製並超過瀏覽器中的 **要求 URL** ：  `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="b2ad0-197">Copy and past the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="b2ad0-198">系統會傳回與下列類似的 JSON：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-198">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

### <a name="update-the-launchurl"></a><span data-ttu-id="b2ad0-199">更新 launchUrl</span><span class="sxs-lookup"><span data-stu-id="b2ad0-199">Update the launchUrl</span></span>

<span data-ttu-id="b2ad0-200">在 *Properties\launchSettings.js開啟* ，請 `launchUrl` 從更新 `"swagger"` 為 `"api/TodoItems"` ：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-200">In *Properties\launchSettings.json* , update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="b2ad0-201">由於 Swagger 已移除，因此上述標記會將啟動的 URL 變更為在下列各節中新增之控制器的 GET 方法。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="b2ad0-202">新增模型類別</span><span class="sxs-lookup"><span data-stu-id="b2ad0-202">Add a model class</span></span>

<span data-ttu-id="b2ad0-203">「模型」是代表應用程式所管理資料的一組類別。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="b2ad0-204">此應用程式的模型是單一 `TodoItem` 類別。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2ad0-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ad0-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b2ad0-206">在 **方案總管** 中，以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-206">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="b2ad0-207">選取 **[**  >  **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b2ad0-208">為資料夾命名 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="b2ad0-209">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [ **加入**  >  **類別** ]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b2ad0-210">將類別命名為 *TodoItem* ，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="b2ad0-211">以下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2ad0-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2ad0-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b2ad0-213">新增名為的資料夾 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="b2ad0-214">`TodoItem`使用下列程式碼，將類別新增至 *Models* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2ad0-215">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2ad0-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b2ad0-216">以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-216">Right-click the project.</span></span> <span data-ttu-id="b2ad0-217">選取 **[**  >  **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b2ad0-218">為資料夾命名 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-218">Name the folder *Models*.</span></span>

  ![新增資料夾](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="b2ad0-220">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 **Add** > [ **新增** 檔案 > **一般** ] > **空白類別** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="b2ad0-221">將類別命名為 *TodoItem* ，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-221">Name the class *TodoItem* , and then click **New**.</span></span>

* <span data-ttu-id="b2ad0-222">以下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="b2ad0-223">`Id` 屬性的功能相當於關聯式資料庫中的唯一索引鍵。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="b2ad0-224">模型類別可移至專案中的任何位置，但依照慣例，會 *Models* 使用資料夾。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="b2ad0-225">新增資料庫內容</span><span class="sxs-lookup"><span data-stu-id="b2ad0-225">Add a database context</span></span>

<span data-ttu-id="b2ad0-226">「資料庫內容」是為資料模型協調 Entity Framework 功能的主要類別。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="b2ad0-227">此類別是透過衍生自 <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> 類別來建立。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2ad0-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ad0-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="b2ad0-229">新增 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="b2ad0-229">Add NuGet packages</span></span>

* <span data-ttu-id="b2ad0-230">在 [工具] 功能表上，選取 [NuGet 套件管理員] > [管理解決方案的 NuGet 套件]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="b2ad0-231">選取 [ **流覽** ] 索引標籤，然後輸入 \* \* Microsoft。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-231">Select the **Browse** tab, and then enter \*\*Microsoft.</span></span>
<span data-ttu-id="b2ad0-232">**Microsoft.entityframeworkcore** 在搜尋方塊中。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-232">**EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="b2ad0-233">選取 [ **包含發行** 前版本] 核取方塊，讓 5.0 RC 版本可供使用。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-233">Select the **Include prerelease** checkbox so the 5.0 RC version is available.</span></span> 
* <span data-ttu-id="b2ad0-234">選取左窗格中的 [ **microsoft.entityframeworkcore** ]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-234">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="b2ad0-235">選取右窗格中的 [專案] 核取方塊，然後選取 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-235">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="b2ad0-236">使用上述指示來新增 **Microsoft.entityframeworkcore InMemory** NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-236">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="b2ad0-237">![NuGet 套件管理員](first-web-api/_static/5/vsNuGet.png) (英文)</span><span class="sxs-lookup"><span data-stu-id="b2ad0-237">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="b2ad0-238">新增 TodoCoNtext 資料庫內容</span><span class="sxs-lookup"><span data-stu-id="b2ad0-238">Add the TodoContext database context</span></span>

* <span data-ttu-id="b2ad0-239">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [ **加入**  >  **類別** ]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-239">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b2ad0-240">將類別命名為 *TodoContext* ，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-240">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b2ad0-241">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2ad0-241">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b2ad0-242">將 `TodoContext` 類別新增至 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-242">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="b2ad0-243">輸入下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-243">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="b2ad0-244">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="b2ad0-244">Register the database context</span></span>

<span data-ttu-id="b2ad0-245">在 ASP.NET Core 中，資料庫內容等服務必須向[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器註冊。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-245">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b2ad0-246">此容器會將服務提供給控制器。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-246">The container provides the service to controllers.</span></span>

<span data-ttu-id="b2ad0-247">使用下列程式碼更新 *Startup.cs* ：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-247">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="b2ad0-248">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-248">The preceding code:</span></span>

* <span data-ttu-id="b2ad0-249">移除 Swagger 呼叫。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-249">Removes the Swagger calls.</span></span>
* <span data-ttu-id="b2ad0-250">移除未使用的 `using` 宣告。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-250">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="b2ad0-251">將資料庫內容新增至 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-251">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="b2ad0-252">指定資料庫內容將會使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-252">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="b2ad0-253">Scaffold 控制器</span><span class="sxs-lookup"><span data-stu-id="b2ad0-253">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2ad0-254">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ad0-254">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b2ad0-255">以滑鼠右鍵按一下 *Controllers* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-255">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="b2ad0-256">選取 [新增] **[新增 Scaffold 項目]** > 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-256">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b2ad0-257">選取 [使用 Entity Framework 執行動作的 API 控制器]，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-257">Select **API Controller with actions, using Entity Framework** , and then select **Add**.</span></span>
* <span data-ttu-id="b2ad0-258">在 [使用 Entity Framework 執行動作的 API 控制器] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-258">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="b2ad0-259">選取 **模型類別** 中的 [ **TodoItem (TodoApi] Models) 。**</span><span class="sxs-lookup"><span data-stu-id="b2ad0-259">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="b2ad0-260">選取 **資料內容類別** 中的 **TodoCoNtext (TodoApi Models) 。**</span><span class="sxs-lookup"><span data-stu-id="b2ad0-260">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="b2ad0-261">選取 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-261">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b2ad0-262">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2ad0-262">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="b2ad0-263">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-263">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="b2ad0-264">上述命令：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-264">The preceding commands:</span></span>

* <span data-ttu-id="b2ad0-265">新增 Scaffolding 所需的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-265">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="b2ad0-266">安裝 Scaffolding 引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-266">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="b2ad0-267">Scaffold `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-267">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="b2ad0-268">產生的程式碼：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-268">The generated code:</span></span>

* <span data-ttu-id="b2ad0-269">標記具有屬性的類別 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-269">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="b2ad0-270">這個屬性表示控制器會回應 Web API 要求。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-270">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="b2ad0-271">如需屬性所啟用之特定行為的相關資訊，請參閱 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-271">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="b2ad0-272">使用 DI 將資料庫內容 (`TodoContext`) 插入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-272">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="b2ad0-273">控制器中的每一個 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法都會使用資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-273">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="b2ad0-274">的 ASP.NET Core 範本：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-274">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="b2ad0-275">具有 views 的控制器包含 `[action]` 在路由範本中。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-275">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="b2ad0-276">API 控制器不包含 `[action]` 在路由範本中。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-276">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="b2ad0-277">當 `[action]` 權杖不在路由範本中時，會從路由中排除 [動作](xref:mvc/controllers/routing#action) 名稱。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-277">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="b2ad0-278">也就是，不會在相符的路由中使用動作的相關聯方法名稱。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-278">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="b2ad0-279">更新 PostTodoItem create 方法</span><span class="sxs-lookup"><span data-stu-id="b2ad0-279">Update the PostTodoItem create method</span></span>

<span data-ttu-id="b2ad0-280">取代 `PostTodoItem` 中的 return 陳述式，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 運算子：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-280">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="b2ad0-281">上述程式碼是 HTTP POST 方法，如屬性所指示 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-281">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="b2ad0-282">該方法會從 HTTP 要求本文取得待辦事項的值。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-282">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="b2ad0-283">如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-283">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="b2ad0-284"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-284">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="b2ad0-285">如果成功，則傳回 [HTTP 201 狀態碼](https://developer.mozilla.org/docs/Web/HTTP/Status/201) 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-285">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="b2ad0-286">對於可在伺服器上建立新資源的 HTTP POST 方法，其標準回應是 HTTP 201。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-286">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="b2ad0-287">將 [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 標頭新增至回應。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-287">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="b2ad0-288">`Location`標頭會指定新建立之待辦事項的[URI](https://developer.mozilla.org/docs/Glossary/URI) 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-288">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="b2ad0-289">如需詳細資訊，請參閱 [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (已建立 10.2.2 201)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-289">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="b2ad0-290">參考 `GetTodoItem` 動作以建立 `Location` 標頭的 URI。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-290">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="b2ad0-291">C# `nameof` 關鍵字是用來避免在 `CreatedAtAction` 呼叫中以硬式編碼方式寫入動作名稱。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-291">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="b2ad0-292">安裝 Postman</span><span class="sxs-lookup"><span data-stu-id="b2ad0-292">Install Postman</span></span>

<span data-ttu-id="b2ad0-293">本教學課程使用 Postman 來測試 Web API。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-293">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="b2ad0-294">安裝 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="b2ad0-294">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="b2ad0-295">啟動 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-295">Start the web app.</span></span>
* <span data-ttu-id="b2ad0-296">啟動 Postman。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-296">Start Postman.</span></span>
* <span data-ttu-id="b2ad0-297">停用 [SSL certificate verification] \(SSL 憑證驗證\)</span><span class="sxs-lookup"><span data-stu-id="b2ad0-297">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="b2ad0-298">從 [檔案] **[設定]** >  ([一般] 索引標籤)，停用 [SSL 憑證驗證]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-298">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="b2ad0-299">在測試控制器之後，請重新啟用 [SSL certificate verification] \(SSL 憑證驗證\)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-299">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="b2ad0-300">使用 Postman 測試 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="b2ad0-300">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="b2ad0-301">建立新的要求。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-301">Create a new request.</span></span>
* <span data-ttu-id="b2ad0-302">將 HTTP 方法設為 `POST`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-302">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="b2ad0-303">將 URI 設定為 `https://localhost:<port>/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-303">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="b2ad0-304">例如，`https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-304">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="b2ad0-305">選取 [Body] \(本文\) 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-305">Select the **Body** tab.</span></span>
* <span data-ttu-id="b2ad0-306">選取 [原始] 選項按鈕。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-306">Select the **raw** radio button.</span></span>
* <span data-ttu-id="b2ad0-307">將類型設定為 **JSON (application/json)** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-307">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="b2ad0-308">在要求本文中，針對待辦項目輸入 JSON：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-308">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="b2ad0-309">選取 [傳送]  。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-309">Select **Send**.</span></span>

  ![Postman 與建立要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="b2ad0-311">測試位置標頭 URI</span><span class="sxs-lookup"><span data-stu-id="b2ad0-311">Test the location header URI</span></span>

<span data-ttu-id="b2ad0-312">您可以在瀏覽器中測試位置標頭 URI。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-312">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="b2ad0-313">複製位置標頭 URI，並將其貼到瀏覽器中。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-313">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="b2ad0-314">若要在 Postman 中進行測試：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-314">To test in Postman:</span></span>

* <span data-ttu-id="b2ad0-315">在 [回應] 窗格中選取 [標頭] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-315">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="b2ad0-316">複製 [位置] 標頭值：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-316">Copy the **Location** header value:</span></span>

  ![Postman 主控台的 [標頭] 索引標籤](first-web-api/_static/3/create.png)

* <span data-ttu-id="b2ad0-318">將 HTTP 方法設為 `GET`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-318">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="b2ad0-319">將 URI 設定為 `https://localhost:<port>/api/TodoItems/1` 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-319">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="b2ad0-320">例如，`https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-320">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="b2ad0-321">選取 [傳送]  。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-321">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="b2ad0-322">檢查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="b2ad0-322">Examine the GET methods</span></span>

<span data-ttu-id="b2ad0-323">系統會執行兩個 GET 端點：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-323">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="b2ad0-324">從瀏覽器或 Postman 呼叫這兩個端點來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-324">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="b2ad0-325">例如：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-325">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="b2ad0-326">`GetTodoItems` 的呼叫會產生類似下列的回應：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-326">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="b2ad0-327">使用 Postman 測試 Get</span><span class="sxs-lookup"><span data-stu-id="b2ad0-327">Test Get with Postman</span></span>

* <span data-ttu-id="b2ad0-328">建立新的要求。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-328">Create a new request.</span></span>
* <span data-ttu-id="b2ad0-329">將 HTTP 方法設定為 **GET** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-329">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="b2ad0-330">將要求 URI 設定為 `https://localhost:<port>/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-330">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="b2ad0-331">例如，`https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-331">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="b2ad0-332">在 Postman 中，設定 [Two pane view] \(雙窗格檢視\)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-332">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="b2ad0-333">選取 [傳送]  。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-333">Select **Send**.</span></span>

<span data-ttu-id="b2ad0-334">這個應用程式會使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-334">This app uses an in-memory database.</span></span> <span data-ttu-id="b2ad0-335">如果應用程式在停止後再啟動，上述 GET 要求將不會傳回任何資料。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-335">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="b2ad0-336">如果沒有傳回任何資料，請將資料 [POST](#post) 到應用程式。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-336">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="b2ad0-337">傳送和 URL 路徑</span><span class="sxs-lookup"><span data-stu-id="b2ad0-337">Routing and URL paths</span></span>

<span data-ttu-id="b2ad0-338">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)屬性代表回應 HTTP GET 要求的方法。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-338">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="b2ad0-339">每個方法的 URL 路徑的建構方式如下：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-339">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="b2ad0-340">一開始在控制器的 `Route` 屬性中使用範本字串：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-340">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="b2ad0-341">以控制器的名稱取代 `[controller]`，也就是將控制器類別名稱減去 "Controller" 字尾。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-341">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="b2ad0-342">在此範例中，控制器類別名稱是 **TodoItems** Controller，因此控制器名稱是 "TodoItems"。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-342">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="b2ad0-343">ASP.NET Core [路由](xref:mvc/controllers/routing)不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-343">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="b2ad0-344">如果 `[HttpGet]` 屬性具有路由範本 (例如 `[HttpGet("products")]`)，請將其附加到路徑。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-344">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="b2ad0-345">此範例不使用範本。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-345">This sample doesn't use a template.</span></span> <span data-ttu-id="b2ad0-346">如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-346">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="b2ad0-347">在下列 `GetTodoItem` 方法中，`"{id}"` 是待辦事項唯一識別碼的預留位置變數。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-347">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="b2ad0-348">當叫 `GetTodoItem` 用時，會將 `"{id}"` URL 中的值提供給方法的 `id` 參數。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-348">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="b2ad0-349">傳回值</span><span class="sxs-lookup"><span data-stu-id="b2ad0-349">Return values</span></span>

<span data-ttu-id="b2ad0-350">和方法的傳回型 `GetTodoItems` 別 `GetTodoItem` 為 [ActionResult \<T> 類型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-350">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="b2ad0-351">ASP.NET Core 會自動將物件序列化為 [JSON](https://www.json.org/)，並將 JSON 寫入至回應訊息的本文。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-351">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="b2ad0-352">如果沒有任何未處理的例外狀況，則此傳回型別的回應碼為 [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-352">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="b2ad0-353">未處理的例外狀況會轉譯成 5xx 錯誤。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-353">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="b2ad0-354">`ActionResult` 傳回型別可代表各種 HTTP 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-354">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="b2ad0-355">例如，`GetTodoItem` 可傳回兩個不同的狀態值：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-355">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="b2ad0-356">如果沒有任何專案符合要求的識別碼，方法會傳回 [404 狀態](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 錯誤碼。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-356">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="b2ad0-357">否則，方法會傳回 200 與 JSON 回應本文。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-357">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="b2ad0-358">傳回 `item` 會導致 HTTP 200 回應。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-358">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="b2ad0-359">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="b2ad0-359">The PutTodoItem method</span></span>

<span data-ttu-id="b2ad0-360">檢查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-360">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="b2ad0-361">`PutTodoItem` 類似於 `PostTodoItem`，但是會使用 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-361">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="b2ad0-362">回應為 [204 (沒有內容) ](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-362">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="b2ad0-363">根據 HTTP 規格，PUT 要求需要用戶端傳送整個更新的實體，而不只是變更。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-363">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="b2ad0-364">若要支援部分更新，請使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-364">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="b2ad0-365">如果在呼叫 `PutTodoItem` 時發生錯誤，請呼叫 `GET` 以確保資料庫中有項目。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-365">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="b2ad0-366">測試 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="b2ad0-366">Test the PutTodoItem method</span></span>

<span data-ttu-id="b2ad0-367">這個範例會使用必須在每次啟動應用程式時初始化的記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-367">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="b2ad0-368">資料庫中必須有項目，您才能進行 PUT 呼叫。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-368">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="b2ad0-369">在進行 PUT 呼叫之前，請先呼叫 GET 以確保資料庫中有專案。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-369">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="b2ad0-370">更新識別碼為1的待辦事項，並將其名稱設定為 `"feed fish"` ：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-370">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="b2ad0-371">下圖顯示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-371">The following image shows the Postman update:</span></span>

![顯示「204 (沒有內容) 回應」的 Postman 主控台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="b2ad0-373">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="b2ad0-373">The DeleteTodoItem method</span></span>

<span data-ttu-id="b2ad0-374">檢查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-374">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="b2ad0-375">測試 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="b2ad0-375">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="b2ad0-376">使用 Postman 刪除待辦事項：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-376">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="b2ad0-377">將方法設定為 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-377">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="b2ad0-378">設定要刪除之物件的 URI (例如 `https://localhost:5001/api/TodoItems/1`) 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-378">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="b2ad0-379">選取 [傳送]  。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-379">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="b2ad0-380">防止過度張貼</span><span class="sxs-lookup"><span data-stu-id="b2ad0-380">Prevent over-posting</span></span>

<span data-ttu-id="b2ad0-381">範例應用程式目前會公開整個 `TodoItem` 物件。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-381">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="b2ad0-382">生產環境應用程式通常會限制使用模型子集來輸入和傳回的資料。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-382">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="b2ad0-383">這項功能有許多原因，而且安全性是主要的原因。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-383">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="b2ad0-384">模型的子集通常稱為資料傳輸物件 (DTO) 、輸入模型或視圖模型。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-384">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="b2ad0-385">此文章中使用 **DTO** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-385">**DTO** is used in this article.</span></span>

<span data-ttu-id="b2ad0-386">DTO 可以用來：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-386">A DTO may be used to:</span></span>

* <span data-ttu-id="b2ad0-387">防止過度張貼。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-387">Prevent over-posting.</span></span>
* <span data-ttu-id="b2ad0-388">隱藏用戶端不應該看到的屬性。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-388">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="b2ad0-389">略過某些屬性，以減少承載大小。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-389">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="b2ad0-390">壓平合併包含嵌套物件的物件圖形。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-390">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="b2ad0-391">簡維物件圖形對於用戶端來說可能更方便。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-391">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="b2ad0-392">若要示範 DTO 方法，請更新 `TodoItem` 類別以包含秘密欄位：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-392">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="b2ad0-393">此應用程式必須隱藏秘密欄位，但系統管理應用程式可以選擇將它公開。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-393">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="b2ad0-394">確認您可以張貼並取得秘密欄位。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-394">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="b2ad0-395">建立 DTO 模型：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-395">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="b2ad0-396">更新 `TodoItemsController` 以使用 `TodoItemDTO` ：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-396">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="b2ad0-397">確認您無法張貼或取得秘密欄位。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-397">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="b2ad0-398">使用 JavaScript 呼叫 Web API</span><span class="sxs-lookup"><span data-stu-id="b2ad0-398">Call the web API with JavaScript</span></span>

<span data-ttu-id="b2ad0-399">請參閱 [教學課程：使用 JavaScript 呼叫 ASP.NET Core WEB API](xref:tutorials/web-api-javascript)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-399">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="b2ad0-400">在本教學課程中，您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-400">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b2ad0-401">建立 Web API 專案。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-401">Create a web API project.</span></span>
> * <span data-ttu-id="b2ad0-402">新增模型類別和資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-402">Add a model class and a database context.</span></span>
> * <span data-ttu-id="b2ad0-403">使用 CRUD 方法 Scaffold 控制器。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-403">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="b2ad0-404">設定路由、URL 路徑和傳回值。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-404">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="b2ad0-405">使用 Postman 呼叫 Web API。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-405">Call the web API with Postman.</span></span>

<span data-ttu-id="b2ad0-406">結束時，您會有一個 Web API，可以管理儲存在資料庫中的「待辦事項」。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-406">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="b2ad0-407">概觀</span><span class="sxs-lookup"><span data-stu-id="b2ad0-407">Overview</span></span>

<span data-ttu-id="b2ad0-408">本教學課程會建立以下 API：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-408">This tutorial creates the following API:</span></span>

|<span data-ttu-id="b2ad0-409">API</span><span class="sxs-lookup"><span data-stu-id="b2ad0-409">API</span></span> | <span data-ttu-id="b2ad0-410">描述</span><span class="sxs-lookup"><span data-stu-id="b2ad0-410">Description</span></span> | <span data-ttu-id="b2ad0-411">Request body</span><span class="sxs-lookup"><span data-stu-id="b2ad0-411">Request body</span></span> | <span data-ttu-id="b2ad0-412">回應本文</span><span class="sxs-lookup"><span data-stu-id="b2ad0-412">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="b2ad0-413">取得所有待辦事項</span><span class="sxs-lookup"><span data-stu-id="b2ad0-413">Get all to-do items</span></span> | <span data-ttu-id="b2ad0-414">無</span><span class="sxs-lookup"><span data-stu-id="b2ad0-414">None</span></span> | <span data-ttu-id="b2ad0-415">待辦事項的陣列</span><span class="sxs-lookup"><span data-stu-id="b2ad0-415">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="b2ad0-416">依識別碼取得項目</span><span class="sxs-lookup"><span data-stu-id="b2ad0-416">Get an item by ID</span></span> | <span data-ttu-id="b2ad0-417">無</span><span class="sxs-lookup"><span data-stu-id="b2ad0-417">None</span></span> | <span data-ttu-id="b2ad0-418">待辦事項</span><span class="sxs-lookup"><span data-stu-id="b2ad0-418">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="b2ad0-419">新增記錄</span><span class="sxs-lookup"><span data-stu-id="b2ad0-419">Add a new item</span></span> | <span data-ttu-id="b2ad0-420">待辦事項</span><span class="sxs-lookup"><span data-stu-id="b2ad0-420">To-do item</span></span> | <span data-ttu-id="b2ad0-421">待辦事項</span><span class="sxs-lookup"><span data-stu-id="b2ad0-421">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="b2ad0-422">更新現有的項目 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b2ad0-422">Update an existing item &nbsp;</span></span> | <span data-ttu-id="b2ad0-423">待辦事項</span><span class="sxs-lookup"><span data-stu-id="b2ad0-423">To-do item</span></span> | <span data-ttu-id="b2ad0-424">無</span><span class="sxs-lookup"><span data-stu-id="b2ad0-424">None</span></span> |
|<span data-ttu-id="b2ad0-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b2ad0-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="b2ad0-426">刪除專案 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="b2ad0-426">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="b2ad0-427">無</span><span class="sxs-lookup"><span data-stu-id="b2ad0-427">None</span></span> | <span data-ttu-id="b2ad0-428">無</span><span class="sxs-lookup"><span data-stu-id="b2ad0-428">None</span></span>|

<span data-ttu-id="b2ad0-429">下圖顯示應用程式的設計。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-429">The following diagram shows the design of the app.</span></span>

![左側方塊代表用戶端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="b2ad0-435">必要條件</span><span class="sxs-lookup"><span data-stu-id="b2ad0-435">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2ad0-436">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ad0-436">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2ad0-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2ad0-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2ad0-438">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2ad0-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="b2ad0-439">建立 Web 專案</span><span class="sxs-lookup"><span data-stu-id="b2ad0-439">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2ad0-440">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ad0-440">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b2ad0-441">從 [ **檔案** ] 功能表選取 [ **新增** > **專案** ]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-441">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b2ad0-442">選取 **ASP.NET Core Web 應用程式** 範本，然後按一下 [下一步]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-442">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="b2ad0-443">將專案命名為 *TodoApi* ，然後按一下 [建立]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-443">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="b2ad0-444">在 [ **建立新的 ASP.NET Core Web 應用程式** ] 對話方塊中，確認已選取 [ **.net Core** ] 和 [ **ASP.NET Core 3.1** ]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-444">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="b2ad0-445">選取 **API** 範本，然後按一下 [建立]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-445">Select the **API** template and click **Create**.</span></span>

![VS 新增專案對話方塊](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2ad0-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2ad0-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b2ad0-448">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-448">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b2ad0-449">將目錄 (`cd`) 變更為包含專案資料夾的資料夾。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-449">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="b2ad0-450">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-450">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="b2ad0-451">當出現對話方塊詢問您是否要將所需的資產新增至專案時，選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-451">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="b2ad0-452">上述命令：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-452">The preceding commands:</span></span>

  * <span data-ttu-id="b2ad0-453">建立新的 Web API 專案，然後在 Visual Studio Code 中予以開啟。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-453">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="b2ad0-454">新增下一節需要的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-454">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2ad0-455">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2ad0-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b2ad0-456">選取 [檔案] **[新增解決方案]** > 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-456">Select **File** > **New Solution**.</span></span>

  ![macOS 新增方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="b2ad0-458">在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用程式**  >  **API**  >  **]** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-458">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="b2ad0-459">在8.6 版或更新版本中，選取 [ **Web] 和 [主控台**  >  **應用程式**  >  **API**  >  **]** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-459">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 範本選取專案](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="b2ad0-461">在 [ **設定新的 ASP.NET Core WEB API** ] 對話方塊中，選取最新的 .net Core 3.X **目標 Framework** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-461">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="b2ad0-462">選取 [下一步]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-462">Select **Next**.</span></span>

* <span data-ttu-id="b2ad0-463">針對 [專案名稱] 輸入 *TodoApi* ，然後選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-463">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![設定對話方塊](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="b2ad0-465">在專案資料夾中開啟命令終端機，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-465">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="b2ad0-466">測試 API</span><span class="sxs-lookup"><span data-stu-id="b2ad0-466">Test the API</span></span>

<span data-ttu-id="b2ad0-467">專案範本會建立 `WeatherForecast` API。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-467">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="b2ad0-468">從瀏覽器呼叫 `Get` 方法來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-468">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2ad0-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ad0-469">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b2ad0-470">按 Ctrl+F5 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-470">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b2ad0-471">Visual Studio 會啟動瀏覽器並巡覽至 `https://localhost:<port>/WeatherForecast`，其中 `<port>` 是隨機選擇的通訊埠編號。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-471">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="b2ad0-472">如果出現對話方塊詢問您是否應該信任 IIS Express 憑證，請選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-472">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="b2ad0-473">在接著出現的 [安全性警告] 對話方塊中，選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-473">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2ad0-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2ad0-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b2ad0-475">按 Ctrl+F5 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-475">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b2ad0-476">在瀏覽器中，前往下列 URL：`https://localhost:5001/WeatherForecast`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-476">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2ad0-477">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2ad0-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b2ad0-478">選取 [ **執行**  >  **開始調試** 程式] 以啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-478">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="b2ad0-479">Visual Studio for Mac 會啟動瀏覽器並巡覽至 `https://localhost:<port>`，其中 `<port>` 是隨機選擇的連接埠號碼。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-479">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="b2ad0-480">傳回 HTTP 404 (找不到) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-480">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="b2ad0-481">將 `/WeatherForecast` 附加至 URL (將 URL 變更為 `https://localhost:<port>/WeatherForecast`)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-481">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="b2ad0-482">系統會傳回與下列類似的 JSON：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-482">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="b2ad0-483">新增模型類別</span><span class="sxs-lookup"><span data-stu-id="b2ad0-483">Add a model class</span></span>

<span data-ttu-id="b2ad0-484">「模型」是代表應用程式所管理資料的一組類別。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-484">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="b2ad0-485">此應用程式的模型是單一 `TodoItem` 類別。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-485">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2ad0-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ad0-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b2ad0-487">在 **方案總管** 中，以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-487">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="b2ad0-488">選取 **[**  >  **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-488">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b2ad0-489">為資料夾命名 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-489">Name the folder *Models*.</span></span>

* <span data-ttu-id="b2ad0-490">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [ **加入**  >  **類別** ]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-490">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b2ad0-491">將類別命名為 *TodoItem* ，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-491">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="b2ad0-492">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-492">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2ad0-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2ad0-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b2ad0-494">新增名為的資料夾 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-494">Add a folder named *Models*.</span></span>

* <span data-ttu-id="b2ad0-495">`TodoItem`使用下列程式碼，將類別新增至 *Models* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-495">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2ad0-496">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2ad0-496">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b2ad0-497">以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-497">Right-click the project.</span></span> <span data-ttu-id="b2ad0-498">選取 **[**  >  **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-498">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b2ad0-499">為資料夾命名 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-499">Name the folder *Models*.</span></span>

  ![新增資料夾](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="b2ad0-501">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 **Add** > [ **新增** 檔案 > **一般** ] > **空白類別** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-501">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="b2ad0-502">將類別命名為 *TodoItem* ，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-502">Name the class *TodoItem* , and then click **New**.</span></span>

* <span data-ttu-id="b2ad0-503">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-503">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="b2ad0-504">`Id` 屬性的功能相當於關聯式資料庫中的唯一索引鍵。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-504">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="b2ad0-505">模型類別可移至專案中的任何位置，但依照慣例，會 *Models* 使用資料夾。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-505">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="b2ad0-506">新增資料庫內容</span><span class="sxs-lookup"><span data-stu-id="b2ad0-506">Add a database context</span></span>

<span data-ttu-id="b2ad0-507">「資料庫內容」是為資料模型協調 Entity Framework 功能的主要類別。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-507">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="b2ad0-508">此類別是透過衍生自 `Microsoft.EntityFrameworkCore.DbContext` 類別來建立。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-508">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2ad0-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ad0-509">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="b2ad0-510">新增 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="b2ad0-510">Add NuGet packages</span></span>

* <span data-ttu-id="b2ad0-511">在 [工具] 功能表上，選取 [NuGet 套件管理員] > [管理解決方案的 NuGet 套件]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-511">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="b2ad0-512">選取 [瀏覽] 索引標籤，然後在搜尋方塊中輸入 **Microsoft.EntityFrameworkCore.SqlServer** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-512">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="b2ad0-513">選取左窗格中的 [ **microsoft.entityframeworkcore** ]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-513">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="b2ad0-514">選取右窗格中的 [專案] 核取方塊，然後選取 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-514">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="b2ad0-515">使用上述指示來新增 **Microsoft.entityframeworkcore InMemory** NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-515">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![NuGet 套件管理員](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="b2ad0-517">新增 TodoCoNtext 資料庫內容</span><span class="sxs-lookup"><span data-stu-id="b2ad0-517">Add the TodoContext database context</span></span>

* <span data-ttu-id="b2ad0-518">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [ **加入**  >  **類別** ]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-518">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b2ad0-519">將類別命名為 *TodoContext* ，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-519">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b2ad0-520">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2ad0-520">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b2ad0-521">將 `TodoContext` 類別新增至 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-521">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="b2ad0-522">輸入下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-522">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="b2ad0-523">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="b2ad0-523">Register the database context</span></span>

<span data-ttu-id="b2ad0-524">在 ASP.NET Core 中，資料庫內容等服務必須向[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器註冊。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-524">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b2ad0-525">此容器會將服務提供給控制器。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-525">The container provides the service to controllers.</span></span>

<span data-ttu-id="b2ad0-526">使用下列醒目提示的程式碼更新 *Startup.cs* ：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-526">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="b2ad0-527">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-527">The preceding code:</span></span>

* <span data-ttu-id="b2ad0-528">移除未使用的 `using` 宣告。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-528">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="b2ad0-529">將資料庫內容新增至 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-529">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="b2ad0-530">指定資料庫內容將會使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-530">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="b2ad0-531">Scaffold 控制器</span><span class="sxs-lookup"><span data-stu-id="b2ad0-531">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2ad0-532">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ad0-532">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b2ad0-533">以滑鼠右鍵按一下 *Controllers* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-533">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="b2ad0-534">選取 [新增] **[新增 Scaffold 項目]** > 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-534">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b2ad0-535">選取 [使用 Entity Framework 執行動作的 API 控制器]，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-535">Select **API Controller with actions, using Entity Framework** , and then select **Add**.</span></span>
* <span data-ttu-id="b2ad0-536">在 [使用 Entity Framework 執行動作的 API 控制器] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-536">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="b2ad0-537">選取 **模型類別** 中的 [ **TodoItem (TodoApi] Models) 。**</span><span class="sxs-lookup"><span data-stu-id="b2ad0-537">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="b2ad0-538">選取 **資料內容類別** 中的 **TodoCoNtext (TodoApi Models) 。**</span><span class="sxs-lookup"><span data-stu-id="b2ad0-538">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="b2ad0-539">選取 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-539">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b2ad0-540">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2ad0-540">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="b2ad0-541">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-541">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="b2ad0-542">上述命令：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-542">The preceding commands:</span></span>

* <span data-ttu-id="b2ad0-543">新增 Scaffolding 所需的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-543">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="b2ad0-544">安裝 Scaffolding 引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-544">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="b2ad0-545">Scaffold `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-545">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="b2ad0-546">產生的程式碼：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-546">The generated code:</span></span>

* <span data-ttu-id="b2ad0-547">標記具有屬性的類別 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-547">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="b2ad0-548">這個屬性表示控制器會回應 Web API 要求。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-548">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="b2ad0-549">如需屬性所啟用之特定行為的相關資訊，請參閱 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-549">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="b2ad0-550">使用 DI 將資料庫內容 (`TodoContext`) 插入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-550">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="b2ad0-551">控制器中的每一個 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法都會使用資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-551">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="b2ad0-552">的 ASP.NET Core 範本：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-552">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="b2ad0-553">具有 views 的控制器包含 `[action]` 在路由範本中。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-553">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="b2ad0-554">API 控制器不包含 `[action]` 在路由範本中。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-554">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="b2ad0-555">當 `[action]` 權杖不在路由範本中時，會從路由中排除 [動作](xref:mvc/controllers/routing#action) 名稱。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-555">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="b2ad0-556">也就是，不會在相符的路由中使用動作的相關聯方法名稱。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-556">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="b2ad0-557">檢查 PostTodoItem 建立方法</span><span class="sxs-lookup"><span data-stu-id="b2ad0-557">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="b2ad0-558">取代 `PostTodoItem` 中的 return 陳述式，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 運算子：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-558">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="b2ad0-559">上述程式碼是 HTTP POST 方法，如屬性所指示 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-559">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="b2ad0-560">該方法會從 HTTP 要求本文取得待辦事項的值。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-560">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="b2ad0-561">如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-561">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="b2ad0-562"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-562">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="b2ad0-563">成功時會傳回 HTTP 201 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-563">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="b2ad0-564">對於可在伺服器上建立新資源的 HTTP POST 方法，其標準回應是 HTTP 201。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-564">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="b2ad0-565">將 [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 標頭新增至回應。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-565">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="b2ad0-566">`Location`標頭會指定新建立之待辦事項的[URI](https://developer.mozilla.org/docs/Glossary/URI) 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-566">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="b2ad0-567">如需詳細資訊，請參閱 [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (已建立 10.2.2 201)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-567">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="b2ad0-568">參考 `GetTodoItem` 動作以建立 `Location` 標頭的 URI。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-568">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="b2ad0-569">C# `nameof` 關鍵字是用來避免在 `CreatedAtAction` 呼叫中以硬式編碼方式寫入動作名稱。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-569">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="b2ad0-570">安裝 Postman</span><span class="sxs-lookup"><span data-stu-id="b2ad0-570">Install Postman</span></span>

<span data-ttu-id="b2ad0-571">本教學課程使用 Postman 來測試 Web API。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-571">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="b2ad0-572">安裝 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="b2ad0-572">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="b2ad0-573">啟動 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-573">Start the web app.</span></span>
* <span data-ttu-id="b2ad0-574">啟動 Postman。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-574">Start Postman.</span></span>
* <span data-ttu-id="b2ad0-575">停用 [SSL certificate verification] \(SSL 憑證驗證\)</span><span class="sxs-lookup"><span data-stu-id="b2ad0-575">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="b2ad0-576">從 [檔案] **[設定]** >  ([一般] 索引標籤)，停用 [SSL 憑證驗證]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-576">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="b2ad0-577">在測試控制器之後，請重新啟用 [SSL certificate verification] \(SSL 憑證驗證\)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-577">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="b2ad0-578">使用 Postman 測試 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="b2ad0-578">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="b2ad0-579">建立新的要求。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-579">Create a new request.</span></span>
* <span data-ttu-id="b2ad0-580">將 HTTP 方法設為 `POST`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-580">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="b2ad0-581">將 URI 設定為 `https://localhost:<port>/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-581">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="b2ad0-582">例如，`https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-582">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="b2ad0-583">選取 [Body] \(本文\) 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-583">Select the **Body** tab.</span></span>
* <span data-ttu-id="b2ad0-584">選取 [原始] 選項按鈕。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-584">Select the **raw** radio button.</span></span>
* <span data-ttu-id="b2ad0-585">將類型設定為 **JSON (application/json)** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-585">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="b2ad0-586">在要求本文中，針對待辦項目輸入 JSON：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-586">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="b2ad0-587">選取 [傳送]  。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-587">Select **Send**.</span></span>

  ![Postman 與建立要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="b2ad0-589">使用 Postman 測試 location 標頭 URI</span><span class="sxs-lookup"><span data-stu-id="b2ad0-589">Test the location header URI with Postman</span></span>

* <span data-ttu-id="b2ad0-590">在 [回應] 窗格中選取 [標頭] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-590">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="b2ad0-591">複製 [位置] 標頭值：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-591">Copy the **Location** header value:</span></span>

  ![Postman 主控台的 [標頭] 索引標籤](first-web-api/_static/3/create.png)

* <span data-ttu-id="b2ad0-593">將 HTTP 方法設為 `GET`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-593">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="b2ad0-594">將 URI 設定為 `https://localhost:<port>/api/TodoItems/1` 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-594">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="b2ad0-595">例如，`https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-595">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="b2ad0-596">選取 [傳送]  。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-596">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="b2ad0-597">檢查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="b2ad0-597">Examine the GET methods</span></span>

<span data-ttu-id="b2ad0-598">這些方法會實作兩個 GET 端點：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-598">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="b2ad0-599">從瀏覽器或 Postman 呼叫這兩個端點來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-599">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="b2ad0-600">例如：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-600">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="b2ad0-601">`GetTodoItems` 的呼叫會產生類似下列的回應：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-601">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="b2ad0-602">使用 Postman 測試 Get</span><span class="sxs-lookup"><span data-stu-id="b2ad0-602">Test Get with Postman</span></span>

* <span data-ttu-id="b2ad0-603">建立新的要求。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-603">Create a new request.</span></span>
* <span data-ttu-id="b2ad0-604">將 HTTP 方法設定為 **GET** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-604">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="b2ad0-605">將要求 URI 設定為 `https://localhost:<port>/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-605">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="b2ad0-606">例如，`https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-606">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="b2ad0-607">在 Postman 中，設定 [Two pane view] \(雙窗格檢視\)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-607">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="b2ad0-608">選取 [傳送]  。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-608">Select **Send**.</span></span>

<span data-ttu-id="b2ad0-609">這個應用程式會使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-609">This app uses an in-memory database.</span></span> <span data-ttu-id="b2ad0-610">如果應用程式在停止後再啟動，上述 GET 要求將不會傳回任何資料。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-610">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="b2ad0-611">如果沒有傳回任何資料，請將資料 [POST](#post) 到應用程式。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-611">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="b2ad0-612">傳送和 URL 路徑</span><span class="sxs-lookup"><span data-stu-id="b2ad0-612">Routing and URL paths</span></span>

<span data-ttu-id="b2ad0-613">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)屬性代表回應 HTTP GET 要求的方法。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-613">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="b2ad0-614">每個方法的 URL 路徑的建構方式如下：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-614">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="b2ad0-615">一開始在控制器的 `Route` 屬性中使用範本字串：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-615">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="b2ad0-616">以控制器的名稱取代 `[controller]`，也就是將控制器類別名稱減去 "Controller" 字尾。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-616">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="b2ad0-617">在此範例中，控制器類別名稱是 **TodoItems** Controller，因此控制器名稱是 "TodoItems"。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-617">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="b2ad0-618">ASP.NET Core [路由](xref:mvc/controllers/routing)不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-618">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="b2ad0-619">如果 `[HttpGet]` 屬性具有路由範本 (例如 `[HttpGet("products")]`)，請將其附加到路徑。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-619">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="b2ad0-620">此範例不使用範本。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-620">This sample doesn't use a template.</span></span> <span data-ttu-id="b2ad0-621">如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-621">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="b2ad0-622">在下列 `GetTodoItem` 方法中，`"{id}"` 是待辦事項唯一識別碼的預留位置變數。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-622">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="b2ad0-623">當叫 `GetTodoItem` 用時，會將 `"{id}"` URL 中的值提供給方法的 `id` 參數。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-623">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="b2ad0-624">傳回值</span><span class="sxs-lookup"><span data-stu-id="b2ad0-624">Return values</span></span> 

<span data-ttu-id="b2ad0-625">和方法的傳回型 `GetTodoItems` 別 `GetTodoItem` 為 [ActionResult \<T> 類型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-625">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="b2ad0-626">ASP.NET Core 會自動將物件序列化為 [JSON](https://www.json.org/)，並將 JSON 寫入至回應訊息的本文。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-626">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="b2ad0-627">此傳回型別的回應碼為 200，假設沒有任何未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-627">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="b2ad0-628">未處理的例外狀況會轉譯成 5xx 錯誤。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-628">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="b2ad0-629">`ActionResult` 傳回型別可代表各種 HTTP 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-629">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="b2ad0-630">例如，`GetTodoItem` 可傳回兩個不同的狀態值：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-630">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="b2ad0-631">如果沒有任何專案符合要求的識別碼，方法會傳回 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 錯誤碼。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-631">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="b2ad0-632">否則，方法會傳回 200 與 JSON 回應本文。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-632">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="b2ad0-633">傳回 `item` 會導致 HTTP 200 回應。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-633">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="b2ad0-634">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="b2ad0-634">The PutTodoItem method</span></span>

<span data-ttu-id="b2ad0-635">檢查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-635">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="b2ad0-636">`PutTodoItem` 類似於 `PostTodoItem`，但是會使用 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-636">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="b2ad0-637">回應為 [204 (沒有內容) ](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-637">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="b2ad0-638">根據 HTTP 規格，PUT 要求需要用戶端傳送整個更新的實體，而不只是變更。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-638">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="b2ad0-639">若要支援部分更新，請使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-639">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="b2ad0-640">如果在呼叫 `PutTodoItem` 時發生錯誤，請呼叫 `GET` 以確保資料庫中有項目。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-640">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="b2ad0-641">測試 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="b2ad0-641">Test the PutTodoItem method</span></span>

<span data-ttu-id="b2ad0-642">這個範例會使用必須在每次啟動應用程式時初始化的記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-642">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="b2ad0-643">資料庫中必須有項目，您才能進行 PUT 呼叫。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-643">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="b2ad0-644">在進行 PUT 呼叫之前，請先呼叫 GET 以確保資料庫中有專案。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-644">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="b2ad0-645">更新識別碼為1的待辦事項，並將其名稱設定為「摘要魚」：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-645">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="b2ad0-646">下圖顯示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-646">The following image shows the Postman update:</span></span>

![顯示「204 (沒有內容) 回應」的 Postman 主控台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="b2ad0-648">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="b2ad0-648">The DeleteTodoItem method</span></span>

<span data-ttu-id="b2ad0-649">檢查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-649">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="b2ad0-650">測試 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="b2ad0-650">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="b2ad0-651">使用 Postman 刪除待辦事項：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-651">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="b2ad0-652">將方法設定為 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-652">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="b2ad0-653">設定要刪除之物件的 URI (例如 `https://localhost:5001/api/TodoItems/1`) 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-653">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="b2ad0-654">選取 [傳送]  。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-654">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="b2ad0-655">防止過度張貼</span><span class="sxs-lookup"><span data-stu-id="b2ad0-655">Prevent over-posting</span></span>

<span data-ttu-id="b2ad0-656">範例應用程式目前會公開整個 `TodoItem` 物件。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-656">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="b2ad0-657">生產環境應用程式通常會限制使用模型子集來輸入和傳回的資料。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-657">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="b2ad0-658">這項功能有許多原因，而且安全性是主要的原因。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-658">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="b2ad0-659">模型的子集通常稱為資料傳輸物件 (DTO) 、輸入模型或視圖模型。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-659">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="b2ad0-660">此文章中使用 **DTO** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-660">**DTO** is used in this article.</span></span>

<span data-ttu-id="b2ad0-661">DTO 可以用來：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-661">A DTO may be used to:</span></span>

* <span data-ttu-id="b2ad0-662">防止過度張貼。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-662">Prevent over-posting.</span></span>
* <span data-ttu-id="b2ad0-663">隱藏用戶端不應該看到的屬性。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-663">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="b2ad0-664">略過某些屬性，以減少承載大小。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-664">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="b2ad0-665">壓平合併包含嵌套物件的物件圖形。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-665">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="b2ad0-666">簡維物件圖形對於用戶端來說可能更方便。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-666">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="b2ad0-667">若要示範 DTO 方法，請更新 `TodoItem` 類別以包含秘密欄位：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-667">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="b2ad0-668">此應用程式必須隱藏秘密欄位，但系統管理應用程式可以選擇將它公開。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-668">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="b2ad0-669">確認您可以張貼並取得秘密欄位。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-669">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="b2ad0-670">建立 DTO 模型：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-670">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="b2ad0-671">更新 `TodoItemsController` 以使用 `TodoItemDTO` ：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-671">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="b2ad0-672">確認您無法張貼或取得秘密欄位。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-672">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="b2ad0-673">使用 JavaScript 呼叫 Web API</span><span class="sxs-lookup"><span data-stu-id="b2ad0-673">Call the web API with JavaScript</span></span>

<span data-ttu-id="b2ad0-674">請參閱 [教學課程：使用 JavaScript 呼叫 ASP.NET Core WEB API](xref:tutorials/web-api-javascript)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-674">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b2ad0-675">在本教學課程中，您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-675">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b2ad0-676">建立 Web API 專案。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-676">Create a web API project.</span></span>
> * <span data-ttu-id="b2ad0-677">新增模型類別和資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-677">Add a model class and a database context.</span></span>
> * <span data-ttu-id="b2ad0-678">新增控制器。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-678">Add a controller.</span></span>
> * <span data-ttu-id="b2ad0-679">新增 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-679">Add CRUD methods.</span></span>
> * <span data-ttu-id="b2ad0-680">設定路由和 URL 路徑。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-680">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="b2ad0-681">指定傳回值。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-681">Specify return values.</span></span>
> * <span data-ttu-id="b2ad0-682">使用 Postman 呼叫 Web API。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-682">Call the web API with Postman.</span></span>
> * <span data-ttu-id="b2ad0-683">使用 JavaScript 呼叫 Web API。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-683">Call the web API with JavaScript.</span></span>

<span data-ttu-id="b2ad0-684">結束時，您將會有一個可管理關聯式資料庫中所儲存「待辦事項」的 Web API。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-684">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="b2ad0-685">總覽2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-685">Overview 2.1</span></span>

<span data-ttu-id="b2ad0-686">本教學課程會建立以下 API：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-686">This tutorial creates the following API:</span></span>

|<span data-ttu-id="b2ad0-687">API</span><span class="sxs-lookup"><span data-stu-id="b2ad0-687">API</span></span> | <span data-ttu-id="b2ad0-688">描述</span><span class="sxs-lookup"><span data-stu-id="b2ad0-688">Description</span></span> | <span data-ttu-id="b2ad0-689">Request body</span><span class="sxs-lookup"><span data-stu-id="b2ad0-689">Request body</span></span> | <span data-ttu-id="b2ad0-690">回應本文</span><span class="sxs-lookup"><span data-stu-id="b2ad0-690">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="b2ad0-691">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="b2ad0-691">GET /api/TodoItems</span></span> | <span data-ttu-id="b2ad0-692">取得所有待辦事項</span><span class="sxs-lookup"><span data-stu-id="b2ad0-692">Get all to-do items</span></span> | <span data-ttu-id="b2ad0-693">無</span><span class="sxs-lookup"><span data-stu-id="b2ad0-693">None</span></span> | <span data-ttu-id="b2ad0-694">待辦事項的陣列</span><span class="sxs-lookup"><span data-stu-id="b2ad0-694">Array of to-do items</span></span>|
|<span data-ttu-id="b2ad0-695">GET /api/TodoItems/{識別碼}</span><span class="sxs-lookup"><span data-stu-id="b2ad0-695">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="b2ad0-696">依識別碼取得項目</span><span class="sxs-lookup"><span data-stu-id="b2ad0-696">Get an item by ID</span></span> | <span data-ttu-id="b2ad0-697">無</span><span class="sxs-lookup"><span data-stu-id="b2ad0-697">None</span></span> | <span data-ttu-id="b2ad0-698">待辦事項</span><span class="sxs-lookup"><span data-stu-id="b2ad0-698">To-do item</span></span>|
|<span data-ttu-id="b2ad0-699">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="b2ad0-699">POST /api/TodoItems</span></span> | <span data-ttu-id="b2ad0-700">新增記錄</span><span class="sxs-lookup"><span data-stu-id="b2ad0-700">Add a new item</span></span> | <span data-ttu-id="b2ad0-701">待辦事項</span><span class="sxs-lookup"><span data-stu-id="b2ad0-701">To-do item</span></span> | <span data-ttu-id="b2ad0-702">待辦事項</span><span class="sxs-lookup"><span data-stu-id="b2ad0-702">To-do item</span></span> |
|<span data-ttu-id="b2ad0-703">PUT /api/TodoItems/{識別碼}</span><span class="sxs-lookup"><span data-stu-id="b2ad0-703">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="b2ad0-704">更新現有的項目 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b2ad0-704">Update an existing item &nbsp;</span></span> | <span data-ttu-id="b2ad0-705">待辦事項</span><span class="sxs-lookup"><span data-stu-id="b2ad0-705">To-do item</span></span> | <span data-ttu-id="b2ad0-706">無</span><span class="sxs-lookup"><span data-stu-id="b2ad0-706">None</span></span> |
|<span data-ttu-id="b2ad0-707">刪除/Api/todoitems/{識別碼} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="b2ad0-707">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="b2ad0-708">刪除專案 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="b2ad0-708">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="b2ad0-709">無</span><span class="sxs-lookup"><span data-stu-id="b2ad0-709">None</span></span> | <span data-ttu-id="b2ad0-710">無</span><span class="sxs-lookup"><span data-stu-id="b2ad0-710">None</span></span>|

<span data-ttu-id="b2ad0-711">下圖顯示應用程式的設計。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-711">The following diagram shows the design of the app.</span></span>

![左側方塊代表用戶端。](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="b2ad0-717">必要條件2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-717">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2ad0-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ad0-718">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2ad0-719">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2ad0-719">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2ad0-720">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2ad0-720">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="b2ad0-721">建立 Web 專案2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-721">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2ad0-722">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ad0-722">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b2ad0-723">從 [ **檔案** ] 功能表選取 [ **新增** > **專案** ]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-723">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b2ad0-724">選取 **ASP.NET Core Web 應用程式** 範本，然後按一下 [下一步]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-724">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="b2ad0-725">將專案命名為 *TodoApi* ，然後按一下 [建立]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-725">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="b2ad0-726">在 [建立新的 ASP.NET Core Web 應用程式] 對話方塊中，確認選取 [.NET Core] 和 [ASP.NET Core 2.2]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-726">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="b2ad0-727">選取 **API** 範本，然後按一下 [建立]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-727">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="b2ad0-728">請 **勿** 選取 [Enable Docker Support] \(啟用 Docker 支援\)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-728">**Don't** select **Enable Docker Support**.</span></span>

![VS 新增專案對話方塊](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2ad0-730">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2ad0-730">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b2ad0-731">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-731">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b2ad0-732">將目錄 (`cd`) 變更為包含專案資料夾的資料夾。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-732">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="b2ad0-733">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-733">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="b2ad0-734">這些命令會建立新的 Web API 專案，並開啟新專案資料夾中的新 Visual Studio Code 執行個體。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-734">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="b2ad0-735">當出現對話方塊詢問您是否要將所需的資產新增至專案時，選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-735">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2ad0-736">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2ad0-736">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b2ad0-737">選取 [檔案] **[新增解決方案]** > 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-737">Select **File** > **New Solution**.</span></span>

  ![macOS 新增方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="b2ad0-739">在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用程式**  >  **API**  >  **]** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-739">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="b2ad0-740">在8.6 版或更新版本中，選取 [ **Web] 和 [主控台**  >  **應用程式**  >  **API**  >  **]** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-740">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="b2ad0-741">在 [ **設定新的 ASP.NET Core WEB API** ] 對話方塊中，選取最新的 .net Core 2.X **目標 Framework** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-741">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="b2ad0-742">選取 [下一步]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-742">Select **Next**.</span></span>

* <span data-ttu-id="b2ad0-743">針對 [專案名稱] 輸入 *TodoApi* ，然後選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-743">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![設定對話方塊](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="b2ad0-745">測試 API 2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-745">Test the API 2.1</span></span>

<span data-ttu-id="b2ad0-746">專案範本會建立 `values` API。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-746">The project template creates a `values` API.</span></span> <span data-ttu-id="b2ad0-747">從瀏覽器呼叫 `Get` 方法來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-747">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2ad0-748">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ad0-748">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b2ad0-749">按 Ctrl+F5 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-749">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b2ad0-750">Visual Studio 會啟動瀏覽器並巡覽至 `https://localhost:<port>/api/values`，其中 `<port>` 是隨機選擇的通訊埠編號。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-750">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="b2ad0-751">如果出現對話方塊詢問您是否應該信任 IIS Express 憑證，請選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-751">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="b2ad0-752">在接著出現的 [安全性警告] 對話方塊中，選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-752">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2ad0-753">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2ad0-753">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b2ad0-754">按 Ctrl+F5 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-754">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b2ad0-755">在瀏覽器中，前往下列 URL：`https://localhost:5001/api/values`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-755">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2ad0-756">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2ad0-756">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b2ad0-757">選取 [ **執行**  >  **開始調試** 程式] 以啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-757">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="b2ad0-758">Visual Studio for Mac 會啟動瀏覽器並巡覽至 `https://localhost:<port>`，其中 `<port>` 是隨機選擇的連接埠號碼。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-758">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="b2ad0-759">傳回 HTTP 404 (找不到) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-759">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="b2ad0-760">將 `/api/values` 附加至 URL (將 URL 變更為 `https://localhost:<port>/api/values`)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-760">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="b2ad0-761">隨即傳回下列 JSON：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-761">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="b2ad0-762">新增模型類別2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-762">Add a model class 2.1</span></span>

<span data-ttu-id="b2ad0-763">「模型」是代表應用程式所管理資料的一組類別。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-763">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="b2ad0-764">此應用程式的模型是單一 `TodoItem` 類別。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-764">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2ad0-765">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ad0-765">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b2ad0-766">在 **方案總管** 中，以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-766">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="b2ad0-767">選取 **[**  >  **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-767">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b2ad0-768">為資料夾命名 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-768">Name the folder *Models*.</span></span>

* <span data-ttu-id="b2ad0-769">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [ **加入**  >  **類別** ]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-769">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b2ad0-770">將類別命名為 *TodoItem* ，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-770">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="b2ad0-771">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-771">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2ad0-772">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2ad0-772">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b2ad0-773">新增名為的資料夾 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-773">Add a folder named *Models*.</span></span>

* <span data-ttu-id="b2ad0-774">`TodoItem`使用下列程式碼，將類別新增至 *Models* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-774">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2ad0-775">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2ad0-775">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b2ad0-776">以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-776">Right-click the project.</span></span> <span data-ttu-id="b2ad0-777">選取 **[**  >  **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-777">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b2ad0-778">為資料夾命名 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-778">Name the folder *Models*.</span></span>

  ![新增資料夾](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="b2ad0-780">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 **Add** > [ **新增** 檔案 > **一般** ] > **空白類別** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-780">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="b2ad0-781">將類別命名為 *TodoItem* ，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-781">Name the class *TodoItem* , and then click **New**.</span></span>

* <span data-ttu-id="b2ad0-782">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-782">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="b2ad0-783">`Id` 屬性的功能相當於關聯式資料庫中的唯一索引鍵。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-783">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="b2ad0-784">模型類別可移至專案中的任何位置，但依照慣例，會 *Models* 使用資料夾。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-784">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="b2ad0-785">新增資料庫內容2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-785">Add a database context 2.1</span></span>

<span data-ttu-id="b2ad0-786">「資料庫內容」是為資料模型協調 Entity Framework 功能的主要類別。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-786">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="b2ad0-787">此類別是透過衍生自 `Microsoft.EntityFrameworkCore.DbContext` 類別來建立。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-787">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2ad0-788">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ad0-788">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b2ad0-789">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [ **加入**  >  **類別** ]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-789">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b2ad0-790">將類別命名為 *TodoContext* ，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-790">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b2ad0-791">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2ad0-791">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b2ad0-792">將 `TodoContext` 類別新增至 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-792">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="b2ad0-793">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-793">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="b2ad0-794">註冊資料庫內容2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-794">Register the database context 2.1</span></span>

<span data-ttu-id="b2ad0-795">在 ASP.NET Core 中，資料庫內容等服務必須向[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器註冊。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-795">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b2ad0-796">此容器會將服務提供給控制器。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-796">The container provides the service to controllers.</span></span>

<span data-ttu-id="b2ad0-797">使用下列醒目提示的程式碼更新 *Startup.cs* ：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-797">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="b2ad0-798">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-798">The preceding code:</span></span>

* <span data-ttu-id="b2ad0-799">移除未使用的 `using` 宣告。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-799">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="b2ad0-800">將資料庫內容新增至 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-800">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="b2ad0-801">指定資料庫內容將會使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-801">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="b2ad0-802">新增控制器2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-802">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2ad0-803">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ad0-803">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b2ad0-804">以滑鼠右鍵按一下 *Controllers* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-804">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="b2ad0-805">選取 [ **加入** > **新專案** ]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-805">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="b2ad0-806">在 [新增項目] 對話方塊中，選取 [API 控制器類別] 範本。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-806">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="b2ad0-807">將類別命名為 *TodoController* ，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-807">Name the class *TodoController* , and select **Add**.</span></span>

  ![在搜尋方塊中輸入 controller 且已選取 Web API 控制器的 [新增項目] 對話方塊](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b2ad0-809">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2ad0-809">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b2ad0-810">在 *Controllers* 資料夾中，建立名為 `TodoController` 的類別。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-810">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="b2ad0-811">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-811">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="b2ad0-812">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-812">The preceding code:</span></span>

* <span data-ttu-id="b2ad0-813">定義不含方法的 API 控制器類別。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-813">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="b2ad0-814">標記具有屬性的類別 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-814">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="b2ad0-815">這個屬性表示控制器會回應 Web API 要求。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-815">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="b2ad0-816">如需屬性所啟用之特定行為的相關資訊，請參閱 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-816">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="b2ad0-817">使用 DI 將資料庫內容 (`TodoContext`) 插入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-817">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="b2ad0-818">控制器中的每一個 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法都會使用資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-818">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="b2ad0-819">如果資料庫是空的，請將名為 `Item1` 的項目新增至資料庫。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-819">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="b2ad0-820">此程式碼是在建構函式中，因此每次執行都會有新的 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-820">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="b2ad0-821">如果您刪除所有項目，則建構函式會在下次呼叫 API 方法時重新建立 `Item1`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-821">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="b2ad0-822">因此看起來雖然像是刪除失敗，但實際為成功。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-822">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="b2ad0-823">新增 Get 方法2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-823">Add Get methods 2.1</span></span>

<span data-ttu-id="b2ad0-824">若要提供擷取待辦事項的 API，請在 `TodoController` 類別中新增下列方法：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-824">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="b2ad0-825">這些方法會實作兩個 GET 端點：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-825">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="b2ad0-826">如果應用程式仍在執行，請將其停止。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-826">Stop the app if it's still running.</span></span> <span data-ttu-id="b2ad0-827">然後重新予以執行以包含最新的變更。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-827">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="b2ad0-828">從瀏覽器呼叫這兩個端點來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-828">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="b2ad0-829">例如：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-829">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="b2ad0-830">以下是呼叫 `GetTodoItems` 所產生的 HTTP 回應：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-830">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="b2ad0-831">路由和 URL 路徑2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-831">Routing and URL paths 2.1</span></span>

<span data-ttu-id="b2ad0-832">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)屬性代表回應 HTTP GET 要求的方法。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-832">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="b2ad0-833">每個方法的 URL 路徑的建構方式如下：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-833">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="b2ad0-834">一開始在控制器的 `Route` 屬性中使用範本字串：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-834">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="b2ad0-835">以控制器的名稱取代 `[controller]`，也就是將控制器類別名稱減去 "Controller" 字尾。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-835">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="b2ad0-836">在此範例中，控制器類別名稱是 **Todo** Controller，因此容器名稱是 "todo"。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-836">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="b2ad0-837">ASP.NET Core [路由](xref:mvc/controllers/routing)不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-837">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="b2ad0-838">如果 `[HttpGet]` 屬性具有路由範本 (例如 `[HttpGet("products")]`)，請將其附加到路徑。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-838">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="b2ad0-839">此範例不使用範本。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-839">This sample doesn't use a template.</span></span> <span data-ttu-id="b2ad0-840">如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-840">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="b2ad0-841">在下列 `GetTodoItem` 方法中，`"{id}"` 是待辦事項唯一識別碼的預留位置變數。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-841">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="b2ad0-842">在叫用 `GetTodoItem` 時，會將 URL 中的 `"{id}"` 值提供給方法的 `id` 參數。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-842">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="b2ad0-843">傳回值2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-843">Return values 2.1</span></span>

<span data-ttu-id="b2ad0-844">和方法的傳回型 `GetTodoItems` 別 `GetTodoItem` 為 [ActionResult \<T> 類型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-844">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="b2ad0-845">ASP.NET Core 會自動將物件序列化為 [JSON](https://www.json.org/)，並將 JSON 寫入至回應訊息的本文。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-845">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="b2ad0-846">此傳回型別的回應碼為 200，假設沒有任何未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-846">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="b2ad0-847">未處理的例外狀況會轉譯成 5xx 錯誤。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-847">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="b2ad0-848">`ActionResult` 傳回型別可代表各種 HTTP 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-848">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="b2ad0-849">例如，`GetTodoItem` 可傳回兩個不同的狀態值：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-849">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="b2ad0-850">如果沒有任何專案符合要求的識別碼，方法會傳回 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 錯誤碼。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-850">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="b2ad0-851">否則，方法會傳回 200 與 JSON 回應本文。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-851">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="b2ad0-852">傳回 `item` 會導致 HTTP 200 回應。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-852">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="b2ad0-853">測試 GetTodoItems 方法2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-853">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="b2ad0-854">本教學課程使用 Postman 來測試 Web API。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-854">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="b2ad0-855">安裝 [Postman](https://www.getpostman.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-855">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="b2ad0-856">啟動 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-856">Start the web app.</span></span>
* <span data-ttu-id="b2ad0-857">啟動 Postman。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-857">Start Postman.</span></span>
* <span data-ttu-id="b2ad0-858">停用 **SSL 憑證驗證** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-858">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2ad0-859">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ad0-859">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b2ad0-860">從 [檔案] **[設定]** >  ([一般] 索引標籤)，停用 [SSL 憑證驗證]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-860">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b2ad0-861">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2ad0-861">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b2ad0-862">從 **Postman**  >  **喜好** 設定 ( **一般** ] 索引標籤) ，停用 **SSL 憑證驗證** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-862">From **Postman** > **Preferences** ( **General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="b2ad0-863">或者，選取扳手並選取 [設定]，然後停用 [SSL 憑證驗證]。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-863">Alternatively, select the wrench and select **Settings** , then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="b2ad0-864">在測試控制器之後，請重新啟用 [SSL certificate verification] \(SSL 憑證驗證\)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-864">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="b2ad0-865">建立新的要求。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-865">Create a new request.</span></span>
  * <span data-ttu-id="b2ad0-866">將 HTTP 方法設定為 **GET** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-866">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="b2ad0-867">將要求 URI 設定為 `https://localhost:<port>/api/todo` 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-867">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="b2ad0-868">例如，`https://localhost:5001/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-868">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="b2ad0-869">在 Postman 中，設定 [Two pane view] \(雙窗格檢視\)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-869">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="b2ad0-870">選取 [傳送]  。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-870">Select **Send**.</span></span>

![Postman 與 GET 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="b2ad0-872">新增 Create 方法2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-872">Add a Create method 2.1</span></span>

<span data-ttu-id="b2ad0-873">在 *Controllers/TodoController.cs* 內部新增下列 `PostTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-873">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs* :</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="b2ad0-874">上述程式碼是 HTTP POST 方法，如屬性所指示 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-874">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="b2ad0-875">該方法會從 HTTP 要求本文取得待辦事項的值。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-875">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="b2ad0-876">`CreatedAtAction` 方法：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-876">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="b2ad0-877">成功時會傳回 HTTP 201 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-877">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="b2ad0-878">對於可在伺服器上建立新資源的 HTTP POST 方法，其標準回應是 HTTP 201。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-878">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="b2ad0-879">將 `Location` 標頭加到回應中。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-879">Adds a `Location` header to the response.</span></span> <span data-ttu-id="b2ad0-880">`Location` 標頭指定新建立之待辦事項的 URI。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-880">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="b2ad0-881">如需詳細資訊，請參閱 [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (已建立 10.2.2 201)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-881">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="b2ad0-882">參考 `GetTodoItem` 動作以建立 `Location` 標頭的 URI。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-882">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="b2ad0-883">C# `nameof` 關鍵字是用來避免在 `CreatedAtAction` 呼叫中以硬式編碼方式寫入動作名稱。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-883">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="b2ad0-884">測試 PostTodoItem 方法2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-884">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="b2ad0-885">建置專案。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-885">Build the project.</span></span>
* <span data-ttu-id="b2ad0-886">在 Postman 中，將 HTTP 方法設定為 `POST`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-886">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="b2ad0-887">將 URI 設定為 `https://localhost:<port>/api/Todo` 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-887">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="b2ad0-888">例如，`https://localhost:5001/api/Todo`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-888">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="b2ad0-889">選取 [Body] \(本文\) 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-889">Select the **Body** tab.</span></span>
* <span data-ttu-id="b2ad0-890">選取 [原始] 選項按鈕。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-890">Select the **raw** radio button.</span></span>
* <span data-ttu-id="b2ad0-891">將類型設定為 **JSON (application/json)** 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-891">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="b2ad0-892">在要求本文中，針對待辦項目輸入 JSON：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-892">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="b2ad0-893">選取 [傳送]  。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-893">Select **Send**.</span></span>

  ![Postman 與建立要求](first-web-api/_static/create.png)

  <span data-ttu-id="b2ad0-895">如果您收到 405「不允許的方法」錯誤，可能是由於新增 `PostTodoItem` 方法之後未編譯專案所導致。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-895">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="b2ad0-896">測試位置標頭 URI 2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-896">Test the location header URI 2.1</span></span>

* <span data-ttu-id="b2ad0-897">在 [回應] 窗格中選取 [標頭] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-897">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="b2ad0-898">複製 [位置] 標頭值：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-898">Copy the **Location** header value:</span></span>

  ![Postman 主控台的 [標頭] 索引標籤](first-web-api/_static/pmc2.png)

* <span data-ttu-id="b2ad0-900">將方法設定為 GET。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-900">Set the method to GET.</span></span>
* <span data-ttu-id="b2ad0-901">將 URI 設定為 `https://localhost:<port>/api/TodoItems/2` 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-901">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="b2ad0-902">例如，`https://localhost:5001/api/TodoItems/2`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-902">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="b2ad0-903">選取 [傳送]  。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-903">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="b2ad0-904">新增 PutTodoItem 方法2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-904">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="b2ad0-905">請新增下列 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-905">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="b2ad0-906">`PutTodoItem` 類似於 `PostTodoItem`，但是會使用 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-906">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="b2ad0-907">回應為 [204 (沒有內容) ](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-907">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="b2ad0-908">根據 HTTP 規格，PUT 要求需要用戶端傳送整個更新的實體，而不只是變更。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-908">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="b2ad0-909">若要支援部分更新，請使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-909">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="b2ad0-910">如果在呼叫 `PutTodoItem` 時發生錯誤，請呼叫 `GET` 以確保資料庫中有項目。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-910">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="b2ad0-911">測試 PutTodoItem 方法2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-911">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="b2ad0-912">這個範例會使用必須在每次啟動應用程式時初始化的記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-912">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="b2ad0-913">資料庫中必須有項目，您才能進行 PUT 呼叫。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-913">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="b2ad0-914">在進行 PUT 呼叫之前，請先呼叫 GET 以確保資料庫中有專案。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-914">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="b2ad0-915">更新識別碼為1的待辦事項，並將其名稱設定為「摘要魚」：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-915">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="b2ad0-916">下圖顯示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-916">The following image shows the Postman update:</span></span>

![顯示「204 (沒有內容) 回應」的 Postman 主控台](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="b2ad0-918">新增 DeleteTodoItem 方法2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-918">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="b2ad0-919">請新增下列 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-919">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="b2ad0-920">`DeleteTodoItem` 回應是 [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) \(204 (沒有內容)\)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-920">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="b2ad0-921">測試 DeleteTodoItem 方法2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-921">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="b2ad0-922">使用 Postman 刪除待辦事項：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-922">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="b2ad0-923">將方法設定為 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-923">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="b2ad0-924">設定要刪除之物件的 URI (例如 `https://localhost:5001/api/todo/1`) 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-924">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="b2ad0-925">選取 [傳送]  。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-925">Select **Send**.</span></span>

<span data-ttu-id="b2ad0-926">範例應用程式可讓您刪除所有項目。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-926">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="b2ad0-927">但刪除最後一個項目之後，模型類別建構函式會在下次呼叫 API 時建立新的項目。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-927">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="b2ad0-928">使用 JavaScript 2.1 呼叫 web API</span><span class="sxs-lookup"><span data-stu-id="b2ad0-928">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="b2ad0-929">在此節中，將會新增 HTML 網頁，以使用 JavaScript 來呼叫 Web API。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-929">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="b2ad0-930">jQuery 會起始要求。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-930">jQuery initiates the request.</span></span> <span data-ttu-id="b2ad0-931">JavaScript 會使用來自 Web API 回應的詳細資料來更新頁面。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-931">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="b2ad0-932">藉由使用下列反白顯示的程式碼更新 *Startup.cs* ，來設定應用程式 [提供靜態檔案](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A)並 [啟用預設檔案對應](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A)：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-932">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="b2ad0-933">在專案目錄中建立 *wwwroot* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-933">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="b2ad0-934">將名為 *index.html* 的 HTML 檔案新增至 *wwwroot* 目錄。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-934">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="b2ad0-935">將其內容取代為下列標記：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-935">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="b2ad0-936">將名為 *site.js* 的 JavaScript 檔案新增至 *wwwroot* 目錄。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-936">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="b2ad0-937">以下列程式碼取代其內容：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-937">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="b2ad0-938">若要在本機測試 HTML 網頁，可能需要變更 ASP.NET Core 專案的啟動設定：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-938">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="b2ad0-939">開啟 *Properties\launchSettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-939">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="b2ad0-940">移除 `launchUrl` 屬性，以強制在專案的預設檔案 *index.html* 開啟應用程式 &mdash; 。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-940">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="b2ad0-941">此範例會呼叫 Web API 的所有 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-941">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="b2ad0-942">以下是關於呼叫 API 的說明。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-942">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="b2ad0-943">取得待辦事項的清單2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-943">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="b2ad0-944">jQuery 會將 HTTP GET 要求傳送至 web API，該 API 會傳回代表待辦事項陣列的 JSON。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-944">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="b2ad0-945">如果要求成功，則會叫用 `success` 回呼函式。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-945">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="b2ad0-946">在回呼中，DOM 已使用待辦事項資訊進行更新。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-946">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="b2ad0-947">新增待辦事項2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-947">Add a to-do item 2.1</span></span>

<span data-ttu-id="b2ad0-948">jQuery 會使用要求主體中的待辦事項來傳送 HTTP POST 要求。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-948">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="b2ad0-949">`accepts` 和 `contentType` 選項都設定為 `application/json`，以指定接收和傳送的媒體類型。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-949">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="b2ad0-950">待辦事項會使用 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 轉換成 JSON。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-950">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="b2ad0-951">當 API 傳回成功狀態碼時，會叫用 `getData` 函式來更新 HTML 資料表。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-951">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="b2ad0-952">更新待辦事項2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-952">Update a to-do item 2.1</span></span>

<span data-ttu-id="b2ad0-953">更新待辦事項類似於新增待辦事項。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-953">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="b2ad0-954">`url` 會變更為新增項目的唯一識別碼，而 `type` 是 `PUT`。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-954">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="b2ad0-955">刪除待辦事項2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-955">Delete a to-do item 2.1</span></span>

<span data-ttu-id="b2ad0-956">刪除待辦事項的達成方法是將 AJAX 呼叫的 `type` 設定為 `DELETE`，並在 URL 中指定項目的唯一識別碼。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-956">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="b2ad0-957">將驗證支援新增至 web API 2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-957">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="b2ad0-958">其他資源2。1</span><span class="sxs-lookup"><span data-stu-id="b2ad0-958">Additional resources 2.1</span></span>

<span data-ttu-id="b2ad0-959">[檢視或下載本教學課程的範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-959">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="b2ad0-960">請參閱[如何下載](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="b2ad0-960">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="b2ad0-961">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="b2ad0-961">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="b2ad0-962">本教學課程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="b2ad0-962">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
