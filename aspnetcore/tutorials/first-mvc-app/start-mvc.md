---
title: ASP.NET Core MVC 使用者入門
author: rick-anderson
description: 了解如何開始使用 ASP.NET Core MVC。
ms.author: riande
ms.date: 10/16/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: cf17aaf8eff342c378536d4f635e09b936459bee
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052899"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="58a59-103">ASP.NET Core MVC 使用者入門</span><span class="sxs-lookup"><span data-stu-id="58a59-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="58a59-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="58a59-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="58a59-105">本教學課程將教導您建置 ASP.NET Core MVC Web 應用程式的基本概念。</span><span class="sxs-lookup"><span data-stu-id="58a59-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="58a59-106">此應用程式會管理電影標題的資料庫。</span><span class="sxs-lookup"><span data-stu-id="58a59-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="58a59-107">您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="58a59-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="58a59-108">建立 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="58a59-108">Create a web app.</span></span>
> * <span data-ttu-id="58a59-109">新增並建構模型。</span><span class="sxs-lookup"><span data-stu-id="58a59-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="58a59-110">使用資料庫。</span><span class="sxs-lookup"><span data-stu-id="58a59-110">Work with a database.</span></span>
> * <span data-ttu-id="58a59-111">新增搜尋和驗證。</span><span class="sxs-lookup"><span data-stu-id="58a59-111">Add search and validation.</span></span>

<span data-ttu-id="58a59-112">結束時，您將會有一個可管理及顯示電影資料的應用程式。</span><span class="sxs-lookup"><span data-stu-id="58a59-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="58a59-113">必要條件</span><span class="sxs-lookup"><span data-stu-id="58a59-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58a59-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58a59-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="58a59-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58a59-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58a59-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="58a59-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="58a59-117">建立 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="58a59-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58a59-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58a59-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="58a59-119">從 Visual Studio 中，選取 [建立新專案]  。</span><span class="sxs-lookup"><span data-stu-id="58a59-119">From the Visual Studio select **Create a new project** .</span></span>

* <span data-ttu-id="58a59-120">選取 **ASP.NET Core Web Application** > **[下一步]** ASP.NET Core Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="58a59-120">Select **ASP.NET Core Web Application** > **Next** .</span></span>

![新增 ASP.NET Core Web 應用程式](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="58a59-122">將專案命名為  。</span><span class="sxs-lookup"><span data-stu-id="58a59-122">Name the project **MvcMovie** and select **Create** .</span></span> <span data-ttu-id="58a59-123">請務必將專案命名為 **MvcMovie** ，以便在複製程式碼時，命名空間得以相符。</span><span class="sxs-lookup"><span data-stu-id="58a59-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新增 ASP.NET Core Web 應用程式](start-mvc/_static/config.png)

* <span data-ttu-id="58a59-125">選取 **Web 應用程式 (模型-視圖控制器)** 。</span><span class="sxs-lookup"><span data-stu-id="58a59-125">Select **Web Application(Model-View-Controller)** .</span></span> <span data-ttu-id="58a59-126">從下拉式方塊中，選取 [ **.Net Core** ] 和 [ **ASP.NET Core 3.1** ]，然後選取 [ **建立** ]。</span><span class="sxs-lookup"><span data-stu-id="58a59-126">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1** , then select **Create** .</span></span>

![<span data-ttu-id="58a59-127">[新增專案] 對話方塊、左窗格中的 [.Net Core]、ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="58a59-127">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="58a59-128">Visual Studio 在您剛才建立的 MVC 專案中使用了預設範本。</span><span class="sxs-lookup"><span data-stu-id="58a59-128">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="58a59-129">您只要輸入專案名稱，然後選取幾個選項，就立刻會有工作中的應用程式。</span><span class="sxs-lookup"><span data-stu-id="58a59-129">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="58a59-130">這是基本的入門專案。</span><span class="sxs-lookup"><span data-stu-id="58a59-130">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="58a59-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58a59-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="58a59-132">本教學課程假設您熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="58a59-132">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="58a59-133">如需詳細資訊，請參閱 [VS Code 使用者入門](https://code.visualstudio.com/docs)和 [Visual Studio Code 說明](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="58a59-133">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="58a59-134">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="58a59-134">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="58a59-135">將目錄 (`cd`) 變更為其中包含專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="58a59-135">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="58a59-136">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="58a59-136">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="58a59-137">會出現一個對話方塊，其中包含 **建立和偵測所需的資產，但缺少 ' MvcMovie '。要新增它們嗎？**</span><span class="sxs-lookup"><span data-stu-id="58a59-137">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="58a59-138">選取 [是]</span><span class="sxs-lookup"><span data-stu-id="58a59-138">Select **Yes**</span></span>

  * <span data-ttu-id="58a59-139">`dotnet new mvc -o MvcMovie`：在 *MvcMovie* 資料夾中建立新的 ASP.NET Core MVC 專案。</span><span class="sxs-lookup"><span data-stu-id="58a59-139">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="58a59-140">`code -r MvcMovie`：載入 Visual Studio Code 中的 *MvcMovie .csproj* 專案檔。</span><span class="sxs-lookup"><span data-stu-id="58a59-140">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58a59-141">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="58a59-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="58a59-142">選取 [檔案]  。</span><span class="sxs-lookup"><span data-stu-id="58a59-142">Select **File** > **New Solution** .</span></span>

  ![macOS 新增方案](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="58a59-144">在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用**  >  **程式 Web 應用程式] (模型-視圖控制器)**  >  **下一步]** 。</span><span class="sxs-lookup"><span data-stu-id="58a59-144">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next** .</span></span> <span data-ttu-id="58a59-145">在8.6 版或更新版本中，選取 [ **web] 和 [主控台**  >  **應用**  >  **程式 web 應用程式] (模型-視圖控制器)**  >  **下一步]** 。</span><span class="sxs-lookup"><span data-stu-id="58a59-145">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next** .</span></span>

  ![macOS web 應用程式範本選取專案](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="58a59-147">在 [ **設定新的 Web 應用程式** ] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="58a59-147">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="58a59-148">確認 [ **驗證** ] 設定為 [ **無驗證** ]。</span><span class="sxs-lookup"><span data-stu-id="58a59-148">Confirm that **Authentication** is set to **No Authentication** .</span></span>
  * <span data-ttu-id="58a59-149">如果有選取 **目標 Framework** 的選項，請選取最新的3.x 版。</span><span class="sxs-lookup"><span data-stu-id="58a59-149">If presented an option to select a **Target Framework** , select the latest 3.x version.</span></span>

  <span data-ttu-id="58a59-150">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="58a59-150">Select **Next** .</span></span>

* <span data-ttu-id="58a59-151">將專案命名為  。</span><span class="sxs-lookup"><span data-stu-id="58a59-151">Name the project **MvcMovie** , and then select **Create** .</span></span>

  ![macOS 為專案命名](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="58a59-153">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="58a59-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58a59-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58a59-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58a59-155">選取 **Ctrl-F5** 以非偵錯模式執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="58a59-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="58a59-156">Visual Studio 會啟動 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)，並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="58a59-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="58a59-157">請注意，位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="58a59-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="58a59-158">這是因為 `localhost` 是本機電腦的標準主機名稱。</span><span class="sxs-lookup"><span data-stu-id="58a59-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="58a59-159">當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。</span><span class="sxs-lookup"><span data-stu-id="58a59-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="58a59-160">使用 Ctrl + F5 (非偵錯模式) 啟動應用程式，可讓您變更程式碼、儲存檔案、重新整理瀏覽器，以及查看程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="58a59-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="58a59-161">許多開發人員偏好使用非偵錯模式來快速啟動應用程式並檢視變更。</span><span class="sxs-lookup"><span data-stu-id="58a59-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="58a59-162">您可以從 [偵錯]  功能表項目的偵錯或非偵錯模式中啟動應用程式：</span><span class="sxs-lookup"><span data-stu-id="58a59-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![[偵錯] 功能表](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="58a59-164">您可以選取 [IIS Express]  按鈕偵錯應用程式</span><span class="sxs-lookup"><span data-stu-id="58a59-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="58a59-166">下圖顯示應用程式：</span><span class="sxs-lookup"><span data-stu-id="58a59-166">The following image shows the app:</span></span>

  ![Home 或 Index 頁面](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="58a59-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58a59-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="58a59-169">按 Ctrl+F5 即可執行而不使用偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="58a59-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="58a59-170">Visual Studio Code 會啟動 [Kestrel](xref:fundamentals/servers/kestrel)、啟動瀏覽器，然後瀏覽至 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="58a59-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="58a59-171">位址列會顯示 `localhost:port:5001`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="58a59-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="58a59-172">這是因為 `localhost` 是本機電腦的標準主機名稱。</span><span class="sxs-lookup"><span data-stu-id="58a59-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="58a59-173">Localhost 只會為來自本機電腦的 Web 要求提供服務。</span><span class="sxs-lookup"><span data-stu-id="58a59-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="58a59-174">使用 Ctrl + F5 (非偵錯模式) 啟動應用程式，可讓您變更程式碼、儲存檔案、重新整理瀏覽器，以及查看程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="58a59-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="58a59-175">許多開發人員想要使用非偵錯模式來重新整理頁面並檢視變更。</span><span class="sxs-lookup"><span data-stu-id="58a59-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Home 或 Index 頁面](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58a59-177">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="58a59-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="58a59-178">選取 [ **執行**  >  **啟動但不進行調試** 程式] 以啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="58a59-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="58a59-179">Visual Studio for Mac 會啟動 [Kestrel](xref:fundamentals/servers/index#kestrel) 伺服器、啟動瀏覽器，然後巡覽至 `http://localhost:port`，其中 *port* 是隨機選擇的連接埠號碼。</span><span class="sxs-lookup"><span data-stu-id="58a59-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="58a59-180">位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="58a59-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="58a59-181">這是因為 `localhost` 是本機電腦的標準主機名稱。</span><span class="sxs-lookup"><span data-stu-id="58a59-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="58a59-182">當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。</span><span class="sxs-lookup"><span data-stu-id="58a59-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="58a59-183">當您執行應用程式時，會看到不同的連接埠編號。</span><span class="sxs-lookup"><span data-stu-id="58a59-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="58a59-184">您可以從 [執行]  功能表的偵錯或非偵錯模式中啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="58a59-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="58a59-185">下圖顯示應用程式：</span><span class="sxs-lookup"><span data-stu-id="58a59-185">The following image shows the app:</span></span>

  ![Home 或 Index 頁面](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="58a59-187">在本教學課程的下一個部分中，您會了解 MVC，並開始撰寫一些程式碼。</span><span class="sxs-lookup"><span data-stu-id="58a59-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="58a59-188">下一步</span><span class="sxs-lookup"><span data-stu-id="58a59-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="58a59-189">本教學課程將教導您建置 ASP.NET Core MVC Web 應用程式的基本概念。</span><span class="sxs-lookup"><span data-stu-id="58a59-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="58a59-190">此應用程式會管理電影標題的資料庫。</span><span class="sxs-lookup"><span data-stu-id="58a59-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="58a59-191">您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="58a59-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="58a59-192">建立 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="58a59-192">Create a web app.</span></span>
> * <span data-ttu-id="58a59-193">新增並建構模型。</span><span class="sxs-lookup"><span data-stu-id="58a59-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="58a59-194">使用資料庫。</span><span class="sxs-lookup"><span data-stu-id="58a59-194">Work with a database.</span></span>
> * <span data-ttu-id="58a59-195">新增搜尋和驗證。</span><span class="sxs-lookup"><span data-stu-id="58a59-195">Add search and validation.</span></span>

<span data-ttu-id="58a59-196">結束時，您將會有一個可管理及顯示電影資料的應用程式。</span><span class="sxs-lookup"><span data-stu-id="58a59-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="58a59-197">必要條件</span><span class="sxs-lookup"><span data-stu-id="58a59-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58a59-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58a59-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="58a59-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58a59-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58a59-200">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="58a59-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="58a59-201">建立 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="58a59-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58a59-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58a59-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="58a59-203">從 Visual Studio 中，選取 [建立新專案]  。</span><span class="sxs-lookup"><span data-stu-id="58a59-203">From the Visual Studio select **Create a new project** .</span></span>

* <span data-ttu-id="58a59-204">依序選取 [ASP.NET Core Web 應用程式]  和 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="58a59-204">Select **ASP.NET Core Web Application** and then select **Next** .</span></span>

![新增 ASP.NET Core Web 應用程式](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="58a59-206">將專案命名為  。</span><span class="sxs-lookup"><span data-stu-id="58a59-206">Name the project **MvcMovie** and select **Create** .</span></span> <span data-ttu-id="58a59-207">請務必將專案命名為 **MvcMovie** ，以便在複製程式碼時，命名空間得以相符。</span><span class="sxs-lookup"><span data-stu-id="58a59-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新增 ASP.NET Core Web 應用程式](start-mvc/_static/config.png)


* <span data-ttu-id="58a59-209">選取 [Web 應用程式 (Model-View-Controller)]  ，然後選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="58a59-209">Select **Web Application(Model-View-Controller)** , and then select **Create** .</span></span>

![<span data-ttu-id="58a59-210">[新增專案] 對話方塊、左窗格中的 [.Net Core]、ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="58a59-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="58a59-211">Visual Studio 在您剛才建立的 MVC 專案中使用了預設範本。</span><span class="sxs-lookup"><span data-stu-id="58a59-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="58a59-212">您只要輸入專案名稱，然後選取幾個選項，就立刻會有工作中的應用程式。</span><span class="sxs-lookup"><span data-stu-id="58a59-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="58a59-213">這是基本的入門專案，讓我們從這裡開始吧。</span><span class="sxs-lookup"><span data-stu-id="58a59-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="58a59-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58a59-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="58a59-215">本教學課程假設您熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="58a59-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="58a59-216">如需詳細資訊，請參閱 [VS Code 使用者入門](https://code.visualstudio.com/docs)和 [Visual Studio Code 說明](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="58a59-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="58a59-217">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="58a59-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="58a59-218">將目錄 (`cd`) 變更為其中包含專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="58a59-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="58a59-219">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="58a59-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="58a59-220">會出現一個對話方塊，其中包含 **建立和偵測所需的資產，但缺少 ' MvcMovie '。要新增它們嗎？**</span><span class="sxs-lookup"><span data-stu-id="58a59-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="58a59-221">選取 [是]</span><span class="sxs-lookup"><span data-stu-id="58a59-221">Select **Yes**</span></span>

  * <span data-ttu-id="58a59-222">`dotnet new mvc -o MvcMovie`：在 *MvcMovie* 資料夾中建立新的 ASP.NET Core MVC 專案。</span><span class="sxs-lookup"><span data-stu-id="58a59-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="58a59-223">`code -r MvcMovie`：載入 Visual Studio Code 中的 *MvcMovie .csproj* 專案檔。</span><span class="sxs-lookup"><span data-stu-id="58a59-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58a59-224">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="58a59-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="58a59-225">選取 [檔案]  。</span><span class="sxs-lookup"><span data-stu-id="58a59-225">Select **File** > **New Solution** .</span></span>

  ![macOS 新增方案](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="58a59-227">在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用**  >  **程式 Web 應用程式] (模型-視圖控制器)**  >  **下一步]** 。</span><span class="sxs-lookup"><span data-stu-id="58a59-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next** .</span></span> <span data-ttu-id="58a59-228">在8.6 版或更新版本中，選取 [ **web] 和 [主控台**  >  **應用**  >  **程式 web 應用程式] (模型-視圖控制器)**  >  **下一步]** 。</span><span class="sxs-lookup"><span data-stu-id="58a59-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next** .</span></span>

* <span data-ttu-id="58a59-229">在 [ **設定新的 Web 應用程式** ] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="58a59-229">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="58a59-230">確認 [ **驗證** ] 設定為 [ **無驗證** ]。</span><span class="sxs-lookup"><span data-stu-id="58a59-230">Confirm that **Authentication** is set to **No Authentication** .</span></span>
  * <span data-ttu-id="58a59-231">如果有選取 **目標 Framework** 的選項，請選取最新的2.x 版本。</span><span class="sxs-lookup"><span data-stu-id="58a59-231">If presented an option to select a **Target Framework** , select the latest 2.x version.</span></span>

  <span data-ttu-id="58a59-232">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="58a59-232">Select **Next** .</span></span>

* <span data-ttu-id="58a59-233">將專案命名為  。</span><span class="sxs-lookup"><span data-stu-id="58a59-233">Name the project **MvcMovie** , and then select **Create** .</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="58a59-234">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="58a59-234">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58a59-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58a59-235">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58a59-236">選取 **Ctrl-F5** 以非偵錯模式執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="58a59-236">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="58a59-237">Visual Studio 會啟動 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)，並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="58a59-237">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="58a59-238">請注意，位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="58a59-238">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="58a59-239">這是因為 `localhost` 是本機電腦的標準主機名稱。</span><span class="sxs-lookup"><span data-stu-id="58a59-239">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="58a59-240">當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。</span><span class="sxs-lookup"><span data-stu-id="58a59-240">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="58a59-241">使用 Ctrl + F5 (非偵錯模式) 啟動應用程式，可讓您變更程式碼、儲存檔案、重新整理瀏覽器，以及查看程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="58a59-241">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="58a59-242">許多開發人員偏好使用非偵錯模式來快速啟動應用程式並檢視變更。</span><span class="sxs-lookup"><span data-stu-id="58a59-242">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="58a59-243">您可以從 [偵錯]  功能表項目的偵錯或非偵錯模式中啟動應用程式：</span><span class="sxs-lookup"><span data-stu-id="58a59-243">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![[偵錯] 功能表](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="58a59-245">您可以選取 [IIS Express]  按鈕偵錯應用程式</span><span class="sxs-lookup"><span data-stu-id="58a59-245">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="58a59-247">選取 [接受] 以同意追蹤。</span><span class="sxs-lookup"><span data-stu-id="58a59-247">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="58a59-248">此應用程式不會追蹤個人資訊。</span><span class="sxs-lookup"><span data-stu-id="58a59-248">This app doesn't track personal information.</span></span> <span data-ttu-id="58a59-249">範本產生之程式碼所包含的資產有利於滿足[一般資料保護規定 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="58a59-249">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home 或 Index 頁面](start-mvc/_static/privacy.png)

  <span data-ttu-id="58a59-251">下圖顯示接受追蹤之後的應用程式：</span><span class="sxs-lookup"><span data-stu-id="58a59-251">The following image shows the app after accepting tracking:</span></span>

  ![Home 或 Index 頁面](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="58a59-253">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58a59-253">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="58a59-254">按 Ctrl+F5 即可執行而不使用偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="58a59-254">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="58a59-255">Visual Studio Code 會啟動 [Kestrel](xref:fundamentals/servers/kestrel)、啟動瀏覽器，然後瀏覽至 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="58a59-255">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="58a59-256">位址列會顯示 `localhost:port:5001`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="58a59-256">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="58a59-257">這是因為 `localhost` 是本機電腦的標準主機名稱。</span><span class="sxs-lookup"><span data-stu-id="58a59-257">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="58a59-258">Localhost 只會為來自本機電腦的 Web 要求提供服務。</span><span class="sxs-lookup"><span data-stu-id="58a59-258">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="58a59-259">使用 Ctrl + F5 (非偵錯模式) 啟動應用程式，可讓您變更程式碼、儲存檔案、重新整理瀏覽器，以及查看程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="58a59-259">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="58a59-260">許多開發人員想要使用非偵錯模式來重新整理頁面並檢視變更。</span><span class="sxs-lookup"><span data-stu-id="58a59-260">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="58a59-261">選取 [接受] 以同意追蹤。</span><span class="sxs-lookup"><span data-stu-id="58a59-261">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="58a59-262">此應用程式不會追蹤個人資訊。</span><span class="sxs-lookup"><span data-stu-id="58a59-262">This app doesn't track personal information.</span></span> <span data-ttu-id="58a59-263">範本產生之程式碼所包含的資產有利於滿足[一般資料保護規定 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="58a59-263">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home 或 Index 頁面](start-mvc/_static/privacy.png)

  <span data-ttu-id="58a59-265">下圖顯示接受追蹤之後的應用程式：</span><span class="sxs-lookup"><span data-stu-id="58a59-265">The following image shows the app after accepting tracking:</span></span>

  ![Home 或 Index 頁面](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58a59-267">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="58a59-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="58a59-268">選取 [ **執行**  >  **啟動但不進行調試** 程式] 以啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="58a59-268">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="58a59-269">Visual Studio for Mac 會啟動 [Kestrel](xref:fundamentals/servers/index#kestrel) 伺服器、啟動瀏覽器，然後巡覽至 `http://localhost:port`，其中 *port* 是隨機選擇的連接埠號碼。</span><span class="sxs-lookup"><span data-stu-id="58a59-269">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="58a59-270">位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="58a59-270">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="58a59-271">這是因為 `localhost` 是本機電腦的標準主機名稱。</span><span class="sxs-lookup"><span data-stu-id="58a59-271">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="58a59-272">當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。</span><span class="sxs-lookup"><span data-stu-id="58a59-272">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="58a59-273">當您執行應用程式時，會看到不同的連接埠編號。</span><span class="sxs-lookup"><span data-stu-id="58a59-273">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="58a59-274">您可以從 [執行]  功能表的偵錯或非偵錯模式中啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="58a59-274">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="58a59-275">選取 [接受] 以同意追蹤。</span><span class="sxs-lookup"><span data-stu-id="58a59-275">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="58a59-276">此應用程式不會追蹤個人資訊。</span><span class="sxs-lookup"><span data-stu-id="58a59-276">This app doesn't track personal information.</span></span> <span data-ttu-id="58a59-277">範本產生之程式碼所包含的資產有利於滿足[一般資料保護規定 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="58a59-277">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home 或 Index 頁面](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="58a59-279">下圖顯示接受追蹤之後的應用程式：</span><span class="sxs-lookup"><span data-stu-id="58a59-279">The following image shows the app after accepting tracking:</span></span>

  ![Home 或 Index 頁面](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="58a59-281">在本教學課程的下一個部分中，您會了解 MVC，並開始撰寫一些程式碼。</span><span class="sxs-lookup"><span data-stu-id="58a59-281">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="58a59-282">下一步</span><span class="sxs-lookup"><span data-stu-id="58a59-282">Next</span></span>](adding-controller.md)

::: moniker-end
