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
ms.openlocfilehash: 99b3b4af31683feb10c01d1a7297a3489b01c797
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711694"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="49043-103">教學課程：使用 ASP.NET Core 建立 Web API</span><span class="sxs-lookup"><span data-stu-id="49043-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="49043-104">由 [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Kirk Larkin](https://twitter.com/serpent5)和 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="49043-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="49043-105">本教學課程將教導您使用 ASP.NET Core 建立 Web API 的基本概念。</span><span class="sxs-lookup"><span data-stu-id="49043-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="49043-106">在本教學課程中，您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="49043-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="49043-107">建立 Web API 專案。</span><span class="sxs-lookup"><span data-stu-id="49043-107">Create a web API project.</span></span>
> * <span data-ttu-id="49043-108">新增模型類別和資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="49043-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="49043-109">使用 CRUD 方法 Scaffold 控制器。</span><span class="sxs-lookup"><span data-stu-id="49043-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="49043-110">設定路由、URL 路徑和傳回值。</span><span class="sxs-lookup"><span data-stu-id="49043-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="49043-111">使用 Postman 呼叫 Web API。</span><span class="sxs-lookup"><span data-stu-id="49043-111">Call the web API with Postman.</span></span>

<span data-ttu-id="49043-112">結束時，您會有一個 Web API，可以管理儲存在資料庫中的「待辦事項」。</span><span class="sxs-lookup"><span data-stu-id="49043-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="49043-113">概觀</span><span class="sxs-lookup"><span data-stu-id="49043-113">Overview</span></span>

<span data-ttu-id="49043-114">本教學課程會建立以下 API：</span><span class="sxs-lookup"><span data-stu-id="49043-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="49043-115">API</span><span class="sxs-lookup"><span data-stu-id="49043-115">API</span></span> | <span data-ttu-id="49043-116">描述</span><span class="sxs-lookup"><span data-stu-id="49043-116">Description</span></span> | <span data-ttu-id="49043-117">要求本文</span><span class="sxs-lookup"><span data-stu-id="49043-117">Request body</span></span> | <span data-ttu-id="49043-118">回應本文</span><span class="sxs-lookup"><span data-stu-id="49043-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="49043-119">取得所有待辦事項</span><span class="sxs-lookup"><span data-stu-id="49043-119">Get all to-do items</span></span> | <span data-ttu-id="49043-120">無</span><span class="sxs-lookup"><span data-stu-id="49043-120">None</span></span> | <span data-ttu-id="49043-121">待辦事項的陣列</span><span class="sxs-lookup"><span data-stu-id="49043-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="49043-122">依識別碼取得項目</span><span class="sxs-lookup"><span data-stu-id="49043-122">Get an item by ID</span></span> | <span data-ttu-id="49043-123">無</span><span class="sxs-lookup"><span data-stu-id="49043-123">None</span></span> | <span data-ttu-id="49043-124">待辦事項</span><span class="sxs-lookup"><span data-stu-id="49043-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="49043-125">新增記錄</span><span class="sxs-lookup"><span data-stu-id="49043-125">Add a new item</span></span> | <span data-ttu-id="49043-126">待辦事項</span><span class="sxs-lookup"><span data-stu-id="49043-126">To-do item</span></span> | <span data-ttu-id="49043-127">待辦事項</span><span class="sxs-lookup"><span data-stu-id="49043-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="49043-128">更新現有的項目 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="49043-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="49043-129">待辦事項</span><span class="sxs-lookup"><span data-stu-id="49043-129">To-do item</span></span> | <span data-ttu-id="49043-130">無</span><span class="sxs-lookup"><span data-stu-id="49043-130">None</span></span> |
|<span data-ttu-id="49043-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="49043-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="49043-132">刪除專案 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="49043-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="49043-133">無</span><span class="sxs-lookup"><span data-stu-id="49043-133">None</span></span> | <span data-ttu-id="49043-134">無</span><span class="sxs-lookup"><span data-stu-id="49043-134">None</span></span>|

<span data-ttu-id="49043-135">下圖顯示應用程式的設計。</span><span class="sxs-lookup"><span data-stu-id="49043-135">The following diagram shows the design of the app.</span></span>

![左側方塊代表用戶端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="49043-141">必要條件</span><span class="sxs-lookup"><span data-stu-id="49043-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49043-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49043-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="49043-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49043-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49043-144">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="49043-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="49043-145">建立 Web 專案</span><span class="sxs-lookup"><span data-stu-id="49043-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49043-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49043-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49043-147">從 [ **檔案** ] 功能表選取 [ **新增** > **專案**]。</span><span class="sxs-lookup"><span data-stu-id="49043-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="49043-148">選取 **ASP.NET Core WEB API** 範本，然後按 **[下一步]**。</span><span class="sxs-lookup"><span data-stu-id="49043-148">Select the **ASP.NET Core Web API** template and click **Next**.</span></span>
* <span data-ttu-id="49043-149">將專案命名為 *TodoApi*，然後按一下 [建立]。</span><span class="sxs-lookup"><span data-stu-id="49043-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="49043-150">在 [ **建立新的 ASP.NET Core Web 應用程式** ] 對話方塊中，確認已選取 [ **.net Core** ] 和 [ **ASP.NET Core 5.0** ]。</span><span class="sxs-lookup"><span data-stu-id="49043-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="49043-151">選取 **API** 範本，然後按一下 [建立]。</span><span class="sxs-lookup"><span data-stu-id="49043-151">Select the **API** template and click **Create**.</span></span>

![VS 新增專案對話方塊](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="49043-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49043-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49043-154">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="49043-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="49043-155">將目錄 (`cd`) 變更為包含專案資料夾的資料夾。</span><span class="sxs-lookup"><span data-stu-id="49043-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="49043-156">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="49043-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="49043-157">當出現對話方塊詢問您是否要將所需的資產新增至專案時，選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="49043-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="49043-158">上述命令：</span><span class="sxs-lookup"><span data-stu-id="49043-158">The preceding commands:</span></span>

  * <span data-ttu-id="49043-159">建立新的 Web API 專案，然後在 Visual Studio Code 中予以開啟。</span><span class="sxs-lookup"><span data-stu-id="49043-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="49043-160">新增下一節需要的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="49043-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49043-161">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="49043-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="49043-162">選取 [檔案]**[新增解決方案]** > 。</span><span class="sxs-lookup"><span data-stu-id="49043-162">Select **File** > **New Solution**.</span></span>

  ![macOS 新增方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="49043-164">在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用程式**  >  **API**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="49043-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="49043-165">在8.6 版或更新版本中，選取 [ **Web] 和 [主控台**  >  **應用程式**  >  **API**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="49043-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 範本選取專案](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="49043-167">在 [ **設定新的 ASP.NET Core WEB API** ] 對話方塊中，選取最新的 .net Core 5.X **目標 Framework**。</span><span class="sxs-lookup"><span data-stu-id="49043-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="49043-168">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="49043-168">Select **Next**.</span></span>

* <span data-ttu-id="49043-169">針對 [專案名稱] 輸入 *TodoApi*，然後選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="49043-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![設定對話方塊](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="49043-171">在專案資料夾中開啟命令終端機，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="49043-171">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="49043-172">測試專案</span><span class="sxs-lookup"><span data-stu-id="49043-172">Test the project</span></span>

<span data-ttu-id="49043-173">專案範本會建立 `WeatherForecast` 支援 [Swagger](xref:tutorials/web-api-help-pages-using-swagger)的 API。</span><span class="sxs-lookup"><span data-stu-id="49043-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49043-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49043-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="49043-175">按 Ctrl+F5 即可執行而不使用偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="49043-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="49043-176">Visual Studio 啟動：</span><span class="sxs-lookup"><span data-stu-id="49043-176">Visual Studio launches:</span></span>

* <span data-ttu-id="49043-177">IIS Express web 伺服器。</span><span class="sxs-lookup"><span data-stu-id="49043-177">The IIS Express web server.</span></span>
* <span data-ttu-id="49043-178">預設瀏覽器並流覽至 `https://localhost:<port>/swagger/index.html` ，其中 `<port>` 是隨機播放的埠號碼。</span><span class="sxs-lookup"><span data-stu-id="49043-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49043-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49043-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="49043-180">按 Ctrl+F5 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="49043-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="49043-181">在瀏覽器中，移至下列 URL： [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="49043-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49043-182">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="49043-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="49043-183">選取 [**執行**  >  **開始調試** 程式] 以啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="49043-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="49043-184">Visual Studio for Mac 會啟動瀏覽器並巡覽至 `https://localhost:<port>`，其中 `<port>` 是隨機選擇的連接埠號碼。</span><span class="sxs-lookup"><span data-stu-id="49043-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="49043-185">傳回 HTTP 404 (找不到) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="49043-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="49043-186">將 `/swagger` 附加至 URL (將 URL 變更為 `https://localhost:<port>/swagger`)。</span><span class="sxs-lookup"><span data-stu-id="49043-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="49043-187">Swagger 頁面隨即 `/swagger/index.html` 顯示。</span><span class="sxs-lookup"><span data-stu-id="49043-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="49043-188">選取 [**立即**  >  **試用**]  >  **執行**。</span><span class="sxs-lookup"><span data-stu-id="49043-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="49043-189">頁面會顯示：</span><span class="sxs-lookup"><span data-stu-id="49043-189">The page displays:</span></span>

* <span data-ttu-id="49043-190">用來測試 WeatherForecast API 的 [捲曲](https://curl.haxx.se/) 命令。</span><span class="sxs-lookup"><span data-stu-id="49043-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="49043-191">用來測試 WeatherForecast API 的 URL。</span><span class="sxs-lookup"><span data-stu-id="49043-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="49043-192">回應碼、主體和標頭。</span><span class="sxs-lookup"><span data-stu-id="49043-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="49043-193">具有媒體類型的下拉式清單方塊，以及範例值和架構。</span><span class="sxs-lookup"><span data-stu-id="49043-193">A drop down list box with media types and the example value and schema.</span></span>

<span data-ttu-id="49043-194">如果 Swagger 頁面未出現，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/21647)。</span><span class="sxs-lookup"><span data-stu-id="49043-194">If the Swagger page doesn't appear, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/21647).</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="49043-195">Swagger 可用來產生 web Api 的實用檔和說明頁面。</span><span class="sxs-lookup"><span data-stu-id="49043-195">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="49043-196">本教學課程著重于建立 web API。</span><span class="sxs-lookup"><span data-stu-id="49043-196">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="49043-197">如需 Swagger 的詳細資訊，請參閱 <xref:tutorials/web-api-help-pages-using-swagger> 。</span><span class="sxs-lookup"><span data-stu-id="49043-197">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="49043-198">將 **要求 URL** 複製並貼入瀏覽器中：  `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="49043-198">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="49043-199">系統會傳回與下列類似的 JSON：</span><span class="sxs-lookup"><span data-stu-id="49043-199">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="49043-200">更新 launchUrl</span><span class="sxs-lookup"><span data-stu-id="49043-200">Update the launchUrl</span></span>

<span data-ttu-id="49043-201">在 *Properties\launchSettings.js開啟*，請 `launchUrl` 從更新 `"swagger"` 為 `"api/TodoItems"` ：</span><span class="sxs-lookup"><span data-stu-id="49043-201">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="49043-202">由於 Swagger 已移除，因此上述標記會將啟動的 URL 變更為在下列各節中新增之控制器的 GET 方法。</span><span class="sxs-lookup"><span data-stu-id="49043-202">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="49043-203">新增模型類別</span><span class="sxs-lookup"><span data-stu-id="49043-203">Add a model class</span></span>

<span data-ttu-id="49043-204">「模型」是代表應用程式所管理資料的一組類別。</span><span class="sxs-lookup"><span data-stu-id="49043-204">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="49043-205">此應用程式的模型是單一 `TodoItem` 類別。</span><span class="sxs-lookup"><span data-stu-id="49043-205">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49043-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49043-206">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49043-207">在 **方案總管** 中，以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="49043-207">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="49043-208">選取 **[**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="49043-208">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="49043-209">為資料夾命名 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="49043-209">Name the folder *Models*.</span></span>

* <span data-ttu-id="49043-210">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [**加入**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="49043-210">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="49043-211">將類別命名為 *TodoItem*，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="49043-211">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="49043-212">以下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="49043-212">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49043-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49043-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49043-214">新增名為的資料夾 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="49043-214">Add a folder named *Models*.</span></span>

* <span data-ttu-id="49043-215">`TodoItem`使用下列程式碼，將類別新增至 *Models* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="49043-215">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49043-216">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="49043-216">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="49043-217">以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="49043-217">Right-click the project.</span></span> <span data-ttu-id="49043-218">選取 **[**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="49043-218">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="49043-219">為資料夾命名 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="49043-219">Name the folder *Models*.</span></span>

  ![新增資料夾](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="49043-221">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 > [**新增** 檔案 > **一般**] > **空白類別**。</span><span class="sxs-lookup"><span data-stu-id="49043-221">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="49043-222">將類別命名為 *TodoItem*，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="49043-222">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="49043-223">以下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="49043-223">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="49043-224">`Id` 屬性的功能相當於關聯式資料庫中的唯一索引鍵。</span><span class="sxs-lookup"><span data-stu-id="49043-224">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="49043-225">模型類別可移至專案中的任何位置，但依照慣例，會 *Models* 使用資料夾。</span><span class="sxs-lookup"><span data-stu-id="49043-225">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="49043-226">新增資料庫內容</span><span class="sxs-lookup"><span data-stu-id="49043-226">Add a database context</span></span>

<span data-ttu-id="49043-227">「資料庫內容」是為資料模型協調 Entity Framework 功能的主要類別。</span><span class="sxs-lookup"><span data-stu-id="49043-227">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="49043-228">此類別是透過衍生自 <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> 類別來建立。</span><span class="sxs-lookup"><span data-stu-id="49043-228">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49043-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49043-229">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="49043-230">新增 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="49043-230">Add NuGet packages</span></span>

* <span data-ttu-id="49043-231">在 [工具] 功能表上，選取 [NuGet 套件管理員] > [管理解決方案的 NuGet 套件]。</span><span class="sxs-lookup"><span data-stu-id="49043-231">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="49043-232">選取 [ **流覽** ] 索引標籤，然後 `Microsoft.EntityFrameworkCore.InMemory` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="49043-232">Select the **Browse** tab, and then enter `Microsoft.EntityFrameworkCore.InMemory` in the search box.</span></span>
* <span data-ttu-id="49043-233">選取 `Microsoft.EntityFrameworkCore.InMemory` 左窗格中的。</span><span class="sxs-lookup"><span data-stu-id="49043-233">Select `Microsoft.EntityFrameworkCore.InMemory` in the left pane.</span></span>
* <span data-ttu-id="49043-234">選取右窗格中的 [專案] 核取方塊，然後選取 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="49043-234">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![NuGet 套件管理員](first-web-api/_static/5/vsNuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="49043-236">新增 TodoCoNtext 資料庫內容</span><span class="sxs-lookup"><span data-stu-id="49043-236">Add the TodoContext database context</span></span>

* <span data-ttu-id="49043-237">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [**加入**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="49043-237">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="49043-238">將類別命名為 *TodoContext*，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="49043-238">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="49043-239">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="49043-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="49043-240">將 `TodoContext` 類別新增至 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="49043-240">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="49043-241">輸入下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="49043-241">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="49043-242">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="49043-242">Register the database context</span></span>

<span data-ttu-id="49043-243">在 ASP.NET Core 中，資料庫內容等服務必須向[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器註冊。</span><span class="sxs-lookup"><span data-stu-id="49043-243">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="49043-244">此容器會將服務提供給控制器。</span><span class="sxs-lookup"><span data-stu-id="49043-244">The container provides the service to controllers.</span></span>

<span data-ttu-id="49043-245">使用下列程式碼來更新 *啟動 .cs* ：</span><span class="sxs-lookup"><span data-stu-id="49043-245">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="49043-246">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="49043-246">The preceding code:</span></span>

* <span data-ttu-id="49043-247">移除 Swagger 呼叫。</span><span class="sxs-lookup"><span data-stu-id="49043-247">Removes the Swagger calls.</span></span>
* <span data-ttu-id="49043-248">移除未使用的 `using` 宣告。</span><span class="sxs-lookup"><span data-stu-id="49043-248">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="49043-249">將資料庫內容新增至 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="49043-249">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="49043-250">指定資料庫內容將會使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="49043-250">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="49043-251">Scaffold 控制器</span><span class="sxs-lookup"><span data-stu-id="49043-251">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49043-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49043-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49043-253">以滑鼠右鍵按一下 *Controllers* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="49043-253">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="49043-254">選取 [新增]**[新增 Scaffold 項目]** > 。</span><span class="sxs-lookup"><span data-stu-id="49043-254">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="49043-255">選取 [使用 Entity Framework 執行動作的 API 控制器]，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="49043-255">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="49043-256">在 [使用 Entity Framework 執行動作的 API 控制器] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="49043-256">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="49043-257">選取 **模型類別** 中的 [ **TodoItem (TodoApi] Models) 。**</span><span class="sxs-lookup"><span data-stu-id="49043-257">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="49043-258">選取 **資料內容類別** 中的 **TodoCoNtext (TodoApi Models) 。**</span><span class="sxs-lookup"><span data-stu-id="49043-258">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="49043-259">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="49043-259">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="49043-260">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="49043-260">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="49043-261">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="49043-261">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="49043-262">上述命令：</span><span class="sxs-lookup"><span data-stu-id="49043-262">The preceding commands:</span></span>

* <span data-ttu-id="49043-263">新增 Scaffolding 所需的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="49043-263">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="49043-264">安裝 Scaffolding 引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="49043-264">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="49043-265">Scaffold `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="49043-265">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="49043-266">產生的程式碼：</span><span class="sxs-lookup"><span data-stu-id="49043-266">The generated code:</span></span>

* <span data-ttu-id="49043-267">標記具有屬性的類別 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="49043-267">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="49043-268">這個屬性表示控制器會回應 Web API 要求。</span><span class="sxs-lookup"><span data-stu-id="49043-268">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="49043-269">如需屬性所啟用之特定行為的相關資訊，請參閱 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="49043-269">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="49043-270">使用 DI 將資料庫內容 (`TodoContext`) 插入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="49043-270">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="49043-271">控制器中的每一個 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法都會使用資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="49043-271">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="49043-272">的 ASP.NET Core 範本：</span><span class="sxs-lookup"><span data-stu-id="49043-272">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="49043-273">具有 views 的控制器包含 `[action]` 在路由範本中。</span><span class="sxs-lookup"><span data-stu-id="49043-273">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="49043-274">API 控制器不包含 `[action]` 在路由範本中。</span><span class="sxs-lookup"><span data-stu-id="49043-274">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="49043-275">當 `[action]` 權杖不在路由範本中時，會從路由中排除 [動作](xref:mvc/controllers/routing#action) 名稱。</span><span class="sxs-lookup"><span data-stu-id="49043-275">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="49043-276">也就是，不會在相符的路由中使用動作的相關聯方法名稱。</span><span class="sxs-lookup"><span data-stu-id="49043-276">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="49043-277">更新 PostTodoItem create 方法</span><span class="sxs-lookup"><span data-stu-id="49043-277">Update the PostTodoItem create method</span></span>

<span data-ttu-id="49043-278">更新中的 return 語句 `PostTodoItem` ，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 運算子：</span><span class="sxs-lookup"><span data-stu-id="49043-278">Update the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="49043-279">上述程式碼是 HTTP POST 方法，如屬性所指示 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="49043-279">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="49043-280">該方法會從 HTTP 要求本文取得待辦事項的值。</span><span class="sxs-lookup"><span data-stu-id="49043-280">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="49043-281">如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="49043-281">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="49043-282"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="49043-282">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="49043-283">如果成功，則傳回 [HTTP 201 狀態碼](https://developer.mozilla.org/docs/Web/HTTP/Status/201) 。</span><span class="sxs-lookup"><span data-stu-id="49043-283">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="49043-284">對於可在伺服器上建立新資源的 HTTP POST 方法，其標準回應是 HTTP 201。</span><span class="sxs-lookup"><span data-stu-id="49043-284">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="49043-285">將 [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 標頭新增至回應。</span><span class="sxs-lookup"><span data-stu-id="49043-285">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="49043-286">`Location`標頭會指定新建立之待辦事項的[URI](https://developer.mozilla.org/docs/Glossary/URI) 。</span><span class="sxs-lookup"><span data-stu-id="49043-286">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="49043-287">如需詳細資訊，請參閱 [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (已建立 10.2.2 201)。</span><span class="sxs-lookup"><span data-stu-id="49043-287">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="49043-288">參考 `GetTodoItem` 動作以建立 `Location` 標頭的 URI。</span><span class="sxs-lookup"><span data-stu-id="49043-288">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="49043-289">C# `nameof` 關鍵字是用來避免在 `CreatedAtAction` 呼叫中以硬式編碼方式寫入動作名稱。</span><span class="sxs-lookup"><span data-stu-id="49043-289">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="49043-290">安裝 Postman</span><span class="sxs-lookup"><span data-stu-id="49043-290">Install Postman</span></span>

<span data-ttu-id="49043-291">本教學課程使用 Postman 來測試 Web API。</span><span class="sxs-lookup"><span data-stu-id="49043-291">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="49043-292">安裝 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="49043-292">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="49043-293">啟動 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="49043-293">Start the web app.</span></span>
* <span data-ttu-id="49043-294">啟動 Postman。</span><span class="sxs-lookup"><span data-stu-id="49043-294">Start Postman.</span></span>
* <span data-ttu-id="49043-295">停用 [SSL certificate verification] \(SSL 憑證驗證\)</span><span class="sxs-lookup"><span data-stu-id="49043-295">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="49043-296">從 [檔案]**[設定]** >  ([一般] 索引標籤)，停用 [SSL 憑證驗證]。</span><span class="sxs-lookup"><span data-stu-id="49043-296">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="49043-297">在測試控制器之後，請重新啟用 [SSL certificate verification] \(SSL 憑證驗證\)。</span><span class="sxs-lookup"><span data-stu-id="49043-297">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="49043-298">使用 Postman 測試 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="49043-298">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="49043-299">建立新的要求。</span><span class="sxs-lookup"><span data-stu-id="49043-299">Create a new request.</span></span>
* <span data-ttu-id="49043-300">將 HTTP 方法設為 `POST`。</span><span class="sxs-lookup"><span data-stu-id="49043-300">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="49043-301">將 URI 設定為 `https://localhost:<port>/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="49043-301">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="49043-302">例如： `https://localhost:5001/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="49043-302">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="49043-303">選取 [Body] \(本文\) 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="49043-303">Select the **Body** tab.</span></span>
* <span data-ttu-id="49043-304">選取 [原始] 選項按鈕。</span><span class="sxs-lookup"><span data-stu-id="49043-304">Select the **raw** radio button.</span></span>
* <span data-ttu-id="49043-305">將類型設定為 **JSON (application/json)**。</span><span class="sxs-lookup"><span data-stu-id="49043-305">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="49043-306">在要求本文中，針對待辦項目輸入 JSON：</span><span class="sxs-lookup"><span data-stu-id="49043-306">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="49043-307">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="49043-307">Select **Send**.</span></span>

  ![Postman 與建立要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="49043-309">測試位置標頭 URI</span><span class="sxs-lookup"><span data-stu-id="49043-309">Test the location header URI</span></span>

<span data-ttu-id="49043-310">您可以在瀏覽器中測試位置標頭 URI。</span><span class="sxs-lookup"><span data-stu-id="49043-310">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="49043-311">複製位置標頭 URI，並將其貼到瀏覽器中。</span><span class="sxs-lookup"><span data-stu-id="49043-311">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="49043-312">若要在 Postman 中進行測試：</span><span class="sxs-lookup"><span data-stu-id="49043-312">To test in Postman:</span></span>

* <span data-ttu-id="49043-313">在 [回應] 窗格中選取 [標頭] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="49043-313">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="49043-314">複製 [位置] 標頭值：</span><span class="sxs-lookup"><span data-stu-id="49043-314">Copy the **Location** header value:</span></span>

  ![Postman 主控台的 [標頭] 索引標籤](first-web-api/_static/3/create.png)

* <span data-ttu-id="49043-316">將 HTTP 方法設為 `GET`。</span><span class="sxs-lookup"><span data-stu-id="49043-316">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="49043-317">將 URI 設定為 `https://localhost:<port>/api/TodoItems/1` 。</span><span class="sxs-lookup"><span data-stu-id="49043-317">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="49043-318">例如： `https://localhost:5001/api/TodoItems/1` 。</span><span class="sxs-lookup"><span data-stu-id="49043-318">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="49043-319">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="49043-319">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="49043-320">檢查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="49043-320">Examine the GET methods</span></span>

<span data-ttu-id="49043-321">系統會執行兩個 GET 端點：</span><span class="sxs-lookup"><span data-stu-id="49043-321">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="49043-322">從瀏覽器或 Postman 呼叫這兩個端點來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="49043-322">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="49043-323">例如：</span><span class="sxs-lookup"><span data-stu-id="49043-323">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="49043-324">`GetTodoItems` 的呼叫會產生類似下列的回應：</span><span class="sxs-lookup"><span data-stu-id="49043-324">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="49043-325">使用 Postman 測試 Get</span><span class="sxs-lookup"><span data-stu-id="49043-325">Test Get with Postman</span></span>

* <span data-ttu-id="49043-326">建立新的要求。</span><span class="sxs-lookup"><span data-stu-id="49043-326">Create a new request.</span></span>
* <span data-ttu-id="49043-327">將 HTTP 方法設定為 **GET**。</span><span class="sxs-lookup"><span data-stu-id="49043-327">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="49043-328">將要求 URI 設定為 `https://localhost:<port>/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="49043-328">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="49043-329">例如： `https://localhost:5001/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="49043-329">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="49043-330">在 Postman 中，設定 [Two pane view] \(雙窗格檢視\)。</span><span class="sxs-lookup"><span data-stu-id="49043-330">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="49043-331">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="49043-331">Select **Send**.</span></span>

<span data-ttu-id="49043-332">這個應用程式會使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="49043-332">This app uses an in-memory database.</span></span> <span data-ttu-id="49043-333">如果應用程式在停止後再啟動，上述 GET 要求將不會傳回任何資料。</span><span class="sxs-lookup"><span data-stu-id="49043-333">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="49043-334">如果沒有傳回任何資料，請將資料 [POST](#post) 到應用程式。</span><span class="sxs-lookup"><span data-stu-id="49043-334">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="49043-335">傳送和 URL 路徑</span><span class="sxs-lookup"><span data-stu-id="49043-335">Routing and URL paths</span></span>

<span data-ttu-id="49043-336">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)屬性代表回應 HTTP GET 要求的方法。</span><span class="sxs-lookup"><span data-stu-id="49043-336">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="49043-337">每個方法的 URL 路徑的建構方式如下：</span><span class="sxs-lookup"><span data-stu-id="49043-337">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="49043-338">一開始在控制器的 `Route` 屬性中使用範本字串：</span><span class="sxs-lookup"><span data-stu-id="49043-338">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="49043-339">以控制器的名稱取代 `[controller]`，也就是將控制器類別名稱減去 "Controller" 字尾。</span><span class="sxs-lookup"><span data-stu-id="49043-339">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="49043-340">在此範例中，控制器類別名稱是 **TodoItems** Controller，因此控制器名稱是 "TodoItems"。</span><span class="sxs-lookup"><span data-stu-id="49043-340">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="49043-341">ASP.NET Core [路由](xref:mvc/controllers/routing)不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="49043-341">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="49043-342">如果 `[HttpGet]` 屬性具有路由範本 (例如 `[HttpGet("products")]`)，請將其附加到路徑。</span><span class="sxs-lookup"><span data-stu-id="49043-342">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="49043-343">此範例不使用範本。</span><span class="sxs-lookup"><span data-stu-id="49043-343">This sample doesn't use a template.</span></span> <span data-ttu-id="49043-344">如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="49043-344">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="49043-345">在下列 `GetTodoItem` 方法中，`"{id}"` 是待辦事項唯一識別碼的預留位置變數。</span><span class="sxs-lookup"><span data-stu-id="49043-345">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="49043-346">當叫 `GetTodoItem` 用時，會將 `"{id}"` URL 中的值提供給方法的 `id` 參數。</span><span class="sxs-lookup"><span data-stu-id="49043-346">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="49043-347">傳回值</span><span class="sxs-lookup"><span data-stu-id="49043-347">Return values</span></span>

<span data-ttu-id="49043-348">和方法的傳回型 `GetTodoItems` 別 `GetTodoItem` 為 [ActionResult \<T> 類型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="49043-348">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="49043-349">ASP.NET Core 會自動將物件序列化為 [JSON](https://www.json.org/)，並將 JSON 寫入至回應訊息的本文。</span><span class="sxs-lookup"><span data-stu-id="49043-349">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="49043-350">如果沒有任何未處理的例外狀況，則此傳回型別的回應碼為 [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200)。</span><span class="sxs-lookup"><span data-stu-id="49043-350">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="49043-351">未處理的例外狀況會轉譯成 5xx 錯誤。</span><span class="sxs-lookup"><span data-stu-id="49043-351">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="49043-352">`ActionResult` 傳回型別可代表各種 HTTP 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="49043-352">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="49043-353">例如，`GetTodoItem` 可傳回兩個不同的狀態值：</span><span class="sxs-lookup"><span data-stu-id="49043-353">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="49043-354">如果沒有任何專案符合要求的識別碼，方法會傳回 [404 狀態](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 錯誤碼。</span><span class="sxs-lookup"><span data-stu-id="49043-354">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="49043-355">否則，方法會傳回 200 與 JSON 回應本文。</span><span class="sxs-lookup"><span data-stu-id="49043-355">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="49043-356">傳回 `item` 會導致 HTTP 200 回應。</span><span class="sxs-lookup"><span data-stu-id="49043-356">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="49043-357">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="49043-357">The PutTodoItem method</span></span>

<span data-ttu-id="49043-358">檢查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="49043-358">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="49043-359">`PutTodoItem` 類似於 `PostTodoItem`，但是會使用 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="49043-359">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="49043-360">回應為 [204 (沒有內容) ](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="49043-360">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="49043-361">根據 HTTP 規格，PUT 要求需要用戶端傳送整個更新的實體，而不只是變更。</span><span class="sxs-lookup"><span data-stu-id="49043-361">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="49043-362">若要支援部分更新，請使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="49043-362">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="49043-363">如果在呼叫 `PutTodoItem` 時發生錯誤，請呼叫 `GET` 以確保資料庫中有項目。</span><span class="sxs-lookup"><span data-stu-id="49043-363">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="49043-364">測試 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="49043-364">Test the PutTodoItem method</span></span>

<span data-ttu-id="49043-365">這個範例會使用必須在每次啟動應用程式時初始化的記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="49043-365">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="49043-366">資料庫中必須有項目，您才能進行 PUT 呼叫。</span><span class="sxs-lookup"><span data-stu-id="49043-366">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="49043-367">在進行 PUT 呼叫之前，請先呼叫 GET 以確保資料庫中有專案。</span><span class="sxs-lookup"><span data-stu-id="49043-367">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="49043-368">更新識別碼為1的待辦事項，並將其名稱設定為 `"feed fish"` ：</span><span class="sxs-lookup"><span data-stu-id="49043-368">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="49043-369">下圖顯示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="49043-369">The following image shows the Postman update:</span></span>

![顯示「204 (沒有內容) 回應」的 Postman 主控台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="49043-371">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="49043-371">The DeleteTodoItem method</span></span>

<span data-ttu-id="49043-372">檢查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="49043-372">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="49043-373">測試 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="49043-373">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="49043-374">使用 Postman 刪除待辦事項：</span><span class="sxs-lookup"><span data-stu-id="49043-374">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="49043-375">將方法設定為 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="49043-375">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="49043-376">設定要刪除之物件的 URI (例如 `https://localhost:5001/api/TodoItems/1`) 。</span><span class="sxs-lookup"><span data-stu-id="49043-376">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="49043-377">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="49043-377">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="49043-378">防止過度張貼</span><span class="sxs-lookup"><span data-stu-id="49043-378">Prevent over-posting</span></span>

<span data-ttu-id="49043-379">範例應用程式目前會公開整個 `TodoItem` 物件。</span><span class="sxs-lookup"><span data-stu-id="49043-379">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="49043-380">生產環境應用程式通常會限制使用模型子集來輸入和傳回的資料。</span><span class="sxs-lookup"><span data-stu-id="49043-380">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="49043-381">這項功能有許多原因，而且安全性是主要的原因。</span><span class="sxs-lookup"><span data-stu-id="49043-381">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="49043-382">模型的子集通常稱為資料傳輸物件 (DTO) 、輸入模型或視圖模型。</span><span class="sxs-lookup"><span data-stu-id="49043-382">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="49043-383">此文章中使用 **DTO** 。</span><span class="sxs-lookup"><span data-stu-id="49043-383">**DTO** is used in this article.</span></span>

<span data-ttu-id="49043-384">DTO 可以用來：</span><span class="sxs-lookup"><span data-stu-id="49043-384">A DTO may be used to:</span></span>

* <span data-ttu-id="49043-385">防止過度張貼。</span><span class="sxs-lookup"><span data-stu-id="49043-385">Prevent over-posting.</span></span>
* <span data-ttu-id="49043-386">隱藏用戶端不應該看到的屬性。</span><span class="sxs-lookup"><span data-stu-id="49043-386">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="49043-387">略過某些屬性，以減少承載大小。</span><span class="sxs-lookup"><span data-stu-id="49043-387">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="49043-388">壓平合併包含嵌套物件的物件圖形。</span><span class="sxs-lookup"><span data-stu-id="49043-388">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="49043-389">簡維物件圖形對於用戶端來說可能更方便。</span><span class="sxs-lookup"><span data-stu-id="49043-389">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="49043-390">若要示範 DTO 方法，請更新 `TodoItem` 類別以包含秘密欄位：</span><span class="sxs-lookup"><span data-stu-id="49043-390">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="49043-391">此應用程式必須隱藏秘密欄位，但系統管理應用程式可以選擇將它公開。</span><span class="sxs-lookup"><span data-stu-id="49043-391">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="49043-392">確認您可以張貼並取得秘密欄位。</span><span class="sxs-lookup"><span data-stu-id="49043-392">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="49043-393">建立 DTO 模型：</span><span class="sxs-lookup"><span data-stu-id="49043-393">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="49043-394">更新 `TodoItemsController` 以使用 `TodoItemDTO` ：</span><span class="sxs-lookup"><span data-stu-id="49043-394">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="49043-395">確認您無法張貼或取得秘密欄位。</span><span class="sxs-lookup"><span data-stu-id="49043-395">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="49043-396">使用 JavaScript 呼叫 Web API</span><span class="sxs-lookup"><span data-stu-id="49043-396">Call the web API with JavaScript</span></span>

<span data-ttu-id="49043-397">請參閱 [教學課程：使用 JavaScript 呼叫 ASP.NET Core WEB API](xref:tutorials/web-api-javascript)。</span><span class="sxs-lookup"><span data-stu-id="49043-397">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="49043-398">在本教學課程中，您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="49043-398">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="49043-399">建立 Web API 專案。</span><span class="sxs-lookup"><span data-stu-id="49043-399">Create a web API project.</span></span>
> * <span data-ttu-id="49043-400">新增模型類別和資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="49043-400">Add a model class and a database context.</span></span>
> * <span data-ttu-id="49043-401">使用 CRUD 方法 Scaffold 控制器。</span><span class="sxs-lookup"><span data-stu-id="49043-401">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="49043-402">設定路由、URL 路徑和傳回值。</span><span class="sxs-lookup"><span data-stu-id="49043-402">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="49043-403">使用 Postman 呼叫 Web API。</span><span class="sxs-lookup"><span data-stu-id="49043-403">Call the web API with Postman.</span></span>

<span data-ttu-id="49043-404">結束時，您會有一個 Web API，可以管理儲存在資料庫中的「待辦事項」。</span><span class="sxs-lookup"><span data-stu-id="49043-404">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="49043-405">概觀</span><span class="sxs-lookup"><span data-stu-id="49043-405">Overview</span></span>

<span data-ttu-id="49043-406">本教學課程會建立以下 API：</span><span class="sxs-lookup"><span data-stu-id="49043-406">This tutorial creates the following API:</span></span>

|<span data-ttu-id="49043-407">API</span><span class="sxs-lookup"><span data-stu-id="49043-407">API</span></span> | <span data-ttu-id="49043-408">描述</span><span class="sxs-lookup"><span data-stu-id="49043-408">Description</span></span> | <span data-ttu-id="49043-409">要求本文</span><span class="sxs-lookup"><span data-stu-id="49043-409">Request body</span></span> | <span data-ttu-id="49043-410">回應本文</span><span class="sxs-lookup"><span data-stu-id="49043-410">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="49043-411">取得所有待辦事項</span><span class="sxs-lookup"><span data-stu-id="49043-411">Get all to-do items</span></span> | <span data-ttu-id="49043-412">無</span><span class="sxs-lookup"><span data-stu-id="49043-412">None</span></span> | <span data-ttu-id="49043-413">待辦事項的陣列</span><span class="sxs-lookup"><span data-stu-id="49043-413">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="49043-414">依識別碼取得項目</span><span class="sxs-lookup"><span data-stu-id="49043-414">Get an item by ID</span></span> | <span data-ttu-id="49043-415">無</span><span class="sxs-lookup"><span data-stu-id="49043-415">None</span></span> | <span data-ttu-id="49043-416">待辦事項</span><span class="sxs-lookup"><span data-stu-id="49043-416">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="49043-417">新增記錄</span><span class="sxs-lookup"><span data-stu-id="49043-417">Add a new item</span></span> | <span data-ttu-id="49043-418">待辦事項</span><span class="sxs-lookup"><span data-stu-id="49043-418">To-do item</span></span> | <span data-ttu-id="49043-419">待辦事項</span><span class="sxs-lookup"><span data-stu-id="49043-419">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="49043-420">更新現有的項目 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="49043-420">Update an existing item &nbsp;</span></span> | <span data-ttu-id="49043-421">待辦事項</span><span class="sxs-lookup"><span data-stu-id="49043-421">To-do item</span></span> | <span data-ttu-id="49043-422">無</span><span class="sxs-lookup"><span data-stu-id="49043-422">None</span></span> |
|<span data-ttu-id="49043-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="49043-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="49043-424">刪除專案 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="49043-424">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="49043-425">無</span><span class="sxs-lookup"><span data-stu-id="49043-425">None</span></span> | <span data-ttu-id="49043-426">無</span><span class="sxs-lookup"><span data-stu-id="49043-426">None</span></span>|

<span data-ttu-id="49043-427">下圖顯示應用程式的設計。</span><span class="sxs-lookup"><span data-stu-id="49043-427">The following diagram shows the design of the app.</span></span>

![左側方塊代表用戶端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="49043-433">必要條件</span><span class="sxs-lookup"><span data-stu-id="49043-433">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49043-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49043-434">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="49043-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49043-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49043-436">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="49043-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="49043-437">建立 Web 專案</span><span class="sxs-lookup"><span data-stu-id="49043-437">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49043-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49043-438">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49043-439">從 [ **檔案** ] 功能表選取 [ **新增** > **專案**]。</span><span class="sxs-lookup"><span data-stu-id="49043-439">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="49043-440">選取 **ASP.NET Core Web 應用程式** 範本，然後按一下 [下一步]。</span><span class="sxs-lookup"><span data-stu-id="49043-440">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="49043-441">將專案命名為 *TodoApi*，然後按一下 [建立]。</span><span class="sxs-lookup"><span data-stu-id="49043-441">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="49043-442">在 [ **建立新的 ASP.NET Core Web 應用程式** ] 對話方塊中，確認已選取 [ **.net Core** ] 和 [ **ASP.NET Core 3.1** ]。</span><span class="sxs-lookup"><span data-stu-id="49043-442">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="49043-443">選取 **API** 範本，然後按一下 [建立]。</span><span class="sxs-lookup"><span data-stu-id="49043-443">Select the **API** template and click **Create**.</span></span>

![VS 新增專案對話方塊](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="49043-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49043-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49043-446">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="49043-446">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="49043-447">將目錄 (`cd`) 變更為包含專案資料夾的資料夾。</span><span class="sxs-lookup"><span data-stu-id="49043-447">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="49043-448">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="49043-448">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="49043-449">當出現對話方塊詢問您是否要將所需的資產新增至專案時，選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="49043-449">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="49043-450">上述命令：</span><span class="sxs-lookup"><span data-stu-id="49043-450">The preceding commands:</span></span>

  * <span data-ttu-id="49043-451">建立新的 Web API 專案，然後在 Visual Studio Code 中予以開啟。</span><span class="sxs-lookup"><span data-stu-id="49043-451">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="49043-452">新增下一節需要的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="49043-452">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49043-453">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="49043-453">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="49043-454">選取 [檔案]**[新增解決方案]** > 。</span><span class="sxs-lookup"><span data-stu-id="49043-454">Select **File** > **New Solution**.</span></span>

  ![macOS 新增方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="49043-456">在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用程式**  >  **API**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="49043-456">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="49043-457">在8.6 版或更新版本中，選取 [ **Web] 和 [主控台**  >  **應用程式**  >  **API**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="49043-457">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 範本選取專案](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="49043-459">在 [ **設定新的 ASP.NET Core WEB API** ] 對話方塊中，選取最新的 .net Core 3.X **目標 Framework**。</span><span class="sxs-lookup"><span data-stu-id="49043-459">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="49043-460">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="49043-460">Select **Next**.</span></span>

* <span data-ttu-id="49043-461">針對 [專案名稱] 輸入 *TodoApi*，然後選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="49043-461">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![設定對話方塊](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="49043-463">在專案資料夾中開啟命令終端機，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="49043-463">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="49043-464">測試 API</span><span class="sxs-lookup"><span data-stu-id="49043-464">Test the API</span></span>

<span data-ttu-id="49043-465">專案範本會建立 `WeatherForecast` API。</span><span class="sxs-lookup"><span data-stu-id="49043-465">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="49043-466">從瀏覽器呼叫 `Get` 方法來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="49043-466">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49043-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49043-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="49043-468">按 Ctrl+F5 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="49043-468">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="49043-469">Visual Studio 會啟動瀏覽器並巡覽至 `https://localhost:<port>/WeatherForecast`，其中 `<port>` 是隨機選擇的通訊埠編號。</span><span class="sxs-lookup"><span data-stu-id="49043-469">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="49043-470">如果出現對話方塊詢問您是否應該信任 IIS Express 憑證，請選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="49043-470">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="49043-471">在接著出現的 [安全性警告] 對話方塊中，選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="49043-471">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49043-472">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49043-472">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="49043-473">按 Ctrl+F5 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="49043-473">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="49043-474">在瀏覽器中，前往下列 URL：`https://localhost:5001/WeatherForecast`。</span><span class="sxs-lookup"><span data-stu-id="49043-474">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49043-475">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="49043-475">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="49043-476">選取 [**執行**  >  **開始調試** 程式] 以啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="49043-476">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="49043-477">Visual Studio for Mac 會啟動瀏覽器並巡覽至 `https://localhost:<port>`，其中 `<port>` 是隨機選擇的連接埠號碼。</span><span class="sxs-lookup"><span data-stu-id="49043-477">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="49043-478">傳回 HTTP 404 (找不到) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="49043-478">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="49043-479">將 `/WeatherForecast` 附加至 URL (將 URL 變更為 `https://localhost:<port>/WeatherForecast`)。</span><span class="sxs-lookup"><span data-stu-id="49043-479">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="49043-480">系統會傳回與下列類似的 JSON：</span><span class="sxs-lookup"><span data-stu-id="49043-480">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="49043-481">新增模型類別</span><span class="sxs-lookup"><span data-stu-id="49043-481">Add a model class</span></span>

<span data-ttu-id="49043-482">「模型」是代表應用程式所管理資料的一組類別。</span><span class="sxs-lookup"><span data-stu-id="49043-482">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="49043-483">此應用程式的模型是單一 `TodoItem` 類別。</span><span class="sxs-lookup"><span data-stu-id="49043-483">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49043-484">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49043-484">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49043-485">在 **方案總管** 中，以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="49043-485">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="49043-486">選取 **[**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="49043-486">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="49043-487">為資料夾命名 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="49043-487">Name the folder *Models*.</span></span>

* <span data-ttu-id="49043-488">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [**加入**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="49043-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="49043-489">將類別命名為 *TodoItem*，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="49043-489">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="49043-490">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="49043-490">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49043-491">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49043-491">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49043-492">新增名為的資料夾 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="49043-492">Add a folder named *Models*.</span></span>

* <span data-ttu-id="49043-493">`TodoItem`使用下列程式碼，將類別新增至 *Models* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="49043-493">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49043-494">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="49043-494">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="49043-495">以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="49043-495">Right-click the project.</span></span> <span data-ttu-id="49043-496">選取 **[**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="49043-496">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="49043-497">為資料夾命名 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="49043-497">Name the folder *Models*.</span></span>

  ![新增資料夾](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="49043-499">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 > [**新增** 檔案 > **一般**] > **空白類別**。</span><span class="sxs-lookup"><span data-stu-id="49043-499">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="49043-500">將類別命名為 *TodoItem*，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="49043-500">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="49043-501">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="49043-501">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="49043-502">`Id` 屬性的功能相當於關聯式資料庫中的唯一索引鍵。</span><span class="sxs-lookup"><span data-stu-id="49043-502">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="49043-503">模型類別可移至專案中的任何位置，但依照慣例，會 *Models* 使用資料夾。</span><span class="sxs-lookup"><span data-stu-id="49043-503">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="49043-504">新增資料庫內容</span><span class="sxs-lookup"><span data-stu-id="49043-504">Add a database context</span></span>

<span data-ttu-id="49043-505">「資料庫內容」是為資料模型協調 Entity Framework 功能的主要類別。</span><span class="sxs-lookup"><span data-stu-id="49043-505">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="49043-506">此類別是透過衍生自 `Microsoft.EntityFrameworkCore.DbContext` 類別來建立。</span><span class="sxs-lookup"><span data-stu-id="49043-506">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49043-507">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49043-507">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="49043-508">新增 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="49043-508">Add NuGet packages</span></span>

* <span data-ttu-id="49043-509">在 [工具] 功能表上，選取 [NuGet 套件管理員] > [管理解決方案的 NuGet 套件]。</span><span class="sxs-lookup"><span data-stu-id="49043-509">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="49043-510">選取 [**流覽**] 索引標籤，然後在 [搜尋] 方塊中輸入 **microsoft.entityframeworkcore。**</span><span class="sxs-lookup"><span data-stu-id="49043-510">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.InMemory** in the search box.</span></span>
* <span data-ttu-id="49043-511">選取左窗格中的 [ **microsoft.entityframeworkcore** ]。</span><span class="sxs-lookup"><span data-stu-id="49043-511">Select **Microsoft.EntityFrameworkCore.InMemory** in the left pane.</span></span>
* <span data-ttu-id="49043-512">選取右窗格中的 [專案] 核取方塊，然後選取 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="49043-512">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![NuGet 套件管理員](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="49043-514">新增 TodoCoNtext 資料庫內容</span><span class="sxs-lookup"><span data-stu-id="49043-514">Add the TodoContext database context</span></span>

* <span data-ttu-id="49043-515">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [**加入**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="49043-515">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="49043-516">將類別命名為 *TodoContext*，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="49043-516">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="49043-517">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="49043-517">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="49043-518">將 `TodoContext` 類別新增至 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="49043-518">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="49043-519">輸入下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="49043-519">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="49043-520">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="49043-520">Register the database context</span></span>

<span data-ttu-id="49043-521">在 ASP.NET Core 中，資料庫內容等服務必須向[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器註冊。</span><span class="sxs-lookup"><span data-stu-id="49043-521">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="49043-522">此容器會將服務提供給控制器。</span><span class="sxs-lookup"><span data-stu-id="49043-522">The container provides the service to controllers.</span></span>

<span data-ttu-id="49043-523">使用下列醒目提示的程式碼更新 *Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="49043-523">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="49043-524">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="49043-524">The preceding code:</span></span>

* <span data-ttu-id="49043-525">移除未使用的 `using` 宣告。</span><span class="sxs-lookup"><span data-stu-id="49043-525">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="49043-526">將資料庫內容新增至 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="49043-526">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="49043-527">指定資料庫內容將會使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="49043-527">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="49043-528">Scaffold 控制器</span><span class="sxs-lookup"><span data-stu-id="49043-528">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49043-529">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49043-529">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49043-530">以滑鼠右鍵按一下 *Controllers* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="49043-530">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="49043-531">選取 [新增]**[新增 Scaffold 項目]** > 。</span><span class="sxs-lookup"><span data-stu-id="49043-531">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="49043-532">選取 [使用 Entity Framework 執行動作的 API 控制器]，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="49043-532">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="49043-533">在 [使用 Entity Framework 執行動作的 API 控制器] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="49043-533">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="49043-534">選取 **模型類別** 中的 [ **TodoItem (TodoApi] Models) 。**</span><span class="sxs-lookup"><span data-stu-id="49043-534">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="49043-535">選取 **資料內容類別** 中的 **TodoCoNtext (TodoApi Models) 。**</span><span class="sxs-lookup"><span data-stu-id="49043-535">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="49043-536">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="49043-536">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="49043-537">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="49043-537">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="49043-538">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="49043-538">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="49043-539">上述命令：</span><span class="sxs-lookup"><span data-stu-id="49043-539">The preceding commands:</span></span>

* <span data-ttu-id="49043-540">新增 Scaffolding 所需的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="49043-540">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="49043-541">安裝 Scaffolding 引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="49043-541">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="49043-542">Scaffold `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="49043-542">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="49043-543">產生的程式碼：</span><span class="sxs-lookup"><span data-stu-id="49043-543">The generated code:</span></span>

* <span data-ttu-id="49043-544">標記具有屬性的類別 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="49043-544">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="49043-545">這個屬性表示控制器會回應 Web API 要求。</span><span class="sxs-lookup"><span data-stu-id="49043-545">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="49043-546">如需屬性所啟用之特定行為的相關資訊，請參閱 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="49043-546">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="49043-547">使用 DI 將資料庫內容 (`TodoContext`) 插入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="49043-547">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="49043-548">控制器中的每一個 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法都會使用資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="49043-548">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="49043-549">的 ASP.NET Core 範本：</span><span class="sxs-lookup"><span data-stu-id="49043-549">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="49043-550">具有 views 的控制器包含 `[action]` 在路由範本中。</span><span class="sxs-lookup"><span data-stu-id="49043-550">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="49043-551">API 控制器不包含 `[action]` 在路由範本中。</span><span class="sxs-lookup"><span data-stu-id="49043-551">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="49043-552">當 `[action]` 權杖不在路由範本中時，會從路由中排除 [動作](xref:mvc/controllers/routing#action) 名稱。</span><span class="sxs-lookup"><span data-stu-id="49043-552">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="49043-553">也就是，不會在相符的路由中使用動作的相關聯方法名稱。</span><span class="sxs-lookup"><span data-stu-id="49043-553">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="49043-554">檢查 PostTodoItem 建立方法</span><span class="sxs-lookup"><span data-stu-id="49043-554">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="49043-555">取代 `PostTodoItem` 中的 return 陳述式，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 運算子：</span><span class="sxs-lookup"><span data-stu-id="49043-555">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="49043-556">上述程式碼是 HTTP POST 方法，如屬性所指示 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="49043-556">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="49043-557">該方法會從 HTTP 要求本文取得待辦事項的值。</span><span class="sxs-lookup"><span data-stu-id="49043-557">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="49043-558">如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="49043-558">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="49043-559"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="49043-559">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="49043-560">成功時會傳回 HTTP 201 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="49043-560">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="49043-561">對於可在伺服器上建立新資源的 HTTP POST 方法，其標準回應是 HTTP 201。</span><span class="sxs-lookup"><span data-stu-id="49043-561">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="49043-562">將 [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 標頭新增至回應。</span><span class="sxs-lookup"><span data-stu-id="49043-562">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="49043-563">`Location`標頭會指定新建立之待辦事項的[URI](https://developer.mozilla.org/docs/Glossary/URI) 。</span><span class="sxs-lookup"><span data-stu-id="49043-563">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="49043-564">如需詳細資訊，請參閱 [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (已建立 10.2.2 201)。</span><span class="sxs-lookup"><span data-stu-id="49043-564">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="49043-565">參考 `GetTodoItem` 動作以建立 `Location` 標頭的 URI。</span><span class="sxs-lookup"><span data-stu-id="49043-565">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="49043-566">C# `nameof` 關鍵字是用來避免在 `CreatedAtAction` 呼叫中以硬式編碼方式寫入動作名稱。</span><span class="sxs-lookup"><span data-stu-id="49043-566">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="49043-567">安裝 Postman</span><span class="sxs-lookup"><span data-stu-id="49043-567">Install Postman</span></span>

<span data-ttu-id="49043-568">本教學課程使用 Postman 來測試 Web API。</span><span class="sxs-lookup"><span data-stu-id="49043-568">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="49043-569">安裝 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="49043-569">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="49043-570">啟動 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="49043-570">Start the web app.</span></span>
* <span data-ttu-id="49043-571">啟動 Postman。</span><span class="sxs-lookup"><span data-stu-id="49043-571">Start Postman.</span></span>
* <span data-ttu-id="49043-572">停用 [SSL certificate verification] \(SSL 憑證驗證\)</span><span class="sxs-lookup"><span data-stu-id="49043-572">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="49043-573">從 [檔案]**[設定]** >  ([一般] 索引標籤)，停用 [SSL 憑證驗證]。</span><span class="sxs-lookup"><span data-stu-id="49043-573">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="49043-574">在測試控制器之後，請重新啟用 [SSL certificate verification] \(SSL 憑證驗證\)。</span><span class="sxs-lookup"><span data-stu-id="49043-574">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="49043-575">使用 Postman 測試 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="49043-575">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="49043-576">建立新的要求。</span><span class="sxs-lookup"><span data-stu-id="49043-576">Create a new request.</span></span>
* <span data-ttu-id="49043-577">將 HTTP 方法設為 `POST`。</span><span class="sxs-lookup"><span data-stu-id="49043-577">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="49043-578">將 URI 設定為 `https://localhost:<port>/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="49043-578">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="49043-579">例如： `https://localhost:5001/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="49043-579">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="49043-580">選取 [Body] \(本文\) 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="49043-580">Select the **Body** tab.</span></span>
* <span data-ttu-id="49043-581">選取 [原始] 選項按鈕。</span><span class="sxs-lookup"><span data-stu-id="49043-581">Select the **raw** radio button.</span></span>
* <span data-ttu-id="49043-582">將類型設定為 **JSON (application/json)**。</span><span class="sxs-lookup"><span data-stu-id="49043-582">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="49043-583">在要求本文中，針對待辦項目輸入 JSON：</span><span class="sxs-lookup"><span data-stu-id="49043-583">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="49043-584">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="49043-584">Select **Send**.</span></span>

  ![Postman 與建立要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="49043-586">使用 Postman 測試 location 標頭 URI</span><span class="sxs-lookup"><span data-stu-id="49043-586">Test the location header URI with Postman</span></span>

* <span data-ttu-id="49043-587">在 [回應] 窗格中選取 [標頭] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="49043-587">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="49043-588">複製 [位置] 標頭值：</span><span class="sxs-lookup"><span data-stu-id="49043-588">Copy the **Location** header value:</span></span>

  ![Postman 主控台的 [標頭] 索引標籤](first-web-api/_static/3/create.png)

* <span data-ttu-id="49043-590">將 HTTP 方法設為 `GET`。</span><span class="sxs-lookup"><span data-stu-id="49043-590">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="49043-591">將 URI 設定為 `https://localhost:<port>/api/TodoItems/1` 。</span><span class="sxs-lookup"><span data-stu-id="49043-591">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="49043-592">例如： `https://localhost:5001/api/TodoItems/1` 。</span><span class="sxs-lookup"><span data-stu-id="49043-592">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="49043-593">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="49043-593">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="49043-594">檢查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="49043-594">Examine the GET methods</span></span>

<span data-ttu-id="49043-595">這些方法會實作兩個 GET 端點：</span><span class="sxs-lookup"><span data-stu-id="49043-595">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="49043-596">從瀏覽器或 Postman 呼叫這兩個端點來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="49043-596">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="49043-597">例如：</span><span class="sxs-lookup"><span data-stu-id="49043-597">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="49043-598">`GetTodoItems` 的呼叫會產生類似下列的回應：</span><span class="sxs-lookup"><span data-stu-id="49043-598">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="49043-599">使用 Postman 測試 Get</span><span class="sxs-lookup"><span data-stu-id="49043-599">Test Get with Postman</span></span>

* <span data-ttu-id="49043-600">建立新的要求。</span><span class="sxs-lookup"><span data-stu-id="49043-600">Create a new request.</span></span>
* <span data-ttu-id="49043-601">將 HTTP 方法設定為 **GET**。</span><span class="sxs-lookup"><span data-stu-id="49043-601">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="49043-602">將要求 URI 設定為 `https://localhost:<port>/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="49043-602">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="49043-603">例如： `https://localhost:5001/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="49043-603">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="49043-604">在 Postman 中，設定 [Two pane view] \(雙窗格檢視\)。</span><span class="sxs-lookup"><span data-stu-id="49043-604">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="49043-605">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="49043-605">Select **Send**.</span></span>

<span data-ttu-id="49043-606">這個應用程式會使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="49043-606">This app uses an in-memory database.</span></span> <span data-ttu-id="49043-607">如果應用程式在停止後再啟動，上述 GET 要求將不會傳回任何資料。</span><span class="sxs-lookup"><span data-stu-id="49043-607">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="49043-608">如果沒有傳回任何資料，請將資料 [POST](#post) 到應用程式。</span><span class="sxs-lookup"><span data-stu-id="49043-608">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="49043-609">傳送和 URL 路徑</span><span class="sxs-lookup"><span data-stu-id="49043-609">Routing and URL paths</span></span>

<span data-ttu-id="49043-610">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)屬性代表回應 HTTP GET 要求的方法。</span><span class="sxs-lookup"><span data-stu-id="49043-610">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="49043-611">每個方法的 URL 路徑的建構方式如下：</span><span class="sxs-lookup"><span data-stu-id="49043-611">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="49043-612">一開始在控制器的 `Route` 屬性中使用範本字串：</span><span class="sxs-lookup"><span data-stu-id="49043-612">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="49043-613">以控制器的名稱取代 `[controller]`，也就是將控制器類別名稱減去 "Controller" 字尾。</span><span class="sxs-lookup"><span data-stu-id="49043-613">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="49043-614">在此範例中，控制器類別名稱是 **TodoItems** Controller，因此控制器名稱是 "TodoItems"。</span><span class="sxs-lookup"><span data-stu-id="49043-614">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="49043-615">ASP.NET Core [路由](xref:mvc/controllers/routing)不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="49043-615">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="49043-616">如果 `[HttpGet]` 屬性具有路由範本 (例如 `[HttpGet("products")]`)，請將其附加到路徑。</span><span class="sxs-lookup"><span data-stu-id="49043-616">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="49043-617">此範例不使用範本。</span><span class="sxs-lookup"><span data-stu-id="49043-617">This sample doesn't use a template.</span></span> <span data-ttu-id="49043-618">如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="49043-618">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="49043-619">在下列 `GetTodoItem` 方法中，`"{id}"` 是待辦事項唯一識別碼的預留位置變數。</span><span class="sxs-lookup"><span data-stu-id="49043-619">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="49043-620">當叫 `GetTodoItem` 用時，會將 `"{id}"` URL 中的值提供給方法的 `id` 參數。</span><span class="sxs-lookup"><span data-stu-id="49043-620">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="49043-621">傳回值</span><span class="sxs-lookup"><span data-stu-id="49043-621">Return values</span></span> 

<span data-ttu-id="49043-622">和方法的傳回型 `GetTodoItems` 別 `GetTodoItem` 為 [ActionResult \<T> 類型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="49043-622">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="49043-623">ASP.NET Core 會自動將物件序列化為 [JSON](https://www.json.org/)，並將 JSON 寫入至回應訊息的本文。</span><span class="sxs-lookup"><span data-stu-id="49043-623">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="49043-624">此傳回型別的回應碼為 200，假設沒有任何未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="49043-624">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="49043-625">未處理的例外狀況會轉譯成 5xx 錯誤。</span><span class="sxs-lookup"><span data-stu-id="49043-625">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="49043-626">`ActionResult` 傳回型別可代表各種 HTTP 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="49043-626">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="49043-627">例如，`GetTodoItem` 可傳回兩個不同的狀態值：</span><span class="sxs-lookup"><span data-stu-id="49043-627">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="49043-628">如果沒有任何專案符合要求的識別碼，方法會傳回 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 錯誤碼。</span><span class="sxs-lookup"><span data-stu-id="49043-628">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="49043-629">否則，方法會傳回 200 與 JSON 回應本文。</span><span class="sxs-lookup"><span data-stu-id="49043-629">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="49043-630">傳回 `item` 會導致 HTTP 200 回應。</span><span class="sxs-lookup"><span data-stu-id="49043-630">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="49043-631">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="49043-631">The PutTodoItem method</span></span>

<span data-ttu-id="49043-632">檢查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="49043-632">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="49043-633">`PutTodoItem` 類似於 `PostTodoItem`，但是會使用 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="49043-633">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="49043-634">回應為 [204 (沒有內容) ](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="49043-634">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="49043-635">根據 HTTP 規格，PUT 要求需要用戶端傳送整個更新的實體，而不只是變更。</span><span class="sxs-lookup"><span data-stu-id="49043-635">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="49043-636">若要支援部分更新，請使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="49043-636">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="49043-637">如果在呼叫 `PutTodoItem` 時發生錯誤，請呼叫 `GET` 以確保資料庫中有項目。</span><span class="sxs-lookup"><span data-stu-id="49043-637">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="49043-638">測試 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="49043-638">Test the PutTodoItem method</span></span>

<span data-ttu-id="49043-639">這個範例會使用必須在每次啟動應用程式時初始化的記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="49043-639">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="49043-640">資料庫中必須有項目，您才能進行 PUT 呼叫。</span><span class="sxs-lookup"><span data-stu-id="49043-640">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="49043-641">在進行 PUT 呼叫之前，請先呼叫 GET 以確保資料庫中有專案。</span><span class="sxs-lookup"><span data-stu-id="49043-641">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="49043-642">更新識別碼為1的待辦事項，並將其名稱設定為「摘要魚」：</span><span class="sxs-lookup"><span data-stu-id="49043-642">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="49043-643">下圖顯示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="49043-643">The following image shows the Postman update:</span></span>

![顯示「204 (沒有內容) 回應」的 Postman 主控台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="49043-645">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="49043-645">The DeleteTodoItem method</span></span>

<span data-ttu-id="49043-646">檢查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="49043-646">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="49043-647">測試 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="49043-647">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="49043-648">使用 Postman 刪除待辦事項：</span><span class="sxs-lookup"><span data-stu-id="49043-648">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="49043-649">將方法設定為 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="49043-649">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="49043-650">設定要刪除之物件的 URI (例如 `https://localhost:5001/api/TodoItems/1`) 。</span><span class="sxs-lookup"><span data-stu-id="49043-650">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="49043-651">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="49043-651">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="49043-652">防止過度張貼</span><span class="sxs-lookup"><span data-stu-id="49043-652">Prevent over-posting</span></span>

<span data-ttu-id="49043-653">範例應用程式目前會公開整個 `TodoItem` 物件。</span><span class="sxs-lookup"><span data-stu-id="49043-653">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="49043-654">生產環境應用程式通常會限制使用模型子集來輸入和傳回的資料。</span><span class="sxs-lookup"><span data-stu-id="49043-654">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="49043-655">這項功能有許多原因，而且安全性是主要的原因。</span><span class="sxs-lookup"><span data-stu-id="49043-655">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="49043-656">模型的子集通常稱為資料傳輸物件 (DTO) 、輸入模型或視圖模型。</span><span class="sxs-lookup"><span data-stu-id="49043-656">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="49043-657">此文章中使用 **DTO** 。</span><span class="sxs-lookup"><span data-stu-id="49043-657">**DTO** is used in this article.</span></span>

<span data-ttu-id="49043-658">DTO 可以用來：</span><span class="sxs-lookup"><span data-stu-id="49043-658">A DTO may be used to:</span></span>

* <span data-ttu-id="49043-659">防止過度張貼。</span><span class="sxs-lookup"><span data-stu-id="49043-659">Prevent over-posting.</span></span>
* <span data-ttu-id="49043-660">隱藏用戶端不應該看到的屬性。</span><span class="sxs-lookup"><span data-stu-id="49043-660">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="49043-661">略過某些屬性，以減少承載大小。</span><span class="sxs-lookup"><span data-stu-id="49043-661">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="49043-662">壓平合併包含嵌套物件的物件圖形。</span><span class="sxs-lookup"><span data-stu-id="49043-662">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="49043-663">簡維物件圖形對於用戶端來說可能更方便。</span><span class="sxs-lookup"><span data-stu-id="49043-663">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="49043-664">若要示範 DTO 方法，請更新 `TodoItem` 類別以包含秘密欄位：</span><span class="sxs-lookup"><span data-stu-id="49043-664">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="49043-665">此應用程式必須隱藏秘密欄位，但系統管理應用程式可以選擇將它公開。</span><span class="sxs-lookup"><span data-stu-id="49043-665">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="49043-666">確認您可以張貼並取得秘密欄位。</span><span class="sxs-lookup"><span data-stu-id="49043-666">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="49043-667">建立 DTO 模型：</span><span class="sxs-lookup"><span data-stu-id="49043-667">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="49043-668">更新 `TodoItemsController` 以使用 `TodoItemDTO` ：</span><span class="sxs-lookup"><span data-stu-id="49043-668">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="49043-669">確認您無法張貼或取得秘密欄位。</span><span class="sxs-lookup"><span data-stu-id="49043-669">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="49043-670">使用 JavaScript 呼叫 Web API</span><span class="sxs-lookup"><span data-stu-id="49043-670">Call the web API with JavaScript</span></span>

<span data-ttu-id="49043-671">請參閱 [教學課程：使用 JavaScript 呼叫 ASP.NET Core WEB API](xref:tutorials/web-api-javascript)。</span><span class="sxs-lookup"><span data-stu-id="49043-671">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="49043-672">在本教學課程中，您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="49043-672">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="49043-673">建立 Web API 專案。</span><span class="sxs-lookup"><span data-stu-id="49043-673">Create a web API project.</span></span>
> * <span data-ttu-id="49043-674">新增模型類別和資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="49043-674">Add a model class and a database context.</span></span>
> * <span data-ttu-id="49043-675">新增控制器。</span><span class="sxs-lookup"><span data-stu-id="49043-675">Add a controller.</span></span>
> * <span data-ttu-id="49043-676">新增 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="49043-676">Add CRUD methods.</span></span>
> * <span data-ttu-id="49043-677">設定路由和 URL 路徑。</span><span class="sxs-lookup"><span data-stu-id="49043-677">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="49043-678">指定傳回值。</span><span class="sxs-lookup"><span data-stu-id="49043-678">Specify return values.</span></span>
> * <span data-ttu-id="49043-679">使用 Postman 呼叫 Web API。</span><span class="sxs-lookup"><span data-stu-id="49043-679">Call the web API with Postman.</span></span>
> * <span data-ttu-id="49043-680">使用 JavaScript 呼叫 Web API。</span><span class="sxs-lookup"><span data-stu-id="49043-680">Call the web API with JavaScript.</span></span>

<span data-ttu-id="49043-681">結束時，您將會有一個可管理關聯式資料庫中所儲存「待辦事項」的 Web API。</span><span class="sxs-lookup"><span data-stu-id="49043-681">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="49043-682">總覽2。1</span><span class="sxs-lookup"><span data-stu-id="49043-682">Overview 2.1</span></span>

<span data-ttu-id="49043-683">本教學課程會建立以下 API：</span><span class="sxs-lookup"><span data-stu-id="49043-683">This tutorial creates the following API:</span></span>

|<span data-ttu-id="49043-684">API</span><span class="sxs-lookup"><span data-stu-id="49043-684">API</span></span> | <span data-ttu-id="49043-685">描述</span><span class="sxs-lookup"><span data-stu-id="49043-685">Description</span></span> | <span data-ttu-id="49043-686">要求本文</span><span class="sxs-lookup"><span data-stu-id="49043-686">Request body</span></span> | <span data-ttu-id="49043-687">回應本文</span><span class="sxs-lookup"><span data-stu-id="49043-687">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="49043-688">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="49043-688">GET /api/TodoItems</span></span> | <span data-ttu-id="49043-689">取得所有待辦事項</span><span class="sxs-lookup"><span data-stu-id="49043-689">Get all to-do items</span></span> | <span data-ttu-id="49043-690">無</span><span class="sxs-lookup"><span data-stu-id="49043-690">None</span></span> | <span data-ttu-id="49043-691">待辦事項的陣列</span><span class="sxs-lookup"><span data-stu-id="49043-691">Array of to-do items</span></span>|
|<span data-ttu-id="49043-692">GET /api/TodoItems/{識別碼}</span><span class="sxs-lookup"><span data-stu-id="49043-692">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="49043-693">依識別碼取得項目</span><span class="sxs-lookup"><span data-stu-id="49043-693">Get an item by ID</span></span> | <span data-ttu-id="49043-694">無</span><span class="sxs-lookup"><span data-stu-id="49043-694">None</span></span> | <span data-ttu-id="49043-695">待辦事項</span><span class="sxs-lookup"><span data-stu-id="49043-695">To-do item</span></span>|
|<span data-ttu-id="49043-696">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="49043-696">POST /api/TodoItems</span></span> | <span data-ttu-id="49043-697">新增記錄</span><span class="sxs-lookup"><span data-stu-id="49043-697">Add a new item</span></span> | <span data-ttu-id="49043-698">待辦事項</span><span class="sxs-lookup"><span data-stu-id="49043-698">To-do item</span></span> | <span data-ttu-id="49043-699">待辦事項</span><span class="sxs-lookup"><span data-stu-id="49043-699">To-do item</span></span> |
|<span data-ttu-id="49043-700">PUT /api/TodoItems/{識別碼}</span><span class="sxs-lookup"><span data-stu-id="49043-700">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="49043-701">更新現有的項目 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="49043-701">Update an existing item &nbsp;</span></span> | <span data-ttu-id="49043-702">待辦事項</span><span class="sxs-lookup"><span data-stu-id="49043-702">To-do item</span></span> | <span data-ttu-id="49043-703">無</span><span class="sxs-lookup"><span data-stu-id="49043-703">None</span></span> |
|<span data-ttu-id="49043-704">刪除/Api/todoitems/{識別碼} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="49043-704">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="49043-705">刪除專案 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="49043-705">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="49043-706">無</span><span class="sxs-lookup"><span data-stu-id="49043-706">None</span></span> | <span data-ttu-id="49043-707">無</span><span class="sxs-lookup"><span data-stu-id="49043-707">None</span></span>|

<span data-ttu-id="49043-708">下圖顯示應用程式的設計。</span><span class="sxs-lookup"><span data-stu-id="49043-708">The following diagram shows the design of the app.</span></span>

![左側方塊代表用戶端。](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="49043-714">必要條件2。1</span><span class="sxs-lookup"><span data-stu-id="49043-714">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49043-715">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49043-715">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="49043-716">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49043-716">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49043-717">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="49043-717">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="49043-718">建立 Web 專案2。1</span><span class="sxs-lookup"><span data-stu-id="49043-718">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49043-719">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49043-719">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49043-720">從 [ **檔案** ] 功能表選取 [ **新增** > **專案**]。</span><span class="sxs-lookup"><span data-stu-id="49043-720">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="49043-721">選取 **ASP.NET Core Web 應用程式** 範本，然後按一下 [下一步]。</span><span class="sxs-lookup"><span data-stu-id="49043-721">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="49043-722">將專案命名為 *TodoApi*，然後按一下 [建立]。</span><span class="sxs-lookup"><span data-stu-id="49043-722">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="49043-723">在 [建立新的 ASP.NET Core Web 應用程式] 對話方塊中，確認選取 [.NET Core] 和 [ASP.NET Core 2.2]。</span><span class="sxs-lookup"><span data-stu-id="49043-723">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="49043-724">選取 **API** 範本，然後按一下 [建立]。</span><span class="sxs-lookup"><span data-stu-id="49043-724">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="49043-725">請 **勿** 選取 [Enable Docker Support] \(啟用 Docker 支援\)。</span><span class="sxs-lookup"><span data-stu-id="49043-725">**Don't** select **Enable Docker Support**.</span></span>

![VS 新增專案對話方塊](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="49043-727">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49043-727">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49043-728">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="49043-728">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="49043-729">將目錄 (`cd`) 變更為包含專案資料夾的資料夾。</span><span class="sxs-lookup"><span data-stu-id="49043-729">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="49043-730">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="49043-730">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="49043-731">這些命令會建立新的 Web API 專案，並開啟新專案資料夾中的新 Visual Studio Code 執行個體。</span><span class="sxs-lookup"><span data-stu-id="49043-731">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="49043-732">當出現對話方塊詢問您是否要將所需的資產新增至專案時，選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="49043-732">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49043-733">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="49043-733">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="49043-734">選取 [檔案]**[新增解決方案]** > 。</span><span class="sxs-lookup"><span data-stu-id="49043-734">Select **File** > **New Solution**.</span></span>

  ![macOS 新增方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="49043-736">在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用程式**  >  **API**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="49043-736">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="49043-737">在8.6 版或更新版本中，選取 [ **Web] 和 [主控台**  >  **應用程式**  >  **API**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="49043-737">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="49043-738">在 [ **設定新的 ASP.NET Core WEB API** ] 對話方塊中，選取最新的 .net Core 2.X **目標 Framework**。</span><span class="sxs-lookup"><span data-stu-id="49043-738">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="49043-739">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="49043-739">Select **Next**.</span></span>

* <span data-ttu-id="49043-740">針對 [專案名稱] 輸入 *TodoApi*，然後選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="49043-740">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![設定對話方塊](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="49043-742">測試 API 2。1</span><span class="sxs-lookup"><span data-stu-id="49043-742">Test the API 2.1</span></span>

<span data-ttu-id="49043-743">專案範本會建立 `values` API。</span><span class="sxs-lookup"><span data-stu-id="49043-743">The project template creates a `values` API.</span></span> <span data-ttu-id="49043-744">從瀏覽器呼叫 `Get` 方法來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="49043-744">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49043-745">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49043-745">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="49043-746">按 Ctrl+F5 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="49043-746">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="49043-747">Visual Studio 會啟動瀏覽器並巡覽至 `https://localhost:<port>/api/values`，其中 `<port>` 是隨機選擇的通訊埠編號。</span><span class="sxs-lookup"><span data-stu-id="49043-747">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="49043-748">如果出現對話方塊詢問您是否應該信任 IIS Express 憑證，請選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="49043-748">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="49043-749">在接著出現的 [安全性警告] 對話方塊中，選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="49043-749">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49043-750">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49043-750">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="49043-751">按 Ctrl+F5 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="49043-751">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="49043-752">在瀏覽器中，前往下列 URL：`https://localhost:5001/api/values`。</span><span class="sxs-lookup"><span data-stu-id="49043-752">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49043-753">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="49043-753">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="49043-754">選取 [**執行**  >  **開始調試** 程式] 以啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="49043-754">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="49043-755">Visual Studio for Mac 會啟動瀏覽器並巡覽至 `https://localhost:<port>`，其中 `<port>` 是隨機選擇的連接埠號碼。</span><span class="sxs-lookup"><span data-stu-id="49043-755">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="49043-756">傳回 HTTP 404 (找不到) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="49043-756">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="49043-757">將 `/api/values` 附加至 URL (將 URL 變更為 `https://localhost:<port>/api/values`)。</span><span class="sxs-lookup"><span data-stu-id="49043-757">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="49043-758">隨即傳回下列 JSON：</span><span class="sxs-lookup"><span data-stu-id="49043-758">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="49043-759">新增模型類別2。1</span><span class="sxs-lookup"><span data-stu-id="49043-759">Add a model class 2.1</span></span>

<span data-ttu-id="49043-760">「模型」是代表應用程式所管理資料的一組類別。</span><span class="sxs-lookup"><span data-stu-id="49043-760">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="49043-761">此應用程式的模型是單一 `TodoItem` 類別。</span><span class="sxs-lookup"><span data-stu-id="49043-761">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49043-762">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49043-762">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49043-763">在 **方案總管** 中，以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="49043-763">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="49043-764">選取 **[**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="49043-764">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="49043-765">為資料夾命名 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="49043-765">Name the folder *Models*.</span></span>

* <span data-ttu-id="49043-766">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [**加入**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="49043-766">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="49043-767">將類別命名為 *TodoItem*，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="49043-767">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="49043-768">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="49043-768">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49043-769">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49043-769">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49043-770">新增名為的資料夾 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="49043-770">Add a folder named *Models*.</span></span>

* <span data-ttu-id="49043-771">`TodoItem`使用下列程式碼，將類別新增至 *Models* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="49043-771">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49043-772">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="49043-772">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="49043-773">以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="49043-773">Right-click the project.</span></span> <span data-ttu-id="49043-774">選取 **[**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="49043-774">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="49043-775">為資料夾命名 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="49043-775">Name the folder *Models*.</span></span>

  ![新增資料夾](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="49043-777">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 > [**新增** 檔案 > **一般**] > **空白類別**。</span><span class="sxs-lookup"><span data-stu-id="49043-777">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="49043-778">將類別命名為 *TodoItem*，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="49043-778">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="49043-779">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="49043-779">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="49043-780">`Id` 屬性的功能相當於關聯式資料庫中的唯一索引鍵。</span><span class="sxs-lookup"><span data-stu-id="49043-780">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="49043-781">模型類別可移至專案中的任何位置，但依照慣例，會 *Models* 使用資料夾。</span><span class="sxs-lookup"><span data-stu-id="49043-781">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="49043-782">新增資料庫內容2。1</span><span class="sxs-lookup"><span data-stu-id="49043-782">Add a database context 2.1</span></span>

<span data-ttu-id="49043-783">「資料庫內容」是為資料模型協調 Entity Framework 功能的主要類別。</span><span class="sxs-lookup"><span data-stu-id="49043-783">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="49043-784">此類別是透過衍生自 `Microsoft.EntityFrameworkCore.DbContext` 類別來建立。</span><span class="sxs-lookup"><span data-stu-id="49043-784">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49043-785">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49043-785">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49043-786">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [**加入**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="49043-786">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="49043-787">將類別命名為 *TodoContext*，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="49043-787">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="49043-788">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="49043-788">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="49043-789">將 `TodoContext` 類別新增至 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="49043-789">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="49043-790">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="49043-790">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="49043-791">註冊資料庫內容2。1</span><span class="sxs-lookup"><span data-stu-id="49043-791">Register the database context 2.1</span></span>

<span data-ttu-id="49043-792">在 ASP.NET Core 中，資料庫內容等服務必須向[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器註冊。</span><span class="sxs-lookup"><span data-stu-id="49043-792">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="49043-793">此容器會將服務提供給控制器。</span><span class="sxs-lookup"><span data-stu-id="49043-793">The container provides the service to controllers.</span></span>

<span data-ttu-id="49043-794">使用下列醒目提示的程式碼更新 *Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="49043-794">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="49043-795">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="49043-795">The preceding code:</span></span>

* <span data-ttu-id="49043-796">移除未使用的 `using` 宣告。</span><span class="sxs-lookup"><span data-stu-id="49043-796">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="49043-797">將資料庫內容新增至 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="49043-797">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="49043-798">指定資料庫內容將會使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="49043-798">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="49043-799">新增控制器2。1</span><span class="sxs-lookup"><span data-stu-id="49043-799">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49043-800">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49043-800">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49043-801">以滑鼠右鍵按一下 *Controllers* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="49043-801">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="49043-802">選取 [ **加入** > **新專案**]。</span><span class="sxs-lookup"><span data-stu-id="49043-802">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="49043-803">在 [新增項目] 對話方塊中，選取 [API 控制器類別] 範本。</span><span class="sxs-lookup"><span data-stu-id="49043-803">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="49043-804">將類別命名為 *TodoController*，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="49043-804">Name the class *TodoController*, and select **Add**.</span></span>

  ![在搜尋方塊中輸入 controller 且已選取 Web API 控制器的 [新增項目] 對話方塊](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="49043-806">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="49043-806">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="49043-807">在 *Controllers* 資料夾中，建立名為 `TodoController` 的類別。</span><span class="sxs-lookup"><span data-stu-id="49043-807">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="49043-808">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="49043-808">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="49043-809">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="49043-809">The preceding code:</span></span>

* <span data-ttu-id="49043-810">定義不含方法的 API 控制器類別。</span><span class="sxs-lookup"><span data-stu-id="49043-810">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="49043-811">標記具有屬性的類別 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="49043-811">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="49043-812">這個屬性表示控制器會回應 Web API 要求。</span><span class="sxs-lookup"><span data-stu-id="49043-812">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="49043-813">如需屬性所啟用之特定行為的相關資訊，請參閱 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="49043-813">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="49043-814">使用 DI 將資料庫內容 (`TodoContext`) 插入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="49043-814">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="49043-815">控制器中的每一個 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法都會使用資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="49043-815">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="49043-816">如果資料庫是空的，請將名為 `Item1` 的項目新增至資料庫。</span><span class="sxs-lookup"><span data-stu-id="49043-816">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="49043-817">此程式碼是在建構函式中，因此每次執行都會有新的 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="49043-817">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="49043-818">如果您刪除所有項目，則建構函式會在下次呼叫 API 方法時重新建立 `Item1`。</span><span class="sxs-lookup"><span data-stu-id="49043-818">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="49043-819">因此看起來雖然像是刪除失敗，但實際為成功。</span><span class="sxs-lookup"><span data-stu-id="49043-819">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="49043-820">新增 Get 方法2。1</span><span class="sxs-lookup"><span data-stu-id="49043-820">Add Get methods 2.1</span></span>

<span data-ttu-id="49043-821">若要提供擷取待辦事項的 API，請在 `TodoController` 類別中新增下列方法：</span><span class="sxs-lookup"><span data-stu-id="49043-821">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="49043-822">這些方法會實作兩個 GET 端點：</span><span class="sxs-lookup"><span data-stu-id="49043-822">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="49043-823">如果應用程式仍在執行，請將其停止。</span><span class="sxs-lookup"><span data-stu-id="49043-823">Stop the app if it's still running.</span></span> <span data-ttu-id="49043-824">然後重新予以執行以包含最新的變更。</span><span class="sxs-lookup"><span data-stu-id="49043-824">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="49043-825">從瀏覽器呼叫這兩個端點來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="49043-825">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="49043-826">例如：</span><span class="sxs-lookup"><span data-stu-id="49043-826">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="49043-827">以下是呼叫 `GetTodoItems` 所產生的 HTTP 回應：</span><span class="sxs-lookup"><span data-stu-id="49043-827">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="49043-828">路由和 URL 路徑2。1</span><span class="sxs-lookup"><span data-stu-id="49043-828">Routing and URL paths 2.1</span></span>

<span data-ttu-id="49043-829">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)屬性代表回應 HTTP GET 要求的方法。</span><span class="sxs-lookup"><span data-stu-id="49043-829">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="49043-830">每個方法的 URL 路徑的建構方式如下：</span><span class="sxs-lookup"><span data-stu-id="49043-830">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="49043-831">一開始在控制器的 `Route` 屬性中使用範本字串：</span><span class="sxs-lookup"><span data-stu-id="49043-831">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="49043-832">以控制器的名稱取代 `[controller]`，也就是將控制器類別名稱減去 "Controller" 字尾。</span><span class="sxs-lookup"><span data-stu-id="49043-832">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="49043-833">在此範例中，控制器類別名稱是 **Todo** Controller，因此容器名稱是 "todo"。</span><span class="sxs-lookup"><span data-stu-id="49043-833">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="49043-834">ASP.NET Core [路由](xref:mvc/controllers/routing)不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="49043-834">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="49043-835">如果 `[HttpGet]` 屬性具有路由範本 (例如 `[HttpGet("products")]`)，請將其附加到路徑。</span><span class="sxs-lookup"><span data-stu-id="49043-835">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="49043-836">此範例不使用範本。</span><span class="sxs-lookup"><span data-stu-id="49043-836">This sample doesn't use a template.</span></span> <span data-ttu-id="49043-837">如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="49043-837">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="49043-838">在下列 `GetTodoItem` 方法中，`"{id}"` 是待辦事項唯一識別碼的預留位置變數。</span><span class="sxs-lookup"><span data-stu-id="49043-838">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="49043-839">在叫用 `GetTodoItem` 時，會將 URL 中的 `"{id}"` 值提供給方法的 `id` 參數。</span><span class="sxs-lookup"><span data-stu-id="49043-839">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="49043-840">傳回值2。1</span><span class="sxs-lookup"><span data-stu-id="49043-840">Return values 2.1</span></span>

<span data-ttu-id="49043-841">和方法的傳回型 `GetTodoItems` 別 `GetTodoItem` 為 [ActionResult \<T> 類型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="49043-841">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="49043-842">ASP.NET Core 會自動將物件序列化為 [JSON](https://www.json.org/)，並將 JSON 寫入至回應訊息的本文。</span><span class="sxs-lookup"><span data-stu-id="49043-842">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="49043-843">此傳回型別的回應碼為 200，假設沒有任何未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="49043-843">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="49043-844">未處理的例外狀況會轉譯成 5xx 錯誤。</span><span class="sxs-lookup"><span data-stu-id="49043-844">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="49043-845">`ActionResult` 傳回型別可代表各種 HTTP 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="49043-845">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="49043-846">例如，`GetTodoItem` 可傳回兩個不同的狀態值：</span><span class="sxs-lookup"><span data-stu-id="49043-846">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="49043-847">如果沒有任何專案符合要求的識別碼，方法會傳回 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 錯誤碼。</span><span class="sxs-lookup"><span data-stu-id="49043-847">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="49043-848">否則，方法會傳回 200 與 JSON 回應本文。</span><span class="sxs-lookup"><span data-stu-id="49043-848">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="49043-849">傳回 `item` 會導致 HTTP 200 回應。</span><span class="sxs-lookup"><span data-stu-id="49043-849">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="49043-850">測試 GetTodoItems 方法2。1</span><span class="sxs-lookup"><span data-stu-id="49043-850">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="49043-851">本教學課程使用 Postman 來測試 Web API。</span><span class="sxs-lookup"><span data-stu-id="49043-851">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="49043-852">安裝 [Postman](https://www.getpostman.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="49043-852">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="49043-853">啟動 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="49043-853">Start the web app.</span></span>
* <span data-ttu-id="49043-854">啟動 Postman。</span><span class="sxs-lookup"><span data-stu-id="49043-854">Start Postman.</span></span>
* <span data-ttu-id="49043-855">停用 **SSL 憑證驗證**。</span><span class="sxs-lookup"><span data-stu-id="49043-855">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49043-856">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49043-856">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49043-857">從 [檔案]**[設定]** >  ([一般] 索引標籤)，停用 [SSL 憑證驗證]。</span><span class="sxs-lookup"><span data-stu-id="49043-857">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="49043-858">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="49043-858">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="49043-859">從 **Postman**  >  **喜好** 設定 (**一般**] 索引標籤) ，停用 **SSL 憑證驗證**。</span><span class="sxs-lookup"><span data-stu-id="49043-859">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="49043-860">或者，選取扳手並選取 [設定]，然後停用 [SSL 憑證驗證]。</span><span class="sxs-lookup"><span data-stu-id="49043-860">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="49043-861">在測試控制器之後，請重新啟用 [SSL certificate verification] \(SSL 憑證驗證\)。</span><span class="sxs-lookup"><span data-stu-id="49043-861">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="49043-862">建立新的要求。</span><span class="sxs-lookup"><span data-stu-id="49043-862">Create a new request.</span></span>
  * <span data-ttu-id="49043-863">將 HTTP 方法設定為 **GET**。</span><span class="sxs-lookup"><span data-stu-id="49043-863">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="49043-864">將要求 URI 設定為 `https://localhost:<port>/api/todo` 。</span><span class="sxs-lookup"><span data-stu-id="49043-864">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="49043-865">例如： `https://localhost:5001/api/todo` 。</span><span class="sxs-lookup"><span data-stu-id="49043-865">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="49043-866">在 Postman 中，設定 [Two pane view] \(雙窗格檢視\)。</span><span class="sxs-lookup"><span data-stu-id="49043-866">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="49043-867">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="49043-867">Select **Send**.</span></span>

![Postman 與 GET 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="49043-869">新增 Create 方法2。1</span><span class="sxs-lookup"><span data-stu-id="49043-869">Add a Create method 2.1</span></span>

<span data-ttu-id="49043-870">在 *Controllers/TodoController.cs* 內部新增下列 `PostTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="49043-870">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="49043-871">上述程式碼是 HTTP POST 方法，如屬性所指示 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="49043-871">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="49043-872">該方法會從 HTTP 要求本文取得待辦事項的值。</span><span class="sxs-lookup"><span data-stu-id="49043-872">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="49043-873">`CreatedAtAction` 方法：</span><span class="sxs-lookup"><span data-stu-id="49043-873">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="49043-874">成功時會傳回 HTTP 201 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="49043-874">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="49043-875">對於可在伺服器上建立新資源的 HTTP POST 方法，其標準回應是 HTTP 201。</span><span class="sxs-lookup"><span data-stu-id="49043-875">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="49043-876">將 `Location` 標頭加到回應中。</span><span class="sxs-lookup"><span data-stu-id="49043-876">Adds a `Location` header to the response.</span></span> <span data-ttu-id="49043-877">`Location` 標頭指定新建立之待辦事項的 URI。</span><span class="sxs-lookup"><span data-stu-id="49043-877">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="49043-878">如需詳細資訊，請參閱 [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (已建立 10.2.2 201)。</span><span class="sxs-lookup"><span data-stu-id="49043-878">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="49043-879">參考 `GetTodoItem` 動作以建立 `Location` 標頭的 URI。</span><span class="sxs-lookup"><span data-stu-id="49043-879">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="49043-880">C# `nameof` 關鍵字是用來避免在 `CreatedAtAction` 呼叫中以硬式編碼方式寫入動作名稱。</span><span class="sxs-lookup"><span data-stu-id="49043-880">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="49043-881">測試 PostTodoItem 方法2。1</span><span class="sxs-lookup"><span data-stu-id="49043-881">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="49043-882">建置專案。</span><span class="sxs-lookup"><span data-stu-id="49043-882">Build the project.</span></span>
* <span data-ttu-id="49043-883">在 Postman 中，將 HTTP 方法設定為 `POST`。</span><span class="sxs-lookup"><span data-stu-id="49043-883">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="49043-884">將 URI 設定為 `https://localhost:<port>/api/Todo` 。</span><span class="sxs-lookup"><span data-stu-id="49043-884">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="49043-885">例如： `https://localhost:5001/api/Todo` 。</span><span class="sxs-lookup"><span data-stu-id="49043-885">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="49043-886">選取 [Body] \(本文\) 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="49043-886">Select the **Body** tab.</span></span>
* <span data-ttu-id="49043-887">選取 [原始] 選項按鈕。</span><span class="sxs-lookup"><span data-stu-id="49043-887">Select the **raw** radio button.</span></span>
* <span data-ttu-id="49043-888">將類型設定為 **JSON (application/json)**。</span><span class="sxs-lookup"><span data-stu-id="49043-888">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="49043-889">在要求本文中，針對待辦項目輸入 JSON：</span><span class="sxs-lookup"><span data-stu-id="49043-889">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="49043-890">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="49043-890">Select **Send**.</span></span>

  ![Postman 與建立要求](first-web-api/_static/create.png)

  <span data-ttu-id="49043-892">如果您收到 405「不允許的方法」錯誤，可能是由於新增 `PostTodoItem` 方法之後未編譯專案所導致。</span><span class="sxs-lookup"><span data-stu-id="49043-892">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="49043-893">測試位置標頭 URI 2。1</span><span class="sxs-lookup"><span data-stu-id="49043-893">Test the location header URI 2.1</span></span>

* <span data-ttu-id="49043-894">在 [回應] 窗格中選取 [標頭] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="49043-894">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="49043-895">複製 [位置] 標頭值：</span><span class="sxs-lookup"><span data-stu-id="49043-895">Copy the **Location** header value:</span></span>

  ![Postman 主控台的 [標頭] 索引標籤](first-web-api/_static/pmc2.png)

* <span data-ttu-id="49043-897">將方法設定為 GET。</span><span class="sxs-lookup"><span data-stu-id="49043-897">Set the method to GET.</span></span>
* <span data-ttu-id="49043-898">將 URI 設定為 `https://localhost:<port>/api/TodoItems/2` 。</span><span class="sxs-lookup"><span data-stu-id="49043-898">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="49043-899">例如： `https://localhost:5001/api/TodoItems/2` 。</span><span class="sxs-lookup"><span data-stu-id="49043-899">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="49043-900">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="49043-900">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="49043-901">新增 PutTodoItem 方法2。1</span><span class="sxs-lookup"><span data-stu-id="49043-901">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="49043-902">請新增下列 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="49043-902">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="49043-903">`PutTodoItem` 類似於 `PostTodoItem`，但是會使用 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="49043-903">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="49043-904">回應為 [204 (沒有內容) ](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="49043-904">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="49043-905">根據 HTTP 規格，PUT 要求需要用戶端傳送整個更新的實體，而不只是變更。</span><span class="sxs-lookup"><span data-stu-id="49043-905">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="49043-906">若要支援部分更新，請使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="49043-906">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="49043-907">如果在呼叫 `PutTodoItem` 時發生錯誤，請呼叫 `GET` 以確保資料庫中有項目。</span><span class="sxs-lookup"><span data-stu-id="49043-907">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="49043-908">測試 PutTodoItem 方法2。1</span><span class="sxs-lookup"><span data-stu-id="49043-908">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="49043-909">這個範例會使用必須在每次啟動應用程式時初始化的記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="49043-909">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="49043-910">資料庫中必須有項目，您才能進行 PUT 呼叫。</span><span class="sxs-lookup"><span data-stu-id="49043-910">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="49043-911">在進行 PUT 呼叫之前，請先呼叫 GET 以確保資料庫中有專案。</span><span class="sxs-lookup"><span data-stu-id="49043-911">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="49043-912">更新識別碼為1的待辦事項，並將其名稱設定為「摘要魚」：</span><span class="sxs-lookup"><span data-stu-id="49043-912">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="49043-913">下圖顯示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="49043-913">The following image shows the Postman update:</span></span>

![顯示「204 (沒有內容) 回應」的 Postman 主控台](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="49043-915">新增 DeleteTodoItem 方法2。1</span><span class="sxs-lookup"><span data-stu-id="49043-915">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="49043-916">請新增下列 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="49043-916">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="49043-917">`DeleteTodoItem` 回應是 [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) \(204 (沒有內容)\)。</span><span class="sxs-lookup"><span data-stu-id="49043-917">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="49043-918">測試 DeleteTodoItem 方法2。1</span><span class="sxs-lookup"><span data-stu-id="49043-918">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="49043-919">使用 Postman 刪除待辦事項：</span><span class="sxs-lookup"><span data-stu-id="49043-919">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="49043-920">將方法設定為 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="49043-920">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="49043-921">設定要刪除之物件的 URI (例如 `https://localhost:5001/api/todo/1`) 。</span><span class="sxs-lookup"><span data-stu-id="49043-921">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="49043-922">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="49043-922">Select **Send**.</span></span>

<span data-ttu-id="49043-923">範例應用程式可讓您刪除所有項目。</span><span class="sxs-lookup"><span data-stu-id="49043-923">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="49043-924">但刪除最後一個項目之後，模型類別建構函式會在下次呼叫 API 時建立新的項目。</span><span class="sxs-lookup"><span data-stu-id="49043-924">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="49043-925">使用 JavaScript 2.1 呼叫 web API</span><span class="sxs-lookup"><span data-stu-id="49043-925">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="49043-926">在此節中，將會新增 HTML 網頁，以使用 JavaScript 來呼叫 Web API。</span><span class="sxs-lookup"><span data-stu-id="49043-926">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="49043-927">jQuery 會起始要求。</span><span class="sxs-lookup"><span data-stu-id="49043-927">jQuery initiates the request.</span></span> <span data-ttu-id="49043-928">JavaScript 會使用來自 Web API 回應的詳細資料來更新頁面。</span><span class="sxs-lookup"><span data-stu-id="49043-928">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="49043-929">藉由使用下列反白顯示的程式碼更新 *Startup.cs*，來設定應用程式 [提供靜態檔案](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A)並 [啟用預設檔案對應](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A)：</span><span class="sxs-lookup"><span data-stu-id="49043-929">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="49043-930">在專案目錄中建立 *wwwroot* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="49043-930">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="49043-931">將名為 *index.html* 的 HTML 檔案新增至 *wwwroot* 目錄。</span><span class="sxs-lookup"><span data-stu-id="49043-931">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="49043-932">將其內容取代為下列標記：</span><span class="sxs-lookup"><span data-stu-id="49043-932">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="49043-933">將名為 *site.js* 的 JavaScript 檔案新增至 *wwwroot* 目錄。</span><span class="sxs-lookup"><span data-stu-id="49043-933">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="49043-934">以下列程式碼取代其內容：</span><span class="sxs-lookup"><span data-stu-id="49043-934">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="49043-935">若要在本機測試 HTML 網頁，可能需要變更 ASP.NET Core 專案的啟動設定：</span><span class="sxs-lookup"><span data-stu-id="49043-935">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="49043-936">開啟 *Properties\launchSettings.json*。</span><span class="sxs-lookup"><span data-stu-id="49043-936">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="49043-937">移除 `launchUrl` 屬性，以強制在專案的預設檔案 *index.html* 開啟應用程式 &mdash; 。</span><span class="sxs-lookup"><span data-stu-id="49043-937">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="49043-938">此範例會呼叫 Web API 的所有 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="49043-938">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="49043-939">以下是關於呼叫 API 的說明。</span><span class="sxs-lookup"><span data-stu-id="49043-939">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="49043-940">取得待辦事項的清單2。1</span><span class="sxs-lookup"><span data-stu-id="49043-940">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="49043-941">jQuery 會將 HTTP GET 要求傳送至 web API，該 API 會傳回代表待辦事項陣列的 JSON。</span><span class="sxs-lookup"><span data-stu-id="49043-941">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="49043-942">如果要求成功，則會叫用 `success` 回呼函式。</span><span class="sxs-lookup"><span data-stu-id="49043-942">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="49043-943">在回呼中，DOM 已使用待辦事項資訊進行更新。</span><span class="sxs-lookup"><span data-stu-id="49043-943">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="49043-944">新增待辦事項2。1</span><span class="sxs-lookup"><span data-stu-id="49043-944">Add a to-do item 2.1</span></span>

<span data-ttu-id="49043-945">jQuery 會使用要求主體中的待辦事項來傳送 HTTP POST 要求。</span><span class="sxs-lookup"><span data-stu-id="49043-945">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="49043-946">`accepts` 和 `contentType` 選項都設定為 `application/json`，以指定接收和傳送的媒體類型。</span><span class="sxs-lookup"><span data-stu-id="49043-946">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="49043-947">待辦事項會使用 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 轉換成 JSON。</span><span class="sxs-lookup"><span data-stu-id="49043-947">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="49043-948">當 API 傳回成功狀態碼時，會叫用 `getData` 函式來更新 HTML 資料表。</span><span class="sxs-lookup"><span data-stu-id="49043-948">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="49043-949">更新待辦事項2。1</span><span class="sxs-lookup"><span data-stu-id="49043-949">Update a to-do item 2.1</span></span>

<span data-ttu-id="49043-950">更新待辦事項類似於新增待辦事項。</span><span class="sxs-lookup"><span data-stu-id="49043-950">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="49043-951">`url` 會變更為新增項目的唯一識別碼，而 `type` 是 `PUT`。</span><span class="sxs-lookup"><span data-stu-id="49043-951">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="49043-952">刪除待辦事項2。1</span><span class="sxs-lookup"><span data-stu-id="49043-952">Delete a to-do item 2.1</span></span>

<span data-ttu-id="49043-953">刪除待辦事項的達成方法是將 AJAX 呼叫的 `type` 設定為 `DELETE`，並在 URL 中指定項目的唯一識別碼。</span><span class="sxs-lookup"><span data-stu-id="49043-953">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="49043-954">將驗證支援新增至 web API 2。1</span><span class="sxs-lookup"><span data-stu-id="49043-954">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="49043-955">其他資源2。1</span><span class="sxs-lookup"><span data-stu-id="49043-955">Additional resources 2.1</span></span>

<span data-ttu-id="49043-956">[檢視或下載本教學課程的範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-web-api/samples)。</span><span class="sxs-lookup"><span data-stu-id="49043-956">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="49043-957">請參閱[如何下載](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="49043-957">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="49043-958">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="49043-958">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="49043-959">本教學課程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="49043-959">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
* [<span data-ttu-id="49043-960">Microsoft Learn：使用 ASP.NET Core 建立 web API</span><span class="sxs-lookup"><span data-stu-id="49043-960">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
