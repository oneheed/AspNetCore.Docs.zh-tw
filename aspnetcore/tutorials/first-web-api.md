---
title: 教學課程：使用 ASP.NET Core 建立 Web API
author: rick-anderson
description: 了解如何使用 ASP.NET Core 建置 Web API。
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 02/04/2021
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: 43b243c946384be55019b39bf558182e1e95de90
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110192"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="ad0e3-103">教學課程：使用 ASP.NET Core 建立 Web API</span><span class="sxs-lookup"><span data-stu-id="ad0e3-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="ad0e3-104">由 [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Kirk Larkin](https://twitter.com/serpent5)和 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="ad0e3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="ad0e3-105">本教學課程將教導您使用 ASP.NET Core 建立 Web API 的基本概念。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ad0e3-106">在本教學課程中，您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ad0e3-107">建立 Web API 專案。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-107">Create a web API project.</span></span>
> * <span data-ttu-id="ad0e3-108">新增模型類別和資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="ad0e3-109">使用 CRUD 方法 Scaffold 控制器。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="ad0e3-110">設定路由、URL 路徑和傳回值。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="ad0e3-111">使用 Postman 呼叫 Web API。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-111">Call the web API with Postman.</span></span>

<span data-ttu-id="ad0e3-112">結束時，您會有一個 Web API，可以管理儲存在資料庫中的「待辦事項」。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="ad0e3-113">概觀</span><span class="sxs-lookup"><span data-stu-id="ad0e3-113">Overview</span></span>

<span data-ttu-id="ad0e3-114">本教學課程會建立以下 API：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="ad0e3-115">API</span><span class="sxs-lookup"><span data-stu-id="ad0e3-115">API</span></span> | <span data-ttu-id="ad0e3-116">描述</span><span class="sxs-lookup"><span data-stu-id="ad0e3-116">Description</span></span> | <span data-ttu-id="ad0e3-117">要求本文</span><span class="sxs-lookup"><span data-stu-id="ad0e3-117">Request body</span></span> | <span data-ttu-id="ad0e3-118">回應本文</span><span class="sxs-lookup"><span data-stu-id="ad0e3-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="ad0e3-119">取得所有待辦事項</span><span class="sxs-lookup"><span data-stu-id="ad0e3-119">Get all to-do items</span></span> | <span data-ttu-id="ad0e3-120">無</span><span class="sxs-lookup"><span data-stu-id="ad0e3-120">None</span></span> | <span data-ttu-id="ad0e3-121">待辦事項的陣列</span><span class="sxs-lookup"><span data-stu-id="ad0e3-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="ad0e3-122">依識別碼取得項目</span><span class="sxs-lookup"><span data-stu-id="ad0e3-122">Get an item by ID</span></span> | <span data-ttu-id="ad0e3-123">無</span><span class="sxs-lookup"><span data-stu-id="ad0e3-123">None</span></span> | <span data-ttu-id="ad0e3-124">待辦事項</span><span class="sxs-lookup"><span data-stu-id="ad0e3-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="ad0e3-125">新增記錄</span><span class="sxs-lookup"><span data-stu-id="ad0e3-125">Add a new item</span></span> | <span data-ttu-id="ad0e3-126">待辦事項</span><span class="sxs-lookup"><span data-stu-id="ad0e3-126">To-do item</span></span> | <span data-ttu-id="ad0e3-127">待辦事項</span><span class="sxs-lookup"><span data-stu-id="ad0e3-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="ad0e3-128">更新現有的項目 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ad0e3-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="ad0e3-129">待辦事項</span><span class="sxs-lookup"><span data-stu-id="ad0e3-129">To-do item</span></span> | <span data-ttu-id="ad0e3-130">無</span><span class="sxs-lookup"><span data-stu-id="ad0e3-130">None</span></span> |
|<span data-ttu-id="ad0e3-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ad0e3-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="ad0e3-132">刪除專案 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="ad0e3-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="ad0e3-133">無</span><span class="sxs-lookup"><span data-stu-id="ad0e3-133">None</span></span> | <span data-ttu-id="ad0e3-134">無</span><span class="sxs-lookup"><span data-stu-id="ad0e3-134">None</span></span>|

<span data-ttu-id="ad0e3-135">下圖顯示應用程式的設計。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-135">The following diagram shows the design of the app.</span></span>

![左側方塊代表用戶端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="ad0e3-141">必要條件</span><span class="sxs-lookup"><span data-stu-id="ad0e3-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad0e3-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad0e3-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad0e3-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad0e3-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ad0e3-144">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ad0e3-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="ad0e3-145">建立 Web 專案</span><span class="sxs-lookup"><span data-stu-id="ad0e3-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad0e3-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad0e3-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ad0e3-147">從 [ **檔案** ] 功能表選取 [ **新增** > **專案**]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ad0e3-148">選取 **ASP.NET Core Web 應用程式** 範本，然後按一下 [下一步]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="ad0e3-149">將專案命名為 *TodoApi*，然後按一下 [建立]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="ad0e3-150">在 [ **建立新的 ASP.NET Core Web 應用程式** ] 對話方塊中，確認已選取 [ **.net Core** ] 和 [ **ASP.NET Core 5.0** ]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="ad0e3-151">選取 **API** 範本，然後按一下 [建立]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-151">Select the **API** template and click **Create**.</span></span>

![VS 新增專案對話方塊](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad0e3-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad0e3-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ad0e3-154">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ad0e3-155">將目錄 (`cd`) 變更為包含專案資料夾的資料夾。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="ad0e3-156">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="ad0e3-157">當出現對話方塊詢問您是否要將所需的資產新增至專案時，選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="ad0e3-158">上述命令：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-158">The preceding commands:</span></span>

  * <span data-ttu-id="ad0e3-159">建立新的 Web API 專案，然後在 Visual Studio Code 中予以開啟。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="ad0e3-160">新增下一節需要的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ad0e3-161">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ad0e3-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ad0e3-162">選取 [檔案]**[新增解決方案]** > 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-162">Select **File** > **New Solution**.</span></span>

  ![macOS 新增方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="ad0e3-164">在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用程式**  >  **API**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="ad0e3-165">在8.6 版或更新版本中，選取 [ **Web] 和 [主控台**  >  **應用程式**  >  **API**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 範本選取專案](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="ad0e3-167">在 [ **設定新的 ASP.NET Core WEB API** ] 對話方塊中，選取最新的 .net Core 5.X **目標 Framework**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="ad0e3-168">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-168">Select **Next**.</span></span>

* <span data-ttu-id="ad0e3-169">針對 [專案名稱] 輸入 *TodoApi*，然後選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![設定對話方塊](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="ad0e3-171">在專案資料夾中開啟命令終端機，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-171">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="ad0e3-172">測試專案</span><span class="sxs-lookup"><span data-stu-id="ad0e3-172">Test the project</span></span>

<span data-ttu-id="ad0e3-173">專案範本會建立 `WeatherForecast` 支援 [Swagger](xref:tutorials/web-api-help-pages-using-swagger)的 API。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad0e3-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad0e3-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ad0e3-175">按 Ctrl+F5 即可執行而不使用偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="ad0e3-176">Visual Studio 會啟動：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-176">Visual Studio launches:</span></span>

* <span data-ttu-id="ad0e3-177">IIS Express web 伺服器。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-177">The IIS Express web server.</span></span>
* <span data-ttu-id="ad0e3-178">預設瀏覽器並流覽至 `https://localhost:<port>/swagger/index.html` ，其中 `<port>` 是隨機播放的埠號碼。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad0e3-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad0e3-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="ad0e3-180">按 Ctrl+F5 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="ad0e3-181">在瀏覽器中，移至下列 URL： [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="ad0e3-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ad0e3-182">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ad0e3-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ad0e3-183">選取 [**執行**  >  **開始調試** 程式] 以啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="ad0e3-184">Visual Studio for Mac 會啟動瀏覽器並巡覽至 `https://localhost:<port>`，其中 `<port>` 是隨機選擇的連接埠號碼。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="ad0e3-185">傳回 HTTP 404 (找不到) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="ad0e3-186">將 `/swagger` 附加至 URL (將 URL 變更為 `https://localhost:<port>/swagger`)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="ad0e3-187">Swagger 頁面隨即 `/swagger/index.html` 顯示。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="ad0e3-188">選取 [**立即**  >  **試用**]  >  **執行**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="ad0e3-189">頁面會顯示：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-189">The page displays:</span></span>

* <span data-ttu-id="ad0e3-190">用來測試 WeatherForecast API 的 [捲曲](https://curl.haxx.se/) 命令。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="ad0e3-191">用來測試 WeatherForecast API 的 URL。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="ad0e3-192">回應碼、主體和標頭。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="ad0e3-193">具有媒體類型的下拉式清單方塊，以及範例值和架構。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="ad0e3-194">Swagger 可用來產生 web Api 的實用檔和說明頁面。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="ad0e3-195">本教學課程著重于建立 web API。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="ad0e3-196">如需 Swagger 的詳細資訊，請參閱 <xref:tutorials/web-api-help-pages-using-swagger> 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="ad0e3-197">將 **要求 URL** 複製並貼入瀏覽器中：  `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="ad0e3-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="ad0e3-198">系統會傳回與下列類似的 JSON：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="ad0e3-199">更新 launchUrl</span><span class="sxs-lookup"><span data-stu-id="ad0e3-199">Update the launchUrl</span></span>

<span data-ttu-id="ad0e3-200">在 *Properties\launchSettings.js開啟*，請 `launchUrl` 從更新 `"swagger"` 為 `"api/TodoItems"` ：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="ad0e3-201">由於 Swagger 已移除，因此上述標記會將啟動的 URL 變更為在下列各節中新增之控制器的 GET 方法。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="ad0e3-202">新增模型類別</span><span class="sxs-lookup"><span data-stu-id="ad0e3-202">Add a model class</span></span>

<span data-ttu-id="ad0e3-203">「模型」是代表應用程式所管理資料的一組類別。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="ad0e3-204">此應用程式的模型是單一 `TodoItem` 類別。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad0e3-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad0e3-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ad0e3-206">在 [ **方案 Explorer**] 中，以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="ad0e3-207">選取 **[**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ad0e3-208">為資料夾命名 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="ad0e3-209">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [**加入**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ad0e3-210">將類別命名為 *TodoItem*，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="ad0e3-211">以下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad0e3-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad0e3-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ad0e3-213">新增名為的資料夾 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="ad0e3-214">`TodoItem`使用下列程式碼，將類別新增至 *Models* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ad0e3-215">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ad0e3-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ad0e3-216">以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-216">Right-click the project.</span></span> <span data-ttu-id="ad0e3-217">選取 **[**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ad0e3-218">為資料夾命名 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-218">Name the folder *Models*.</span></span>

  ![新增資料夾](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="ad0e3-220">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 > [**新增** 檔案 > **一般**] > **空白類別**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="ad0e3-221">將類別命名為 *TodoItem*，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="ad0e3-222">以下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="ad0e3-223">`Id` 屬性的功能相當於關聯式資料庫中的唯一索引鍵。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="ad0e3-224">模型類別可移至專案中的任何位置，但依照慣例，會 *Models* 使用資料夾。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="ad0e3-225">新增資料庫內容</span><span class="sxs-lookup"><span data-stu-id="ad0e3-225">Add a database context</span></span>

<span data-ttu-id="ad0e3-226">「資料庫內容」是為資料模型協調 Entity Framework 功能的主要類別。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="ad0e3-227">此類別是透過衍生自 <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> 類別來建立。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad0e3-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad0e3-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="ad0e3-229">新增 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="ad0e3-229">Add NuGet packages</span></span>

* <span data-ttu-id="ad0e3-230">在 [工具] 功能表上，選取 [NuGet 套件管理員] > [管理解決方案的 NuGet 套件]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="ad0e3-231">選取 [ **流覽** ] 索引標籤，然後 `Microsoft.EntityFrameworkCore.InMemory` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-231">Select the **Browse** tab, and then enter `Microsoft.EntityFrameworkCore.InMemory` in the search box.</span></span>
* <span data-ttu-id="ad0e3-232">選取 `Microsoft.EntityFrameworkCore.InMemory` 左窗格中的。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-232">Select `Microsoft.EntityFrameworkCore.InMemory` in the left pane.</span></span>
* <span data-ttu-id="ad0e3-233">選取右窗格中的 [專案] 核取方塊，然後選取 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![NuGet 套件管理員](first-web-api/_static/5/vsNuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="ad0e3-235">新增 TodoCoNtext 資料庫內容</span><span class="sxs-lookup"><span data-stu-id="ad0e3-235">Add the TodoContext database context</span></span>

* <span data-ttu-id="ad0e3-236">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [**加入**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-236">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ad0e3-237">將類別命名為 *TodoContext*，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-237">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ad0e3-238">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ad0e3-238">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ad0e3-239">將 `TodoContext` 類別新增至 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-239">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="ad0e3-240">輸入下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-240">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="ad0e3-241">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="ad0e3-241">Register the database context</span></span>

<span data-ttu-id="ad0e3-242">在 ASP.NET Core 中，資料庫內容等服務必須向[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器註冊。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-242">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ad0e3-243">此容器會將服務提供給控制器。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-243">The container provides the service to controllers.</span></span>

<span data-ttu-id="ad0e3-244">使用下列程式碼更新 *Startup.cs* ：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-244">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="ad0e3-245">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-245">The preceding code:</span></span>

* <span data-ttu-id="ad0e3-246">移除 Swagger 呼叫。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-246">Removes the Swagger calls.</span></span>
* <span data-ttu-id="ad0e3-247">移除未使用的 `using` 宣告。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-247">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="ad0e3-248">將資料庫內容新增至 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-248">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="ad0e3-249">指定資料庫內容將會使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-249">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="ad0e3-250">Scaffold 控制器</span><span class="sxs-lookup"><span data-stu-id="ad0e3-250">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad0e3-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad0e3-251">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ad0e3-252">以滑鼠右鍵按一下 *Controllers* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-252">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="ad0e3-253">選取 [新增]**[新增 Scaffold 項目]** > 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-253">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="ad0e3-254">選取 [使用 Entity Framework 執行動作的 API 控制器]，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-254">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="ad0e3-255">在 [使用 Entity Framework 執行動作的 API 控制器] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-255">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="ad0e3-256">選取 **模型類別** 中的 [ **TodoItem (TodoApi] Models) 。**</span><span class="sxs-lookup"><span data-stu-id="ad0e3-256">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="ad0e3-257">選取 **資料內容類別** 中的 **TodoCoNtext (TodoApi Models) 。**</span><span class="sxs-lookup"><span data-stu-id="ad0e3-257">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="ad0e3-258">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-258">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ad0e3-259">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ad0e3-259">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="ad0e3-260">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-260">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="ad0e3-261">上述命令：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-261">The preceding commands:</span></span>

* <span data-ttu-id="ad0e3-262">新增 Scaffolding 所需的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-262">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="ad0e3-263">安裝 Scaffolding 引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-263">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="ad0e3-264">Scaffold `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-264">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="ad0e3-265">產生的程式碼：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-265">The generated code:</span></span>

* <span data-ttu-id="ad0e3-266">標記具有屬性的類別 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-266">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="ad0e3-267">這個屬性表示控制器會回應 Web API 要求。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-267">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="ad0e3-268">如需屬性所啟用之特定行為的相關資訊，請參閱 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-268">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="ad0e3-269">使用 DI 將資料庫內容 (`TodoContext`) 插入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-269">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="ad0e3-270">控制器中的每一個 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法都會使用資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-270">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="ad0e3-271">的 ASP.NET 核心範本：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-271">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="ad0e3-272">具有 views 的控制器包含 `[action]` 在路由範本中。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-272">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="ad0e3-273">API 控制器不包含 `[action]` 在路由範本中。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-273">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="ad0e3-274">當 `[action]` 權杖不在路由範本中時，會從路由中排除 [動作](xref:mvc/controllers/routing#action) 名稱。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-274">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="ad0e3-275">也就是，不會在相符的路由中使用動作的相關聯方法名稱。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-275">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="ad0e3-276">更新 PostTodoItem create 方法</span><span class="sxs-lookup"><span data-stu-id="ad0e3-276">Update the PostTodoItem create method</span></span>

<span data-ttu-id="ad0e3-277">更新中的 return 語句 `PostTodoItem` ，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 運算子：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-277">Update the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="ad0e3-278">上述程式碼是 HTTP POST 方法，如屬性所指示 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-278">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="ad0e3-279">該方法會從 HTTP 要求本文取得待辦事項的值。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-279">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="ad0e3-280">如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-280">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="ad0e3-281"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-281">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="ad0e3-282">如果成功，則傳回 [HTTP 201 狀態碼](https://developer.mozilla.org/docs/Web/HTTP/Status/201) 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-282">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="ad0e3-283">對於可在伺服器上建立新資源的 HTTP POST 方法，其標準回應是 HTTP 201。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-283">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="ad0e3-284">將 [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 標頭新增至回應。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-284">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="ad0e3-285">`Location`標頭會指定新建立之待辦事項的[URI](https://developer.mozilla.org/docs/Glossary/URI) 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-285">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="ad0e3-286">如需詳細資訊，請參閱 [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (已建立 10.2.2 201)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-286">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="ad0e3-287">參考 `GetTodoItem` 動作以建立 `Location` 標頭的 URI。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-287">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="ad0e3-288">C# `nameof` 關鍵字是用來避免在 `CreatedAtAction` 呼叫中以硬式編碼方式寫入動作名稱。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-288">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="ad0e3-289">安裝 Postman</span><span class="sxs-lookup"><span data-stu-id="ad0e3-289">Install Postman</span></span>

<span data-ttu-id="ad0e3-290">本教學課程使用 Postman 來測試 Web API。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-290">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="ad0e3-291">安裝 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="ad0e3-291">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="ad0e3-292">啟動 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-292">Start the web app.</span></span>
* <span data-ttu-id="ad0e3-293">啟動 Postman。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-293">Start Postman.</span></span>
* <span data-ttu-id="ad0e3-294">停用 [SSL certificate verification] \(SSL 憑證驗證\)</span><span class="sxs-lookup"><span data-stu-id="ad0e3-294">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="ad0e3-295">從 [檔案]**[設定]** >  ([一般] 索引標籤)，停用 [SSL 憑證驗證]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-295">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="ad0e3-296">在測試控制器之後，請重新啟用 [SSL certificate verification] \(SSL 憑證驗證\)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-296">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="ad0e3-297">使用 Postman 測試 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="ad0e3-297">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="ad0e3-298">建立新的要求。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-298">Create a new request.</span></span>
* <span data-ttu-id="ad0e3-299">將 HTTP 方法設為 `POST`。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-299">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="ad0e3-300">將 URI 設定為 `https://localhost:<port>/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-300">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="ad0e3-301">例如： `https://localhost:5001/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-301">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="ad0e3-302">選取 [Body] \(本文\) 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-302">Select the **Body** tab.</span></span>
* <span data-ttu-id="ad0e3-303">選取 [原始] 選項按鈕。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-303">Select the **raw** radio button.</span></span>
* <span data-ttu-id="ad0e3-304">將類型設定為 **JSON (application/json)**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-304">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="ad0e3-305">在要求本文中，針對待辦項目輸入 JSON：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-305">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="ad0e3-306">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-306">Select **Send**.</span></span>

  ![Postman 與建立要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="ad0e3-308">測試位置標頭 URI</span><span class="sxs-lookup"><span data-stu-id="ad0e3-308">Test the location header URI</span></span>

<span data-ttu-id="ad0e3-309">您可以在瀏覽器中測試位置標頭 URI。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-309">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="ad0e3-310">複製位置標頭 URI，並將其貼到瀏覽器中。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-310">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="ad0e3-311">若要在 Postman 中進行測試：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-311">To test in Postman:</span></span>

* <span data-ttu-id="ad0e3-312">在 [回應] 窗格中選取 [標頭] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-312">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="ad0e3-313">複製 [位置] 標頭值：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-313">Copy the **Location** header value:</span></span>

  ![Postman 主控台的 [標頭] 索引標籤](first-web-api/_static/3/create.png)

* <span data-ttu-id="ad0e3-315">將 HTTP 方法設為 `GET`。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-315">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="ad0e3-316">將 URI 設定為 `https://localhost:<port>/api/TodoItems/1` 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-316">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="ad0e3-317">例如： `https://localhost:5001/api/TodoItems/1` 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-317">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="ad0e3-318">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-318">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="ad0e3-319">檢查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="ad0e3-319">Examine the GET methods</span></span>

<span data-ttu-id="ad0e3-320">系統會執行兩個 GET 端點：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-320">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="ad0e3-321">從瀏覽器或 Postman 呼叫這兩個端點來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-321">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="ad0e3-322">例如：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-322">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="ad0e3-323">`GetTodoItems` 的呼叫會產生類似下列的回應：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-323">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="ad0e3-324">使用 Postman 測試 Get</span><span class="sxs-lookup"><span data-stu-id="ad0e3-324">Test Get with Postman</span></span>

* <span data-ttu-id="ad0e3-325">建立新的要求。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-325">Create a new request.</span></span>
* <span data-ttu-id="ad0e3-326">將 HTTP 方法設定為 **GET**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-326">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="ad0e3-327">將要求 URI 設定為 `https://localhost:<port>/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-327">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="ad0e3-328">例如： `https://localhost:5001/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-328">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="ad0e3-329">在 Postman 中，設定 [Two pane view] \(雙窗格檢視\)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-329">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="ad0e3-330">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-330">Select **Send**.</span></span>

<span data-ttu-id="ad0e3-331">這個應用程式會使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-331">This app uses an in-memory database.</span></span> <span data-ttu-id="ad0e3-332">如果應用程式在停止後再啟動，上述 GET 要求將不會傳回任何資料。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-332">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="ad0e3-333">如果沒有傳回任何資料，請將資料 [POST](#post) 到應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-333">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="ad0e3-334">傳送和 URL 路徑</span><span class="sxs-lookup"><span data-stu-id="ad0e3-334">Routing and URL paths</span></span>

<span data-ttu-id="ad0e3-335">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)屬性代表回應 HTTP GET 要求的方法。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-335">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="ad0e3-336">每個方法的 URL 路徑的建構方式如下：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-336">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="ad0e3-337">一開始在控制器的 `Route` 屬性中使用範本字串：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-337">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="ad0e3-338">以控制器的名稱取代 `[controller]`，也就是將控制器類別名稱減去 "Controller" 字尾。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-338">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="ad0e3-339">在此範例中，控制器類別名稱是 **TodoItems** Controller，因此控制器名稱是 "TodoItems"。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-339">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="ad0e3-340">ASP.NET Core [路由](xref:mvc/controllers/routing)不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-340">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="ad0e3-341">如果 `[HttpGet]` 屬性具有路由範本 (例如 `[HttpGet("products")]`)，請將其附加到路徑。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-341">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="ad0e3-342">此範例不使用範本。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-342">This sample doesn't use a template.</span></span> <span data-ttu-id="ad0e3-343">如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-343">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="ad0e3-344">在下列 `GetTodoItem` 方法中，`"{id}"` 是待辦事項唯一識別碼的預留位置變數。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-344">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="ad0e3-345">當叫 `GetTodoItem` 用時，會將 `"{id}"` URL 中的值提供給方法的 `id` 參數。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-345">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="ad0e3-346">傳回值</span><span class="sxs-lookup"><span data-stu-id="ad0e3-346">Return values</span></span>

<span data-ttu-id="ad0e3-347">和方法的傳回型 `GetTodoItems` 別 `GetTodoItem` 為 [ActionResult \<T> 類型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-347">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="ad0e3-348">ASP.NET Core 會自動將物件序列化為 [JSON](https://www.json.org/)，並將 JSON 寫入至回應訊息的本文。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-348">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="ad0e3-349">如果沒有任何未處理的例外狀況，則此傳回型別的回應碼為 [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-349">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="ad0e3-350">未處理的例外狀況會轉譯成 5xx 錯誤。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-350">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="ad0e3-351">`ActionResult` 傳回型別可代表各種 HTTP 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-351">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="ad0e3-352">例如，`GetTodoItem` 可傳回兩個不同的狀態值：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-352">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="ad0e3-353">如果沒有任何專案符合要求的識別碼，方法會傳回 [404 狀態](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 錯誤碼。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-353">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="ad0e3-354">否則，方法會傳回 200 與 JSON 回應本文。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-354">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="ad0e3-355">傳回 `item` 會導致 HTTP 200 回應。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-355">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="ad0e3-356">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="ad0e3-356">The PutTodoItem method</span></span>

<span data-ttu-id="ad0e3-357">檢查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-357">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="ad0e3-358">`PutTodoItem` 類似於 `PostTodoItem`，但是會使用 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-358">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="ad0e3-359">回應為 [204 (沒有內容) ](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-359">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="ad0e3-360">根據 HTTP 規格，PUT 要求需要用戶端傳送整個更新的實體，而不只是變更。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-360">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="ad0e3-361">若要支援部分更新，請使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-361">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="ad0e3-362">如果在呼叫 `PutTodoItem` 時發生錯誤，請呼叫 `GET` 以確保資料庫中有項目。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-362">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="ad0e3-363">測試 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="ad0e3-363">Test the PutTodoItem method</span></span>

<span data-ttu-id="ad0e3-364">這個範例會使用必須在每次啟動應用程式時初始化的記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-364">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="ad0e3-365">資料庫中必須有項目，您才能進行 PUT 呼叫。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-365">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="ad0e3-366">在進行 PUT 呼叫之前，請先呼叫 GET 以確保資料庫中有專案。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-366">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="ad0e3-367">更新識別碼為1的待辦事項，並將其名稱設定為 `"feed fish"` ：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-367">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="ad0e3-368">下圖顯示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-368">The following image shows the Postman update:</span></span>

![顯示「204 (沒有內容) 回應」的 Postman 主控台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="ad0e3-370">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="ad0e3-370">The DeleteTodoItem method</span></span>

<span data-ttu-id="ad0e3-371">檢查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-371">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="ad0e3-372">測試 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="ad0e3-372">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="ad0e3-373">使用 Postman 刪除待辦事項：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-373">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="ad0e3-374">將方法設定為 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-374">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="ad0e3-375">設定要刪除之物件的 URI (例如 `https://localhost:5001/api/TodoItems/1`) 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-375">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="ad0e3-376">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-376">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="ad0e3-377">防止過度張貼</span><span class="sxs-lookup"><span data-stu-id="ad0e3-377">Prevent over-posting</span></span>

<span data-ttu-id="ad0e3-378">範例應用程式目前會公開整個 `TodoItem` 物件。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-378">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="ad0e3-379">生產環境應用程式通常會限制使用模型子集來輸入和傳回的資料。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-379">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="ad0e3-380">這項功能有許多原因，而且安全性是主要的原因。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-380">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="ad0e3-381">模型的子集通常稱為資料傳輸物件 (DTO) 、輸入模型或視圖模型。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-381">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="ad0e3-382">此文章中使用 **DTO** 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-382">**DTO** is used in this article.</span></span>

<span data-ttu-id="ad0e3-383">DTO 可以用來：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-383">A DTO may be used to:</span></span>

* <span data-ttu-id="ad0e3-384">防止過度張貼。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-384">Prevent over-posting.</span></span>
* <span data-ttu-id="ad0e3-385">隱藏用戶端不應該看到的屬性。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-385">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="ad0e3-386">略過某些屬性，以減少承載大小。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-386">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="ad0e3-387">壓平合併包含嵌套物件的物件圖形。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-387">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="ad0e3-388">簡維物件圖形對於用戶端來說可能更方便。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-388">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="ad0e3-389">若要示範 DTO 方法，請更新 `TodoItem` 類別以包含秘密欄位：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-389">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="ad0e3-390">此應用程式必須隱藏秘密欄位，但系統管理應用程式可以選擇將它公開。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-390">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="ad0e3-391">確認您可以張貼並取得秘密欄位。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-391">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="ad0e3-392">建立 DTO 模型：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-392">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="ad0e3-393">更新 `TodoItemsController` 以使用 `TodoItemDTO` ：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-393">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="ad0e3-394">確認您無法張貼或取得秘密欄位。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-394">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="ad0e3-395">使用 JavaScript 呼叫 Web API</span><span class="sxs-lookup"><span data-stu-id="ad0e3-395">Call the web API with JavaScript</span></span>

<span data-ttu-id="ad0e3-396">請參閱 [教學課程：使用 JavaScript 呼叫 ASP.NET Core WEB API](xref:tutorials/web-api-javascript)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-396">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="ad0e3-397">在本教學課程中，您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-397">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ad0e3-398">建立 Web API 專案。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-398">Create a web API project.</span></span>
> * <span data-ttu-id="ad0e3-399">新增模型類別和資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-399">Add a model class and a database context.</span></span>
> * <span data-ttu-id="ad0e3-400">使用 CRUD 方法 Scaffold 控制器。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-400">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="ad0e3-401">設定路由、URL 路徑和傳回值。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-401">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="ad0e3-402">使用 Postman 呼叫 Web API。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-402">Call the web API with Postman.</span></span>

<span data-ttu-id="ad0e3-403">結束時，您會有一個 Web API，可以管理儲存在資料庫中的「待辦事項」。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-403">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="ad0e3-404">概觀</span><span class="sxs-lookup"><span data-stu-id="ad0e3-404">Overview</span></span>

<span data-ttu-id="ad0e3-405">本教學課程會建立以下 API：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-405">This tutorial creates the following API:</span></span>

|<span data-ttu-id="ad0e3-406">API</span><span class="sxs-lookup"><span data-stu-id="ad0e3-406">API</span></span> | <span data-ttu-id="ad0e3-407">描述</span><span class="sxs-lookup"><span data-stu-id="ad0e3-407">Description</span></span> | <span data-ttu-id="ad0e3-408">要求本文</span><span class="sxs-lookup"><span data-stu-id="ad0e3-408">Request body</span></span> | <span data-ttu-id="ad0e3-409">回應本文</span><span class="sxs-lookup"><span data-stu-id="ad0e3-409">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="ad0e3-410">取得所有待辦事項</span><span class="sxs-lookup"><span data-stu-id="ad0e3-410">Get all to-do items</span></span> | <span data-ttu-id="ad0e3-411">無</span><span class="sxs-lookup"><span data-stu-id="ad0e3-411">None</span></span> | <span data-ttu-id="ad0e3-412">待辦事項的陣列</span><span class="sxs-lookup"><span data-stu-id="ad0e3-412">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="ad0e3-413">依識別碼取得項目</span><span class="sxs-lookup"><span data-stu-id="ad0e3-413">Get an item by ID</span></span> | <span data-ttu-id="ad0e3-414">無</span><span class="sxs-lookup"><span data-stu-id="ad0e3-414">None</span></span> | <span data-ttu-id="ad0e3-415">待辦事項</span><span class="sxs-lookup"><span data-stu-id="ad0e3-415">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="ad0e3-416">新增記錄</span><span class="sxs-lookup"><span data-stu-id="ad0e3-416">Add a new item</span></span> | <span data-ttu-id="ad0e3-417">待辦事項</span><span class="sxs-lookup"><span data-stu-id="ad0e3-417">To-do item</span></span> | <span data-ttu-id="ad0e3-418">待辦事項</span><span class="sxs-lookup"><span data-stu-id="ad0e3-418">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="ad0e3-419">更新現有的項目 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ad0e3-419">Update an existing item &nbsp;</span></span> | <span data-ttu-id="ad0e3-420">待辦事項</span><span class="sxs-lookup"><span data-stu-id="ad0e3-420">To-do item</span></span> | <span data-ttu-id="ad0e3-421">無</span><span class="sxs-lookup"><span data-stu-id="ad0e3-421">None</span></span> |
|<span data-ttu-id="ad0e3-422">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ad0e3-422">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="ad0e3-423">刪除專案 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="ad0e3-423">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="ad0e3-424">無</span><span class="sxs-lookup"><span data-stu-id="ad0e3-424">None</span></span> | <span data-ttu-id="ad0e3-425">無</span><span class="sxs-lookup"><span data-stu-id="ad0e3-425">None</span></span>|

<span data-ttu-id="ad0e3-426">下圖顯示應用程式的設計。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-426">The following diagram shows the design of the app.</span></span>

![左側方塊代表用戶端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="ad0e3-432">必要條件</span><span class="sxs-lookup"><span data-stu-id="ad0e3-432">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad0e3-433">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad0e3-433">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad0e3-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad0e3-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ad0e3-435">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ad0e3-435">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="ad0e3-436">建立 Web 專案</span><span class="sxs-lookup"><span data-stu-id="ad0e3-436">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad0e3-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad0e3-437">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ad0e3-438">從 [ **檔案** ] 功能表選取 [ **新增** > **專案**]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-438">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ad0e3-439">選取 **ASP.NET Core Web 應用程式** 範本，然後按一下 [下一步]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-439">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="ad0e3-440">將專案命名為 *TodoApi*，然後按一下 [建立]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-440">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="ad0e3-441">在 [ **建立新的 ASP.NET Core Web 應用程式** ] 對話方塊中，確認已選取 [ **.net Core** ] 和 [ **ASP.NET Core 3.1** ]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-441">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="ad0e3-442">選取 **API** 範本，然後按一下 [建立]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-442">Select the **API** template and click **Create**.</span></span>

![VS 新增專案對話方塊](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad0e3-444">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad0e3-444">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ad0e3-445">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-445">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ad0e3-446">將目錄 (`cd`) 變更為包含專案資料夾的資料夾。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-446">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="ad0e3-447">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-447">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="ad0e3-448">當出現對話方塊詢問您是否要將所需的資產新增至專案時，選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-448">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="ad0e3-449">上述命令：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-449">The preceding commands:</span></span>

  * <span data-ttu-id="ad0e3-450">建立新的 Web API 專案，然後在 Visual Studio Code 中予以開啟。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-450">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="ad0e3-451">新增下一節需要的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-451">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ad0e3-452">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ad0e3-452">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ad0e3-453">選取 [檔案]**[新增解決方案]** > 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-453">Select **File** > **New Solution**.</span></span>

  ![macOS 新增方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="ad0e3-455">在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用程式**  >  **API**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-455">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="ad0e3-456">在8.6 版或更新版本中，選取 [ **Web] 和 [主控台**  >  **應用程式**  >  **API**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-456">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 範本選取專案](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="ad0e3-458">在 [ **設定新的 ASP.NET Core WEB API** ] 對話方塊中，選取最新的 .net Core 3.X **目標 Framework**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-458">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="ad0e3-459">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-459">Select **Next**.</span></span>

* <span data-ttu-id="ad0e3-460">針對 [專案名稱] 輸入 *TodoApi*，然後選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-460">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![設定對話方塊](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="ad0e3-462">在專案資料夾中開啟命令終端機，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-462">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="ad0e3-463">測試 API</span><span class="sxs-lookup"><span data-stu-id="ad0e3-463">Test the API</span></span>

<span data-ttu-id="ad0e3-464">專案範本會建立 `WeatherForecast` API。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-464">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="ad0e3-465">從瀏覽器呼叫 `Get` 方法來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-465">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad0e3-466">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad0e3-466">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ad0e3-467">按 Ctrl+F5 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-467">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="ad0e3-468">Visual Studio 會啟動瀏覽器並巡覽至 `https://localhost:<port>/WeatherForecast`，其中 `<port>` 是隨機選擇的通訊埠編號。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-468">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="ad0e3-469">如果出現對話方塊詢問您是否應該信任 IIS Express 憑證，請選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-469">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="ad0e3-470">在接著出現的 [安全性警告] 對話方塊中，選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-470">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad0e3-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad0e3-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ad0e3-472">按 Ctrl+F5 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-472">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="ad0e3-473">在瀏覽器中，前往下列 URL：`https://localhost:5001/WeatherForecast`。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-473">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ad0e3-474">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ad0e3-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ad0e3-475">選取 [**執行**  >  **開始調試** 程式] 以啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-475">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="ad0e3-476">Visual Studio for Mac 會啟動瀏覽器並巡覽至 `https://localhost:<port>`，其中 `<port>` 是隨機選擇的連接埠號碼。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-476">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="ad0e3-477">傳回 HTTP 404 (找不到) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-477">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="ad0e3-478">將 `/WeatherForecast` 附加至 URL (將 URL 變更為 `https://localhost:<port>/WeatherForecast`)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-478">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="ad0e3-479">系統會傳回與下列類似的 JSON：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-479">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="ad0e3-480">新增模型類別</span><span class="sxs-lookup"><span data-stu-id="ad0e3-480">Add a model class</span></span>

<span data-ttu-id="ad0e3-481">「模型」是代表應用程式所管理資料的一組類別。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-481">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="ad0e3-482">此應用程式的模型是單一 `TodoItem` 類別。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-482">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad0e3-483">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad0e3-483">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ad0e3-484">在 [ **方案 Explorer**] 中，以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-484">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="ad0e3-485">選取 **[**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-485">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ad0e3-486">為資料夾命名 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-486">Name the folder *Models*.</span></span>

* <span data-ttu-id="ad0e3-487">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [**加入**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-487">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ad0e3-488">將類別命名為 *TodoItem*，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-488">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="ad0e3-489">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-489">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad0e3-490">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad0e3-490">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ad0e3-491">新增名為的資料夾 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-491">Add a folder named *Models*.</span></span>

* <span data-ttu-id="ad0e3-492">`TodoItem`使用下列程式碼，將類別新增至 *Models* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-492">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ad0e3-493">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ad0e3-493">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ad0e3-494">以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-494">Right-click the project.</span></span> <span data-ttu-id="ad0e3-495">選取 **[**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-495">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ad0e3-496">為資料夾命名 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-496">Name the folder *Models*.</span></span>

  ![新增資料夾](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="ad0e3-498">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 > [**新增** 檔案 > **一般**] > **空白類別**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-498">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="ad0e3-499">將類別命名為 *TodoItem*，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-499">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="ad0e3-500">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-500">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="ad0e3-501">`Id` 屬性的功能相當於關聯式資料庫中的唯一索引鍵。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-501">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="ad0e3-502">模型類別可移至專案中的任何位置，但依照慣例，會 *Models* 使用資料夾。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-502">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="ad0e3-503">新增資料庫內容</span><span class="sxs-lookup"><span data-stu-id="ad0e3-503">Add a database context</span></span>

<span data-ttu-id="ad0e3-504">「資料庫內容」是為資料模型協調 Entity Framework 功能的主要類別。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-504">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="ad0e3-505">此類別是透過衍生自 `Microsoft.EntityFrameworkCore.DbContext` 類別來建立。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-505">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad0e3-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad0e3-506">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="ad0e3-507">新增 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="ad0e3-507">Add NuGet packages</span></span>

* <span data-ttu-id="ad0e3-508">在 [工具] 功能表上，選取 [NuGet 套件管理員] > [管理解決方案的 NuGet 套件]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-508">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="ad0e3-509">選取 [**流覽**] 索引標籤，然後在 [搜尋] 方塊中輸入 **microsoft.entityframeworkcore。**</span><span class="sxs-lookup"><span data-stu-id="ad0e3-509">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.InMemory** in the search box.</span></span>
* <span data-ttu-id="ad0e3-510">選取左窗格中的 [ **microsoft.entityframeworkcore** ]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-510">Select **Microsoft.EntityFrameworkCore.InMemory** in the left pane.</span></span>
* <span data-ttu-id="ad0e3-511">選取右窗格中的 [專案] 核取方塊，然後選取 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-511">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![NuGet 套件管理員](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="ad0e3-513">新增 TodoCoNtext 資料庫內容</span><span class="sxs-lookup"><span data-stu-id="ad0e3-513">Add the TodoContext database context</span></span>

* <span data-ttu-id="ad0e3-514">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [**加入**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-514">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ad0e3-515">將類別命名為 *TodoContext*，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-515">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ad0e3-516">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ad0e3-516">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ad0e3-517">將 `TodoContext` 類別新增至 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-517">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="ad0e3-518">輸入下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-518">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="ad0e3-519">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="ad0e3-519">Register the database context</span></span>

<span data-ttu-id="ad0e3-520">在 ASP.NET Core 中，資料庫內容等服務必須向[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器註冊。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-520">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ad0e3-521">此容器會將服務提供給控制器。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-521">The container provides the service to controllers.</span></span>

<span data-ttu-id="ad0e3-522">使用下列醒目提示的程式碼更新 *Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-522">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="ad0e3-523">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-523">The preceding code:</span></span>

* <span data-ttu-id="ad0e3-524">移除未使用的 `using` 宣告。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-524">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="ad0e3-525">將資料庫內容新增至 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-525">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="ad0e3-526">指定資料庫內容將會使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-526">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="ad0e3-527">Scaffold 控制器</span><span class="sxs-lookup"><span data-stu-id="ad0e3-527">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad0e3-528">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad0e3-528">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ad0e3-529">以滑鼠右鍵按一下 *Controllers* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-529">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="ad0e3-530">選取 [新增]**[新增 Scaffold 項目]** > 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-530">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="ad0e3-531">選取 [使用 Entity Framework 執行動作的 API 控制器]，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-531">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="ad0e3-532">在 [使用 Entity Framework 執行動作的 API 控制器] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-532">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="ad0e3-533">選取 **模型類別** 中的 [ **TodoItem (TodoApi] Models) 。**</span><span class="sxs-lookup"><span data-stu-id="ad0e3-533">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="ad0e3-534">選取 **資料內容類別** 中的 **TodoCoNtext (TodoApi Models) 。**</span><span class="sxs-lookup"><span data-stu-id="ad0e3-534">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="ad0e3-535">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-535">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ad0e3-536">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ad0e3-536">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="ad0e3-537">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-537">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="ad0e3-538">上述命令：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-538">The preceding commands:</span></span>

* <span data-ttu-id="ad0e3-539">新增 Scaffolding 所需的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-539">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="ad0e3-540">安裝 Scaffolding 引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-540">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="ad0e3-541">Scaffold `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-541">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="ad0e3-542">產生的程式碼：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-542">The generated code:</span></span>

* <span data-ttu-id="ad0e3-543">標記具有屬性的類別 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-543">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="ad0e3-544">這個屬性表示控制器會回應 Web API 要求。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-544">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="ad0e3-545">如需屬性所啟用之特定行為的相關資訊，請參閱 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-545">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="ad0e3-546">使用 DI 將資料庫內容 (`TodoContext`) 插入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-546">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="ad0e3-547">控制器中的每一個 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法都會使用資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-547">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="ad0e3-548">的 ASP.NET 核心範本：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-548">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="ad0e3-549">具有 views 的控制器包含 `[action]` 在路由範本中。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-549">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="ad0e3-550">API 控制器不包含 `[action]` 在路由範本中。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-550">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="ad0e3-551">當 `[action]` 權杖不在路由範本中時，會從路由中排除 [動作](xref:mvc/controllers/routing#action) 名稱。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-551">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="ad0e3-552">也就是，不會在相符的路由中使用動作的相關聯方法名稱。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-552">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="ad0e3-553">檢查 PostTodoItem 建立方法</span><span class="sxs-lookup"><span data-stu-id="ad0e3-553">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="ad0e3-554">取代 `PostTodoItem` 中的 return 陳述式，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 運算子：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-554">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="ad0e3-555">上述程式碼是 HTTP POST 方法，如屬性所指示 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-555">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="ad0e3-556">該方法會從 HTTP 要求本文取得待辦事項的值。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-556">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="ad0e3-557">如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-557">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="ad0e3-558"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-558">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="ad0e3-559">成功時會傳回 HTTP 201 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-559">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="ad0e3-560">對於可在伺服器上建立新資源的 HTTP POST 方法，其標準回應是 HTTP 201。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-560">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="ad0e3-561">將 [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 標頭新增至回應。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-561">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="ad0e3-562">`Location`標頭會指定新建立之待辦事項的[URI](https://developer.mozilla.org/docs/Glossary/URI) 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-562">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="ad0e3-563">如需詳細資訊，請參閱 [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (已建立 10.2.2 201)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-563">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="ad0e3-564">參考 `GetTodoItem` 動作以建立 `Location` 標頭的 URI。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-564">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="ad0e3-565">C# `nameof` 關鍵字是用來避免在 `CreatedAtAction` 呼叫中以硬式編碼方式寫入動作名稱。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-565">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="ad0e3-566">安裝 Postman</span><span class="sxs-lookup"><span data-stu-id="ad0e3-566">Install Postman</span></span>

<span data-ttu-id="ad0e3-567">本教學課程使用 Postman 來測試 Web API。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-567">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="ad0e3-568">安裝 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="ad0e3-568">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="ad0e3-569">啟動 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-569">Start the web app.</span></span>
* <span data-ttu-id="ad0e3-570">啟動 Postman。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-570">Start Postman.</span></span>
* <span data-ttu-id="ad0e3-571">停用 [SSL certificate verification] \(SSL 憑證驗證\)</span><span class="sxs-lookup"><span data-stu-id="ad0e3-571">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="ad0e3-572">從 [檔案]**[設定]** >  ([一般] 索引標籤)，停用 [SSL 憑證驗證]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-572">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="ad0e3-573">在測試控制器之後，請重新啟用 [SSL certificate verification] \(SSL 憑證驗證\)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-573">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="ad0e3-574">使用 Postman 測試 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="ad0e3-574">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="ad0e3-575">建立新的要求。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-575">Create a new request.</span></span>
* <span data-ttu-id="ad0e3-576">將 HTTP 方法設為 `POST`。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-576">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="ad0e3-577">將 URI 設定為 `https://localhost:<port>/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-577">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="ad0e3-578">例如： `https://localhost:5001/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-578">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="ad0e3-579">選取 [Body] \(本文\) 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-579">Select the **Body** tab.</span></span>
* <span data-ttu-id="ad0e3-580">選取 [原始] 選項按鈕。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-580">Select the **raw** radio button.</span></span>
* <span data-ttu-id="ad0e3-581">將類型設定為 **JSON (application/json)**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-581">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="ad0e3-582">在要求本文中，針對待辦項目輸入 JSON：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-582">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="ad0e3-583">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-583">Select **Send**.</span></span>

  ![Postman 與建立要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="ad0e3-585">使用 Postman 測試 location 標頭 URI</span><span class="sxs-lookup"><span data-stu-id="ad0e3-585">Test the location header URI with Postman</span></span>

* <span data-ttu-id="ad0e3-586">在 [回應] 窗格中選取 [標頭] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-586">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="ad0e3-587">複製 [位置] 標頭值：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-587">Copy the **Location** header value:</span></span>

  ![Postman 主控台的 [標頭] 索引標籤](first-web-api/_static/3/create.png)

* <span data-ttu-id="ad0e3-589">將 HTTP 方法設為 `GET`。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-589">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="ad0e3-590">將 URI 設定為 `https://localhost:<port>/api/TodoItems/1` 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-590">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="ad0e3-591">例如： `https://localhost:5001/api/TodoItems/1` 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-591">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="ad0e3-592">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-592">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="ad0e3-593">檢查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="ad0e3-593">Examine the GET methods</span></span>

<span data-ttu-id="ad0e3-594">這些方法會實作兩個 GET 端點：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-594">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="ad0e3-595">從瀏覽器或 Postman 呼叫這兩個端點來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-595">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="ad0e3-596">例如：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-596">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="ad0e3-597">`GetTodoItems` 的呼叫會產生類似下列的回應：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-597">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="ad0e3-598">使用 Postman 測試 Get</span><span class="sxs-lookup"><span data-stu-id="ad0e3-598">Test Get with Postman</span></span>

* <span data-ttu-id="ad0e3-599">建立新的要求。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-599">Create a new request.</span></span>
* <span data-ttu-id="ad0e3-600">將 HTTP 方法設定為 **GET**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-600">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="ad0e3-601">將要求 URI 設定為 `https://localhost:<port>/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-601">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="ad0e3-602">例如： `https://localhost:5001/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-602">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="ad0e3-603">在 Postman 中，設定 [Two pane view] \(雙窗格檢視\)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-603">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="ad0e3-604">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-604">Select **Send**.</span></span>

<span data-ttu-id="ad0e3-605">這個應用程式會使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-605">This app uses an in-memory database.</span></span> <span data-ttu-id="ad0e3-606">如果應用程式在停止後再啟動，上述 GET 要求將不會傳回任何資料。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-606">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="ad0e3-607">如果沒有傳回任何資料，請將資料 [POST](#post) 到應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-607">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="ad0e3-608">傳送和 URL 路徑</span><span class="sxs-lookup"><span data-stu-id="ad0e3-608">Routing and URL paths</span></span>

<span data-ttu-id="ad0e3-609">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)屬性代表回應 HTTP GET 要求的方法。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-609">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="ad0e3-610">每個方法的 URL 路徑的建構方式如下：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-610">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="ad0e3-611">一開始在控制器的 `Route` 屬性中使用範本字串：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-611">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="ad0e3-612">以控制器的名稱取代 `[controller]`，也就是將控制器類別名稱減去 "Controller" 字尾。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-612">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="ad0e3-613">在此範例中，控制器類別名稱是 **TodoItems** Controller，因此控制器名稱是 "TodoItems"。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-613">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="ad0e3-614">ASP.NET Core [路由](xref:mvc/controllers/routing)不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-614">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="ad0e3-615">如果 `[HttpGet]` 屬性具有路由範本 (例如 `[HttpGet("products")]`)，請將其附加到路徑。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-615">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="ad0e3-616">此範例不使用範本。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-616">This sample doesn't use a template.</span></span> <span data-ttu-id="ad0e3-617">如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-617">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="ad0e3-618">在下列 `GetTodoItem` 方法中，`"{id}"` 是待辦事項唯一識別碼的預留位置變數。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-618">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="ad0e3-619">當叫 `GetTodoItem` 用時，會將 `"{id}"` URL 中的值提供給方法的 `id` 參數。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-619">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="ad0e3-620">傳回值</span><span class="sxs-lookup"><span data-stu-id="ad0e3-620">Return values</span></span> 

<span data-ttu-id="ad0e3-621">和方法的傳回型 `GetTodoItems` 別 `GetTodoItem` 為 [ActionResult \<T> 類型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-621">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="ad0e3-622">ASP.NET Core 會自動將物件序列化為 [JSON](https://www.json.org/)，並將 JSON 寫入至回應訊息的本文。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-622">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="ad0e3-623">此傳回型別的回應碼為 200，假設沒有任何未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-623">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="ad0e3-624">未處理的例外狀況會轉譯成 5xx 錯誤。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-624">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="ad0e3-625">`ActionResult` 傳回型別可代表各種 HTTP 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-625">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="ad0e3-626">例如，`GetTodoItem` 可傳回兩個不同的狀態值：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-626">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="ad0e3-627">如果沒有任何專案符合要求的識別碼，方法會傳回 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 錯誤碼。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-627">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="ad0e3-628">否則，方法會傳回 200 與 JSON 回應本文。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-628">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="ad0e3-629">傳回 `item` 會導致 HTTP 200 回應。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-629">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="ad0e3-630">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="ad0e3-630">The PutTodoItem method</span></span>

<span data-ttu-id="ad0e3-631">檢查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-631">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="ad0e3-632">`PutTodoItem` 類似於 `PostTodoItem`，但是會使用 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-632">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="ad0e3-633">回應為 [204 (沒有內容) ](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-633">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="ad0e3-634">根據 HTTP 規格，PUT 要求需要用戶端傳送整個更新的實體，而不只是變更。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-634">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="ad0e3-635">若要支援部分更新，請使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-635">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="ad0e3-636">如果在呼叫 `PutTodoItem` 時發生錯誤，請呼叫 `GET` 以確保資料庫中有項目。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-636">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="ad0e3-637">測試 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="ad0e3-637">Test the PutTodoItem method</span></span>

<span data-ttu-id="ad0e3-638">這個範例會使用必須在每次啟動應用程式時初始化的記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-638">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="ad0e3-639">資料庫中必須有項目，您才能進行 PUT 呼叫。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-639">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="ad0e3-640">在進行 PUT 呼叫之前，請先呼叫 GET 以確保資料庫中有專案。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-640">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="ad0e3-641">更新識別碼為1的待辦事項，並將其名稱設定為「摘要魚」：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-641">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="ad0e3-642">下圖顯示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-642">The following image shows the Postman update:</span></span>

![顯示「204 (沒有內容) 回應」的 Postman 主控台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="ad0e3-644">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="ad0e3-644">The DeleteTodoItem method</span></span>

<span data-ttu-id="ad0e3-645">檢查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-645">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="ad0e3-646">測試 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="ad0e3-646">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="ad0e3-647">使用 Postman 刪除待辦事項：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-647">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="ad0e3-648">將方法設定為 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-648">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="ad0e3-649">設定要刪除之物件的 URI (例如 `https://localhost:5001/api/TodoItems/1`) 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-649">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="ad0e3-650">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-650">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="ad0e3-651">防止過度張貼</span><span class="sxs-lookup"><span data-stu-id="ad0e3-651">Prevent over-posting</span></span>

<span data-ttu-id="ad0e3-652">範例應用程式目前會公開整個 `TodoItem` 物件。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-652">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="ad0e3-653">生產環境應用程式通常會限制使用模型子集來輸入和傳回的資料。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-653">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="ad0e3-654">這項功能有許多原因，而且安全性是主要的原因。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-654">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="ad0e3-655">模型的子集通常稱為資料傳輸物件 (DTO) 、輸入模型或視圖模型。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-655">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="ad0e3-656">此文章中使用 **DTO** 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-656">**DTO** is used in this article.</span></span>

<span data-ttu-id="ad0e3-657">DTO 可以用來：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-657">A DTO may be used to:</span></span>

* <span data-ttu-id="ad0e3-658">防止過度張貼。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-658">Prevent over-posting.</span></span>
* <span data-ttu-id="ad0e3-659">隱藏用戶端不應該看到的屬性。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-659">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="ad0e3-660">略過某些屬性，以減少承載大小。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-660">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="ad0e3-661">壓平合併包含嵌套物件的物件圖形。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-661">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="ad0e3-662">簡維物件圖形對於用戶端來說可能更方便。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-662">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="ad0e3-663">若要示範 DTO 方法，請更新 `TodoItem` 類別以包含秘密欄位：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-663">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="ad0e3-664">此應用程式必須隱藏秘密欄位，但系統管理應用程式可以選擇將它公開。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-664">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="ad0e3-665">確認您可以張貼並取得秘密欄位。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-665">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="ad0e3-666">建立 DTO 模型：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-666">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="ad0e3-667">更新 `TodoItemsController` 以使用 `TodoItemDTO` ：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-667">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="ad0e3-668">確認您無法張貼或取得秘密欄位。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-668">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="ad0e3-669">使用 JavaScript 呼叫 Web API</span><span class="sxs-lookup"><span data-stu-id="ad0e3-669">Call the web API with JavaScript</span></span>

<span data-ttu-id="ad0e3-670">請參閱 [教學課程：使用 JavaScript 呼叫 ASP.NET Core WEB API](xref:tutorials/web-api-javascript)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-670">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ad0e3-671">在本教學課程中，您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-671">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ad0e3-672">建立 Web API 專案。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-672">Create a web API project.</span></span>
> * <span data-ttu-id="ad0e3-673">新增模型類別和資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-673">Add a model class and a database context.</span></span>
> * <span data-ttu-id="ad0e3-674">新增控制器。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-674">Add a controller.</span></span>
> * <span data-ttu-id="ad0e3-675">新增 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-675">Add CRUD methods.</span></span>
> * <span data-ttu-id="ad0e3-676">設定路由和 URL 路徑。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-676">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="ad0e3-677">指定傳回值。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-677">Specify return values.</span></span>
> * <span data-ttu-id="ad0e3-678">使用 Postman 呼叫 Web API。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-678">Call the web API with Postman.</span></span>
> * <span data-ttu-id="ad0e3-679">使用 JavaScript 呼叫 Web API。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-679">Call the web API with JavaScript.</span></span>

<span data-ttu-id="ad0e3-680">結束時，您將會有一個可管理關聯式資料庫中所儲存「待辦事項」的 Web API。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-680">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="ad0e3-681">總覽2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-681">Overview 2.1</span></span>

<span data-ttu-id="ad0e3-682">本教學課程會建立以下 API：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-682">This tutorial creates the following API:</span></span>

|<span data-ttu-id="ad0e3-683">API</span><span class="sxs-lookup"><span data-stu-id="ad0e3-683">API</span></span> | <span data-ttu-id="ad0e3-684">描述</span><span class="sxs-lookup"><span data-stu-id="ad0e3-684">Description</span></span> | <span data-ttu-id="ad0e3-685">要求本文</span><span class="sxs-lookup"><span data-stu-id="ad0e3-685">Request body</span></span> | <span data-ttu-id="ad0e3-686">回應本文</span><span class="sxs-lookup"><span data-stu-id="ad0e3-686">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="ad0e3-687">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="ad0e3-687">GET /api/TodoItems</span></span> | <span data-ttu-id="ad0e3-688">取得所有待辦事項</span><span class="sxs-lookup"><span data-stu-id="ad0e3-688">Get all to-do items</span></span> | <span data-ttu-id="ad0e3-689">無</span><span class="sxs-lookup"><span data-stu-id="ad0e3-689">None</span></span> | <span data-ttu-id="ad0e3-690">待辦事項的陣列</span><span class="sxs-lookup"><span data-stu-id="ad0e3-690">Array of to-do items</span></span>|
|<span data-ttu-id="ad0e3-691">GET /api/TodoItems/{識別碼}</span><span class="sxs-lookup"><span data-stu-id="ad0e3-691">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="ad0e3-692">依識別碼取得項目</span><span class="sxs-lookup"><span data-stu-id="ad0e3-692">Get an item by ID</span></span> | <span data-ttu-id="ad0e3-693">無</span><span class="sxs-lookup"><span data-stu-id="ad0e3-693">None</span></span> | <span data-ttu-id="ad0e3-694">待辦事項</span><span class="sxs-lookup"><span data-stu-id="ad0e3-694">To-do item</span></span>|
|<span data-ttu-id="ad0e3-695">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="ad0e3-695">POST /api/TodoItems</span></span> | <span data-ttu-id="ad0e3-696">新增記錄</span><span class="sxs-lookup"><span data-stu-id="ad0e3-696">Add a new item</span></span> | <span data-ttu-id="ad0e3-697">待辦事項</span><span class="sxs-lookup"><span data-stu-id="ad0e3-697">To-do item</span></span> | <span data-ttu-id="ad0e3-698">待辦事項</span><span class="sxs-lookup"><span data-stu-id="ad0e3-698">To-do item</span></span> |
|<span data-ttu-id="ad0e3-699">PUT /api/TodoItems/{識別碼}</span><span class="sxs-lookup"><span data-stu-id="ad0e3-699">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="ad0e3-700">更新現有的項目 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ad0e3-700">Update an existing item &nbsp;</span></span> | <span data-ttu-id="ad0e3-701">待辦事項</span><span class="sxs-lookup"><span data-stu-id="ad0e3-701">To-do item</span></span> | <span data-ttu-id="ad0e3-702">無</span><span class="sxs-lookup"><span data-stu-id="ad0e3-702">None</span></span> |
|<span data-ttu-id="ad0e3-703">刪除/Api/todoitems/{識別碼} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="ad0e3-703">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="ad0e3-704">刪除專案 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="ad0e3-704">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="ad0e3-705">無</span><span class="sxs-lookup"><span data-stu-id="ad0e3-705">None</span></span> | <span data-ttu-id="ad0e3-706">無</span><span class="sxs-lookup"><span data-stu-id="ad0e3-706">None</span></span>|

<span data-ttu-id="ad0e3-707">下圖顯示應用程式的設計。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-707">The following diagram shows the design of the app.</span></span>

![左側方塊代表用戶端。](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="ad0e3-713">必要條件2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-713">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad0e3-714">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad0e3-714">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad0e3-715">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad0e3-715">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ad0e3-716">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ad0e3-716">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="ad0e3-717">建立 Web 專案2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-717">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad0e3-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad0e3-718">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ad0e3-719">從 [ **檔案** ] 功能表選取 [ **新增** > **專案**]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-719">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ad0e3-720">選取 **ASP.NET Core Web 應用程式** 範本，然後按一下 [下一步]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-720">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="ad0e3-721">將專案命名為 *TodoApi*，然後按一下 [建立]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-721">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="ad0e3-722">在 [建立新的 ASP.NET Core Web 應用程式] 對話方塊中，確認選取 [.NET Core] 和 [ASP.NET Core 2.2]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-722">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="ad0e3-723">選取 **API** 範本，然後按一下 [建立]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-723">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="ad0e3-724">請 **勿** 選取 [Enable Docker Support] \(啟用 Docker 支援\)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-724">**Don't** select **Enable Docker Support**.</span></span>

![VS 新增專案對話方塊](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad0e3-726">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad0e3-726">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ad0e3-727">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-727">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ad0e3-728">將目錄 (`cd`) 變更為包含專案資料夾的資料夾。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-728">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="ad0e3-729">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-729">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="ad0e3-730">這些命令會建立新的 Web API 專案，並開啟新專案資料夾中的新 Visual Studio Code 執行個體。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-730">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="ad0e3-731">當出現對話方塊詢問您是否要將所需的資產新增至專案時，選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-731">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ad0e3-732">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ad0e3-732">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ad0e3-733">選取 [檔案]**[新增解決方案]** > 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-733">Select **File** > **New Solution**.</span></span>

  ![macOS 新增方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="ad0e3-735">在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用程式**  >  **API**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-735">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="ad0e3-736">在8.6 版或更新版本中，選取 [ **Web] 和 [主控台**  >  **應用程式**  >  **API**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-736">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="ad0e3-737">在 [ **設定新的 ASP.NET Core WEB API** ] 對話方塊中，選取最新的 .net Core 2.X **目標 Framework**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-737">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="ad0e3-738">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-738">Select **Next**.</span></span>

* <span data-ttu-id="ad0e3-739">針對 [專案名稱] 輸入 *TodoApi*，然後選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-739">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![設定對話方塊](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="ad0e3-741">測試 API 2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-741">Test the API 2.1</span></span>

<span data-ttu-id="ad0e3-742">專案範本會建立 `values` API。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-742">The project template creates a `values` API.</span></span> <span data-ttu-id="ad0e3-743">從瀏覽器呼叫 `Get` 方法來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-743">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad0e3-744">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad0e3-744">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ad0e3-745">按 Ctrl+F5 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-745">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="ad0e3-746">Visual Studio 會啟動瀏覽器並巡覽至 `https://localhost:<port>/api/values`，其中 `<port>` 是隨機選擇的通訊埠編號。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-746">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="ad0e3-747">如果出現對話方塊詢問您是否應該信任 IIS Express 憑證，請選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-747">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="ad0e3-748">在接著出現的 [安全性警告] 對話方塊中，選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-748">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad0e3-749">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad0e3-749">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ad0e3-750">按 Ctrl+F5 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-750">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="ad0e3-751">在瀏覽器中，前往下列 URL：`https://localhost:5001/api/values`。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-751">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ad0e3-752">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ad0e3-752">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ad0e3-753">選取 [**執行**  >  **開始調試** 程式] 以啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-753">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="ad0e3-754">Visual Studio for Mac 會啟動瀏覽器並巡覽至 `https://localhost:<port>`，其中 `<port>` 是隨機選擇的連接埠號碼。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-754">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="ad0e3-755">傳回 HTTP 404 (找不到) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-755">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="ad0e3-756">將 `/api/values` 附加至 URL (將 URL 變更為 `https://localhost:<port>/api/values`)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-756">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="ad0e3-757">隨即傳回下列 JSON：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-757">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="ad0e3-758">新增模型類別2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-758">Add a model class 2.1</span></span>

<span data-ttu-id="ad0e3-759">「模型」是代表應用程式所管理資料的一組類別。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-759">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="ad0e3-760">此應用程式的模型是單一 `TodoItem` 類別。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-760">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad0e3-761">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad0e3-761">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ad0e3-762">在 [ **方案 Explorer**] 中，以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-762">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="ad0e3-763">選取 **[**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-763">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ad0e3-764">為資料夾命名 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-764">Name the folder *Models*.</span></span>

* <span data-ttu-id="ad0e3-765">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [**加入**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-765">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ad0e3-766">將類別命名為 *TodoItem*，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-766">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="ad0e3-767">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-767">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad0e3-768">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad0e3-768">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ad0e3-769">新增名為的資料夾 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-769">Add a folder named *Models*.</span></span>

* <span data-ttu-id="ad0e3-770">`TodoItem`使用下列程式碼，將類別新增至 *Models* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-770">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ad0e3-771">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ad0e3-771">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ad0e3-772">以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-772">Right-click the project.</span></span> <span data-ttu-id="ad0e3-773">選取 **[**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-773">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ad0e3-774">為資料夾命名 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-774">Name the folder *Models*.</span></span>

  ![新增資料夾](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="ad0e3-776">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 > [**新增** 檔案 > **一般**] > **空白類別**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-776">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="ad0e3-777">將類別命名為 *TodoItem*，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-777">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="ad0e3-778">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-778">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="ad0e3-779">`Id` 屬性的功能相當於關聯式資料庫中的唯一索引鍵。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-779">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="ad0e3-780">模型類別可移至專案中的任何位置，但依照慣例，會 *Models* 使用資料夾。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-780">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="ad0e3-781">新增資料庫內容2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-781">Add a database context 2.1</span></span>

<span data-ttu-id="ad0e3-782">「資料庫內容」是為資料模型協調 Entity Framework 功能的主要類別。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-782">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="ad0e3-783">此類別是透過衍生自 `Microsoft.EntityFrameworkCore.DbContext` 類別來建立。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-783">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad0e3-784">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad0e3-784">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ad0e3-785">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [**加入**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-785">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ad0e3-786">將類別命名為 *TodoContext*，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-786">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ad0e3-787">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ad0e3-787">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ad0e3-788">將 `TodoContext` 類別新增至 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-788">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="ad0e3-789">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-789">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="ad0e3-790">註冊資料庫內容2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-790">Register the database context 2.1</span></span>

<span data-ttu-id="ad0e3-791">在 ASP.NET Core 中，資料庫內容等服務必須向[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器註冊。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-791">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ad0e3-792">此容器會將服務提供給控制器。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-792">The container provides the service to controllers.</span></span>

<span data-ttu-id="ad0e3-793">使用下列醒目提示的程式碼更新 *Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-793">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="ad0e3-794">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-794">The preceding code:</span></span>

* <span data-ttu-id="ad0e3-795">移除未使用的 `using` 宣告。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-795">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="ad0e3-796">將資料庫內容新增至 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-796">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="ad0e3-797">指定資料庫內容將會使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-797">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="ad0e3-798">新增控制器2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-798">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad0e3-799">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad0e3-799">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ad0e3-800">以滑鼠右鍵按一下 *Controllers* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-800">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="ad0e3-801">選取 [ **加入** > **新專案**]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-801">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="ad0e3-802">在 [新增項目] 對話方塊中，選取 [API 控制器類別] 範本。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-802">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="ad0e3-803">將類別命名為 *TodoController*，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-803">Name the class *TodoController*, and select **Add**.</span></span>

  ![在搜尋方塊中輸入 controller 且已選取 Web API 控制器的 [新增項目] 對話方塊](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ad0e3-805">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ad0e3-805">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ad0e3-806">在 *Controllers* 資料夾中，建立名為 `TodoController` 的類別。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-806">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="ad0e3-807">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-807">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="ad0e3-808">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-808">The preceding code:</span></span>

* <span data-ttu-id="ad0e3-809">定義不含方法的 API 控制器類別。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-809">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="ad0e3-810">標記具有屬性的類別 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-810">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="ad0e3-811">這個屬性表示控制器會回應 Web API 要求。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-811">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="ad0e3-812">如需屬性所啟用之特定行為的相關資訊，請參閱 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-812">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="ad0e3-813">使用 DI 將資料庫內容 (`TodoContext`) 插入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-813">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="ad0e3-814">控制器中的每一個 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法都會使用資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-814">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="ad0e3-815">如果資料庫是空的，請將名為 `Item1` 的項目新增至資料庫。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-815">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="ad0e3-816">此程式碼是在建構函式中，因此每次執行都會有新的 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-816">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="ad0e3-817">如果您刪除所有項目，則建構函式會在下次呼叫 API 方法時重新建立 `Item1`。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-817">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="ad0e3-818">因此看起來雖然像是刪除失敗，但實際為成功。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-818">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="ad0e3-819">新增 Get 方法2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-819">Add Get methods 2.1</span></span>

<span data-ttu-id="ad0e3-820">若要提供擷取待辦事項的 API，請在 `TodoController` 類別中新增下列方法：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-820">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="ad0e3-821">這些方法會實作兩個 GET 端點：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-821">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="ad0e3-822">如果應用程式仍在執行，請將其停止。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-822">Stop the app if it's still running.</span></span> <span data-ttu-id="ad0e3-823">然後重新予以執行以包含最新的變更。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-823">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="ad0e3-824">從瀏覽器呼叫這兩個端點來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-824">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="ad0e3-825">例如：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-825">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="ad0e3-826">以下是呼叫 `GetTodoItems` 所產生的 HTTP 回應：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-826">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="ad0e3-827">路由和 URL 路徑2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-827">Routing and URL paths 2.1</span></span>

<span data-ttu-id="ad0e3-828">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)屬性代表回應 HTTP GET 要求的方法。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-828">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="ad0e3-829">每個方法的 URL 路徑的建構方式如下：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-829">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="ad0e3-830">一開始在控制器的 `Route` 屬性中使用範本字串：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-830">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="ad0e3-831">以控制器的名稱取代 `[controller]`，也就是將控制器類別名稱減去 "Controller" 字尾。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-831">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="ad0e3-832">在此範例中，控制器類別名稱是 **Todo** Controller，因此容器名稱是 "todo"。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-832">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="ad0e3-833">ASP.NET Core [路由](xref:mvc/controllers/routing)不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-833">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="ad0e3-834">如果 `[HttpGet]` 屬性具有路由範本 (例如 `[HttpGet("products")]`)，請將其附加到路徑。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-834">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="ad0e3-835">此範例不使用範本。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-835">This sample doesn't use a template.</span></span> <span data-ttu-id="ad0e3-836">如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-836">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="ad0e3-837">在下列 `GetTodoItem` 方法中，`"{id}"` 是待辦事項唯一識別碼的預留位置變數。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-837">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="ad0e3-838">在叫用 `GetTodoItem` 時，會將 URL 中的 `"{id}"` 值提供給方法的 `id` 參數。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-838">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="ad0e3-839">傳回值2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-839">Return values 2.1</span></span>

<span data-ttu-id="ad0e3-840">和方法的傳回型 `GetTodoItems` 別 `GetTodoItem` 為 [ActionResult \<T> 類型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-840">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="ad0e3-841">ASP.NET Core 會自動將物件序列化為 [JSON](https://www.json.org/)，並將 JSON 寫入至回應訊息的本文。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-841">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="ad0e3-842">此傳回型別的回應碼為 200，假設沒有任何未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-842">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="ad0e3-843">未處理的例外狀況會轉譯成 5xx 錯誤。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-843">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="ad0e3-844">`ActionResult` 傳回型別可代表各種 HTTP 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-844">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="ad0e3-845">例如，`GetTodoItem` 可傳回兩個不同的狀態值：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-845">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="ad0e3-846">如果沒有任何專案符合要求的識別碼，方法會傳回 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 錯誤碼。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-846">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="ad0e3-847">否則，方法會傳回 200 與 JSON 回應本文。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-847">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="ad0e3-848">傳回 `item` 會導致 HTTP 200 回應。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-848">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="ad0e3-849">測試 GetTodoItems 方法2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-849">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="ad0e3-850">本教學課程使用 Postman 來測試 Web API。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-850">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="ad0e3-851">安裝 [Postman](https://www.getpostman.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-851">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="ad0e3-852">啟動 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-852">Start the web app.</span></span>
* <span data-ttu-id="ad0e3-853">啟動 Postman。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-853">Start Postman.</span></span>
* <span data-ttu-id="ad0e3-854">停用 **SSL 憑證驗證**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-854">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad0e3-855">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad0e3-855">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ad0e3-856">從 [檔案]**[設定]** >  ([一般] 索引標籤)，停用 [SSL 憑證驗證]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-856">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ad0e3-857">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ad0e3-857">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ad0e3-858">從 **Postman**  >  **喜好** 設定 (**一般**] 索引標籤) ，停用 **SSL 憑證驗證**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-858">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="ad0e3-859">或者，選取扳手並選取 [設定]，然後停用 [SSL 憑證驗證]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-859">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="ad0e3-860">在測試控制器之後，請重新啟用 [SSL certificate verification] \(SSL 憑證驗證\)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-860">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="ad0e3-861">建立新的要求。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-861">Create a new request.</span></span>
  * <span data-ttu-id="ad0e3-862">將 HTTP 方法設定為 **GET**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-862">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="ad0e3-863">將要求 URI 設定為 `https://localhost:<port>/api/todo` 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-863">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="ad0e3-864">例如： `https://localhost:5001/api/todo` 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-864">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="ad0e3-865">在 Postman 中，設定 [Two pane view] \(雙窗格檢視\)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-865">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="ad0e3-866">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-866">Select **Send**.</span></span>

![Postman 與 GET 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="ad0e3-868">新增 Create 方法2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-868">Add a Create method 2.1</span></span>

<span data-ttu-id="ad0e3-869">在 *Controllers/TodoController.cs* 內部新增下列 `PostTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-869">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="ad0e3-870">上述程式碼是 HTTP POST 方法，如屬性所指示 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-870">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="ad0e3-871">該方法會從 HTTP 要求本文取得待辦事項的值。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-871">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="ad0e3-872">`CreatedAtAction` 方法：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-872">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="ad0e3-873">成功時會傳回 HTTP 201 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-873">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="ad0e3-874">對於可在伺服器上建立新資源的 HTTP POST 方法，其標準回應是 HTTP 201。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-874">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="ad0e3-875">將 `Location` 標頭加到回應中。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-875">Adds a `Location` header to the response.</span></span> <span data-ttu-id="ad0e3-876">`Location` 標頭指定新建立之待辦事項的 URI。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-876">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="ad0e3-877">如需詳細資訊，請參閱 [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (已建立 10.2.2 201)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-877">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="ad0e3-878">參考 `GetTodoItem` 動作以建立 `Location` 標頭的 URI。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-878">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="ad0e3-879">C# `nameof` 關鍵字是用來避免在 `CreatedAtAction` 呼叫中以硬式編碼方式寫入動作名稱。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-879">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="ad0e3-880">測試 PostTodoItem 方法2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-880">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="ad0e3-881">建置專案。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-881">Build the project.</span></span>
* <span data-ttu-id="ad0e3-882">在 Postman 中，將 HTTP 方法設定為 `POST`。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-882">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="ad0e3-883">將 URI 設定為 `https://localhost:<port>/api/Todo` 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-883">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="ad0e3-884">例如： `https://localhost:5001/api/Todo` 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-884">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="ad0e3-885">選取 [Body] \(本文\) 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-885">Select the **Body** tab.</span></span>
* <span data-ttu-id="ad0e3-886">選取 [原始] 選項按鈕。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-886">Select the **raw** radio button.</span></span>
* <span data-ttu-id="ad0e3-887">將類型設定為 **JSON (application/json)**。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-887">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="ad0e3-888">在要求本文中，針對待辦項目輸入 JSON：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-888">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="ad0e3-889">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-889">Select **Send**.</span></span>

  ![Postman 與建立要求](first-web-api/_static/create.png)

  <span data-ttu-id="ad0e3-891">如果您收到 405「不允許的方法」錯誤，可能是由於新增 `PostTodoItem` 方法之後未編譯專案所導致。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-891">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="ad0e3-892">測試位置標頭 URI 2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-892">Test the location header URI 2.1</span></span>

* <span data-ttu-id="ad0e3-893">在 [回應] 窗格中選取 [標頭] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-893">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="ad0e3-894">複製 [位置] 標頭值：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-894">Copy the **Location** header value:</span></span>

  ![Postman 主控台的 [標頭] 索引標籤](first-web-api/_static/pmc2.png)

* <span data-ttu-id="ad0e3-896">將方法設定為 GET。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-896">Set the method to GET.</span></span>
* <span data-ttu-id="ad0e3-897">將 URI 設定為 `https://localhost:<port>/api/TodoItems/2` 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-897">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="ad0e3-898">例如： `https://localhost:5001/api/TodoItems/2` 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-898">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="ad0e3-899">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-899">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="ad0e3-900">新增 PutTodoItem 方法2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-900">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="ad0e3-901">請新增下列 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-901">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="ad0e3-902">`PutTodoItem` 類似於 `PostTodoItem`，但是會使用 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-902">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="ad0e3-903">回應為 [204 (沒有內容) ](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-903">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="ad0e3-904">根據 HTTP 規格，PUT 要求需要用戶端傳送整個更新的實體，而不只是變更。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-904">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="ad0e3-905">若要支援部分更新，請使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-905">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="ad0e3-906">如果在呼叫 `PutTodoItem` 時發生錯誤，請呼叫 `GET` 以確保資料庫中有項目。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-906">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="ad0e3-907">測試 PutTodoItem 方法2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-907">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="ad0e3-908">這個範例會使用必須在每次啟動應用程式時初始化的記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-908">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="ad0e3-909">資料庫中必須有項目，您才能進行 PUT 呼叫。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-909">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="ad0e3-910">在進行 PUT 呼叫之前，請先呼叫 GET 以確保資料庫中有專案。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-910">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="ad0e3-911">更新識別碼為1的待辦事項，並將其名稱設定為「摘要魚」：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-911">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="ad0e3-912">下圖顯示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-912">The following image shows the Postman update:</span></span>

![顯示「204 (沒有內容) 回應」的 Postman 主控台](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="ad0e3-914">新增 DeleteTodoItem 方法2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-914">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="ad0e3-915">請新增下列 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-915">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="ad0e3-916">`DeleteTodoItem` 回應是 [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) \(204 (沒有內容)\)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-916">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="ad0e3-917">測試 DeleteTodoItem 方法2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-917">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="ad0e3-918">使用 Postman 刪除待辦事項：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-918">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="ad0e3-919">將方法設定為 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-919">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="ad0e3-920">設定要刪除之物件的 URI (例如 `https://localhost:5001/api/todo/1`) 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-920">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="ad0e3-921">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-921">Select **Send**.</span></span>

<span data-ttu-id="ad0e3-922">範例應用程式可讓您刪除所有項目。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-922">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="ad0e3-923">但刪除最後一個項目之後，模型類別建構函式會在下次呼叫 API 時建立新的項目。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-923">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="ad0e3-924">使用 JavaScript 2.1 呼叫 web API</span><span class="sxs-lookup"><span data-stu-id="ad0e3-924">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="ad0e3-925">在此節中，將會新增 HTML 網頁，以使用 JavaScript 來呼叫 Web API。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-925">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="ad0e3-926">jQuery 會起始要求。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-926">jQuery initiates the request.</span></span> <span data-ttu-id="ad0e3-927">JavaScript 會使用來自 Web API 回應的詳細資料來更新頁面。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-927">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="ad0e3-928">藉由使用下列反白顯示的程式碼更新 *Startup.cs*，來設定應用程式 [提供靜態檔案](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A)並 [啟用預設檔案對應](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A)：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-928">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="ad0e3-929">在專案目錄中建立 *wwwroot* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-929">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="ad0e3-930">將名為 *index.html* 的 HTML 檔案新增至 *wwwroot* 目錄。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-930">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="ad0e3-931">將其內容取代為下列標記：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-931">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="ad0e3-932">將名為 *site.js* 的 JavaScript 檔案新增至 *wwwroot* 目錄。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-932">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="ad0e3-933">以下列程式碼取代其內容：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-933">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="ad0e3-934">若要在本機測試 HTML 網頁，可能需要變更 ASP.NET Core 專案的啟動設定：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-934">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="ad0e3-935">開啟 *Properties\launchSettings.json*。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-935">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="ad0e3-936">移除 `launchUrl` 屬性，以強制在專案的預設檔案 *index.html* 開啟應用程式 &mdash; 。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-936">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="ad0e3-937">此範例會呼叫 Web API 的所有 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-937">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="ad0e3-938">以下是關於呼叫 API 的說明。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-938">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="ad0e3-939">取得待辦事項的清單2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-939">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="ad0e3-940">jQuery 會將 HTTP GET 要求傳送至 web API，該 API 會傳回代表待辦事項陣列的 JSON。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-940">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="ad0e3-941">如果要求成功，則會叫用 `success` 回呼函式。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-941">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="ad0e3-942">在回呼中，DOM 已使用待辦事項資訊進行更新。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-942">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="ad0e3-943">新增待辦事項2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-943">Add a to-do item 2.1</span></span>

<span data-ttu-id="ad0e3-944">jQuery 會使用要求主體中的待辦事項來傳送 HTTP POST 要求。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-944">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="ad0e3-945">`accepts` 和 `contentType` 選項都設定為 `application/json`，以指定接收和傳送的媒體類型。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-945">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="ad0e3-946">待辦事項會使用 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 轉換成 JSON。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-946">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="ad0e3-947">當 API 傳回成功狀態碼時，會叫用 `getData` 函式來更新 HTML 資料表。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-947">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="ad0e3-948">更新待辦事項2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-948">Update a to-do item 2.1</span></span>

<span data-ttu-id="ad0e3-949">更新待辦事項類似於新增待辦事項。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-949">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="ad0e3-950">`url` 會變更為新增項目的唯一識別碼，而 `type` 是 `PUT`。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-950">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="ad0e3-951">刪除待辦事項2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-951">Delete a to-do item 2.1</span></span>

<span data-ttu-id="ad0e3-952">刪除待辦事項的達成方法是將 AJAX 呼叫的 `type` 設定為 `DELETE`，並在 URL 中指定項目的唯一識別碼。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-952">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="ad0e3-953">將驗證支援新增至 web API 2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-953">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="ad0e3-954">其他資源2。1</span><span class="sxs-lookup"><span data-stu-id="ad0e3-954">Additional resources 2.1</span></span>

<span data-ttu-id="ad0e3-955">[檢視或下載本教學課程的範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-955">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="ad0e3-956">請參閱[如何下載](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="ad0e3-956">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="ad0e3-957">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="ad0e3-957">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="ad0e3-958">本教學課程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="ad0e3-958">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
* [<span data-ttu-id="ad0e3-959">Microsoft 瞭解：使用 ASP.NET Core 建立 web API</span><span class="sxs-lookup"><span data-stu-id="ad0e3-959">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
