---
title: ASP.NET Core MVC 使用者入門
author: rick-anderson
description: 了解如何開始使用 ASP.NET Core MVC。
ms.author: riande
ms.date: 11/16/2020
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
ms.openlocfilehash: c96e7107c85bf36f55f6571c71c20d09bc94ddb3
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "94688496"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="02460-103">ASP.NET Core MVC 使用者入門</span><span class="sxs-lookup"><span data-stu-id="02460-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="02460-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="02460-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="02460-105">本教學課程將教導您建置 ASP.NET Core MVC Web 應用程式的基本概念。</span><span class="sxs-lookup"><span data-stu-id="02460-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="02460-106">此應用程式會管理電影標題的資料庫。</span><span class="sxs-lookup"><span data-stu-id="02460-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="02460-107">您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="02460-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="02460-108">建立 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-108">Create a web app.</span></span>
> * <span data-ttu-id="02460-109">新增並建構模型。</span><span class="sxs-lookup"><span data-stu-id="02460-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="02460-110">使用資料庫。</span><span class="sxs-lookup"><span data-stu-id="02460-110">Work with a database.</span></span>
> * <span data-ttu-id="02460-111">新增搜尋和驗證。</span><span class="sxs-lookup"><span data-stu-id="02460-111">Add search and validation.</span></span>

<span data-ttu-id="02460-112">結束時，您將會有一個可管理及顯示電影資料的應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="02460-113">必要條件</span><span class="sxs-lookup"><span data-stu-id="02460-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02460-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02460-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="02460-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02460-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02460-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02460-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="02460-117">建立 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="02460-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02460-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02460-118">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="02460-119">啟動 Visual Studio，然後選取 [建立新專案]。</span><span class="sxs-lookup"><span data-stu-id="02460-119">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="02460-120">在 [ **建立新專案** ] 對話方塊中，選取 [ **ASP.NET Core Web 應用程式** > **]**。</span><span class="sxs-lookup"><span data-stu-id="02460-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="02460-121">在 [ **設定您的新專案** ] 對話方塊中，輸入 [ `MvcMovie` **專案名稱**]。</span><span class="sxs-lookup"><span data-stu-id="02460-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="02460-122">請務必使用此完整名稱（包括大小寫），以便 `namespace` 在複製程式碼時使用每個相符專案。</span><span class="sxs-lookup"><span data-stu-id="02460-122">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="02460-123">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="02460-123">Select **Create**.</span></span>
1. <span data-ttu-id="02460-124">在 [ **建立新的 ASP.NET Core web 應用程式** ] 對話方塊中，選取：</span><span class="sxs-lookup"><span data-stu-id="02460-124">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="02460-125">下拉式清單中的 **.Net Core** 和 **ASP.NET Core 5.0** 。</span><span class="sxs-lookup"><span data-stu-id="02460-125">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="02460-126">**ASP.NET Core Web 應用程式 (模型-視圖控制器)**。</span><span class="sxs-lookup"><span data-stu-id="02460-126">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="02460-127">**建立**</span><span class="sxs-lookup"><span data-stu-id="02460-127">**Create**</span></span>

![<span data-ttu-id="02460-128">建立新的 ASP.NET Core web 應用程式</span><span class="sxs-lookup"><span data-stu-id="02460-128">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="02460-129">如需建立專案的替代方法，請參閱 [在 Visual Studio 中建立新專案](/visualstudio/ide/create-new-project)。</span><span class="sxs-lookup"><span data-stu-id="02460-129">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="02460-130">Visual Studio 在您剛才建立的 MVC 專案中使用了預設範本。</span><span class="sxs-lookup"><span data-stu-id="02460-130">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="02460-131">您只要輸入專案名稱，然後選取幾個選項，就立刻會有工作中的應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-131">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="02460-132">這是基本的入門專案。</span><span class="sxs-lookup"><span data-stu-id="02460-132">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="02460-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02460-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="02460-134">本教學課程假設您已熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="02460-134">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="02460-135">如需詳細資訊，請參閱 [VS Code 使用者入門](https://code.visualstudio.com/docs)和 [Visual Studio Code 說明](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="02460-135">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="02460-136">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="02460-136">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="02460-137">將目錄 (`cd`) 變更為其中包含專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="02460-137">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="02460-138">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="02460-138">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="02460-139">會出現一個對話方塊，其中包含 **建立和偵測所需的資產，但缺少 ' MvcMovie '。要新增它們嗎？**</span><span class="sxs-lookup"><span data-stu-id="02460-139">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="02460-140">選取 [是]</span><span class="sxs-lookup"><span data-stu-id="02460-140">Select **Yes**</span></span>

  * <span data-ttu-id="02460-141">`dotnet new mvc -o MvcMovie`：在 *MvcMovie* 資料夾中建立新的 ASP.NET Core MVC 專案。</span><span class="sxs-lookup"><span data-stu-id="02460-141">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="02460-142">`code -r MvcMovie`：載入 Visual Studio Code 中的 *MvcMovie .csproj* 專案檔。</span><span class="sxs-lookup"><span data-stu-id="02460-142">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02460-143">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02460-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="02460-144">選取 [檔案]**[新增解決方案]** > 。</span><span class="sxs-lookup"><span data-stu-id="02460-144">Select **File** > **New Solution**.</span></span>

  ![macOS 新增方案](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="02460-146">在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用**  >  **程式 Web 應用程式] (模型-視圖控制器)**  >  **下一步]**。</span><span class="sxs-lookup"><span data-stu-id="02460-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="02460-147">在8.6 版或更新版本中，選取 [ **web] 和 [主控台**  >  **應用**  >  **程式 web 應用程式] (模型-視圖控制器)**  >  **下一步]**。</span><span class="sxs-lookup"><span data-stu-id="02460-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS web 應用程式範本選取專案](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="02460-149">在 [ **設定新的 Web 應用程式** ] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="02460-149">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="02460-150">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="02460-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="02460-151">如果有選取 **目標 Framework** 的選項，請選取最新的5.x 版。</span><span class="sxs-lookup"><span data-stu-id="02460-151">If presented an option to select a **Target Framework**, select the latest 5.x version.</span></span>

  <span data-ttu-id="02460-152">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="02460-152">Select **Next**.</span></span>

* <span data-ttu-id="02460-153">將專案命名為 **MvcMovie**，然後選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="02460-153">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS 為專案命名](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="02460-155">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="02460-155">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02460-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02460-156">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="02460-157">選取 **Ctrl-F5** 以非偵錯模式執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-157">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="02460-158">Visual Studio 會啟動 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)，並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-158">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="02460-159">請注意，位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="02460-159">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="02460-160">這是因為 `localhost` 是本機電腦的標準主機名稱。</span><span class="sxs-lookup"><span data-stu-id="02460-160">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="02460-161">當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。</span><span class="sxs-lookup"><span data-stu-id="02460-161">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="02460-162">使用 Ctrl + F5 (非偵錯模式) 啟動應用程式，可讓您變更程式碼、儲存檔案、重新整理瀏覽器，以及查看程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="02460-162">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="02460-163">許多開發人員偏好使用非偵錯模式來快速啟動應用程式並檢視變更。</span><span class="sxs-lookup"><span data-stu-id="02460-163">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="02460-164">您可以從 [偵錯] 功能表項目的偵錯或非偵錯模式中啟動應用程式：</span><span class="sxs-lookup"><span data-stu-id="02460-164">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![[偵錯] 功能表](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="02460-166">您可以選取 [IIS Express] 按鈕偵錯應用程式</span><span class="sxs-lookup"><span data-stu-id="02460-166">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="02460-168">下圖顯示應用程式：</span><span class="sxs-lookup"><span data-stu-id="02460-168">The following image shows the app:</span></span>

  ![Home 或 Index 頁面](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="02460-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02460-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="02460-171">按 Ctrl+F5 即可執行而不使用偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="02460-171">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="02460-172">Visual Studio Code 會啟動 [Kestrel](xref:fundamentals/servers/kestrel)、啟動瀏覽器，然後瀏覽至 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="02460-172">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="02460-173">位址列會顯示 `localhost:port:5001`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="02460-173">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="02460-174">這是因為 `localhost` 是本機電腦的標準主機名稱。</span><span class="sxs-lookup"><span data-stu-id="02460-174">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="02460-175">Localhost 只會為來自本機電腦的 Web 要求提供服務。</span><span class="sxs-lookup"><span data-stu-id="02460-175">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="02460-176">使用 Ctrl + F5 (非偵錯模式) 啟動應用程式，可讓您變更程式碼、儲存檔案、重新整理瀏覽器，以及查看程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="02460-176">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="02460-177">許多開發人員想要使用非偵錯模式來重新整理頁面並檢視變更。</span><span class="sxs-lookup"><span data-stu-id="02460-177">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Home 或 Index 頁面](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02460-179">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02460-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="02460-180">選取 [**執行**  >  **啟動但不進行調試** 程式] 以啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-180">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="02460-181">Visual Studio for Mac 會啟動 [Kestrel](xref:fundamentals/servers/index#kestrel) 伺服器、啟動瀏覽器，然後巡覽至 `http://localhost:port`，其中 *port* 是隨機選擇的連接埠號碼。</span><span class="sxs-lookup"><span data-stu-id="02460-181">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="02460-182">位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="02460-182">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="02460-183">這是因為 `localhost` 是本機電腦的標準主機名稱。</span><span class="sxs-lookup"><span data-stu-id="02460-183">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="02460-184">當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。</span><span class="sxs-lookup"><span data-stu-id="02460-184">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="02460-185">當您執行應用程式時，會看到不同的連接埠編號。</span><span class="sxs-lookup"><span data-stu-id="02460-185">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="02460-186">您可以從 [執行] 功能表的偵錯或非偵錯模式中啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-186">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="02460-187">下圖顯示應用程式：</span><span class="sxs-lookup"><span data-stu-id="02460-187">The following image shows the app:</span></span>

  ![Home 或 Index 頁面](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="02460-189">在本教學課程的下一個部分中，您會了解 MVC，並開始撰寫一些程式碼。</span><span class="sxs-lookup"><span data-stu-id="02460-189">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="02460-190">下一步</span><span class="sxs-lookup"><span data-stu-id="02460-190">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="02460-191">本教學課程將教導您建置 ASP.NET Core MVC Web 應用程式的基本概念。</span><span class="sxs-lookup"><span data-stu-id="02460-191">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="02460-192">此應用程式會管理電影標題的資料庫。</span><span class="sxs-lookup"><span data-stu-id="02460-192">The app manages a database of movie titles.</span></span> <span data-ttu-id="02460-193">您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="02460-193">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="02460-194">建立 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-194">Create a web app.</span></span>
> * <span data-ttu-id="02460-195">新增並建構模型。</span><span class="sxs-lookup"><span data-stu-id="02460-195">Add and scaffold a model.</span></span>
> * <span data-ttu-id="02460-196">使用資料庫。</span><span class="sxs-lookup"><span data-stu-id="02460-196">Work with a database.</span></span>
> * <span data-ttu-id="02460-197">新增搜尋和驗證。</span><span class="sxs-lookup"><span data-stu-id="02460-197">Add search and validation.</span></span>

<span data-ttu-id="02460-198">結束時，您將會有一個可管理及顯示電影資料的應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-198">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="02460-199">必要條件</span><span class="sxs-lookup"><span data-stu-id="02460-199">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02460-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02460-200">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="02460-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02460-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02460-202">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02460-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="02460-203">建立 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="02460-203">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02460-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02460-204">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="02460-205">從 Visual Studio 中，選取 [建立新專案]。</span><span class="sxs-lookup"><span data-stu-id="02460-205">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="02460-206">選取 > **[下一步]** ASP.NET Core Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-206">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![新增 ASP.NET Core Web 應用程式](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="02460-208">將專案命名為 **MvcMovie**，然後選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="02460-208">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="02460-209">請務必將專案命名為 **MvcMovie**，以便在複製程式碼時，命名空間得以相符。</span><span class="sxs-lookup"><span data-stu-id="02460-209">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新增 ASP.NET Core Web 應用程式](start-mvc/_static/config.png)

* <span data-ttu-id="02460-211">選取 **Web 應用程式 (模型-視圖控制器)**。</span><span class="sxs-lookup"><span data-stu-id="02460-211">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="02460-212">從下拉式方塊中，選取 [ **.Net Core** ] 和 [ **ASP.NET Core 3.1**]，然後選取 [ **建立**]。</span><span class="sxs-lookup"><span data-stu-id="02460-212">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

![<span data-ttu-id="02460-213">[新增專案] 對話方塊、左窗格中的 [.Net Core]、ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="02460-213">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="02460-214">Visual Studio 在您剛才建立的 MVC 專案中使用了預設範本。</span><span class="sxs-lookup"><span data-stu-id="02460-214">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="02460-215">您只要輸入專案名稱，然後選取幾個選項，就立刻會有工作中的應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-215">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="02460-216">這是基本的入門專案。</span><span class="sxs-lookup"><span data-stu-id="02460-216">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="02460-217">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02460-217">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="02460-218">本教學課程假設您熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="02460-218">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="02460-219">如需詳細資訊，請參閱 [VS Code 使用者入門](https://code.visualstudio.com/docs)和 [Visual Studio Code 說明](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="02460-219">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="02460-220">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="02460-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="02460-221">將目錄 (`cd`) 變更為其中包含專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="02460-221">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="02460-222">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="02460-222">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="02460-223">會出現一個對話方塊，其中包含 **建立和偵測所需的資產，但缺少 ' MvcMovie '。要新增它們嗎？**</span><span class="sxs-lookup"><span data-stu-id="02460-223">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="02460-224">選取 [是]</span><span class="sxs-lookup"><span data-stu-id="02460-224">Select **Yes**</span></span>

  * <span data-ttu-id="02460-225">`dotnet new mvc -o MvcMovie`：在 *MvcMovie* 資料夾中建立新的 ASP.NET Core MVC 專案。</span><span class="sxs-lookup"><span data-stu-id="02460-225">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="02460-226">`code -r MvcMovie`：載入 Visual Studio Code 中的 *MvcMovie .csproj* 專案檔。</span><span class="sxs-lookup"><span data-stu-id="02460-226">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02460-227">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02460-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="02460-228">選取 [檔案]**[新增解決方案]** > 。</span><span class="sxs-lookup"><span data-stu-id="02460-228">Select **File** > **New Solution**.</span></span>

  ![macOS 新增方案](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="02460-230">在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用**  >  **程式 Web 應用程式] (模型-視圖控制器)**  >  **下一步]**。</span><span class="sxs-lookup"><span data-stu-id="02460-230">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="02460-231">在8.6 版或更新版本中，選取 [ **web] 和 [主控台**  >  **應用**  >  **程式 web 應用程式] (模型-視圖控制器)**  >  **下一步]**。</span><span class="sxs-lookup"><span data-stu-id="02460-231">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS web 應用程式範本選取專案](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="02460-233">在 [ **設定新的 Web 應用程式** ] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="02460-233">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="02460-234">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="02460-234">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="02460-235">如果有選取 **目標 Framework** 的選項，請選取最新的3.x 版。</span><span class="sxs-lookup"><span data-stu-id="02460-235">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="02460-236">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="02460-236">Select **Next**.</span></span>

* <span data-ttu-id="02460-237">將專案命名為 **MvcMovie**，然後選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="02460-237">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS 為專案命名](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="02460-239">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="02460-239">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02460-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02460-240">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="02460-241">選取 **Ctrl-F5** 以非偵錯模式執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-241">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="02460-242">Visual Studio 會啟動 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)，並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-242">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="02460-243">請注意，位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="02460-243">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="02460-244">這是因為 `localhost` 是本機電腦的標準主機名稱。</span><span class="sxs-lookup"><span data-stu-id="02460-244">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="02460-245">當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。</span><span class="sxs-lookup"><span data-stu-id="02460-245">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="02460-246">使用 Ctrl + F5 (非偵錯模式) 啟動應用程式，可讓您變更程式碼、儲存檔案、重新整理瀏覽器，以及查看程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="02460-246">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="02460-247">許多開發人員偏好使用非偵錯模式來快速啟動應用程式並檢視變更。</span><span class="sxs-lookup"><span data-stu-id="02460-247">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="02460-248">您可以從 [偵錯] 功能表項目的偵錯或非偵錯模式中啟動應用程式：</span><span class="sxs-lookup"><span data-stu-id="02460-248">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![[偵錯] 功能表](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="02460-250">您可以選取 [IIS Express] 按鈕偵錯應用程式</span><span class="sxs-lookup"><span data-stu-id="02460-250">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="02460-252">下圖顯示應用程式：</span><span class="sxs-lookup"><span data-stu-id="02460-252">The following image shows the app:</span></span>

  ![Home 或 Index 頁面](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="02460-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02460-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="02460-255">按 Ctrl+F5 即可執行而不使用偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="02460-255">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="02460-256">Visual Studio Code 會啟動 [Kestrel](xref:fundamentals/servers/kestrel)、啟動瀏覽器，然後瀏覽至 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="02460-256">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="02460-257">位址列會顯示 `localhost:port:5001`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="02460-257">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="02460-258">這是因為 `localhost` 是本機電腦的標準主機名稱。</span><span class="sxs-lookup"><span data-stu-id="02460-258">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="02460-259">Localhost 只會為來自本機電腦的 Web 要求提供服務。</span><span class="sxs-lookup"><span data-stu-id="02460-259">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="02460-260">使用 Ctrl + F5 (非偵錯模式) 啟動應用程式，可讓您變更程式碼、儲存檔案、重新整理瀏覽器，以及查看程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="02460-260">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="02460-261">許多開發人員想要使用非偵錯模式來重新整理頁面並檢視變更。</span><span class="sxs-lookup"><span data-stu-id="02460-261">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Home 或 Index 頁面](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02460-263">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02460-263">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="02460-264">選取 [**執行**  >  **啟動但不進行調試** 程式] 以啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-264">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="02460-265">Visual Studio for Mac 會啟動 [Kestrel](xref:fundamentals/servers/index#kestrel) 伺服器、啟動瀏覽器，然後巡覽至 `http://localhost:port`，其中 *port* 是隨機選擇的連接埠號碼。</span><span class="sxs-lookup"><span data-stu-id="02460-265">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="02460-266">位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="02460-266">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="02460-267">這是因為 `localhost` 是本機電腦的標準主機名稱。</span><span class="sxs-lookup"><span data-stu-id="02460-267">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="02460-268">當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。</span><span class="sxs-lookup"><span data-stu-id="02460-268">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="02460-269">當您執行應用程式時，會看到不同的連接埠編號。</span><span class="sxs-lookup"><span data-stu-id="02460-269">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="02460-270">您可以從 [執行] 功能表的偵錯或非偵錯模式中啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-270">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="02460-271">下圖顯示應用程式：</span><span class="sxs-lookup"><span data-stu-id="02460-271">The following image shows the app:</span></span>

  ![Home 或 Index 頁面](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="02460-273">在本教學課程的下一個部分中，您會了解 MVC，並開始撰寫一些程式碼。</span><span class="sxs-lookup"><span data-stu-id="02460-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="02460-274">下一步</span><span class="sxs-lookup"><span data-stu-id="02460-274">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="02460-275">本教學課程將教導您建置 ASP.NET Core MVC Web 應用程式的基本概念。</span><span class="sxs-lookup"><span data-stu-id="02460-275">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="02460-276">此應用程式會管理電影標題的資料庫。</span><span class="sxs-lookup"><span data-stu-id="02460-276">The app manages a database of movie titles.</span></span> <span data-ttu-id="02460-277">您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="02460-277">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="02460-278">建立 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-278">Create a web app.</span></span>
> * <span data-ttu-id="02460-279">新增並建構模型。</span><span class="sxs-lookup"><span data-stu-id="02460-279">Add and scaffold a model.</span></span>
> * <span data-ttu-id="02460-280">使用資料庫。</span><span class="sxs-lookup"><span data-stu-id="02460-280">Work with a database.</span></span>
> * <span data-ttu-id="02460-281">新增搜尋和驗證。</span><span class="sxs-lookup"><span data-stu-id="02460-281">Add search and validation.</span></span>

<span data-ttu-id="02460-282">結束時，您將會有一個可管理及顯示電影資料的應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-282">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="02460-283">必要條件</span><span class="sxs-lookup"><span data-stu-id="02460-283">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02460-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02460-284">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="02460-285">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02460-285">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02460-286">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02460-286">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="02460-287">建立 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="02460-287">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02460-288">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02460-288">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="02460-289">從 Visual Studio 中，選取 [建立新專案]。</span><span class="sxs-lookup"><span data-stu-id="02460-289">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="02460-290">依序選取 [ASP.NET Core Web 應用程式] 和 [下一步]。</span><span class="sxs-lookup"><span data-stu-id="02460-290">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新增 ASP.NET Core Web 應用程式](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="02460-292">將專案命名為 **MvcMovie**，然後選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="02460-292">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="02460-293">請務必將專案命名為 **MvcMovie**，以便在複製程式碼時，命名空間得以相符。</span><span class="sxs-lookup"><span data-stu-id="02460-293">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新增 ASP.NET Core Web 應用程式](start-mvc/_static/config.png)


* <span data-ttu-id="02460-295">選取 [Web 應用程式 (Model-View-Controller)]，然後選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="02460-295">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="02460-296">[新增專案] 對話方塊、左窗格中的 [.Net Core]、ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="02460-296">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="02460-297">Visual Studio 在您剛才建立的 MVC 專案中使用了預設範本。</span><span class="sxs-lookup"><span data-stu-id="02460-297">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="02460-298">您只要輸入專案名稱，然後選取幾個選項，就立刻會有工作中的應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-298">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="02460-299">這是基本的入門專案，讓我們從這裡開始吧。</span><span class="sxs-lookup"><span data-stu-id="02460-299">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="02460-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02460-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="02460-301">本教學課程假設您熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="02460-301">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="02460-302">如需詳細資訊，請參閱 [VS Code 使用者入門](https://code.visualstudio.com/docs)和 [Visual Studio Code 說明](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="02460-302">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="02460-303">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="02460-303">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="02460-304">將目錄 (`cd`) 變更為其中包含專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="02460-304">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="02460-305">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="02460-305">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="02460-306">會出現一個對話方塊，其中包含 **建立和偵測所需的資產，但缺少 ' MvcMovie '。要新增它們嗎？**</span><span class="sxs-lookup"><span data-stu-id="02460-306">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="02460-307">選取 [是]</span><span class="sxs-lookup"><span data-stu-id="02460-307">Select **Yes**</span></span>

  * <span data-ttu-id="02460-308">`dotnet new mvc -o MvcMovie`：在 *MvcMovie* 資料夾中建立新的 ASP.NET Core MVC 專案。</span><span class="sxs-lookup"><span data-stu-id="02460-308">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="02460-309">`code -r MvcMovie`：載入 Visual Studio Code 中的 *MvcMovie .csproj* 專案檔。</span><span class="sxs-lookup"><span data-stu-id="02460-309">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02460-310">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02460-310">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="02460-311">選取 [檔案]**[新增解決方案]** > 。</span><span class="sxs-lookup"><span data-stu-id="02460-311">Select **File** > **New Solution**.</span></span>

  ![macOS 新增方案](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="02460-313">在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用**  >  **程式 Web 應用程式] (模型-視圖控制器)**  >  **下一步]**。</span><span class="sxs-lookup"><span data-stu-id="02460-313">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="02460-314">在8.6 版或更新版本中，選取 [ **web] 和 [主控台**  >  **應用**  >  **程式 web 應用程式] (模型-視圖控制器)**  >  **下一步]**。</span><span class="sxs-lookup"><span data-stu-id="02460-314">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="02460-315">在 [ **設定新的 Web 應用程式** ] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="02460-315">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="02460-316">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="02460-316">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="02460-317">如果有選取 **目標 Framework** 的選項，請選取最新的2.x 版本。</span><span class="sxs-lookup"><span data-stu-id="02460-317">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="02460-318">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="02460-318">Select **Next**.</span></span>

* <span data-ttu-id="02460-319">將專案命名為 **MvcMovie**，然後選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="02460-319">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="02460-320">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="02460-320">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02460-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02460-321">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="02460-322">選取 **Ctrl-F5** 以非偵錯模式執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-322">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="02460-323">Visual Studio 會啟動 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)，並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-323">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="02460-324">請注意，位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="02460-324">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="02460-325">這是因為 `localhost` 是本機電腦的標準主機名稱。</span><span class="sxs-lookup"><span data-stu-id="02460-325">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="02460-326">當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。</span><span class="sxs-lookup"><span data-stu-id="02460-326">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="02460-327">使用 Ctrl + F5 (非偵錯模式) 啟動應用程式，可讓您變更程式碼、儲存檔案、重新整理瀏覽器，以及查看程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="02460-327">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="02460-328">許多開發人員偏好使用非偵錯模式來快速啟動應用程式並檢視變更。</span><span class="sxs-lookup"><span data-stu-id="02460-328">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="02460-329">您可以從 [偵錯] 功能表項目的偵錯或非偵錯模式中啟動應用程式：</span><span class="sxs-lookup"><span data-stu-id="02460-329">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![[偵錯] 功能表](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="02460-331">您可以選取 [IIS Express] 按鈕偵錯應用程式</span><span class="sxs-lookup"><span data-stu-id="02460-331">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="02460-333">選取 [接受] 以同意追蹤。</span><span class="sxs-lookup"><span data-stu-id="02460-333">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="02460-334">此應用程式不會追蹤個人資訊。</span><span class="sxs-lookup"><span data-stu-id="02460-334">This app doesn't track personal information.</span></span> <span data-ttu-id="02460-335">範本產生之程式碼所包含的資產有利於滿足[一般資料保護規定 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="02460-335">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home 或 Index 頁面](start-mvc/_static/privacy.png)

  <span data-ttu-id="02460-337">下圖顯示接受追蹤之後的應用程式：</span><span class="sxs-lookup"><span data-stu-id="02460-337">The following image shows the app after accepting tracking:</span></span>

  ![Home 或 Index 頁面](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="02460-339">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02460-339">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="02460-340">按 Ctrl+F5 即可執行而不使用偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="02460-340">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="02460-341">Visual Studio Code 會啟動 [Kestrel](xref:fundamentals/servers/kestrel)、啟動瀏覽器，然後瀏覽至 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="02460-341">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="02460-342">位址列會顯示 `localhost:port:5001`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="02460-342">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="02460-343">這是因為 `localhost` 是本機電腦的標準主機名稱。</span><span class="sxs-lookup"><span data-stu-id="02460-343">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="02460-344">Localhost 只會為來自本機電腦的 Web 要求提供服務。</span><span class="sxs-lookup"><span data-stu-id="02460-344">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="02460-345">使用 Ctrl + F5 (非偵錯模式) 啟動應用程式，可讓您變更程式碼、儲存檔案、重新整理瀏覽器，以及查看程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="02460-345">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="02460-346">許多開發人員想要使用非偵錯模式來重新整理頁面並檢視變更。</span><span class="sxs-lookup"><span data-stu-id="02460-346">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="02460-347">選取 [接受] 以同意追蹤。</span><span class="sxs-lookup"><span data-stu-id="02460-347">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="02460-348">此應用程式不會追蹤個人資訊。</span><span class="sxs-lookup"><span data-stu-id="02460-348">This app doesn't track personal information.</span></span> <span data-ttu-id="02460-349">範本產生之程式碼所包含的資產有利於滿足[一般資料保護規定 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="02460-349">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home 或 Index 頁面](start-mvc/_static/privacy.png)

  <span data-ttu-id="02460-351">下圖顯示接受追蹤之後的應用程式：</span><span class="sxs-lookup"><span data-stu-id="02460-351">The following image shows the app after accepting tracking:</span></span>

  ![Home 或 Index 頁面](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02460-353">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02460-353">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="02460-354">選取 [**執行**  >  **啟動但不進行調試** 程式] 以啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-354">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="02460-355">Visual Studio for Mac 會啟動 [Kestrel](xref:fundamentals/servers/index#kestrel) 伺服器、啟動瀏覽器，然後巡覽至 `http://localhost:port`，其中 *port* 是隨機選擇的連接埠號碼。</span><span class="sxs-lookup"><span data-stu-id="02460-355">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="02460-356">位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="02460-356">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="02460-357">這是因為 `localhost` 是本機電腦的標準主機名稱。</span><span class="sxs-lookup"><span data-stu-id="02460-357">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="02460-358">當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。</span><span class="sxs-lookup"><span data-stu-id="02460-358">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="02460-359">當您執行應用程式時，會看到不同的連接埠編號。</span><span class="sxs-lookup"><span data-stu-id="02460-359">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="02460-360">您可以從 [執行] 功能表的偵錯或非偵錯模式中啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="02460-360">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="02460-361">選取 [接受] 以同意追蹤。</span><span class="sxs-lookup"><span data-stu-id="02460-361">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="02460-362">此應用程式不會追蹤個人資訊。</span><span class="sxs-lookup"><span data-stu-id="02460-362">This app doesn't track personal information.</span></span> <span data-ttu-id="02460-363">範本產生之程式碼所包含的資產有利於滿足[一般資料保護規定 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="02460-363">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home 或 Index 頁面](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="02460-365">下圖顯示接受追蹤之後的應用程式：</span><span class="sxs-lookup"><span data-stu-id="02460-365">The following image shows the app after accepting tracking:</span></span>

  ![Home 或 Index 頁面](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="02460-367">在本教學課程的下一個部分中，您會了解 MVC，並開始撰寫一些程式碼。</span><span class="sxs-lookup"><span data-stu-id="02460-367">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="02460-368">下一步</span><span class="sxs-lookup"><span data-stu-id="02460-368">Next</span></span>](adding-controller.md)

::: moniker-end
