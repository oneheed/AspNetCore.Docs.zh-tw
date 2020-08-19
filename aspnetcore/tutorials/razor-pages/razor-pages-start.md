---
title: 教學課程：開始使用 Razor ASP.NET Core 中的頁面
author: rick-anderson
description: 這一系列的教學課程將說明如何使用 Razor ASP.NET Core 中的頁面。 瞭解如何建立模型、產生頁面的程式碼 Razor 、使用 Entity Framework Core 和 SQL Server 來存取資料、加入搜尋功能、新增輸入驗證，以及使用遷移來更新模型。
ms.author: riande
ms.date: 11/12/2019
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: db05b8243b91a936a6ed72b0445e0770d44df014
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634575"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="42ec9-104">教學課程：開始使用 Razor ASP.NET Core 中的頁面</span><span class="sxs-lookup"><span data-stu-id="42ec9-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="42ec9-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="42ec9-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="42ec9-106">這是一個系列的第一個教學課程，教您建立 ASP.NET Core Razor 頁面 web 應用程式的基本概念。</span><span class="sxs-lookup"><span data-stu-id="42ec9-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="42ec9-107">在本系列結束時，您將會有一個可管理電影資料庫的應用程式。</span><span class="sxs-lookup"><span data-stu-id="42ec9-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="42ec9-108">在本教學課程中，您：</span><span class="sxs-lookup"><span data-stu-id="42ec9-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="42ec9-109">建立 Razor 頁面 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="42ec9-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="42ec9-110">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="42ec9-110">Run the app.</span></span>
> * <span data-ttu-id="42ec9-111">檢查專案檔。</span><span class="sxs-lookup"><span data-stu-id="42ec9-111">Examine the project files.</span></span>

<span data-ttu-id="42ec9-112">在本教學課程結尾，您將會有一個工作 Razor 頁面 web 應用程式，您將在稍後的教學課程中建立此應用程式。</span><span class="sxs-lookup"><span data-stu-id="42ec9-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Home 或 Index 頁面](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="42ec9-114">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="42ec9-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="42ec9-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42ec9-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="42ec9-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="42ec9-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="42ec9-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="42ec9-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="42ec9-118">建立 Razor 頁面 web 應用程式</span><span class="sxs-lookup"><span data-stu-id="42ec9-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="42ec9-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42ec9-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="42ec9-120">從 Visual Studio 的 [檔案]\*\*\*\* 功能表中，選取 [新增]**[專案]** > \*\*\*\* 。</span><span class="sxs-lookup"><span data-stu-id="42ec9-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="42ec9-121">建立新的 ASP.NET Core Web 應用程式並選取 [下一步]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="42ec9-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="42ec9-122">![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="42ec9-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="42ec9-123">將專案命名為\*\* Razor PagesMovie\*\*。</span><span class="sxs-lookup"><span data-stu-id="42ec9-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="42ec9-124">請務必將專案命名為\* Razor PagesMovie\* ，如此一來，當您複製並貼上程式碼時，命名空間才會相符。</span><span class="sxs-lookup"><span data-stu-id="42ec9-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="42ec9-125">![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="42ec9-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="42ec9-126">在 [ **Web 應用程式**] 下拉式清單中選取**ASP.NET Core 3.1** ，然後選取 [**建立**]。</span><span class="sxs-lookup"><span data-stu-id="42ec9-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="42ec9-128">下列起始專案會隨即建立：</span><span class="sxs-lookup"><span data-stu-id="42ec9-128">The following starter project is created:</span></span>

  ![方案總管](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="42ec9-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="42ec9-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="42ec9-131">開啟[整合式終端機](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="42ec9-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="42ec9-132">變更為將包含專案的目錄 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="42ec9-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="42ec9-133">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="42ec9-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="42ec9-134">此 `dotnet new` 命令會 Razor 在\* Razor PagesMovie\*資料夾中建立新的頁面專案。</span><span class="sxs-lookup"><span data-stu-id="42ec9-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="42ec9-135">此 `code` 命令會在 Visual Studio Code 的目前實例中開啟 [ \* Razor PagesMovie\* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="42ec9-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="42ec9-136">在狀態列的 OmniSharp 火焰圖示變成綠色之後，會有一個對話方塊要求 **從 ' PagesMovie ' 中找不到建立和偵測所需的資產 Razor 。要新增它們嗎？**</span><span class="sxs-lookup"><span data-stu-id="42ec9-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="42ec9-137">選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="42ec9-137">Select **Yes**.</span></span>

  <span data-ttu-id="42ec9-138">*.vscode* 目錄 (其中包含 *launch.json* 和 *tasks.json* 檔案) 會被新增至專案的根目錄。</span><span class="sxs-lookup"><span data-stu-id="42ec9-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="42ec9-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="42ec9-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="42ec9-140">選取 [檔案]\*\* [新增解決方案]\*\* > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="42ec9-140">Select **File** > **New Solution**.</span></span>

  ![macOS 新增方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="42ec9-142">在8.6 版之前的 Visual Studio for Mac 中，請選取 [ **.net Core**  >  **應用**  >  **程式 Web 應用程式**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="42ec9-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="42ec9-143">在8.6 版或更新版本中，請選取 [ **web] 和 [主控台**  >  **應用**  >  **程式 web 應用程式**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="42ec9-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS web 應用程式範本選取專案](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="42ec9-145">在 [ **設定新的 Web 應用程式** ] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="42ec9-145">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="42ec9-146">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="42ec9-146">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="42ec9-147">如果有選取 **目標 Framework**的選項，請選取最新的3.x 版。</span><span class="sxs-lookup"><span data-stu-id="42ec9-147">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="42ec9-148">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="42ec9-148">Select **Next**.</span></span>

* <span data-ttu-id="42ec9-149">將專案命名為\*\* Razor PagesMovie**，然後選取 [**建立\*\*]。</span><span class="sxs-lookup"><span data-stu-id="42ec9-149">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS 為專案命名](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="42ec9-151">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="42ec9-151">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="42ec9-152">檢查專案檔</span><span class="sxs-lookup"><span data-stu-id="42ec9-152">Examine the project files</span></span>

<span data-ttu-id="42ec9-153">以下概要說明主要專案資料夾和檔案，您將在稍後的教學課程中使用這些資料夾和檔案。</span><span class="sxs-lookup"><span data-stu-id="42ec9-153">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="42ec9-154">Pages 資料夾</span><span class="sxs-lookup"><span data-stu-id="42ec9-154">Pages folder</span></span>

<span data-ttu-id="42ec9-155">包含 Razor 頁面和支援檔案。</span><span class="sxs-lookup"><span data-stu-id="42ec9-155">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="42ec9-156">每個 Razor 頁面都是一對檔案：</span><span class="sxs-lookup"><span data-stu-id="42ec9-156">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="42ec9-157">*Cshtml*檔案，其中包含使用語法的 HTML 標籤與 c # 程式碼 Razor 。</span><span class="sxs-lookup"><span data-stu-id="42ec9-157">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="42ec9-158">*.cshtml.cs* 檔案，其中包含處理頁面事件的 C# 程式碼。</span><span class="sxs-lookup"><span data-stu-id="42ec9-158">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="42ec9-159">支援檔案的名稱以底線開頭。</span><span class="sxs-lookup"><span data-stu-id="42ec9-159">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="42ec9-160">例如， *_Layout cshtml* 檔案會設定所有頁面通用的 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="42ec9-160">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="42ec9-161">此檔案會設定頁面頂端的導覽功能表和頁面底部的著作權標示。</span><span class="sxs-lookup"><span data-stu-id="42ec9-161">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="42ec9-162">如需詳細資訊，請參閱<xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="42ec9-162">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="42ec9-163">wwwroot 資料夾</span><span class="sxs-lookup"><span data-stu-id="42ec9-163">wwwroot folder</span></span>

<span data-ttu-id="42ec9-164">包含靜態檔案，例如 HTML 檔案、JavaScript 檔案和 CSS 檔案。</span><span class="sxs-lookup"><span data-stu-id="42ec9-164">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="42ec9-165">如需詳細資訊，請參閱<xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="42ec9-165">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="42ec9-166">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="42ec9-166">appSettings.json</span></span>

<span data-ttu-id="42ec9-167">包含組態資料，例如連接字串。</span><span class="sxs-lookup"><span data-stu-id="42ec9-167">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="42ec9-168">如需詳細資訊，請參閱<xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="42ec9-168">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="42ec9-169">Program.cs</span><span class="sxs-lookup"><span data-stu-id="42ec9-169">Program.cs</span></span>

<span data-ttu-id="42ec9-170">包含程式的進入點。</span><span class="sxs-lookup"><span data-stu-id="42ec9-170">Contains the entry point for the program.</span></span> <span data-ttu-id="42ec9-171">如需詳細資訊，請參閱<xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="42ec9-171">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="42ec9-172">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="42ec9-172">Startup.cs</span></span>

<span data-ttu-id="42ec9-173">包含設定應用程式行為的程式碼。</span><span class="sxs-lookup"><span data-stu-id="42ec9-173">Contains code that configures app behavior.</span></span> <span data-ttu-id="42ec9-174">如需詳細資訊，請參閱<xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="42ec9-174">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="42ec9-175">後續步驟</span><span class="sxs-lookup"><span data-stu-id="42ec9-175">Next steps</span></span>

<span data-ttu-id="42ec9-176">前進到系列中的下一個教學課程：</span><span class="sxs-lookup"><span data-stu-id="42ec9-176">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="42ec9-177">新增模型</span><span class="sxs-lookup"><span data-stu-id="42ec9-177">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="42ec9-178">這是本系列的第一個教學課程。</span><span class="sxs-lookup"><span data-stu-id="42ec9-178">This is the first tutorial of a series.</span></span> <span data-ttu-id="42ec9-179">[此系列](xref:tutorials/razor-pages/index) 會教您建立 ASP.NET Core Razor 頁面 web 應用程式的基本概念。</span><span class="sxs-lookup"><span data-stu-id="42ec9-179">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="42ec9-180">在本系列結束時，您將會有一個可管理電影資料庫的應用程式。</span><span class="sxs-lookup"><span data-stu-id="42ec9-180">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="42ec9-181">在本教學課程中，您：</span><span class="sxs-lookup"><span data-stu-id="42ec9-181">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="42ec9-182">建立 Razor 頁面 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="42ec9-182">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="42ec9-183">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="42ec9-183">Run the app.</span></span>
> * <span data-ttu-id="42ec9-184">檢查專案檔。</span><span class="sxs-lookup"><span data-stu-id="42ec9-184">Examine the project files.</span></span>

<span data-ttu-id="42ec9-185">在本教學課程結尾，您將會有一個工作 Razor 頁面 web 應用程式，您將在稍後的教學課程中建立此應用程式。</span><span class="sxs-lookup"><span data-stu-id="42ec9-185">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Home 或 Index 頁面](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="42ec9-187">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="42ec9-187">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="42ec9-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42ec9-188">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="42ec9-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="42ec9-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="42ec9-190">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="42ec9-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="42ec9-191">建立 Razor 頁面 web 應用程式</span><span class="sxs-lookup"><span data-stu-id="42ec9-191">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="42ec9-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42ec9-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="42ec9-193">從 Visual Studio 的 [檔案]\*\*\*\* 功能表中，選取 [新增]**[專案]** > \*\*\*\* 。</span><span class="sxs-lookup"><span data-stu-id="42ec9-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="42ec9-194">建立新的 ASP.NET Core Web 應用程式並選取 [下一步]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="42ec9-194">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="42ec9-196">將專案命名為\*\* Razor PagesMovie\*\*。</span><span class="sxs-lookup"><span data-stu-id="42ec9-196">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="42ec9-197">請務必將專案命名為\* Razor PagesMovie\* ，如此一來，當您複製並貼上程式碼時，命名空間才會相符。</span><span class="sxs-lookup"><span data-stu-id="42ec9-197">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/config.png)

* <span data-ttu-id="42ec9-199">在下拉式清單中選取 [ASP.NET Core 2.2]\*\*\*\*，然後選取 [Web 應用程式]\*\*\*\* 及 [建立]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="42ec9-199">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="42ec9-201">下列起始專案會隨即建立：</span><span class="sxs-lookup"><span data-stu-id="42ec9-201">The following starter project is created:</span></span>

  ![方案總管](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="42ec9-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="42ec9-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="42ec9-204">開啟[整合式終端機](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="42ec9-204">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="42ec9-205">變更為將包含專案的目錄 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="42ec9-205">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="42ec9-206">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="42ec9-206">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="42ec9-207">此 `dotnet new` 命令會 Razor 在\* Razor PagesMovie\*資料夾中建立新的頁面專案。</span><span class="sxs-lookup"><span data-stu-id="42ec9-207">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="42ec9-208">此 `code` 命令會在 Visual Studio Code 的目前實例中開啟 [ \* Razor PagesMovie\* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="42ec9-208">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="42ec9-209">在狀態列的 OmniSharp 火焰圖示變成綠色之後，會有一個對話方塊要求 **從 ' PagesMovie ' 中找不到建立和偵測所需的資產 Razor 。要新增它們嗎？**</span><span class="sxs-lookup"><span data-stu-id="42ec9-209">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="42ec9-210">選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="42ec9-210">Select **Yes**.</span></span>

  <span data-ttu-id="42ec9-211">*.vscode* 目錄 (其中包含 *launch.json* 和 *tasks.json* 檔案) 會被新增至專案的根目錄。</span><span class="sxs-lookup"><span data-stu-id="42ec9-211">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="42ec9-212">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="42ec9-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="42ec9-213">選取 [檔案]\*\* [新增解決方案]\*\* > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="42ec9-213">Select **File** > **New Solution**.</span></span>

![macOS 新增方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="42ec9-215">在8.6 版之前的 Visual Studio for Mac 中，請選取 [ **.net Core**  >  **應用**  >  **程式 Web 應用程式**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="42ec9-215">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="42ec9-216">在8.6 版或更新版本中，請選取 [ **web] 和 [主控台**  >  **應用**  >  **程式 web 應用程式**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="42ec9-216">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="42ec9-217">在 [ **設定新的 Web 應用程式** ] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="42ec9-217">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="42ec9-218">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="42ec9-218">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="42ec9-219">如果有選取 **目標 Framework**的選項，請選取最新的2.x 版本。</span><span class="sxs-lookup"><span data-stu-id="42ec9-219">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="42ec9-220">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="42ec9-220">Select **Next**.</span></span>

* <span data-ttu-id="42ec9-221">將專案命名為\*\* Razor PagesMovie**，然後選取 [**建立\*\*]。</span><span class="sxs-lookup"><span data-stu-id="42ec9-221">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="42ec9-223">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="42ec9-223">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="42ec9-224">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42ec9-224">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="42ec9-225">按 Ctrl+F5 即可執行而不使用偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="42ec9-225">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="42ec9-226">Visual Studio 會啟動 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)，並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="42ec9-226">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="42ec9-227">位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="42ec9-227">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="42ec9-228">這是因為 `localhost` 是本機電腦的標準主機名稱。</span><span class="sxs-lookup"><span data-stu-id="42ec9-228">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="42ec9-229">Localhost 只會為來自本機電腦的 Web 要求提供服務。</span><span class="sxs-lookup"><span data-stu-id="42ec9-229">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="42ec9-230">當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。</span><span class="sxs-lookup"><span data-stu-id="42ec9-230">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="42ec9-231">在應用程式的首頁上，選取 [接受]\*\*\*\* 同意追蹤。</span><span class="sxs-lookup"><span data-stu-id="42ec9-231">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="42ec9-232">此應用程式不會追蹤個人資訊，但專案範本會包含同意功能，以防您需要同意才符合歐盟的[一般資料保護規定 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="42ec9-232">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home 或 Index 頁面](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="42ec9-234">下圖顯示您同意追蹤之後的應用程式：</span><span class="sxs-lookup"><span data-stu-id="42ec9-234">The following image shows the app after you give consent to tracking:</span></span>

  ![Home 或 Index 頁面](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="42ec9-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="42ec9-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="42ec9-237">按 **Ctrl-F5** 即可執行而不使用偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="42ec9-237">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="42ec9-238">Visual Studio Code 會啟動 [Kestrel](xref:fundamentals/servers/kestrel)、啟動瀏覽器，然後瀏覽至 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="42ec9-238">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="42ec9-239">位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="42ec9-239">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="42ec9-240">這是因為 `localhost` 是本機電腦的標準主機名稱。</span><span class="sxs-lookup"><span data-stu-id="42ec9-240">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="42ec9-241">Localhost 只會為來自本機電腦的 Web 要求提供服務。</span><span class="sxs-lookup"><span data-stu-id="42ec9-241">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="42ec9-242">在應用程式的首頁上，選取 [接受]\*\*\*\* 同意追蹤。</span><span class="sxs-lookup"><span data-stu-id="42ec9-242">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="42ec9-243">此應用程式不會追蹤個人資訊，但專案範本會包含同意功能，以防您需要同意才符合歐盟的[一般資料保護規定 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="42ec9-243">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home 或 Index 頁面](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="42ec9-245">下圖顯示您同意追蹤之後的應用程式：</span><span class="sxs-lookup"><span data-stu-id="42ec9-245">The following image shows the app after you give consent to tracking:</span></span>

  ![Home 或 Index 頁面](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="42ec9-247">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="42ec9-247">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="42ec9-248">按 **Cmd-Opt-F5** 以在不使用偵錯工具的情況下執行。</span><span class="sxs-lookup"><span data-stu-id="42ec9-248">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="42ec9-249">Visual Studio 會啟動 [Kestrel](xref:fundamentals/servers/kestrel)啟動瀏覽器，然後巡覽至 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="42ec9-249">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="42ec9-250">在應用程式的首頁上，選取 [接受]\*\*\*\* 同意追蹤。</span><span class="sxs-lookup"><span data-stu-id="42ec9-250">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="42ec9-251">此應用程式不會追蹤個人資訊，但專案範本會包含同意功能，以防您需要同意才符合歐盟的[一般資料保護規定 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="42ec9-251">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home 或 Index 頁面](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="42ec9-253">下圖顯示您同意追蹤之後的應用程式：</span><span class="sxs-lookup"><span data-stu-id="42ec9-253">The following image shows the app after you give consent to tracking:</span></span>

  ![Home 或 Index 頁面](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="42ec9-255">檢查專案檔</span><span class="sxs-lookup"><span data-stu-id="42ec9-255">Examine the project files</span></span>

<span data-ttu-id="42ec9-256">以下概要說明主要專案資料夾和檔案，您將在稍後的教學課程中使用這些資料夾和檔案。</span><span class="sxs-lookup"><span data-stu-id="42ec9-256">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="42ec9-257">Pages 資料夾</span><span class="sxs-lookup"><span data-stu-id="42ec9-257">Pages folder</span></span>

<span data-ttu-id="42ec9-258">包含 Razor 頁面和支援檔案。</span><span class="sxs-lookup"><span data-stu-id="42ec9-258">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="42ec9-259">每個 Razor 頁面都是一對檔案：</span><span class="sxs-lookup"><span data-stu-id="42ec9-259">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="42ec9-260">*Cshtml*檔案，其中包含使用語法的 HTML 標籤與 c # 程式碼 Razor 。</span><span class="sxs-lookup"><span data-stu-id="42ec9-260">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="42ec9-261">*.cshtml.cs* 檔案，其中包含處理頁面事件的 C# 程式碼。</span><span class="sxs-lookup"><span data-stu-id="42ec9-261">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="42ec9-262">支援檔案的名稱以底線開頭。</span><span class="sxs-lookup"><span data-stu-id="42ec9-262">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="42ec9-263">例如， *_Layout cshtml* 檔案會設定所有頁面通用的 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="42ec9-263">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="42ec9-264">此檔案會設定頁面頂端的導覽功能表和頁面底部的著作權標示。</span><span class="sxs-lookup"><span data-stu-id="42ec9-264">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="42ec9-265">如需詳細資訊，請參閱<xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="42ec9-265">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="42ec9-266">wwwroot 資料夾</span><span class="sxs-lookup"><span data-stu-id="42ec9-266">wwwroot folder</span></span>

<span data-ttu-id="42ec9-267">包含靜態檔案，例如 HTML 檔案、JavaScript 檔案和 CSS 檔案。</span><span class="sxs-lookup"><span data-stu-id="42ec9-267">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="42ec9-268">如需詳細資訊，請參閱<xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="42ec9-268">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="42ec9-269">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="42ec9-269">appSettings.json</span></span>

<span data-ttu-id="42ec9-270">包含組態資料，例如連接字串。</span><span class="sxs-lookup"><span data-stu-id="42ec9-270">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="42ec9-271">如需詳細資訊，請參閱<xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="42ec9-271">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="42ec9-272">Program.cs</span><span class="sxs-lookup"><span data-stu-id="42ec9-272">Program.cs</span></span>

<span data-ttu-id="42ec9-273">包含程式的進入點。</span><span class="sxs-lookup"><span data-stu-id="42ec9-273">Contains the entry point for the program.</span></span> <span data-ttu-id="42ec9-274">如需詳細資訊，請參閱<xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="42ec9-274">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="42ec9-275">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="42ec9-275">Startup.cs</span></span>

<span data-ttu-id="42ec9-276">包含設定應用程式行為的程式碼，例如是否需要的同意 cookie 。</span><span class="sxs-lookup"><span data-stu-id="42ec9-276">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="42ec9-277">如需詳細資訊，請參閱<xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="42ec9-277">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="42ec9-278">其他資源</span><span class="sxs-lookup"><span data-stu-id="42ec9-278">Additional resources</span></span>

* [<span data-ttu-id="42ec9-279">這個教學課程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="42ec9-279">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="42ec9-280">後續步驟</span><span class="sxs-lookup"><span data-stu-id="42ec9-280">Next steps</span></span>

<span data-ttu-id="42ec9-281">前進到系列中的下一個教學課程：</span><span class="sxs-lookup"><span data-stu-id="42ec9-281">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="42ec9-282">新增模型</span><span class="sxs-lookup"><span data-stu-id="42ec9-282">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
