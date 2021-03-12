---
title: ASP.NET Core MVC 使用者入門
author: rick-anderson
description: 了解如何開始使用 ASP.NET Core MVC。
ms.author: riande
ms.date: 01/20/2021
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
uid: tutorials/first-mvc-app/start-mvc
ms.custom: contperf-fy21q3
ms.openlocfilehash: 8e5f216a354b1ed7559b433d3a4867627bf60df3
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589773"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="57458-103">ASP.NET Core MVC 使用者入門</span><span class="sxs-lookup"><span data-stu-id="57458-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="57458-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="57458-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="57458-105">這是一個系列的第一個教學課程，教您使用控制器和 views ASP.NET 核心 MVC 網頁程式開發。</span><span class="sxs-lookup"><span data-stu-id="57458-105">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="57458-106">在系列結束時，您將會有一個可管理及顯示電影資料的應用程式。</span><span class="sxs-lookup"><span data-stu-id="57458-106">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="57458-107">您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="57458-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="57458-108">建立 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="57458-108">Create a web app.</span></span>
> * <span data-ttu-id="57458-109">新增並建構模型。</span><span class="sxs-lookup"><span data-stu-id="57458-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="57458-110">使用資料庫。</span><span class="sxs-lookup"><span data-stu-id="57458-110">Work with a database.</span></span>
> * <span data-ttu-id="57458-111">新增搜尋和驗證。</span><span class="sxs-lookup"><span data-stu-id="57458-111">Add search and validation.</span></span>

<span data-ttu-id="57458-112">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="57458-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="57458-113">必要條件</span><span class="sxs-lookup"><span data-stu-id="57458-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57458-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57458-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="57458-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57458-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57458-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="57458-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="57458-117">建立 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="57458-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57458-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57458-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="57458-119">啟動 Visual Studio，然後選取 [建立新專案]。</span><span class="sxs-lookup"><span data-stu-id="57458-119">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="57458-120">在 [ **建立新專案** ] 對話方塊中，選取 [下一步] **ASP.NET Core Web 應用程式** > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="57458-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
* <span data-ttu-id="57458-121">在 [ **設定您的新專案** ] 對話方塊中，輸入 [ `MvcMovie` **專案名稱**]。</span><span class="sxs-lookup"><span data-stu-id="57458-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="57458-122">請務必將專案命名為 *MvcMovie*。</span><span class="sxs-lookup"><span data-stu-id="57458-122">It's important to name the project *MvcMovie*.</span></span> <span data-ttu-id="57458-123">複製程式碼時，大小寫必須符合每個 `namespace` 相符專案。</span><span class="sxs-lookup"><span data-stu-id="57458-123">Capitalization needs to match each `namespace` matches when code is copied.</span></span>
* <span data-ttu-id="57458-124">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="57458-124">Select **Create**.</span></span>
* <span data-ttu-id="57458-125">在 [ **建立新的 ASP.NET Core web 應用程式** ] 對話方塊中，選取：</span><span class="sxs-lookup"><span data-stu-id="57458-125">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
  * <span data-ttu-id="57458-126">下拉式清單中的 **.Net core** 和 **ASP.NET core 5.0** 。</span><span class="sxs-lookup"><span data-stu-id="57458-126">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
  * <span data-ttu-id="57458-127">**ASP.NET Core Web 應用程式 (模型-視圖控制器)**。</span><span class="sxs-lookup"><span data-stu-id="57458-127">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
  * <span data-ttu-id="57458-128">**Create**。</span><span class="sxs-lookup"><span data-stu-id="57458-128">**Create**.</span></span>

![<span data-ttu-id="57458-129">建立新的 ASP.NET Core web 應用程式</span><span class="sxs-lookup"><span data-stu-id="57458-129">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="57458-130">如需建立專案的替代方法，請參閱 [在 Visual Studio 中建立新專案](/visualstudio/ide/create-new-project)。</span><span class="sxs-lookup"><span data-stu-id="57458-130">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="57458-131">Visual Studio 已針對建立的 MVC 專案使用預設專案範本。</span><span class="sxs-lookup"><span data-stu-id="57458-131">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="57458-132">建立的專案：</span><span class="sxs-lookup"><span data-stu-id="57458-132">The created project:</span></span>

* <span data-ttu-id="57458-133">是正常運作的應用程式。</span><span class="sxs-lookup"><span data-stu-id="57458-133">Is a working app.</span></span>
* <span data-ttu-id="57458-134">是基本的入門專案。</span><span class="sxs-lookup"><span data-stu-id="57458-134">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="57458-135">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57458-135">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="57458-136">本教學課程假設您已熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="57458-136">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="57458-137">如需詳細資訊，請參閱 [開始使用 VS Code](https://code.visualstudio.com/docs) 和 [Visual Studio Code](#visual-studio-code-help)說明。</span><span class="sxs-lookup"><span data-stu-id="57458-137">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="57458-138">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="57458-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="57458-139">變更至 `cd` 將包含專案的目錄 () 。</span><span class="sxs-lookup"><span data-stu-id="57458-139">Change to the directory (`cd`) that will contain the project.</span></span>
* <span data-ttu-id="57458-140">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="57458-140">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="57458-141">如果出現一個對話方塊，其中包含 **要建立的必要資產，且 ' MvcMovie ' 中遺漏了 debug 錯。要新增它們嗎？**，請選取 **[是]**</span><span class="sxs-lookup"><span data-stu-id="57458-141">If a dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**</span></span>

  * <span data-ttu-id="57458-142">`dotnet new mvc -o MvcMovie`：在 *MvcMovie* 資料夾中建立新的 ASP.NET Core MVC 專案。</span><span class="sxs-lookup"><span data-stu-id="57458-142">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="57458-143">`code -r MvcMovie`：載入 Visual Studio Code 中的 *MvcMovie .csproj* 專案檔。</span><span class="sxs-lookup"><span data-stu-id="57458-143">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57458-144">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="57458-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="57458-145">選取 [檔案]**[新增解決方案]** > 。</span><span class="sxs-lookup"><span data-stu-id="57458-145">Select **File** > **New Solution**.</span></span>

  ![macOS 新增方案](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="57458-147">在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用**  >  **程式 Web 應用程式] (模型-視圖控制器)**  >  **下一步]**。</span><span class="sxs-lookup"><span data-stu-id="57458-147">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="57458-148">在8.6 版或更新版本中，選取 [ **web] 和 [主控台**  >  **應用**  >  **程式 web 應用程式] (模型-視圖控制器)**  >  **下一步]**。</span><span class="sxs-lookup"><span data-stu-id="57458-148">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS web 應用程式範本選取專案](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="57458-150">在 [ **設定新的 Web 應用程式** ] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="57458-150">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="57458-151">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="57458-151">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="57458-152">如果出現選取 **目標 Framework** 的選項，請選取最新的5.x 版。</span><span class="sxs-lookup"><span data-stu-id="57458-152">If an option to select a **Target Framework** is presented, select the latest 5.x version.</span></span>
  * <span data-ttu-id="57458-153">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="57458-153">Select **Next**.</span></span>

* <span data-ttu-id="57458-154">將專案命名為 **MvcMovie**，然後選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="57458-154">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS 為專案命名](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="57458-156">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="57458-156">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57458-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57458-157">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="57458-158">選取 Ctrl + F5 以執行應用程式，而不執行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="57458-158">Select Ctrl+F5 to run the app without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="57458-159">Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="57458-159">Visual Studio:</span></span>

  * <span data-ttu-id="57458-160">啟動 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)。</span><span class="sxs-lookup"><span data-stu-id="57458-160">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="57458-161">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="57458-161">Runs the app.</span></span>

  <span data-ttu-id="57458-162">位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="57458-162">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="57458-163">本機電腦的標準主機名稱為 `localhost` 。</span><span class="sxs-lookup"><span data-stu-id="57458-163">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="57458-164">當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。</span><span class="sxs-lookup"><span data-stu-id="57458-164">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="57458-165">藉由選取 Ctrl + F5 啟動應用程式而不進行偵錯工具，可讓您：</span><span class="sxs-lookup"><span data-stu-id="57458-165">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="57458-166">變更程式碼。</span><span class="sxs-lookup"><span data-stu-id="57458-166">Make code changes.</span></span>
* <span data-ttu-id="57458-167">儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="57458-167">Save the file.</span></span>
* <span data-ttu-id="57458-168">快速重新整理瀏覽器並查看程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="57458-168">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="57458-169">您可以從 [偵錯] 功能表項目的偵錯或非偵錯模式中啟動應用程式：</span><span class="sxs-lookup"><span data-stu-id="57458-169">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![[偵錯] 功能表](start-mvc/_static/debug_menu50.png)

<span data-ttu-id="57458-171">您可以選取 [IIS Express] 按鈕偵錯應用程式</span><span class="sxs-lookup"><span data-stu-id="57458-171">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express50.png)

<span data-ttu-id="57458-173">下圖顯示應用程式：</span><span class="sxs-lookup"><span data-stu-id="57458-173">The following image shows the app:</span></span>

![Home 或 Index 頁面](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="57458-175">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57458-175">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="57458-176">選取 Ctrl + F5 以在不執行偵錯工具的情況下執行。</span><span class="sxs-lookup"><span data-stu-id="57458-176">Select Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="57458-177">Visual Studio Code：</span><span class="sxs-lookup"><span data-stu-id="57458-177">Visual Studio Code:</span></span>

  * <span data-ttu-id="57458-178">開始 [Kestrel](xref:fundamentals/servers/kestrel)</span><span class="sxs-lookup"><span data-stu-id="57458-178">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="57458-179">啟動瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="57458-179">Launches a browser.</span></span>
  * <span data-ttu-id="57458-180">流覽至 `https://localhost:5001` 。</span><span class="sxs-lookup"><span data-stu-id="57458-180">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="57458-181">位址列會顯示 `localhost:port:5001`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="57458-181">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="57458-182">本機電腦的標準主機名稱為 `localhost` 。</span><span class="sxs-lookup"><span data-stu-id="57458-182">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="57458-183">Localhost 只會為來自本機電腦的 Web 要求提供服務。</span><span class="sxs-lookup"><span data-stu-id="57458-183">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="57458-184">藉由選取 Ctrl + F5 啟動應用程式而不進行偵錯工具，可讓您：</span><span class="sxs-lookup"><span data-stu-id="57458-184">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="57458-185">變更程式碼。</span><span class="sxs-lookup"><span data-stu-id="57458-185">Make code changes.</span></span>
* <span data-ttu-id="57458-186">儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="57458-186">Save the file.</span></span>
* <span data-ttu-id="57458-187">快速重新整理瀏覽器並查看程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="57458-187">Quickly refresh the browser and see the code changes.</span></span>

  ![Home 或 Index 頁面](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57458-189">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="57458-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="57458-190">選取 [執行]**[啟動但不偵錯]** >  來啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="57458-190">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="57458-191">Visual Studio for Mac：</span><span class="sxs-lookup"><span data-stu-id="57458-191">Visual Studio for Mac:</span></span>

  * <span data-ttu-id="57458-192">啟動 [Kestrel](xref:fundamentals/servers/index#kestrel) 伺服器。</span><span class="sxs-lookup"><span data-stu-id="57458-192">Starts [Kestrel](xref:fundamentals/servers/index#kestrel) server.</span></span>
  * <span data-ttu-id="57458-193">啟動瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="57458-193">Launches a browser.</span></span>
  * <span data-ttu-id="57458-194">流覽至 `http://localhost:port` ，其中 *port* 是隨機播放的埠號碼。</span><span class="sxs-lookup"><span data-stu-id="57458-194">Navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

  [!INCLUDE[](~/includes/trustCertMac.md)]

  <span data-ttu-id="57458-195">位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="57458-195">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="57458-196">本機電腦的標準主機名稱為 `localhost` 。</span><span class="sxs-lookup"><span data-stu-id="57458-196">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="57458-197">當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。</span><span class="sxs-lookup"><span data-stu-id="57458-197">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="57458-198">您可以從 [執行] 功能表的偵錯或非偵錯模式中啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="57458-198">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="57458-199">下圖顯示應用程式：</span><span class="sxs-lookup"><span data-stu-id="57458-199">The following image shows the app:</span></span>

![Home 或 Index 頁面](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="57458-201">在本教學課程的下一個部分中，您會了解 MVC，並開始撰寫一些程式碼。</span><span class="sxs-lookup"><span data-stu-id="57458-201">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="57458-202">下一步：新增控制器</span><span class="sxs-lookup"><span data-stu-id="57458-202">Next: Add a controller</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="57458-203">這是一個系列的第一個教學課程，教您使用控制器和 views ASP.NET 核心 MVC 網頁程式開發。</span><span class="sxs-lookup"><span data-stu-id="57458-203">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="57458-204">在系列結束時，您將會有一個可管理及顯示電影資料的應用程式。</span><span class="sxs-lookup"><span data-stu-id="57458-204">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="57458-205">您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="57458-205">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="57458-206">建立 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="57458-206">Create a web app.</span></span>
> * <span data-ttu-id="57458-207">新增並建構模型。</span><span class="sxs-lookup"><span data-stu-id="57458-207">Add and scaffold a model.</span></span>
> * <span data-ttu-id="57458-208">使用資料庫。</span><span class="sxs-lookup"><span data-stu-id="57458-208">Work with a database.</span></span>
> * <span data-ttu-id="57458-209">新增搜尋和驗證。</span><span class="sxs-lookup"><span data-stu-id="57458-209">Add search and validation.</span></span>

<span data-ttu-id="57458-210">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="57458-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="57458-211">必要條件</span><span class="sxs-lookup"><span data-stu-id="57458-211">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57458-212">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57458-212">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="57458-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57458-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57458-214">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="57458-214">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="57458-215">建立 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="57458-215">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57458-216">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57458-216">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="57458-217">從 Visual Studio 中，選取 [ **建立新專案**]。</span><span class="sxs-lookup"><span data-stu-id="57458-217">From the Visual Studio, select **Create a new project**.</span></span>

* <span data-ttu-id="57458-218">選取 [下一步 **ASP.NET Core Web 應用程式** > **]**。</span><span class="sxs-lookup"><span data-stu-id="57458-218">Select **ASP.NET Core Web Application** > **Next**.</span></span>

  ![建立新的 ASP.NET Core Web 應用程式專案](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="57458-220">將專案命名為 **MvcMovie**，然後選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="57458-220">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="57458-221">請務必將專案命名為 **MvcMovie**，以便在複製程式碼時，命名空間得以相符。</span><span class="sxs-lookup"><span data-stu-id="57458-221">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![設定您的新專案](start-mvc/_static/config.png)

* <span data-ttu-id="57458-223">選取 **Web 應用程式 (模型-視圖控制器)**。</span><span class="sxs-lookup"><span data-stu-id="57458-223">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="57458-224">從下拉式方塊中，選取 [ **.Net core** ] 和 [ **ASP.NET Core 3.1**]，然後選取 [ **建立**]。</span><span class="sxs-lookup"><span data-stu-id="57458-224">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

  ![<span data-ttu-id="57458-225">[新增專案] 對話方塊、左窗格中的 [.Net Core]、ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="57458-225">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="57458-226">Visual Studio 已針對建立的 MVC 專案使用預設專案範本。</span><span class="sxs-lookup"><span data-stu-id="57458-226">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="57458-227">建立的專案：</span><span class="sxs-lookup"><span data-stu-id="57458-227">The created project:</span></span>

* <span data-ttu-id="57458-228">是正常運作的應用程式。</span><span class="sxs-lookup"><span data-stu-id="57458-228">Is a working app.</span></span>
* <span data-ttu-id="57458-229">是基本的入門專案。</span><span class="sxs-lookup"><span data-stu-id="57458-229">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="57458-230">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57458-230">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="57458-231">本教學課程假設您已熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="57458-231">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="57458-232">如需詳細資訊，請參閱 [開始使用 VS Code](https://code.visualstudio.com/docs) 和 [Visual Studio Code](#visual-studio-code-help)說明。</span><span class="sxs-lookup"><span data-stu-id="57458-232">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="57458-233">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="57458-233">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="57458-234">將目錄 (`cd`) 變更為包含專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="57458-234">Change directories (`cd`) to a folder that will contain the project.</span></span>
* <span data-ttu-id="57458-235">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="57458-235">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="57458-236">會出現一個對話方塊，其中包含 **建立和偵測所需的資產，但缺少 ' MvcMovie '。要加入它們嗎？**，請選取 **[是]**。</span><span class="sxs-lookup"><span data-stu-id="57458-236">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**.</span></span>

  * <span data-ttu-id="57458-237">`dotnet new mvc -o MvcMovie`：在 *MvcMovie* 資料夾中建立新的 ASP.NET Core MVC 專案。</span><span class="sxs-lookup"><span data-stu-id="57458-237">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="57458-238">`code -r MvcMovie`：載入 Visual Studio Code 中的 *MvcMovie .csproj* 專案檔。</span><span class="sxs-lookup"><span data-stu-id="57458-238">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57458-239">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="57458-239">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="57458-240">選取 [檔案]**[新增解決方案]** > 。</span><span class="sxs-lookup"><span data-stu-id="57458-240">Select **File** > **New Solution**.</span></span>

  ![macOS 新增方案](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="57458-242">在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用**  >  **程式 Web 應用程式] (模型-視圖控制器)**  >  **下一步]**。</span><span class="sxs-lookup"><span data-stu-id="57458-242">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="57458-243">在8.6 版或更新版本中，選取 [ **web] 和 [主控台**  >  **應用**  >  **程式 web 應用程式] (模型-視圖控制器)**  >  **下一步]**。</span><span class="sxs-lookup"><span data-stu-id="57458-243">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS web 應用程式範本選取專案](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="57458-245">在 [ **設定新的 Web 應用程式** ] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="57458-245">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="57458-246">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="57458-246">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="57458-247">如果出現選取 **目標 Framework** 的選項，請選取最新的3.x 版。</span><span class="sxs-lookup"><span data-stu-id="57458-247">If an option to select a **Target Framework** is presented, select the latest 3.x version.</span></span>
  * <span data-ttu-id="57458-248">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="57458-248">Select **Next**.</span></span>

* <span data-ttu-id="57458-249">將專案命名為 **MvcMovie**，然後選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="57458-249">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS 為專案命名](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="57458-251">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="57458-251">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57458-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57458-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="57458-253">選取 Ctrl + F5 以執行應用程式，而不進行任何偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="57458-253">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="57458-254">Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="57458-254">Visual Studio:</span></span>

  * <span data-ttu-id="57458-255">啟動 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)。</span><span class="sxs-lookup"><span data-stu-id="57458-255">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="57458-256">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="57458-256">Runs the app.</span></span>

  <span data-ttu-id="57458-257">位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="57458-257">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="57458-258">本機電腦的標準主機名稱為 `localhost` 。</span><span class="sxs-lookup"><span data-stu-id="57458-258">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="57458-259">當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。</span><span class="sxs-lookup"><span data-stu-id="57458-259">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="57458-260">藉由選取 Ctrl + F5 啟動應用程式而不進行偵錯工具，可讓您：</span><span class="sxs-lookup"><span data-stu-id="57458-260">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="57458-261">變更程式碼。</span><span class="sxs-lookup"><span data-stu-id="57458-261">Make code changes.</span></span>
* <span data-ttu-id="57458-262">儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="57458-262">Save the file.</span></span>
* <span data-ttu-id="57458-263">快速重新整理瀏覽器並查看程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="57458-263">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="57458-264">您可以從 [偵錯] 功能表項目的偵錯或非偵錯模式中啟動應用程式：</span><span class="sxs-lookup"><span data-stu-id="57458-264">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![[偵錯] 功能表](start-mvc/_static/debug_menu.png)

<span data-ttu-id="57458-266">您可以選取 [IIS Express] 按鈕偵錯應用程式</span><span class="sxs-lookup"><span data-stu-id="57458-266">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="57458-268">下圖顯示應用程式：</span><span class="sxs-lookup"><span data-stu-id="57458-268">The following image shows the app:</span></span>

![Home 或 Index 頁面](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="57458-270">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57458-270">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="57458-271">選取 Ctrl + F5 以執行應用程式，而不進行任何偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="57458-271">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="57458-272">Visual Studio Code：</span><span class="sxs-lookup"><span data-stu-id="57458-272">Visual Studio Code:</span></span>

  * <span data-ttu-id="57458-273">開始 [Kestrel](xref:fundamentals/servers/kestrel)</span><span class="sxs-lookup"><span data-stu-id="57458-273">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="57458-274">啟動瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="57458-274">Launches a browser.</span></span>
  * <span data-ttu-id="57458-275">流覽至 `https://localhost:5001` 。</span><span class="sxs-lookup"><span data-stu-id="57458-275">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="57458-276">位址列會顯示 `localhost:port:5001`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="57458-276">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="57458-277">本機電腦的標準主機名稱為 `localhost` 。</span><span class="sxs-lookup"><span data-stu-id="57458-277">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="57458-278">Localhost 只會為來自本機電腦的 Web 要求提供服務。</span><span class="sxs-lookup"><span data-stu-id="57458-278">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="57458-279">藉由選取 Ctrl + F5 啟動應用程式而不進行偵錯工具，可讓您：</span><span class="sxs-lookup"><span data-stu-id="57458-279">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="57458-280">變更程式碼。</span><span class="sxs-lookup"><span data-stu-id="57458-280">Make code changes.</span></span>
* <span data-ttu-id="57458-281">儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="57458-281">Save the file.</span></span>
* <span data-ttu-id="57458-282">快速重新整理瀏覽器並查看程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="57458-282">Quickly refresh the browser and see the code changes.</span></span>

  ![Home 或 Index 頁面](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57458-284">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="57458-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="57458-285">選取 [執行]**[啟動但不偵錯]** >  來啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="57458-285">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="57458-286">Visual Studio for Mac：啟動 [Kestrel](xref:fundamentals/servers/index#kestrel) 伺服器、啟動瀏覽器，然後流覽至 `http://localhost:port` ，其中 *埠* 是隨機播放的埠號碼。</span><span class="sxs-lookup"><span data-stu-id="57458-286">Visual Studio for Mac: starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

<span data-ttu-id="57458-287">位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="57458-287">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="57458-288">本機電腦的標準主機名稱為 `localhost` 。</span><span class="sxs-lookup"><span data-stu-id="57458-288">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="57458-289">當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。</span><span class="sxs-lookup"><span data-stu-id="57458-289">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="57458-290">當您執行應用程式時，會看到不同的連接埠編號。</span><span class="sxs-lookup"><span data-stu-id="57458-290">When you run the app, you'll see a different port number.</span></span>

<span data-ttu-id="57458-291">您可以從 [執行] 功能表的偵錯或非偵錯模式中啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="57458-291">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="57458-292">下圖顯示應用程式：</span><span class="sxs-lookup"><span data-stu-id="57458-292">The following image shows the app:</span></span>

![Home 或 Index 頁面](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="57458-294">在本教學課程的下一個部分中，您會了解 MVC，並開始撰寫一些程式碼。</span><span class="sxs-lookup"><span data-stu-id="57458-294">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="57458-295">下一步</span><span class="sxs-lookup"><span data-stu-id="57458-295">Next</span></span>](adding-controller.md)

::: moniker-end
