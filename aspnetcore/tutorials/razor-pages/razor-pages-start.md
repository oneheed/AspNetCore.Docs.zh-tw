---
title: 教學課程：開始使用 Razor ASP.NET Core 中的頁面
author: rick-anderson
description: 這是一個系列的第一個教學課程，教您建立 ASP.NET Core Razor 頁面 web 應用程式的基本概念。
ms.author: riande
ms.date: 09/15/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 4d4e50f8acea73859f5e839616f13f90a42291c4
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486222"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="35ac5-103">教學課程：開始使用 Razor ASP.NET Core 中的頁面</span><span class="sxs-lookup"><span data-stu-id="35ac5-103">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="35ac5-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="35ac5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="35ac5-105">這是一個系列的第一個教學課程，教您建立 ASP.NET Core Razor 頁面 web 應用程式的基本概念。</span><span class="sxs-lookup"><span data-stu-id="35ac5-105">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="35ac5-106">如需更高階的簡介，適用于熟悉控制器和 views 的開發人員，請參閱 [ Razor 頁面簡介](xref:razor-pages/index)。</span><span class="sxs-lookup"><span data-stu-id="35ac5-106">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="35ac5-107">在本系列結束時，您將會有一個可管理電影資料庫的應用程式。</span><span class="sxs-lookup"><span data-stu-id="35ac5-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="35ac5-108">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="35ac5-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="35ac5-109">在本教學課程中，您：</span><span class="sxs-lookup"><span data-stu-id="35ac5-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="35ac5-110">建立 Razor 頁面 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="35ac5-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="35ac5-111">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="35ac5-111">Run the app.</span></span>
> * <span data-ttu-id="35ac5-112">檢查專案檔。</span><span class="sxs-lookup"><span data-stu-id="35ac5-112">Examine the project files.</span></span>

<span data-ttu-id="35ac5-113">在本教學課程結尾，您將會有一個工作 Razor 頁面 web 應用程式，您將在稍後的教學課程中加以增強。</span><span class="sxs-lookup"><span data-stu-id="35ac5-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll enhance in later tutorials.</span></span>

![Home 或：：：非 loc (Index) ：：： page](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a><span data-ttu-id="35ac5-115">必要條件</span><span class="sxs-lookup"><span data-stu-id="35ac5-115">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35ac5-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35ac5-116">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="35ac5-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="35ac5-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="35ac5-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="35ac5-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="35ac5-119">建立 Razor 頁面 web 應用程式</span><span class="sxs-lookup"><span data-stu-id="35ac5-119">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35ac5-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35ac5-120">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="35ac5-121">啟動 Visual Studio，然後選取 [建立新專案]。</span><span class="sxs-lookup"><span data-stu-id="35ac5-121">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="35ac5-122">如需詳細資訊，請參閱 [在 Visual Studio 中建立新專案](/visualstudio/ide/create-new-project)。</span><span class="sxs-lookup"><span data-stu-id="35ac5-122">For more information, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

   ![從 [開始] 視窗建立新專案](razor-pages-start/_static/5/start-window-create-new-project.png)

1. <span data-ttu-id="35ac5-124">在 [建立新專案] 對話方塊中，選取 [ASP.NET Core Web 應用程式]，然後選取 [下一步]。</span><span class="sxs-lookup"><span data-stu-id="35ac5-124">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

    ![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/5/np.png)
    
1. <span data-ttu-id="35ac5-126">在 [ **設定您的新專案** ] 對話方塊中，輸入 [ `RazorPagesMovie` **專案名稱**]。</span><span class="sxs-lookup"><span data-stu-id="35ac5-126">In the **Configure your new project** dialog, enter `RazorPagesMovie` for **Project name**.</span></span> <span data-ttu-id="35ac5-127">請務必將專案命名為 *Razor PagesMovie*，包括符合大小寫，如此一來，當您複製並貼上範例程式碼時，命名空間會相符。</span><span class="sxs-lookup"><span data-stu-id="35ac5-127">It's important to name the project *RazorPagesMovie*, including matching the capitalization, so the namespaces will match when you copy and paste example code.</span></span>

1. <span data-ttu-id="35ac5-128">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="35ac5-128">Select **Create**.</span></span>

    ![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/config.png)

1. <span data-ttu-id="35ac5-130">在 [ **建立新的 ASP.NET Core web 應用程式** ] 對話方塊中，選取：</span><span class="sxs-lookup"><span data-stu-id="35ac5-130">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="35ac5-131">下拉式清單中的 **.Net Core** 和 **ASP.NET Core 5.0** 。</span><span class="sxs-lookup"><span data-stu-id="35ac5-131">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="35ac5-132">**Web 應用程式**。</span><span class="sxs-lookup"><span data-stu-id="35ac5-132">**Web Application**.</span></span>
    1. <span data-ttu-id="35ac5-133">**Create**。</span><span class="sxs-lookup"><span data-stu-id="35ac5-133">**Create**.</span></span>

     ![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/5/npx.png)

    <span data-ttu-id="35ac5-135">下列起始專案會隨即建立：</span><span class="sxs-lookup"><span data-stu-id="35ac5-135">The following starter project is created:</span></span>

    ![方案總管](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="35ac5-137">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="35ac5-137">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="35ac5-138">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="35ac5-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

1. <span data-ttu-id="35ac5-139">變更為將包含專案的目錄 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="35ac5-139">Change to the directory (`cd`) which will contain the project.</span></span>

1. <span data-ttu-id="35ac5-140">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="35ac5-140">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * <span data-ttu-id="35ac5-141">此 `dotnet new` 命令會 Razor 在 *Razor PagesMovie* 資料夾中建立新的頁面專案。</span><span class="sxs-lookup"><span data-stu-id="35ac5-141">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
   * <span data-ttu-id="35ac5-142">此 `code` 命令會在 Visual Studio Code 的目前實例中開啟 [ *Razor PagesMovie* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="35ac5-142">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="35ac5-143">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="35ac5-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="35ac5-144">選取 [檔案]**[新增解決方案]** > 。</span><span class="sxs-lookup"><span data-stu-id="35ac5-144">Select **File** > **New Solution**.</span></span>

    ![macOS 新增方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. <span data-ttu-id="35ac5-146">在8.6 版之前的 Visual Studio for Mac 中，請選取 [ **.net Core**  >  **應用**  >  **程式 Web 應用程式**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="35ac5-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="35ac5-147">在8.6 版或更新版本中，請選取 [ **web] 和 [主控台**  >  **應用**  >  **程式 web 應用程式**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="35ac5-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

    ![macOS web 應用程式範本選取專案](razor-pages-start/_static/web_app_template_vsmac.png)

1. <span data-ttu-id="35ac5-149">在 [ **設定新的 Web 應用程式** ] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="35ac5-149">In the **Configure the new Web Application** dialog:</span></span>

    1. <span data-ttu-id="35ac5-150">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="35ac5-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
    1. <span data-ttu-id="35ac5-151">如果有選取 **目標 Framework** 的選項，請選取最新的 .net 5.x 版本。</span><span class="sxs-lookup"><span data-stu-id="35ac5-151">If presented an option to select a **Target Framework**, select the latest .NET 5.x version.</span></span>
    1. <span data-ttu-id="35ac5-152">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="35ac5-152">Select **Next**.</span></span>

1. <span data-ttu-id="35ac5-153">將專案命名為 *Razor PagesMovie* ，然後選取 [**建立**]。</span><span class="sxs-lookup"><span data-stu-id="35ac5-153">Name the project *RazorPagesMovie* and select **Create**.</span></span>

    ![macOS 為專案命名](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="35ac5-155">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="35ac5-155">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="35ac5-156">檢查專案檔</span><span class="sxs-lookup"><span data-stu-id="35ac5-156">Examine the project files</span></span>

<span data-ttu-id="35ac5-157">以下概要說明主要專案資料夾和檔案，您將在稍後的教學課程中使用這些資料夾和檔案。</span><span class="sxs-lookup"><span data-stu-id="35ac5-157">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="35ac5-158">Pages 資料夾</span><span class="sxs-lookup"><span data-stu-id="35ac5-158">Pages folder</span></span>

<span data-ttu-id="35ac5-159">包含 Razor 頁面和支援檔案。</span><span class="sxs-lookup"><span data-stu-id="35ac5-159">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="35ac5-160">每個 Razor 頁面都是一對檔案：</span><span class="sxs-lookup"><span data-stu-id="35ac5-160">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="35ac5-161">使用語法以 c # 程式碼撰寫 HTML 標籤的 *cshtml 檔案。* Razor</span><span class="sxs-lookup"><span data-stu-id="35ac5-161">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="35ac5-162">具有處理頁面事件之 c # 程式碼的 *cshtml.cs 檔案。*</span><span class="sxs-lookup"><span data-stu-id="35ac5-162">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="35ac5-163">支援檔案的名稱以底線開頭。</span><span class="sxs-lookup"><span data-stu-id="35ac5-163">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="35ac5-164">例如， *_Layout cshtml* 檔案會設定所有頁面通用的 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="35ac5-164">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="35ac5-165">此檔案會設定頁面頂端的導覽功能表和頁面底部的著作權標示。</span><span class="sxs-lookup"><span data-stu-id="35ac5-165">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="35ac5-166">如需詳細資訊，請參閱<xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="35ac5-166">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="35ac5-167">wwwroot 資料夾</span><span class="sxs-lookup"><span data-stu-id="35ac5-167">wwwroot folder</span></span>

<span data-ttu-id="35ac5-168">包含靜態資產，例如 HTML 檔案、JavaScript 檔案和 CSS 檔案。</span><span class="sxs-lookup"><span data-stu-id="35ac5-168">Contains static assets, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="35ac5-169">如需詳細資訊，請參閱<xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="35ac5-169">For more information, see <xref:fundamentals/static-files>.</span></span>

### appsettings.json

<span data-ttu-id="35ac5-170">包含設定資料，例如連接字串。</span><span class="sxs-lookup"><span data-stu-id="35ac5-170">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="35ac5-171">如需詳細資訊，請參閱<xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="35ac5-171">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="35ac5-172">Program.cs</span><span class="sxs-lookup"><span data-stu-id="35ac5-172">Program.cs</span></span>

<span data-ttu-id="35ac5-173">包含應用程式的進入點。</span><span class="sxs-lookup"><span data-stu-id="35ac5-173">Contains the entry point for the app.</span></span> <span data-ttu-id="35ac5-174">如需詳細資訊，請參閱<xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="35ac5-174">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="35ac5-175">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="35ac5-175">Startup.cs</span></span>

<span data-ttu-id="35ac5-176">包含設定應用程式行為的程式碼。</span><span class="sxs-lookup"><span data-stu-id="35ac5-176">Contains code that configures app behavior.</span></span> <span data-ttu-id="35ac5-177">如需詳細資訊，請參閱<xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="35ac5-177">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="35ac5-178">後續步驟</span><span class="sxs-lookup"><span data-stu-id="35ac5-178">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="35ac5-179">下一步：新增模型</span><span class="sxs-lookup"><span data-stu-id="35ac5-179">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="35ac5-180">這是一個系列的第一個教學課程，教您建立 ASP.NET Core Razor 頁面 web 應用程式的基本概念。</span><span class="sxs-lookup"><span data-stu-id="35ac5-180">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="35ac5-181">如需更高階的簡介，適用于熟悉控制器和 views 的開發人員，請參閱 [ Razor 頁面簡介](xref:razor-pages/index)。</span><span class="sxs-lookup"><span data-stu-id="35ac5-181">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="35ac5-182">在本系列結束時，您將會有一個可管理電影資料庫的應用程式。</span><span class="sxs-lookup"><span data-stu-id="35ac5-182">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="35ac5-183">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="35ac5-183">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="35ac5-184">在本教學課程中，您：</span><span class="sxs-lookup"><span data-stu-id="35ac5-184">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="35ac5-185">建立 Razor 頁面 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="35ac5-185">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="35ac5-186">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="35ac5-186">Run the app.</span></span>
> * <span data-ttu-id="35ac5-187">檢查專案檔。</span><span class="sxs-lookup"><span data-stu-id="35ac5-187">Examine the project files.</span></span>

<span data-ttu-id="35ac5-188">在本教學課程結尾，您將會有一個工作 Razor 頁面 web 應用程式，您將在稍後的教學課程中建立此應用程式。</span><span class="sxs-lookup"><span data-stu-id="35ac5-188">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Home 或：：：非 loc (Index) ：：： page](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="35ac5-190">必要條件</span><span class="sxs-lookup"><span data-stu-id="35ac5-190">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35ac5-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35ac5-191">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="35ac5-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="35ac5-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="35ac5-193">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="35ac5-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="35ac5-194">建立 Razor 頁面 web 應用程式</span><span class="sxs-lookup"><span data-stu-id="35ac5-194">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35ac5-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35ac5-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="35ac5-196">從 Visual Studio 的 [檔案] 功能表中，選取 [新增]**[專案]** >  。</span><span class="sxs-lookup"><span data-stu-id="35ac5-196">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="35ac5-197">建立新的 ASP.NET Core Web 應用程式並選取 [下一步]。</span><span class="sxs-lookup"><span data-stu-id="35ac5-197">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="35ac5-198">![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="35ac5-198">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="35ac5-199">將專案命名為 **Razor PagesMovie**。</span><span class="sxs-lookup"><span data-stu-id="35ac5-199">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="35ac5-200">請務必將專案命名為 *Razor PagesMovie* ，如此一來，當您複製並貼上程式碼時，命名空間才會相符。</span><span class="sxs-lookup"><span data-stu-id="35ac5-200">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="35ac5-201">![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="35ac5-201">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="35ac5-202">在 [ **Web 應用程式**] 下拉式清單中選取 **ASP.NET Core 3.1** ，然後選取 [**建立**]。</span><span class="sxs-lookup"><span data-stu-id="35ac5-202">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="35ac5-204">下列起始專案會隨即建立：</span><span class="sxs-lookup"><span data-stu-id="35ac5-204">The following starter project is created:</span></span>

  ![方案總管](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="35ac5-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="35ac5-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="35ac5-207">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="35ac5-207">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="35ac5-208">變更為將包含專案的目錄 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="35ac5-208">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="35ac5-209">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="35ac5-209">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="35ac5-210">此 `dotnet new` 命令會 Razor 在 *Razor PagesMovie* 資料夾中建立新的頁面專案。</span><span class="sxs-lookup"><span data-stu-id="35ac5-210">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="35ac5-211">此 `code` 命令會在 Visual Studio Code 的目前實例中開啟 [ *Razor PagesMovie* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="35ac5-211">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="35ac5-212">在狀態列的 OmniSharp 火焰圖示變成綠色之後，會有一個對話方塊要求 **從 ' PagesMovie ' 中找不到建立和偵測所需的資產 Razor 。要新增它們嗎？**</span><span class="sxs-lookup"><span data-stu-id="35ac5-212">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="35ac5-213">選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="35ac5-213">Select **Yes**.</span></span>

  <span data-ttu-id="35ac5-214">*.vscode* 目錄 (其中包含 *launch.json* 和 *tasks.json* 檔案) 會被新增至專案的根目錄。</span><span class="sxs-lookup"><span data-stu-id="35ac5-214">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="35ac5-215">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="35ac5-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="35ac5-216">選取 [檔案]**[新增解決方案]** > 。</span><span class="sxs-lookup"><span data-stu-id="35ac5-216">Select **File** > **New Solution**.</span></span>

  ![macOS 新增方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="35ac5-218">在8.6 版之前的 Visual Studio for Mac 中，請選取 [ **.net Core**  >  **應用**  >  **程式 Web 應用程式**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="35ac5-218">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="35ac5-219">在8.6 版或更新版本中，請選取 [ **web] 和 [主控台**  >  **應用**  >  **程式 web 應用程式**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="35ac5-219">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS web 應用程式範本選取專案](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="35ac5-221">在 [ **設定新的 Web 應用程式** ] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="35ac5-221">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="35ac5-222">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="35ac5-222">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="35ac5-223">如果有選取 **目標 Framework** 的選項，請選取最新的3.x 版。</span><span class="sxs-lookup"><span data-stu-id="35ac5-223">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="35ac5-224">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="35ac5-224">Select **Next**.</span></span>

* <span data-ttu-id="35ac5-225">將專案命名為 **Razor PagesMovie**，然後選取 [**建立**]。</span><span class="sxs-lookup"><span data-stu-id="35ac5-225">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS 為專案命名](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="35ac5-227">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="35ac5-227">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="35ac5-228">檢查專案檔</span><span class="sxs-lookup"><span data-stu-id="35ac5-228">Examine the project files</span></span>

<span data-ttu-id="35ac5-229">以下概要說明主要專案資料夾和檔案，您將在稍後的教學課程中使用這些資料夾和檔案。</span><span class="sxs-lookup"><span data-stu-id="35ac5-229">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="35ac5-230">Pages 資料夾</span><span class="sxs-lookup"><span data-stu-id="35ac5-230">Pages folder</span></span>

<span data-ttu-id="35ac5-231">包含 Razor 頁面和支援檔案。</span><span class="sxs-lookup"><span data-stu-id="35ac5-231">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="35ac5-232">每個 Razor 頁面都是一對檔案：</span><span class="sxs-lookup"><span data-stu-id="35ac5-232">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="35ac5-233">使用語法以 c # 程式碼撰寫 HTML 標籤的 *cshtml 檔案。* Razor</span><span class="sxs-lookup"><span data-stu-id="35ac5-233">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="35ac5-234">具有處理頁面事件之 c # 程式碼的 *cshtml.cs 檔案。*</span><span class="sxs-lookup"><span data-stu-id="35ac5-234">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="35ac5-235">支援檔案的名稱以底線開頭。</span><span class="sxs-lookup"><span data-stu-id="35ac5-235">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="35ac5-236">例如， *_Layout cshtml* 檔案會設定所有頁面通用的 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="35ac5-236">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="35ac5-237">此檔案會設定頁面頂端的導覽功能表和頁面底部的著作權標示。</span><span class="sxs-lookup"><span data-stu-id="35ac5-237">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="35ac5-238">如需詳細資訊，請參閱<xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="35ac5-238">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="35ac5-239">wwwroot 資料夾</span><span class="sxs-lookup"><span data-stu-id="35ac5-239">wwwroot folder</span></span>

<span data-ttu-id="35ac5-240">包含靜態檔案，例如 HTML 檔案、JavaScript 檔案和 CSS 檔案。</span><span class="sxs-lookup"><span data-stu-id="35ac5-240">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="35ac5-241">如需詳細資訊，請參閱<xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="35ac5-241">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="35ac5-242">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="35ac5-242">appSettings.json</span></span>

<span data-ttu-id="35ac5-243">包含設定資料，例如連接字串。</span><span class="sxs-lookup"><span data-stu-id="35ac5-243">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="35ac5-244">如需詳細資訊，請參閱<xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="35ac5-244">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="35ac5-245">Program.cs</span><span class="sxs-lookup"><span data-stu-id="35ac5-245">Program.cs</span></span>

<span data-ttu-id="35ac5-246">包含程式的進入點。</span><span class="sxs-lookup"><span data-stu-id="35ac5-246">Contains the entry point for the program.</span></span> <span data-ttu-id="35ac5-247">如需詳細資訊，請參閱<xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="35ac5-247">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="35ac5-248">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="35ac5-248">Startup.cs</span></span>

<span data-ttu-id="35ac5-249">包含設定應用程式行為的程式碼。</span><span class="sxs-lookup"><span data-stu-id="35ac5-249">Contains code that configures app behavior.</span></span> <span data-ttu-id="35ac5-250">如需詳細資訊，請參閱<xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="35ac5-250">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="35ac5-251">後續步驟</span><span class="sxs-lookup"><span data-stu-id="35ac5-251">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="35ac5-252">下一步：新增模型</span><span class="sxs-lookup"><span data-stu-id="35ac5-252">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="35ac5-253">這是本系列的第一個教學課程。</span><span class="sxs-lookup"><span data-stu-id="35ac5-253">This is the first tutorial of a series.</span></span> <span data-ttu-id="35ac5-254">[此系列](xref:tutorials/razor-pages/index) 會教您建立 ASP.NET Core Razor 頁面 web 應用程式的基本概念。</span><span class="sxs-lookup"><span data-stu-id="35ac5-254">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="35ac5-255">如需更高階的簡介，適用于熟悉控制器和 views 的開發人員，請參閱 [ Razor 頁面簡介](xref:razor-pages/index)。</span><span class="sxs-lookup"><span data-stu-id="35ac5-255">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="35ac5-256">在本系列結束時，您將會有一個可管理電影資料庫的應用程式。</span><span class="sxs-lookup"><span data-stu-id="35ac5-256">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="35ac5-257">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="35ac5-257">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="35ac5-258">在本教學課程中，您：</span><span class="sxs-lookup"><span data-stu-id="35ac5-258">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="35ac5-259">建立 Razor 頁面 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="35ac5-259">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="35ac5-260">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="35ac5-260">Run the app.</span></span>
> * <span data-ttu-id="35ac5-261">檢查專案檔。</span><span class="sxs-lookup"><span data-stu-id="35ac5-261">Examine the project files.</span></span>

<span data-ttu-id="35ac5-262">在本教學課程結尾，您將會有一個工作 Razor 頁面 web 應用程式，您將在稍後的教學課程中建立此應用程式。</span><span class="sxs-lookup"><span data-stu-id="35ac5-262">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Home 或：：：非 loc (Index) ：：： page](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="35ac5-264">必要條件</span><span class="sxs-lookup"><span data-stu-id="35ac5-264">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35ac5-265">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35ac5-265">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="35ac5-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="35ac5-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="35ac5-267">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="35ac5-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="35ac5-268">建立 Razor 頁面 web 應用程式</span><span class="sxs-lookup"><span data-stu-id="35ac5-268">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35ac5-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35ac5-269">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="35ac5-270">從 Visual Studio 的 [檔案] 功能表中，選取 [新增]**[專案]** >  。</span><span class="sxs-lookup"><span data-stu-id="35ac5-270">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="35ac5-271">建立新的 ASP.NET Core Web 應用程式並選取 [下一步]。</span><span class="sxs-lookup"><span data-stu-id="35ac5-271">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="35ac5-273">將專案命名為 **Razor PagesMovie**。</span><span class="sxs-lookup"><span data-stu-id="35ac5-273">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="35ac5-274">請務必將專案命名為 *Razor PagesMovie* ，如此一來，當您複製並貼上程式碼時，命名空間才會相符。</span><span class="sxs-lookup"><span data-stu-id="35ac5-274">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/config.png)

* <span data-ttu-id="35ac5-276">在下拉式清單中選取 [ASP.NET Core 2.2]，然後選取 [Web 應用程式] 及 [建立]。</span><span class="sxs-lookup"><span data-stu-id="35ac5-276">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="35ac5-278">下列起始專案會隨即建立：</span><span class="sxs-lookup"><span data-stu-id="35ac5-278">The following starter project is created:</span></span>

  ![方案總管](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="35ac5-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="35ac5-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="35ac5-281">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="35ac5-281">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="35ac5-282">變更為將包含專案的目錄 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="35ac5-282">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="35ac5-283">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="35ac5-283">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="35ac5-284">此 `dotnet new` 命令會 Razor 在 *Razor PagesMovie* 資料夾中建立新的頁面專案。</span><span class="sxs-lookup"><span data-stu-id="35ac5-284">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="35ac5-285">此 `code` 命令會在 Visual Studio Code 的目前實例中開啟 [ *Razor PagesMovie* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="35ac5-285">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="35ac5-286">在狀態列的 OmniSharp 火焰圖示變成綠色之後，會有一個對話方塊要求 **從 ' PagesMovie ' 中找不到建立和偵測所需的資產 Razor 。要新增它們嗎？**</span><span class="sxs-lookup"><span data-stu-id="35ac5-286">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="35ac5-287">選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="35ac5-287">Select **Yes**.</span></span>

  <span data-ttu-id="35ac5-288">*.vscode* 目錄 (其中包含 *launch.json* 和 *tasks.json* 檔案) 會被新增至專案的根目錄。</span><span class="sxs-lookup"><span data-stu-id="35ac5-288">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="35ac5-289">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="35ac5-289">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="35ac5-290">選取 [檔案]**[新增解決方案]** > 。</span><span class="sxs-lookup"><span data-stu-id="35ac5-290">Select **File** > **New Solution**.</span></span>

![macOS 新增方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="35ac5-292">在8.6 版之前的 Visual Studio for Mac 中，請選取 [ **.net Core**  >  **應用**  >  **程式 Web 應用程式**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="35ac5-292">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="35ac5-293">在8.6 版或更新版本中，請選取 [ **web] 和 [主控台**  >  **應用**  >  **程式 web 應用程式**  >  **]**。</span><span class="sxs-lookup"><span data-stu-id="35ac5-293">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="35ac5-294">在 [ **設定新的 Web 應用程式** ] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="35ac5-294">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="35ac5-295">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="35ac5-295">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="35ac5-296">如果有選取 **目標 Framework** 的選項，請選取最新的2.x 版本。</span><span class="sxs-lookup"><span data-stu-id="35ac5-296">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="35ac5-297">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="35ac5-297">Select **Next**.</span></span>

* <span data-ttu-id="35ac5-298">將專案命名為 **Razor PagesMovie**，然後選取 [**建立**]。</span><span class="sxs-lookup"><span data-stu-id="35ac5-298">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="35ac5-300">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="35ac5-300">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35ac5-301">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35ac5-301">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="35ac5-302">按 Ctrl+F5 即可執行而不使用偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="35ac5-302">Press Ctrl+F5 to run without the debugger.</span></span>

  <span data-ttu-id="35ac5-303">使用 <kbd>Ctrl + F5</kbd> 啟動應用程式 (非 debug 模式) 可讓您進行程式碼變更、儲存檔案、重新整理瀏覽器，以及查看程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="35ac5-303">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="35ac5-304">許多開發人員偏好使用非偵錯模式來快速啟動應用程式並檢視變更。</span><span class="sxs-lookup"><span data-stu-id="35ac5-304">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="35ac5-305">Visual Studio 會啟動 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)，並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="35ac5-305">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="35ac5-306">位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="35ac5-306">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="35ac5-307">這是因為 `localhost` 是本機電腦的標準主機名稱。</span><span class="sxs-lookup"><span data-stu-id="35ac5-307">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="35ac5-308">Localhost 只會為來自本機電腦的 Web 要求提供服務。</span><span class="sxs-lookup"><span data-stu-id="35ac5-308">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="35ac5-309">當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。</span><span class="sxs-lookup"><span data-stu-id="35ac5-309">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="35ac5-310">在應用程式的首頁上，選取 [接受] 同意追蹤。</span><span class="sxs-lookup"><span data-stu-id="35ac5-310">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="35ac5-311">此應用程式不會追蹤個人資訊，但專案範本會包含同意功能，以防您需要同意才符合歐盟的[一般資料保護規定 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="35ac5-311">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home 或：：：非 loc (Index) ：：： page](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="35ac5-313">下圖顯示同意追蹤之後的應用程式：</span><span class="sxs-lookup"><span data-stu-id="35ac5-313">The following image shows the app after consent to tracking is provided:</span></span>

  ![Home 或：：：非 loc (Index) ：：： page](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="35ac5-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="35ac5-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="35ac5-316">按 <kbd>Ctrl + F5</kbd> ，在沒有偵錯工具的情況下執行。</span><span class="sxs-lookup"><span data-stu-id="35ac5-316">Press <kbd>Ctrl+F5</kbd> to run without the debugger.</span></span>

  <span data-ttu-id="35ac5-317">使用 <kbd>Ctrl + F5</kbd> 啟動應用程式 (非 debug 模式) 可讓您進行程式碼變更、儲存檔案、重新整理瀏覽器，以及查看程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="35ac5-317">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="35ac5-318">許多開發人員偏好使用非偵錯模式來快速啟動應用程式並檢視變更。</span><span class="sxs-lookup"><span data-stu-id="35ac5-318">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="35ac5-319">Visual Studio Code 開始 [Kestrel](xref:fundamentals/servers/kestrel)、啟動瀏覽器，然後移至 `http://localhost:5001` 。</span><span class="sxs-lookup"><span data-stu-id="35ac5-319">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span> <span data-ttu-id="35ac5-320">位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。</span><span class="sxs-lookup"><span data-stu-id="35ac5-320">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="35ac5-321">這是因為 `localhost` 是本機電腦的標準主機名稱。</span><span class="sxs-lookup"><span data-stu-id="35ac5-321">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="35ac5-322">Localhost 只會為來自本機電腦的 Web 要求提供服務。</span><span class="sxs-lookup"><span data-stu-id="35ac5-322">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="35ac5-323">在應用程式的首頁上，選取 [接受] 同意追蹤。</span><span class="sxs-lookup"><span data-stu-id="35ac5-323">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="35ac5-324">此應用程式不會追蹤個人資訊，但專案範本會包含同意功能，以防您需要同意才符合歐盟的[一般資料保護規定 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="35ac5-324">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home 或：：：非 loc (Index) ：：： page](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="35ac5-326">下圖顯示您同意追蹤之後的應用程式：</span><span class="sxs-lookup"><span data-stu-id="35ac5-326">The following image shows the app after you give consent to tracking:</span></span>

  ![Home 或：：：非 loc (Index) ：：： page](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="35ac5-328">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="35ac5-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="35ac5-329">按 **Cmd-Opt-F5** 以在不使用偵錯工具的情況下執行。</span><span class="sxs-lookup"><span data-stu-id="35ac5-329">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="35ac5-330">使用 <kbd>Cmd + Opt + F5</kbd> (非偵測模式啟動應用程式) 可讓您進行程式碼變更、儲存檔案、重新整理瀏覽器，以及查看程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="35ac5-330">Launching the app with <kbd>Cmd+Opt+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="35ac5-331">許多開發人員偏好使用非偵錯模式來快速啟動應用程式並檢視變更。</span><span class="sxs-lookup"><span data-stu-id="35ac5-331">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="35ac5-332">Visual Studio 開始 [Kestrel](xref:fundamentals/servers/kestrel)、啟動瀏覽器，然後移至 `http://localhost:5001` 。</span><span class="sxs-lookup"><span data-stu-id="35ac5-332">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span>

* <span data-ttu-id="35ac5-333">在應用程式的首頁上，選取 [接受] 同意追蹤。</span><span class="sxs-lookup"><span data-stu-id="35ac5-333">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="35ac5-334">此應用程式不會追蹤個人資訊，但專案範本會包含同意功能，以防您需要同意才符合歐盟的[一般資料保護規定 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="35ac5-334">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home 或：：：非 loc (Index) ：：： page](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="35ac5-336">下圖顯示同意追蹤之後的應用程式：</span><span class="sxs-lookup"><span data-stu-id="35ac5-336">The following image shows the app after consent to tracking is provided:</span></span>

  ![Home 或：：：非 loc (Index) ：：： page](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="35ac5-338">檢查專案檔</span><span class="sxs-lookup"><span data-stu-id="35ac5-338">Examine the project files</span></span>

<span data-ttu-id="35ac5-339">以下概要說明主要專案資料夾和檔案，您將在稍後的教學課程中使用這些資料夾和檔案。</span><span class="sxs-lookup"><span data-stu-id="35ac5-339">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="35ac5-340">Pages 資料夾</span><span class="sxs-lookup"><span data-stu-id="35ac5-340">Pages folder</span></span>

<span data-ttu-id="35ac5-341">包含 Razor 頁面和支援檔案。</span><span class="sxs-lookup"><span data-stu-id="35ac5-341">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="35ac5-342">每個 Razor 頁面都是一對檔案：</span><span class="sxs-lookup"><span data-stu-id="35ac5-342">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="35ac5-343">使用語法以 c # 程式碼撰寫 HTML 標籤的 *cshtml 檔案。* Razor</span><span class="sxs-lookup"><span data-stu-id="35ac5-343">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="35ac5-344">具有處理頁面事件之 c # 程式碼的 *cshtml.cs 檔案。*</span><span class="sxs-lookup"><span data-stu-id="35ac5-344">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="35ac5-345">支援檔案的名稱以底線開頭。</span><span class="sxs-lookup"><span data-stu-id="35ac5-345">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="35ac5-346">例如， *_Layout cshtml* 檔案會設定所有頁面通用的 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="35ac5-346">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="35ac5-347">此檔案會設定頁面頂端的導覽功能表和頁面底部的著作權標示。</span><span class="sxs-lookup"><span data-stu-id="35ac5-347">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="35ac5-348">如需詳細資訊，請參閱<xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="35ac5-348">For more information, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="35ac5-349">Razor 頁面衍生自 `PageModel` 。</span><span class="sxs-lookup"><span data-stu-id="35ac5-349">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="35ac5-350">依照慣例， `PageModel` 衍生的類別會命名為 `<PageName>Model` 。</span><span class="sxs-lookup"><span data-stu-id="35ac5-350">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="35ac5-351">wwwroot 資料夾</span><span class="sxs-lookup"><span data-stu-id="35ac5-351">wwwroot folder</span></span>

<span data-ttu-id="35ac5-352">包含靜態檔案，例如 HTML 檔案、JavaScript 檔案和 CSS 檔案。</span><span class="sxs-lookup"><span data-stu-id="35ac5-352">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="35ac5-353">如需詳細資訊，請參閱<xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="35ac5-353">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="35ac5-354">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="35ac5-354">appSettings.json</span></span>

<span data-ttu-id="35ac5-355">包含設定資料，例如連接字串。</span><span class="sxs-lookup"><span data-stu-id="35ac5-355">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="35ac5-356">如需詳細資訊，請參閱<xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="35ac5-356">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="35ac5-357">Program.cs</span><span class="sxs-lookup"><span data-stu-id="35ac5-357">Program.cs</span></span>

<span data-ttu-id="35ac5-358">包含程式的進入點。</span><span class="sxs-lookup"><span data-stu-id="35ac5-358">Contains the entry point for the program.</span></span> <span data-ttu-id="35ac5-359">如需詳細資訊，請參閱<xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="35ac5-359">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="35ac5-360">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="35ac5-360">Startup.cs</span></span>

<span data-ttu-id="35ac5-361">包含設定應用程式行為的程式碼，例如是否需要的同意 cookie 。</span><span class="sxs-lookup"><span data-stu-id="35ac5-361">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="35ac5-362">如需詳細資訊，請參閱<xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="35ac5-362">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="35ac5-363">其他資源</span><span class="sxs-lookup"><span data-stu-id="35ac5-363">Additional resources</span></span>

* [<span data-ttu-id="35ac5-364">本教學課程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="35ac5-364">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="35ac5-365">後續步驟</span><span class="sxs-lookup"><span data-stu-id="35ac5-365">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="35ac5-366">下一步：新增模型</span><span class="sxs-lookup"><span data-stu-id="35ac5-366">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
