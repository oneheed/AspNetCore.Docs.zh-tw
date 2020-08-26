---
title: 教學課程：使用 ASP.NET Core 建立 web API
author: rick-anderson
description: 了解如何使用 ASP.NET Core 建置 Web API。
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2020
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
uid: tutorials/first-web-api
ms.openlocfilehash: 3d83141b8b638a369b08b0fadafccd96c0b48214
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876785"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="92879-103">教學課程：使用 ASP.NET Core 建立 web API</span><span class="sxs-lookup"><span data-stu-id="92879-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="92879-104">由 [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Kirk Larkin](https://twitter.com/serpent5)和 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="92879-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="92879-105">本教學課程將教導您使用 ASP.NET Core 建立 Web API 的基本概念。</span><span class="sxs-lookup"><span data-stu-id="92879-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="92879-106">在本教學課程中，您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="92879-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="92879-107">建立 Web API 專案。</span><span class="sxs-lookup"><span data-stu-id="92879-107">Create a web API project.</span></span>
> * <span data-ttu-id="92879-108">新增模型類別和資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="92879-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="92879-109">使用 CRUD 方法 Scaffold 控制器。</span><span class="sxs-lookup"><span data-stu-id="92879-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="92879-110">設定路由、URL 路徑和傳回值。</span><span class="sxs-lookup"><span data-stu-id="92879-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="92879-111">使用 Postman 呼叫 Web API。</span><span class="sxs-lookup"><span data-stu-id="92879-111">Call the web API with Postman.</span></span>

<span data-ttu-id="92879-112">結束時，您會有一個 Web API，可以管理儲存在資料庫中的「待辦事項」。</span><span class="sxs-lookup"><span data-stu-id="92879-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="92879-113">概觀</span><span class="sxs-lookup"><span data-stu-id="92879-113">Overview</span></span>

<span data-ttu-id="92879-114">本教學課程會建立以下 API：</span><span class="sxs-lookup"><span data-stu-id="92879-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="92879-115">API</span><span class="sxs-lookup"><span data-stu-id="92879-115">API</span></span> | <span data-ttu-id="92879-116">描述</span><span class="sxs-lookup"><span data-stu-id="92879-116">Description</span></span> | <span data-ttu-id="92879-117">Request body</span><span class="sxs-lookup"><span data-stu-id="92879-117">Request body</span></span> | <span data-ttu-id="92879-118">回應本文</span><span class="sxs-lookup"><span data-stu-id="92879-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="92879-119">取得所有待辦事項</span><span class="sxs-lookup"><span data-stu-id="92879-119">Get all to-do items</span></span> | <span data-ttu-id="92879-120">無</span><span class="sxs-lookup"><span data-stu-id="92879-120">None</span></span> | <span data-ttu-id="92879-121">待辦事項的陣列</span><span class="sxs-lookup"><span data-stu-id="92879-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="92879-122">依識別碼取得項目</span><span class="sxs-lookup"><span data-stu-id="92879-122">Get an item by ID</span></span> | <span data-ttu-id="92879-123">無</span><span class="sxs-lookup"><span data-stu-id="92879-123">None</span></span> | <span data-ttu-id="92879-124">待辦事項</span><span class="sxs-lookup"><span data-stu-id="92879-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="92879-125">新增記錄</span><span class="sxs-lookup"><span data-stu-id="92879-125">Add a new item</span></span> | <span data-ttu-id="92879-126">待辦事項</span><span class="sxs-lookup"><span data-stu-id="92879-126">To-do item</span></span> | <span data-ttu-id="92879-127">待辦事項</span><span class="sxs-lookup"><span data-stu-id="92879-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="92879-128">更新現有的項目 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="92879-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="92879-129">待辦事項</span><span class="sxs-lookup"><span data-stu-id="92879-129">To-do item</span></span> | <span data-ttu-id="92879-130">無</span><span class="sxs-lookup"><span data-stu-id="92879-130">None</span></span> |
|<span data-ttu-id="92879-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="92879-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="92879-132">刪除專案 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="92879-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="92879-133">無</span><span class="sxs-lookup"><span data-stu-id="92879-133">None</span></span> | <span data-ttu-id="92879-134">無</span><span class="sxs-lookup"><span data-stu-id="92879-134">None</span></span>|

<span data-ttu-id="92879-135">下圖顯示應用程式的設計。</span><span class="sxs-lookup"><span data-stu-id="92879-135">The following diagram shows the design of the app.</span></span>

![左側方塊代表用戶端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="92879-141">先決條件</span><span class="sxs-lookup"><span data-stu-id="92879-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92879-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92879-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="92879-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="92879-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="92879-144">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="92879-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="92879-145">建立 Web 專案</span><span class="sxs-lookup"><span data-stu-id="92879-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92879-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92879-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="92879-147">從 [檔案]\*\*\*\* 功能表選取 [新增]**[專案]** > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="92879-148">選取 **ASP.NET Core Web 應用程式**範本，然後按一下 [下一步]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="92879-149">將專案命名為 *TodoApi*，然後按一下 [建立]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="92879-150">在 [ **建立新的 ASP.NET Core Web 應用程式** ] 對話方塊中，確認已選取 [ **.net Core** ] 和 [ **ASP.NET Core 3.1** ]。</span><span class="sxs-lookup"><span data-stu-id="92879-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="92879-151">選取 **API** 範本，然後按一下 [建立]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-151">Select the **API** template and click **Create**.</span></span>

![VS 新增專案對話方塊](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="92879-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="92879-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="92879-154">開啟[整合式終端機](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="92879-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="92879-155">將目錄 (`cd`) 變更為包含專案資料夾的資料夾。</span><span class="sxs-lookup"><span data-stu-id="92879-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="92879-156">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="92879-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="92879-157">當出現對話方塊詢問您是否要將所需的資產新增至專案時，選取 [是]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="92879-158">上述命令：</span><span class="sxs-lookup"><span data-stu-id="92879-158">The preceding commands:</span></span>

  * <span data-ttu-id="92879-159">建立新的 Web API 專案，然後在 Visual Studio Code 中予以開啟。</span><span class="sxs-lookup"><span data-stu-id="92879-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="92879-160">新增下一節需要的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="92879-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="92879-161">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="92879-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="92879-162">選取 [檔案]\*\* [新增解決方案]\*\* > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-162">Select **File** > **New Solution**.</span></span>

  ![macOS 新增方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="92879-164">在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用程式**  >  **API**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="92879-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="92879-165">在8.6 版或更新版本中，選取 [ **Web] 和 [主控台**  >  **應用程式**  >  **API**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="92879-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 範本選取專案](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="92879-167">在 [ **設定新的 ASP.NET Core WEB API** ] 對話方塊中，選取最新的 .net Core 3.X **目標 Framework**。</span><span class="sxs-lookup"><span data-stu-id="92879-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="92879-168">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="92879-168">Select **Next**.</span></span>

* <span data-ttu-id="92879-169">針對 [專案名稱]\*\*\*\* 輸入 *TodoApi*，然後選取 [建立]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![設定對話方塊](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="92879-171">在專案資料夾中開啟命令終端機，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="92879-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="92879-172">測試 API</span><span class="sxs-lookup"><span data-stu-id="92879-172">Test the API</span></span>

<span data-ttu-id="92879-173">專案範本會建立 `WeatherForecast` API。</span><span class="sxs-lookup"><span data-stu-id="92879-173">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="92879-174">從瀏覽器呼叫 `Get` 方法來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="92879-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92879-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92879-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="92879-176">按 Ctrl+F5 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="92879-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="92879-177">Visual Studio 會啟動瀏覽器並巡覽至 `https://localhost:<port>/WeatherForecast`，其中 `<port>` 是隨機選擇的通訊埠編號。</span><span class="sxs-lookup"><span data-stu-id="92879-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="92879-178">如果出現對話方塊詢問您是否應該信任 IIS Express 憑證，請選取 [是]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="92879-179">在接著出現的 [安全性警告]\*\*\*\* 對話方塊中，選取 [是]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="92879-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="92879-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="92879-181">按 Ctrl+F5 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="92879-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="92879-182">在瀏覽器中，前往下列 URL：`https://localhost:5001/WeatherForecast`。</span><span class="sxs-lookup"><span data-stu-id="92879-182">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="92879-183">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="92879-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="92879-184">選取 [**執行**  >  **開始調試**程式] 以啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="92879-184">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="92879-185">Visual Studio for Mac 會啟動瀏覽器並巡覽至 `https://localhost:<port>`，其中 `<port>` 是隨機選擇的連接埠號碼。</span><span class="sxs-lookup"><span data-stu-id="92879-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="92879-186">傳回 HTTP 404 (找不到) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="92879-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="92879-187">將 `/WeatherForecast` 附加至 URL (將 URL 變更為 `https://localhost:<port>/WeatherForecast`)。</span><span class="sxs-lookup"><span data-stu-id="92879-187">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="92879-188">系統會傳回與下列類似的 JSON：</span><span class="sxs-lookup"><span data-stu-id="92879-188">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="92879-189">新增模型類別</span><span class="sxs-lookup"><span data-stu-id="92879-189">Add a model class</span></span>

<span data-ttu-id="92879-190">「模型」\*\* 是代表應用程式所管理資料的一組類別。</span><span class="sxs-lookup"><span data-stu-id="92879-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="92879-191">此應用程式的模型是單一 `TodoItem` 類別。</span><span class="sxs-lookup"><span data-stu-id="92879-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92879-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92879-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="92879-193">在 **方案總管**中，以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="92879-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="92879-194">選取 **[**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="92879-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="92879-195">將資料夾命名為 *Models*。</span><span class="sxs-lookup"><span data-stu-id="92879-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="92879-196">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [新增]\*\*\*\* > [類別]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="92879-197">將類別命名為 *TodoItem*，然後選取 [新增]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="92879-198">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="92879-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="92879-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="92879-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="92879-200">新增名為 *Models* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="92879-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="92879-201">將 `TodoItem` 類別新增至具有下列程式碼的 *Models* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="92879-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="92879-202">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="92879-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="92879-203">以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="92879-203">Right-click the project.</span></span> <span data-ttu-id="92879-204">選取 **[**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="92879-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="92879-205">將資料夾命名為 *Models*。</span><span class="sxs-lookup"><span data-stu-id="92879-205">Name the folder *Models*.</span></span>

  ![新增資料夾](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="92879-207">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [新增]**[新增檔案]** > **[一般]** > **[空類別]** > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="92879-208">將類別命名為 *TodoItem*，然後按一下 [新增]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="92879-209">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="92879-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="92879-210">`Id` 屬性的功能相當於關聯式資料庫中的唯一索引鍵。</span><span class="sxs-lookup"><span data-stu-id="92879-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="92879-211">模型類別可位於專案中的任何位置，但依照慣例會使用 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="92879-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="92879-212">新增資料庫內容</span><span class="sxs-lookup"><span data-stu-id="92879-212">Add a database context</span></span>

<span data-ttu-id="92879-213">「資料庫內容」\*\* 是為資料模型協調 Entity Framework 功能的主要類別。</span><span class="sxs-lookup"><span data-stu-id="92879-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="92879-214">此類別是透過衍生自 `Microsoft.EntityFrameworkCore.DbContext` 類別來建立。</span><span class="sxs-lookup"><span data-stu-id="92879-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92879-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92879-215">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="92879-216">新增 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="92879-216">Add NuGet packages</span></span>

* <span data-ttu-id="92879-217">在 [工具]\*\*\*\* 功能表上，選取 [NuGet 套件管理員] > [管理解決方案的 NuGet 套件]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-217">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="92879-218">選取 [瀏覽]\*\*\*\* 索引標籤，然後在搜尋方塊中輸入 **Microsoft.EntityFrameworkCore.SqlServer**。</span><span class="sxs-lookup"><span data-stu-id="92879-218">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="92879-219">選取左窗格中的 [ **microsoft.entityframeworkcore** ]。</span><span class="sxs-lookup"><span data-stu-id="92879-219">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="92879-220">選取右窗格中的 [專案]\*\*\*\* 核取方塊，然後選取 [安裝]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-220">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="92879-221">使用上述指示來新增 **Microsoft.entityframeworkcore InMemory** NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="92879-221">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![NuGet 套件管理員](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="92879-223">新增 TodoCoNtext 資料庫內容</span><span class="sxs-lookup"><span data-stu-id="92879-223">Add the TodoContext database context</span></span>

* <span data-ttu-id="92879-224">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [新增]\*\*\*\* > [類別]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-224">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="92879-225">將類別命名為 *TodoContext*，然後按一下 [新增]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-225">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="92879-226">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="92879-226">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="92879-227">將 `TodoContext` 類別新增至 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="92879-227">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="92879-228">輸入下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="92879-228">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="92879-229">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="92879-229">Register the database context</span></span>

<span data-ttu-id="92879-230">在 ASP.NET Core 中，資料庫內容等服務必須向[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器註冊。</span><span class="sxs-lookup"><span data-stu-id="92879-230">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="92879-231">此容器會將服務提供給控制器。</span><span class="sxs-lookup"><span data-stu-id="92879-231">The container provides the service to controllers.</span></span>

<span data-ttu-id="92879-232">使用下列醒目提示的程式碼更新 *Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="92879-232">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="92879-233">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="92879-233">The preceding code:</span></span>

* <span data-ttu-id="92879-234">移除未使用的 `using` 宣告。</span><span class="sxs-lookup"><span data-stu-id="92879-234">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="92879-235">將資料庫內容新增至 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="92879-235">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="92879-236">指定資料庫內容將會使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="92879-236">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="92879-237">Scaffold 控制器</span><span class="sxs-lookup"><span data-stu-id="92879-237">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92879-238">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92879-238">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="92879-239">以滑鼠右鍵按一下 *Controllers* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="92879-239">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="92879-240">選取 [新增]\*\* [新增 Scaffold 項目]\*\* > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-240">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="92879-241">選取 [使用 Entity Framework 執行動作的 API 控制器]\*\*\*\*，然後選取 [新增]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-241">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="92879-242">在 [使用 Entity Framework 執行動作的 API 控制器]\*\*\*\* 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="92879-242">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="92879-243">選取**模型類別**中的 [ \*\*TodoItem (TodoApi]) \*\* 。</span><span class="sxs-lookup"><span data-stu-id="92879-243">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="92879-244">選取**資料內容類別**中 **) 的 TodoCoNtext (TodoApi** 。</span><span class="sxs-lookup"><span data-stu-id="92879-244">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="92879-245">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="92879-245">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="92879-246">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="92879-246">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="92879-247">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="92879-247">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="92879-248">上述命令：</span><span class="sxs-lookup"><span data-stu-id="92879-248">The preceding commands:</span></span>

* <span data-ttu-id="92879-249">新增 Scaffolding 所需的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="92879-249">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="92879-250">安裝 Scaffolding 引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="92879-250">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="92879-251">Scaffold `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="92879-251">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="92879-252">產生的程式碼：</span><span class="sxs-lookup"><span data-stu-id="92879-252">The generated code:</span></span>

* <span data-ttu-id="92879-253">標記具有屬性的類別 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="92879-253">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="92879-254">這個屬性表示控制器會回應 Web API 要求。</span><span class="sxs-lookup"><span data-stu-id="92879-254">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="92879-255">如需屬性所啟用之特定行為的相關資訊，請參閱 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="92879-255">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="92879-256">使用 DI 將資料庫內容 (`TodoContext`) 插入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="92879-256">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="92879-257">控制器中的每一個 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法都會使用資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="92879-257">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="92879-258">的 ASP.NET Core 範本：</span><span class="sxs-lookup"><span data-stu-id="92879-258">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="92879-259">具有 views 的控制器包含 `[action]` 在路由範本中。</span><span class="sxs-lookup"><span data-stu-id="92879-259">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="92879-260">API 控制器不包含 `[action]` 在路由範本中。</span><span class="sxs-lookup"><span data-stu-id="92879-260">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="92879-261">當 `[action]` 權杖不在路由範本中時，會從路由中排除 [動作](xref:mvc/controllers/routing#action) 名稱。</span><span class="sxs-lookup"><span data-stu-id="92879-261">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="92879-262">也就是，不會在相符的路由中使用動作的相關聯方法名稱。</span><span class="sxs-lookup"><span data-stu-id="92879-262">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="92879-263">檢查 PostTodoItem 建立方法</span><span class="sxs-lookup"><span data-stu-id="92879-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="92879-264">取代 `PostTodoItem` 中的 return 陳述式，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 運算子：</span><span class="sxs-lookup"><span data-stu-id="92879-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="92879-265">上述程式碼是 HTTP POST 方法，如屬性所指示 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="92879-265">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="92879-266">該方法會從 HTTP 要求本文取得待辦事項的值。</span><span class="sxs-lookup"><span data-stu-id="92879-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="92879-267">如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="92879-267">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="92879-268"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="92879-268">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="92879-269">成功時會傳回 HTTP 201 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="92879-269">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="92879-270">對於可在伺服器上建立新資源的 HTTP POST 方法，其標準回應是 HTTP 201。</span><span class="sxs-lookup"><span data-stu-id="92879-270">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="92879-271">將 [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 標頭新增至回應。</span><span class="sxs-lookup"><span data-stu-id="92879-271">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="92879-272">`Location`標頭會指定新建立之待辦事項的[URI](https://developer.mozilla.org/docs/Glossary/URI) 。</span><span class="sxs-lookup"><span data-stu-id="92879-272">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="92879-273">如需詳細資訊，請參閱 [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (已建立 10.2.2 201)。</span><span class="sxs-lookup"><span data-stu-id="92879-273">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="92879-274">參考 `GetTodoItem` 動作以建立 `Location` 標頭的 URI。</span><span class="sxs-lookup"><span data-stu-id="92879-274">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="92879-275">C# `nameof` 關鍵字是用來避免在 `CreatedAtAction` 呼叫中以硬式編碼方式寫入動作名稱。</span><span class="sxs-lookup"><span data-stu-id="92879-275">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="92879-276">安裝 Postman</span><span class="sxs-lookup"><span data-stu-id="92879-276">Install Postman</span></span>

<span data-ttu-id="92879-277">本教學課程使用 Postman 來測試 Web API。</span><span class="sxs-lookup"><span data-stu-id="92879-277">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="92879-278">安裝 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="92879-278">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="92879-279">啟動 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="92879-279">Start the web app.</span></span>
* <span data-ttu-id="92879-280">啟動 Postman。</span><span class="sxs-lookup"><span data-stu-id="92879-280">Start Postman.</span></span>
* <span data-ttu-id="92879-281">停用 [SSL certificate verification] \(SSL 憑證驗證\)\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="92879-281">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="92879-282">從 [檔案]\*\* [設定]\*\* > \*\*\*\* ([一般]\*\*\*\* 索引標籤)，停用 [SSL 憑證驗證]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-282">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="92879-283">在測試控制器之後，請重新啟用 [SSL certificate verification] \(SSL 憑證驗證\)。</span><span class="sxs-lookup"><span data-stu-id="92879-283">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="92879-284">使用 Postman 測試 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="92879-284">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="92879-285">建立新的要求。</span><span class="sxs-lookup"><span data-stu-id="92879-285">Create a new request.</span></span>
* <span data-ttu-id="92879-286">將 HTTP 方法設為 `POST`。</span><span class="sxs-lookup"><span data-stu-id="92879-286">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="92879-287">將 URI 設定為 `https://localhost:<port>/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="92879-287">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="92879-288">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="92879-288">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="92879-289">選取 [Body]\*\*\*\* \(本文\) 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="92879-289">Select the **Body** tab.</span></span>
* <span data-ttu-id="92879-290">選取 [原始]\*\*\*\* 選項按鈕。</span><span class="sxs-lookup"><span data-stu-id="92879-290">Select the **raw** radio button.</span></span>
* <span data-ttu-id="92879-291">將類型設定為 **JSON (application/json)**。</span><span class="sxs-lookup"><span data-stu-id="92879-291">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="92879-292">在要求本文中，針對待辦項目輸入 JSON：</span><span class="sxs-lookup"><span data-stu-id="92879-292">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="92879-293">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="92879-293">Select **Send**.</span></span>

  ![Postman 與建立要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="92879-295">使用 Postman 測試 location 標頭 URI</span><span class="sxs-lookup"><span data-stu-id="92879-295">Test the location header URI with Postman</span></span>

* <span data-ttu-id="92879-296">在 [回應]\*\*\*\* 窗格中選取 [標頭]\*\*\*\* 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="92879-296">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="92879-297">複製 [位置]\*\*\*\* 標頭值：</span><span class="sxs-lookup"><span data-stu-id="92879-297">Copy the **Location** header value:</span></span>

  ![Postman 主控台的 [標頭] 索引標籤](first-web-api/_static/3/create.png)

* <span data-ttu-id="92879-299">將 HTTP 方法設為 `GET`。</span><span class="sxs-lookup"><span data-stu-id="92879-299">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="92879-300">將 URI 設定為 `https://localhost:<port>/api/TodoItems/1` 。</span><span class="sxs-lookup"><span data-stu-id="92879-300">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="92879-301">例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="92879-301">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="92879-302">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="92879-302">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="92879-303">檢查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="92879-303">Examine the GET methods</span></span>

<span data-ttu-id="92879-304">這些方法會實作兩個 GET 端點：</span><span class="sxs-lookup"><span data-stu-id="92879-304">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="92879-305">從瀏覽器或 Postman 呼叫這兩個端點來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="92879-305">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="92879-306">例如：</span><span class="sxs-lookup"><span data-stu-id="92879-306">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="92879-307">`GetTodoItems` 的呼叫會產生類似下列的回應：</span><span class="sxs-lookup"><span data-stu-id="92879-307">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="92879-308">使用 Postman 測試 Get</span><span class="sxs-lookup"><span data-stu-id="92879-308">Test Get with Postman</span></span>

* <span data-ttu-id="92879-309">建立新的要求。</span><span class="sxs-lookup"><span data-stu-id="92879-309">Create a new request.</span></span>
* <span data-ttu-id="92879-310">將 HTTP 方法設定為 **GET**。</span><span class="sxs-lookup"><span data-stu-id="92879-310">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="92879-311">將要求 URI 設定為 `https://localhost:<port>/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="92879-311">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="92879-312">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="92879-312">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="92879-313">在 Postman 中，設定 [Two pane view] \(雙窗格檢視\)\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-313">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="92879-314">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="92879-314">Select **Send**.</span></span>

<span data-ttu-id="92879-315">這個應用程式會使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="92879-315">This app uses an in-memory database.</span></span> <span data-ttu-id="92879-316">如果應用程式在停止後再啟動，上述 GET 要求將不會傳回任何資料。</span><span class="sxs-lookup"><span data-stu-id="92879-316">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="92879-317">如果沒有傳回任何資料，請將資料 [POST](#post) 到應用程式。</span><span class="sxs-lookup"><span data-stu-id="92879-317">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="92879-318">傳送和 URL 路徑</span><span class="sxs-lookup"><span data-stu-id="92879-318">Routing and URL paths</span></span>

<span data-ttu-id="92879-319">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)屬性代表回應 HTTP GET 要求的方法。</span><span class="sxs-lookup"><span data-stu-id="92879-319">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="92879-320">每個方法的 URL 路徑的建構方式如下：</span><span class="sxs-lookup"><span data-stu-id="92879-320">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="92879-321">一開始在控制器的 `Route` 屬性中使用範本字串：</span><span class="sxs-lookup"><span data-stu-id="92879-321">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="92879-322">以控制器的名稱取代 `[controller]`，也就是將控制器類別名稱減去 "Controller" 字尾。</span><span class="sxs-lookup"><span data-stu-id="92879-322">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="92879-323">在此範例中，控制器類別名稱是 **TodoItems**Controller，因此控制器名稱是 "TodoItems"。</span><span class="sxs-lookup"><span data-stu-id="92879-323">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="92879-324">ASP.NET Core [路由](xref:mvc/controllers/routing)不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="92879-324">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="92879-325">如果 `[HttpGet]` 屬性具有路由範本 (例如 `[HttpGet("products")]`)，請將其附加到路徑。</span><span class="sxs-lookup"><span data-stu-id="92879-325">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="92879-326">此範例不使用範本。</span><span class="sxs-lookup"><span data-stu-id="92879-326">This sample doesn't use a template.</span></span> <span data-ttu-id="92879-327">如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="92879-327">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="92879-328">在下列 `GetTodoItem` 方法中，`"{id}"` 是待辦事項唯一識別碼的預留位置變數。</span><span class="sxs-lookup"><span data-stu-id="92879-328">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="92879-329">當叫 `GetTodoItem` 用時，會將 `"{id}"` URL 中的值提供給方法的 `id` 參數。</span><span class="sxs-lookup"><span data-stu-id="92879-329">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="92879-330">傳回值</span><span class="sxs-lookup"><span data-stu-id="92879-330">Return values</span></span>

<span data-ttu-id="92879-331">和方法的傳回型 `GetTodoItems` 別 `GetTodoItem` 為 [ActionResult \<T> 類型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="92879-331">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="92879-332">ASP.NET Core 會自動將物件序列化為 [JSON](https://www.json.org/)，並將 JSON 寫入至回應訊息的本文。</span><span class="sxs-lookup"><span data-stu-id="92879-332">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="92879-333">此傳回型別的回應碼為 200，假設沒有任何未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="92879-333">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="92879-334">未處理的例外狀況會轉譯成 5xx 錯誤。</span><span class="sxs-lookup"><span data-stu-id="92879-334">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="92879-335">`ActionResult` 傳回型別可代表各種 HTTP 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="92879-335">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="92879-336">例如，`GetTodoItem` 可傳回兩個不同的狀態值：</span><span class="sxs-lookup"><span data-stu-id="92879-336">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="92879-337">如果沒有任何專案符合要求的識別碼，方法會傳回 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 錯誤碼。</span><span class="sxs-lookup"><span data-stu-id="92879-337">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="92879-338">否則，方法會傳回 200 與 JSON 回應本文。</span><span class="sxs-lookup"><span data-stu-id="92879-338">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="92879-339">傳回 `item` 會導致 HTTP 200 回應。</span><span class="sxs-lookup"><span data-stu-id="92879-339">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="92879-340">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="92879-340">The PutTodoItem method</span></span>

<span data-ttu-id="92879-341">檢查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="92879-341">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="92879-342">`PutTodoItem` 類似於 `PostTodoItem`，但是會使用 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="92879-342">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="92879-343">回應為 [204 (沒有內容) ](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="92879-343">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="92879-344">根據 HTTP 規格，PUT 要求需要用戶端傳送整個更新的實體，而不只是變更。</span><span class="sxs-lookup"><span data-stu-id="92879-344">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="92879-345">若要支援部分更新，請使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="92879-345">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="92879-346">如果在呼叫 `PutTodoItem` 時發生錯誤，請呼叫 `GET` 以確保資料庫中有項目。</span><span class="sxs-lookup"><span data-stu-id="92879-346">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="92879-347">測試 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="92879-347">Test the PutTodoItem method</span></span>

<span data-ttu-id="92879-348">這個範例會使用必須在每次啟動應用程式時初始化的記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="92879-348">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="92879-349">資料庫中必須有項目，您才能進行 PUT 呼叫。</span><span class="sxs-lookup"><span data-stu-id="92879-349">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="92879-350">在發出 PUT 呼叫之前，請先呼叫 GET 以確保資料庫中有項目。</span><span class="sxs-lookup"><span data-stu-id="92879-350">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="92879-351">更新識別碼為 1 的待辦事項，並將其名稱設定為 "feed fish"：</span><span class="sxs-lookup"><span data-stu-id="92879-351">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="92879-352">下圖顯示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="92879-352">The following image shows the Postman update:</span></span>

![顯示「204 (沒有內容) 回應」的 Postman 主控台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="92879-354">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="92879-354">The DeleteTodoItem method</span></span>

<span data-ttu-id="92879-355">檢查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="92879-355">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="92879-356">測試 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="92879-356">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="92879-357">使用 Postman 刪除待辦事項：</span><span class="sxs-lookup"><span data-stu-id="92879-357">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="92879-358">將方法設定為 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="92879-358">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="92879-359">設定要刪除之物件的 URI (例如 `https://localhost:5001/api/TodoItems/1`) 。</span><span class="sxs-lookup"><span data-stu-id="92879-359">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="92879-360">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="92879-360">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="92879-361">防止過度張貼</span><span class="sxs-lookup"><span data-stu-id="92879-361">Prevent over-posting</span></span>

<span data-ttu-id="92879-362">範例應用程式目前會公開整個 `TodoItem` 物件。</span><span class="sxs-lookup"><span data-stu-id="92879-362">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="92879-363">生產環境應用程式通常會限制使用模型子集來輸入和傳回的資料。</span><span class="sxs-lookup"><span data-stu-id="92879-363">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="92879-364">這項功能有許多原因，而且安全性是主要的原因。</span><span class="sxs-lookup"><span data-stu-id="92879-364">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="92879-365">模型的子集通常稱為資料傳輸物件 (DTO) 、輸入模型或視圖模型。</span><span class="sxs-lookup"><span data-stu-id="92879-365">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="92879-366">此文章中使用**DTO** 。</span><span class="sxs-lookup"><span data-stu-id="92879-366">**DTO** is used in this article.</span></span>

<span data-ttu-id="92879-367">DTO 可以用來：</span><span class="sxs-lookup"><span data-stu-id="92879-367">A DTO may be used to:</span></span>

* <span data-ttu-id="92879-368">防止過度張貼。</span><span class="sxs-lookup"><span data-stu-id="92879-368">Prevent over-posting.</span></span>
* <span data-ttu-id="92879-369">隱藏用戶端不應該看到的屬性。</span><span class="sxs-lookup"><span data-stu-id="92879-369">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="92879-370">略過某些屬性，以減少承載大小。</span><span class="sxs-lookup"><span data-stu-id="92879-370">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="92879-371">壓平合併包含嵌套物件的物件圖形。</span><span class="sxs-lookup"><span data-stu-id="92879-371">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="92879-372">簡維物件圖形對於用戶端來說可能更方便。</span><span class="sxs-lookup"><span data-stu-id="92879-372">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="92879-373">若要示範 DTO 方法，請更新 `TodoItem` 類別以包含秘密欄位：</span><span class="sxs-lookup"><span data-stu-id="92879-373">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="92879-374">此應用程式必須隱藏秘密欄位，但系統管理應用程式可以選擇將它公開。</span><span class="sxs-lookup"><span data-stu-id="92879-374">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="92879-375">確認您可以張貼並取得秘密欄位。</span><span class="sxs-lookup"><span data-stu-id="92879-375">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="92879-376">建立 DTO 模型：</span><span class="sxs-lookup"><span data-stu-id="92879-376">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="92879-377">更新 `TodoItemsController` 以使用 `TodoItemDTO` ：</span><span class="sxs-lookup"><span data-stu-id="92879-377">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="92879-378">確認您無法張貼或取得秘密欄位。</span><span class="sxs-lookup"><span data-stu-id="92879-378">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="92879-379">使用 JavaScript 呼叫 Web API</span><span class="sxs-lookup"><span data-stu-id="92879-379">Call the web API with JavaScript</span></span>

<span data-ttu-id="92879-380">請參閱 [教學課程：使用 JavaScript 呼叫 ASP.NET Core WEB API](xref:tutorials/web-api-javascript)。</span><span class="sxs-lookup"><span data-stu-id="92879-380">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="92879-381">在本教學課程中，您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="92879-381">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="92879-382">建立 Web API 專案。</span><span class="sxs-lookup"><span data-stu-id="92879-382">Create a web API project.</span></span>
> * <span data-ttu-id="92879-383">新增模型類別和資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="92879-383">Add a model class and a database context.</span></span>
> * <span data-ttu-id="92879-384">新增控制器。</span><span class="sxs-lookup"><span data-stu-id="92879-384">Add a controller.</span></span>
> * <span data-ttu-id="92879-385">新增 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="92879-385">Add CRUD methods.</span></span>
> * <span data-ttu-id="92879-386">設定路由和 URL 路徑。</span><span class="sxs-lookup"><span data-stu-id="92879-386">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="92879-387">指定傳回值。</span><span class="sxs-lookup"><span data-stu-id="92879-387">Specify return values.</span></span>
> * <span data-ttu-id="92879-388">使用 Postman 呼叫 Web API。</span><span class="sxs-lookup"><span data-stu-id="92879-388">Call the web API with Postman.</span></span>
> * <span data-ttu-id="92879-389">使用 JavaScript 呼叫 Web API。</span><span class="sxs-lookup"><span data-stu-id="92879-389">Call the web API with JavaScript.</span></span>

<span data-ttu-id="92879-390">結束時，您將會有一個可管理關聯式資料庫中所儲存「待辦事項」的 Web API。</span><span class="sxs-lookup"><span data-stu-id="92879-390">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="92879-391">概觀</span><span class="sxs-lookup"><span data-stu-id="92879-391">Overview</span></span>

<span data-ttu-id="92879-392">本教學課程會建立以下 API：</span><span class="sxs-lookup"><span data-stu-id="92879-392">This tutorial creates the following API:</span></span>

|<span data-ttu-id="92879-393">API</span><span class="sxs-lookup"><span data-stu-id="92879-393">API</span></span> | <span data-ttu-id="92879-394">描述</span><span class="sxs-lookup"><span data-stu-id="92879-394">Description</span></span> | <span data-ttu-id="92879-395">Request body</span><span class="sxs-lookup"><span data-stu-id="92879-395">Request body</span></span> | <span data-ttu-id="92879-396">回應本文</span><span class="sxs-lookup"><span data-stu-id="92879-396">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="92879-397">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="92879-397">GET /api/TodoItems</span></span> | <span data-ttu-id="92879-398">取得所有待辦事項</span><span class="sxs-lookup"><span data-stu-id="92879-398">Get all to-do items</span></span> | <span data-ttu-id="92879-399">無</span><span class="sxs-lookup"><span data-stu-id="92879-399">None</span></span> | <span data-ttu-id="92879-400">待辦事項的陣列</span><span class="sxs-lookup"><span data-stu-id="92879-400">Array of to-do items</span></span>|
|<span data-ttu-id="92879-401">GET /api/TodoItems/{識別碼}</span><span class="sxs-lookup"><span data-stu-id="92879-401">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="92879-402">依識別碼取得項目</span><span class="sxs-lookup"><span data-stu-id="92879-402">Get an item by ID</span></span> | <span data-ttu-id="92879-403">無</span><span class="sxs-lookup"><span data-stu-id="92879-403">None</span></span> | <span data-ttu-id="92879-404">待辦事項</span><span class="sxs-lookup"><span data-stu-id="92879-404">To-do item</span></span>|
|<span data-ttu-id="92879-405">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="92879-405">POST /api/TodoItems</span></span> | <span data-ttu-id="92879-406">新增記錄</span><span class="sxs-lookup"><span data-stu-id="92879-406">Add a new item</span></span> | <span data-ttu-id="92879-407">待辦事項</span><span class="sxs-lookup"><span data-stu-id="92879-407">To-do item</span></span> | <span data-ttu-id="92879-408">待辦事項</span><span class="sxs-lookup"><span data-stu-id="92879-408">To-do item</span></span> |
|<span data-ttu-id="92879-409">PUT /api/TodoItems/{識別碼}</span><span class="sxs-lookup"><span data-stu-id="92879-409">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="92879-410">更新現有的項目 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="92879-410">Update an existing item &nbsp;</span></span> | <span data-ttu-id="92879-411">待辦事項</span><span class="sxs-lookup"><span data-stu-id="92879-411">To-do item</span></span> | <span data-ttu-id="92879-412">無</span><span class="sxs-lookup"><span data-stu-id="92879-412">None</span></span> |
|<span data-ttu-id="92879-413">刪除/Api/todoitems/{識別碼} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="92879-413">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="92879-414">刪除專案 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="92879-414">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="92879-415">無</span><span class="sxs-lookup"><span data-stu-id="92879-415">None</span></span> | <span data-ttu-id="92879-416">無</span><span class="sxs-lookup"><span data-stu-id="92879-416">None</span></span>|

<span data-ttu-id="92879-417">下圖顯示應用程式的設計。</span><span class="sxs-lookup"><span data-stu-id="92879-417">The following diagram shows the design of the app.</span></span>

![左側方塊代表用戶端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="92879-423">先決條件</span><span class="sxs-lookup"><span data-stu-id="92879-423">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92879-424">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92879-424">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="92879-425">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="92879-425">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="92879-426">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="92879-426">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="92879-427">建立 Web 專案</span><span class="sxs-lookup"><span data-stu-id="92879-427">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92879-428">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92879-428">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="92879-429">從 [檔案]\*\*\*\* 功能表選取 [新增]**[專案]** > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-429">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="92879-430">選取 **ASP.NET Core Web 應用程式**範本，然後按一下 [下一步]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-430">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="92879-431">將專案命名為 *TodoApi*，然後按一下 [建立]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-431">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="92879-432">在 [建立新的 ASP.NET Core Web 應用程式]\*\*\*\* 對話方塊中，確認選取 [.NET Core]\*\*\*\* 和 [ASP.NET Core 2.2]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-432">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="92879-433">選取 **API** 範本，然後按一下 [建立]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-433">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="92879-434">請**勿**選取 [Enable Docker Support] \(啟用 Docker 支援\)\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-434">**Don't** select **Enable Docker Support**.</span></span>

![VS 新增專案對話方塊](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="92879-436">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="92879-436">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="92879-437">開啟[整合式終端機](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="92879-437">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="92879-438">將目錄 (`cd`) 變更為包含專案資料夾的資料夾。</span><span class="sxs-lookup"><span data-stu-id="92879-438">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="92879-439">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="92879-439">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="92879-440">這些命令會建立新的 Web API 專案，並開啟新專案資料夾中的新 Visual Studio Code 執行個體。</span><span class="sxs-lookup"><span data-stu-id="92879-440">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="92879-441">當出現對話方塊詢問您是否要將所需的資產新增至專案時，選取 [是]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-441">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="92879-442">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="92879-442">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="92879-443">選取 [檔案]\*\* [新增解決方案]\*\* > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-443">Select **File** > **New Solution**.</span></span>

  ![macOS 新增方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="92879-445">在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用程式**  >  **API**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="92879-445">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="92879-446">在8.6 版或更新版本中，選取 [ **Web] 和 [主控台**  >  **應用程式**  >  **API**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="92879-446">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="92879-447">在 [ **設定新的 ASP.NET Core WEB API** ] 對話方塊中，選取最新的 .net Core 2.X **目標 Framework**。</span><span class="sxs-lookup"><span data-stu-id="92879-447">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="92879-448">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="92879-448">Select **Next**.</span></span>

* <span data-ttu-id="92879-449">針對 [專案名稱]\*\*\*\* 輸入 *TodoApi*，然後選取 [建立]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-449">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![設定對話方塊](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="92879-451">測試 API</span><span class="sxs-lookup"><span data-stu-id="92879-451">Test the API</span></span>

<span data-ttu-id="92879-452">專案範本會建立 `values` API。</span><span class="sxs-lookup"><span data-stu-id="92879-452">The project template creates a `values` API.</span></span> <span data-ttu-id="92879-453">從瀏覽器呼叫 `Get` 方法來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="92879-453">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92879-454">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92879-454">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="92879-455">按 Ctrl+F5 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="92879-455">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="92879-456">Visual Studio 會啟動瀏覽器並巡覽至 `https://localhost:<port>/api/values`，其中 `<port>` 是隨機選擇的通訊埠編號。</span><span class="sxs-lookup"><span data-stu-id="92879-456">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="92879-457">如果出現對話方塊詢問您是否應該信任 IIS Express 憑證，請選取 [是]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-457">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="92879-458">在接著出現的 [安全性警告]\*\*\*\* 對話方塊中，選取 [是]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-458">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="92879-459">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="92879-459">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="92879-460">按 Ctrl+F5 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="92879-460">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="92879-461">在瀏覽器中，前往下列 URL：`https://localhost:5001/api/values`。</span><span class="sxs-lookup"><span data-stu-id="92879-461">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="92879-462">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="92879-462">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="92879-463">選取 [**執行**  >  **開始調試**程式] 以啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="92879-463">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="92879-464">Visual Studio for Mac 會啟動瀏覽器並巡覽至 `https://localhost:<port>`，其中 `<port>` 是隨機選擇的連接埠號碼。</span><span class="sxs-lookup"><span data-stu-id="92879-464">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="92879-465">傳回 HTTP 404 (找不到) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="92879-465">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="92879-466">將 `/api/values` 附加至 URL (將 URL 變更為 `https://localhost:<port>/api/values`)。</span><span class="sxs-lookup"><span data-stu-id="92879-466">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="92879-467">隨即傳回下列 JSON：</span><span class="sxs-lookup"><span data-stu-id="92879-467">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="92879-468">新增模型類別</span><span class="sxs-lookup"><span data-stu-id="92879-468">Add a model class</span></span>

<span data-ttu-id="92879-469">「模型」\*\* 是代表應用程式所管理資料的一組類別。</span><span class="sxs-lookup"><span data-stu-id="92879-469">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="92879-470">此應用程式的模型是單一 `TodoItem` 類別。</span><span class="sxs-lookup"><span data-stu-id="92879-470">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92879-471">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92879-471">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="92879-472">在 **方案總管**中，以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="92879-472">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="92879-473">選取 **[**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="92879-473">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="92879-474">將資料夾命名為 *Models*。</span><span class="sxs-lookup"><span data-stu-id="92879-474">Name the folder *Models*.</span></span>

* <span data-ttu-id="92879-475">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [新增]\*\*\*\* > [類別]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-475">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="92879-476">將類別命名為 *TodoItem*，然後選取 [新增]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-476">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="92879-477">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="92879-477">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="92879-478">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="92879-478">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="92879-479">新增名為 *Models* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="92879-479">Add a folder named *Models*.</span></span>

* <span data-ttu-id="92879-480">將 `TodoItem` 類別新增至具有下列程式碼的 *Models* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="92879-480">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="92879-481">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="92879-481">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="92879-482">以滑鼠右鍵按一下專案。</span><span class="sxs-lookup"><span data-stu-id="92879-482">Right-click the project.</span></span> <span data-ttu-id="92879-483">選取 **[**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="92879-483">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="92879-484">將資料夾命名為 *Models*。</span><span class="sxs-lookup"><span data-stu-id="92879-484">Name the folder *Models*.</span></span>

  ![新增資料夾](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="92879-486">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [新增]**[新增檔案]** > **[一般]** > **[空類別]** > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-486">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="92879-487">將類別命名為 *TodoItem*，然後按一下 [新增]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-487">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="92879-488">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="92879-488">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="92879-489">`Id` 屬性的功能相當於關聯式資料庫中的唯一索引鍵。</span><span class="sxs-lookup"><span data-stu-id="92879-489">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="92879-490">模型類別可位於專案中的任何位置，但依照慣例會使用 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="92879-490">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="92879-491">新增資料庫內容</span><span class="sxs-lookup"><span data-stu-id="92879-491">Add a database context</span></span>

<span data-ttu-id="92879-492">「資料庫內容」\*\* 是為資料模型協調 Entity Framework 功能的主要類別。</span><span class="sxs-lookup"><span data-stu-id="92879-492">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="92879-493">此類別是透過衍生自 `Microsoft.EntityFrameworkCore.DbContext` 類別來建立。</span><span class="sxs-lookup"><span data-stu-id="92879-493">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92879-494">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92879-494">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="92879-495">以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [新增]\*\*\*\* > [類別]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-495">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="92879-496">將類別命名為 *TodoContext*，然後按一下 [新增]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-496">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="92879-497">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="92879-497">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="92879-498">將 `TodoContext` 類別新增至 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="92879-498">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="92879-499">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="92879-499">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="92879-500">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="92879-500">Register the database context</span></span>

<span data-ttu-id="92879-501">在 ASP.NET Core 中，資料庫內容等服務必須向[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器註冊。</span><span class="sxs-lookup"><span data-stu-id="92879-501">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="92879-502">此容器會將服務提供給控制器。</span><span class="sxs-lookup"><span data-stu-id="92879-502">The container provides the service to controllers.</span></span>

<span data-ttu-id="92879-503">使用下列醒目提示的程式碼更新 *Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="92879-503">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="92879-504">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="92879-504">The preceding code:</span></span>

* <span data-ttu-id="92879-505">移除未使用的 `using` 宣告。</span><span class="sxs-lookup"><span data-stu-id="92879-505">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="92879-506">將資料庫內容新增至 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="92879-506">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="92879-507">指定資料庫內容將會使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="92879-507">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="92879-508">新增控制器</span><span class="sxs-lookup"><span data-stu-id="92879-508">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92879-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92879-509">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="92879-510">以滑鼠右鍵按一下 *Controllers* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="92879-510">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="92879-511">選取 [新增]**[新項目]** > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-511">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="92879-512">在 [新增項目]\*\*\*\* 對話方塊中，選取 [API 控制器類別]\*\*\*\* 範本。</span><span class="sxs-lookup"><span data-stu-id="92879-512">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="92879-513">將類別命名為 *TodoController*，然後選取 [新增]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-513">Name the class *TodoController*, and select **Add**.</span></span>

  ![在搜尋方塊中輸入 controller 且已選取 Web API 控制器的 [新增項目] 對話方塊](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="92879-515">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="92879-515">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="92879-516">在 *Controllers* 資料夾中，建立名為 `TodoController` 的類別。</span><span class="sxs-lookup"><span data-stu-id="92879-516">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="92879-517">使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="92879-517">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="92879-518">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="92879-518">The preceding code:</span></span>

* <span data-ttu-id="92879-519">定義不含方法的 API 控制器類別。</span><span class="sxs-lookup"><span data-stu-id="92879-519">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="92879-520">標記具有屬性的類別 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="92879-520">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="92879-521">這個屬性表示控制器會回應 Web API 要求。</span><span class="sxs-lookup"><span data-stu-id="92879-521">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="92879-522">如需屬性所啟用之特定行為的相關資訊，請參閱 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="92879-522">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="92879-523">使用 DI 將資料庫內容 (`TodoContext`) 插入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="92879-523">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="92879-524">控制器中的每一個 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法都會使用資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="92879-524">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="92879-525">如果資料庫是空的，請將名為 `Item1` 的項目新增至資料庫。</span><span class="sxs-lookup"><span data-stu-id="92879-525">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="92879-526">此程式碼是在建構函式中，因此每次執行都會有新的 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="92879-526">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="92879-527">如果您刪除所有項目，則建構函式會在下次呼叫 API 方法時重新建立 `Item1`。</span><span class="sxs-lookup"><span data-stu-id="92879-527">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="92879-528">因此看起來雖然像是刪除失敗，但實際為成功。</span><span class="sxs-lookup"><span data-stu-id="92879-528">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="92879-529">新增 Get 方法</span><span class="sxs-lookup"><span data-stu-id="92879-529">Add Get methods</span></span>

<span data-ttu-id="92879-530">若要提供擷取待辦事項的 API，請在 `TodoController` 類別中新增下列方法：</span><span class="sxs-lookup"><span data-stu-id="92879-530">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="92879-531">這些方法會實作兩個 GET 端點：</span><span class="sxs-lookup"><span data-stu-id="92879-531">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="92879-532">如果應用程式仍在執行，請將其停止。</span><span class="sxs-lookup"><span data-stu-id="92879-532">Stop the app if it's still running.</span></span> <span data-ttu-id="92879-533">然後重新予以執行以包含最新的變更。</span><span class="sxs-lookup"><span data-stu-id="92879-533">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="92879-534">從瀏覽器呼叫這兩個端點來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="92879-534">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="92879-535">例如：</span><span class="sxs-lookup"><span data-stu-id="92879-535">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="92879-536">以下是呼叫 `GetTodoItems` 所產生的 HTTP 回應：</span><span class="sxs-lookup"><span data-stu-id="92879-536">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="92879-537">傳送和 URL 路徑</span><span class="sxs-lookup"><span data-stu-id="92879-537">Routing and URL paths</span></span>

<span data-ttu-id="92879-538">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)屬性代表回應 HTTP GET 要求的方法。</span><span class="sxs-lookup"><span data-stu-id="92879-538">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="92879-539">每個方法的 URL 路徑的建構方式如下：</span><span class="sxs-lookup"><span data-stu-id="92879-539">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="92879-540">一開始在控制器的 `Route` 屬性中使用範本字串：</span><span class="sxs-lookup"><span data-stu-id="92879-540">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="92879-541">以控制器的名稱取代 `[controller]`，也就是將控制器類別名稱減去 "Controller" 字尾。</span><span class="sxs-lookup"><span data-stu-id="92879-541">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="92879-542">在此範例中，控制器類別名稱是 **Todo**Controller，因此容器名稱是 "todo"。</span><span class="sxs-lookup"><span data-stu-id="92879-542">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="92879-543">ASP.NET Core [路由](xref:mvc/controllers/routing)不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="92879-543">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="92879-544">如果 `[HttpGet]` 屬性具有路由範本 (例如 `[HttpGet("products")]`)，請將其附加到路徑。</span><span class="sxs-lookup"><span data-stu-id="92879-544">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="92879-545">此範例不使用範本。</span><span class="sxs-lookup"><span data-stu-id="92879-545">This sample doesn't use a template.</span></span> <span data-ttu-id="92879-546">如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="92879-546">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="92879-547">在下列 `GetTodoItem` 方法中，`"{id}"` 是待辦事項唯一識別碼的預留位置變數。</span><span class="sxs-lookup"><span data-stu-id="92879-547">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="92879-548">在叫用 `GetTodoItem` 時，會將 URL 中的 `"{id}"` 值提供給方法的 `id` 參數。</span><span class="sxs-lookup"><span data-stu-id="92879-548">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="92879-549">傳回值</span><span class="sxs-lookup"><span data-stu-id="92879-549">Return values</span></span>

<span data-ttu-id="92879-550">和方法的傳回型 `GetTodoItems` 別 `GetTodoItem` 為 [ActionResult \<T> 類型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="92879-550">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="92879-551">ASP.NET Core 會自動將物件序列化為 [JSON](https://www.json.org/)，並將 JSON 寫入至回應訊息的本文。</span><span class="sxs-lookup"><span data-stu-id="92879-551">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="92879-552">此傳回型別的回應碼為 200，假設沒有任何未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="92879-552">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="92879-553">未處理的例外狀況會轉譯成 5xx 錯誤。</span><span class="sxs-lookup"><span data-stu-id="92879-553">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="92879-554">`ActionResult` 傳回型別可代表各種 HTTP 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="92879-554">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="92879-555">例如，`GetTodoItem` 可傳回兩個不同的狀態值：</span><span class="sxs-lookup"><span data-stu-id="92879-555">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="92879-556">如果沒有任何專案符合要求的識別碼，方法會傳回 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 錯誤碼。</span><span class="sxs-lookup"><span data-stu-id="92879-556">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="92879-557">否則，方法會傳回 200 與 JSON 回應本文。</span><span class="sxs-lookup"><span data-stu-id="92879-557">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="92879-558">傳回 `item` 會導致 HTTP 200 回應。</span><span class="sxs-lookup"><span data-stu-id="92879-558">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="92879-559">測試 GetTodoItems 方法</span><span class="sxs-lookup"><span data-stu-id="92879-559">Test the GetTodoItems method</span></span>

<span data-ttu-id="92879-560">本教學課程使用 Postman 來測試 Web API。</span><span class="sxs-lookup"><span data-stu-id="92879-560">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="92879-561">安裝 [Postman](https://www.getpostman.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="92879-561">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="92879-562">啟動 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="92879-562">Start the web app.</span></span>
* <span data-ttu-id="92879-563">啟動 Postman。</span><span class="sxs-lookup"><span data-stu-id="92879-563">Start Postman.</span></span>
* <span data-ttu-id="92879-564">停用 **SSL 憑證驗證**。</span><span class="sxs-lookup"><span data-stu-id="92879-564">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92879-565">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92879-565">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="92879-566">從 [檔案]\*\* [設定]\*\* > \*\*\*\* ([一般]\*\*\*\* 索引標籤)，停用 [SSL 憑證驗證]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-566">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="92879-567">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="92879-567">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="92879-568">從**Postman**  >  **喜好**設定 (**一般**] 索引標籤) ，停用**SSL 憑證驗證**。</span><span class="sxs-lookup"><span data-stu-id="92879-568">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="92879-569">或者，選取扳手並選取 [設定]\*\*\*\*，然後停用 [SSL 憑證驗證]。</span><span class="sxs-lookup"><span data-stu-id="92879-569">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="92879-570">在測試控制器之後，請重新啟用 [SSL certificate verification] \(SSL 憑證驗證\)。</span><span class="sxs-lookup"><span data-stu-id="92879-570">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="92879-571">建立新的要求。</span><span class="sxs-lookup"><span data-stu-id="92879-571">Create a new request.</span></span>
  * <span data-ttu-id="92879-572">將 HTTP 方法設定為 **GET**。</span><span class="sxs-lookup"><span data-stu-id="92879-572">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="92879-573">將要求 URI 設定為 `https://localhost:<port>/api/todo` 。</span><span class="sxs-lookup"><span data-stu-id="92879-573">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="92879-574">例如 `https://localhost:5001/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="92879-574">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="92879-575">在 Postman 中，設定 [Two pane view] \(雙窗格檢視\)\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="92879-575">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="92879-576">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="92879-576">Select **Send**.</span></span>

![Postman 與 GET 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="92879-578">新增 Create 方法</span><span class="sxs-lookup"><span data-stu-id="92879-578">Add a Create method</span></span>

<span data-ttu-id="92879-579">在 *Controllers/TodoController.cs* 內部新增下列 `PostTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="92879-579">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="92879-580">上述程式碼是 HTTP POST 方法，如屬性所指示 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="92879-580">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="92879-581">該方法會從 HTTP 要求本文取得待辦事項的值。</span><span class="sxs-lookup"><span data-stu-id="92879-581">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="92879-582">`CreatedAtAction` 方法：</span><span class="sxs-lookup"><span data-stu-id="92879-582">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="92879-583">成功時會傳回 HTTP 201 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="92879-583">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="92879-584">對於可在伺服器上建立新資源的 HTTP POST 方法，其標準回應是 HTTP 201。</span><span class="sxs-lookup"><span data-stu-id="92879-584">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="92879-585">將 `Location` 標頭加到回應中。</span><span class="sxs-lookup"><span data-stu-id="92879-585">Adds a `Location` header to the response.</span></span> <span data-ttu-id="92879-586">`Location` 標頭指定新建立之待辦事項的 URI。</span><span class="sxs-lookup"><span data-stu-id="92879-586">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="92879-587">如需詳細資訊，請參閱 [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (已建立 10.2.2 201)。</span><span class="sxs-lookup"><span data-stu-id="92879-587">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="92879-588">參考 `GetTodoItem` 動作以建立 `Location` 標頭的 URI。</span><span class="sxs-lookup"><span data-stu-id="92879-588">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="92879-589">C# `nameof` 關鍵字是用來避免在 `CreatedAtAction` 呼叫中以硬式編碼方式寫入動作名稱。</span><span class="sxs-lookup"><span data-stu-id="92879-589">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="92879-590">測試 PostTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="92879-590">Test the PostTodoItem method</span></span>

* <span data-ttu-id="92879-591">建置專案。</span><span class="sxs-lookup"><span data-stu-id="92879-591">Build the project.</span></span>
* <span data-ttu-id="92879-592">在 Postman 中，將 HTTP 方法設定為 `POST`。</span><span class="sxs-lookup"><span data-stu-id="92879-592">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="92879-593">將 URI 設定為 `https://localhost:<port>/api/TodoItem` 。</span><span class="sxs-lookup"><span data-stu-id="92879-593">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="92879-594">例如 `https://localhost:5001/api/TodoItem`。</span><span class="sxs-lookup"><span data-stu-id="92879-594">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="92879-595">選取 [Body]\*\*\*\* \(本文\) 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="92879-595">Select the **Body** tab.</span></span>
* <span data-ttu-id="92879-596">選取 [原始]\*\*\*\* 選項按鈕。</span><span class="sxs-lookup"><span data-stu-id="92879-596">Select the **raw** radio button.</span></span>
* <span data-ttu-id="92879-597">將類型設定為 **JSON (application/json)**。</span><span class="sxs-lookup"><span data-stu-id="92879-597">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="92879-598">在要求本文中，針對待辦項目輸入 JSON：</span><span class="sxs-lookup"><span data-stu-id="92879-598">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="92879-599">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="92879-599">Select **Send**.</span></span>

  ![Postman 與建立要求](first-web-api/_static/create.png)

  <span data-ttu-id="92879-601">如果您收到 405「不允許的方法」錯誤，可能是由於新增 `PostTodoItem` 方法之後未編譯專案所導致。</span><span class="sxs-lookup"><span data-stu-id="92879-601">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="92879-602">測試位置標頭 URI</span><span class="sxs-lookup"><span data-stu-id="92879-602">Test the location header URI</span></span>

* <span data-ttu-id="92879-603">在 [回應]\*\*\*\* 窗格中選取 [標頭]\*\*\*\* 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="92879-603">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="92879-604">複製 [位置]\*\*\*\* 標頭值：</span><span class="sxs-lookup"><span data-stu-id="92879-604">Copy the **Location** header value:</span></span>

  ![Postman 主控台的 [標頭] 索引標籤](first-web-api/_static/pmc2.png)

* <span data-ttu-id="92879-606">將方法設定為 GET。</span><span class="sxs-lookup"><span data-stu-id="92879-606">Set the method to GET.</span></span>
<span data-ttu-id="92879-607">\* 將 URI 設定為  `https://localhost:<port>/api/TodoItems/2` 。</span><span class="sxs-lookup"><span data-stu-id="92879-607">\* Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span><span data-ttu-id="92879-608">例如，  `https://localhost:5001/api/TodoItems/2` 。</span><span class="sxs-lookup"><span data-stu-id="92879-608"> For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="92879-609">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="92879-609">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="92879-610">新增 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="92879-610">Add a PutTodoItem method</span></span>

<span data-ttu-id="92879-611">請新增下列 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="92879-611">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="92879-612">`PutTodoItem` 類似於 `PostTodoItem`，但是會使用 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="92879-612">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="92879-613">回應為 [204 (沒有內容) ](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="92879-613">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="92879-614">根據 HTTP 規格，PUT 要求需要用戶端傳送整個更新的實體，而不只是變更。</span><span class="sxs-lookup"><span data-stu-id="92879-614">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="92879-615">若要支援部分更新，請使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="92879-615">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="92879-616">如果在呼叫 `PutTodoItem` 時發生錯誤，請呼叫 `GET` 以確保資料庫中有項目。</span><span class="sxs-lookup"><span data-stu-id="92879-616">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="92879-617">測試 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="92879-617">Test the PutTodoItem method</span></span>

<span data-ttu-id="92879-618">這個範例會使用必須在每次啟動應用程式時初始化的記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="92879-618">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="92879-619">資料庫中必須有項目，您才能進行 PUT 呼叫。</span><span class="sxs-lookup"><span data-stu-id="92879-619">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="92879-620">在發出 PUT 呼叫之前，請先呼叫 GET 以確保資料庫中有項目。</span><span class="sxs-lookup"><span data-stu-id="92879-620">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="92879-621">更新識別碼為 1 的待辦事項，並將其名稱設定為 "feed fish"：</span><span class="sxs-lookup"><span data-stu-id="92879-621">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="92879-622">下圖顯示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="92879-622">The following image shows the Postman update:</span></span>

![顯示「204 (沒有內容) 回應」的 Postman 主控台](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="92879-624">新增 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="92879-624">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="92879-625">請新增下列 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="92879-625">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="92879-626">`DeleteTodoItem` 回應是 [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) \(204 (沒有內容)\)。</span><span class="sxs-lookup"><span data-stu-id="92879-626">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="92879-627">測試 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="92879-627">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="92879-628">使用 Postman 刪除待辦事項：</span><span class="sxs-lookup"><span data-stu-id="92879-628">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="92879-629">將方法設定為 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="92879-629">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="92879-630">設定要刪除之物件的 URI (例如 `https://localhost:5001/api/todo/1`) 。</span><span class="sxs-lookup"><span data-stu-id="92879-630">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="92879-631">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="92879-631">Select **Send**.</span></span>

<span data-ttu-id="92879-632">範例應用程式可讓您刪除所有項目。</span><span class="sxs-lookup"><span data-stu-id="92879-632">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="92879-633">但刪除最後一個項目之後，模型類別建構函式會在下次呼叫 API 時建立新的項目。</span><span class="sxs-lookup"><span data-stu-id="92879-633">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="92879-634">使用 JavaScript 呼叫 Web API</span><span class="sxs-lookup"><span data-stu-id="92879-634">Call the web API with JavaScript</span></span>

<span data-ttu-id="92879-635">在此節中，將會新增 HTML 網頁，以使用 JavaScript 來呼叫 Web API。</span><span class="sxs-lookup"><span data-stu-id="92879-635">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="92879-636">jQuery 會起始要求。</span><span class="sxs-lookup"><span data-stu-id="92879-636">jQuery initiates the request.</span></span> <span data-ttu-id="92879-637">JavaScript 會使用來自 Web API 回應的詳細資料來更新頁面。</span><span class="sxs-lookup"><span data-stu-id="92879-637">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="92879-638">藉由使用下列反白顯示的程式碼更新 *Startup.cs*，來設定應用程式[提供靜態檔案](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A)並[啟用預設檔案對應](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A)：</span><span class="sxs-lookup"><span data-stu-id="92879-638">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="92879-639">在專案目錄中建立 *wwwroot* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="92879-639">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="92879-640">將名為 *index.html* 的 HTML 檔案新增至 *wwwroot* 目錄。</span><span class="sxs-lookup"><span data-stu-id="92879-640">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="92879-641">將其內容取代為下列標記：</span><span class="sxs-lookup"><span data-stu-id="92879-641">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="92879-642">將名為 *site.js* 的 JavaScript 檔案新增至 *wwwroot* 目錄。</span><span class="sxs-lookup"><span data-stu-id="92879-642">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="92879-643">以下列程式碼取代其內容：</span><span class="sxs-lookup"><span data-stu-id="92879-643">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="92879-644">若要在本機測試 HTML 網頁，可能需要變更 ASP.NET Core 專案的啟動設定：</span><span class="sxs-lookup"><span data-stu-id="92879-644">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="92879-645">開啟 *Properties\launchSettings.json*。</span><span class="sxs-lookup"><span data-stu-id="92879-645">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="92879-646">移除 `launchUrl` 屬性，以強制在專案的預設檔案*index.html*開啟應用程式 &mdash; 。</span><span class="sxs-lookup"><span data-stu-id="92879-646">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="92879-647">此範例會呼叫 Web API 的所有 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="92879-647">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="92879-648">以下是關於呼叫 API 的說明。</span><span class="sxs-lookup"><span data-stu-id="92879-648">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="92879-649">取得待辦事項的清單</span><span class="sxs-lookup"><span data-stu-id="92879-649">Get a list of to-do items</span></span>

<span data-ttu-id="92879-650">jQuery 會將 HTTP GET 要求傳送至 web API，該 API 會傳回代表待辦事項陣列的 JSON。</span><span class="sxs-lookup"><span data-stu-id="92879-650">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="92879-651">如果要求成功，則會叫用 `success` 回呼函式。</span><span class="sxs-lookup"><span data-stu-id="92879-651">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="92879-652">在回呼中，DOM 已使用待辦事項資訊進行更新。</span><span class="sxs-lookup"><span data-stu-id="92879-652">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="92879-653">新增待辦事項</span><span class="sxs-lookup"><span data-stu-id="92879-653">Add a to-do item</span></span>

<span data-ttu-id="92879-654">jQuery 會使用要求主體中的待辦事項來傳送 HTTP POST 要求。</span><span class="sxs-lookup"><span data-stu-id="92879-654">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="92879-655">`accepts` 和 `contentType` 選項都設定為 `application/json`，以指定接收和傳送的媒體類型。</span><span class="sxs-lookup"><span data-stu-id="92879-655">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="92879-656">待辦事項會使用 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 轉換成 JSON。</span><span class="sxs-lookup"><span data-stu-id="92879-656">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="92879-657">當 API 傳回成功狀態碼時，會叫用 `getData` 函式來更新 HTML 資料表。</span><span class="sxs-lookup"><span data-stu-id="92879-657">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="92879-658">更新待辦事項</span><span class="sxs-lookup"><span data-stu-id="92879-658">Update a to-do item</span></span>

<span data-ttu-id="92879-659">更新待辦事項類似於新增待辦事項。</span><span class="sxs-lookup"><span data-stu-id="92879-659">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="92879-660">`url` 會變更為新增項目的唯一識別碼，而 `type` 是 `PUT`。</span><span class="sxs-lookup"><span data-stu-id="92879-660">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="92879-661">刪除待辦事項</span><span class="sxs-lookup"><span data-stu-id="92879-661">Delete a to-do item</span></span>

<span data-ttu-id="92879-662">刪除待辦事項的達成方法是將 AJAX 呼叫的 `type` 設定為 `DELETE`，並在 URL 中指定項目的唯一識別碼。</span><span class="sxs-lookup"><span data-stu-id="92879-662">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="92879-663">將驗證支援新增至 web API</span><span class="sxs-lookup"><span data-stu-id="92879-663">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="92879-664">其他資源</span><span class="sxs-lookup"><span data-stu-id="92879-664">Additional resources</span></span>

<span data-ttu-id="92879-665">[檢視或下載本教學課程的範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)。</span><span class="sxs-lookup"><span data-stu-id="92879-665">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="92879-666">請參閱[如何下載](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="92879-666">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="92879-667">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="92879-667">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="92879-668">本教學課程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="92879-668">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
