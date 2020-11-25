---
title: 教學課程：開始使用 Razor ASP.NET Core 中的頁面
author: rick-anderson
description: 這是一個系列的第一個教學課程，教您建立 ASP.NET Core Razor 頁面 web 應用程式的基本概念。
ms.author: riande
ms.date: 09/15/2020
no-loc:
- Index
- Create
- Delete
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
ms.openlocfilehash: fa113a3e0a2a69fb4aa1318056dcfc6e261490f6
ms.sourcegitcommit: 8b867c4cb0c3b39bbc4d2d87815610d2ef858ae7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "96025018"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a>教學課程：開始使用 Razor ASP.NET Core 中的頁面

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"
這是一個系列的第一個教學課程，教您建立 ASP.NET Core Razor 頁面 web 應用程式的基本概念。

如需更高階的簡介，適用于熟悉控制器和 views 的開發人員，請參閱 [ Razor 頁面簡介](xref:razor-pages/index)。

在本系列結束時，您將會有一個可管理電影資料庫的應用程式。  

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([如何下載](xref:index#how-to-download-a-sample))。

在本教學課程中，您：

> [!div class="checklist"]
> * CreateRazor頁面 web 應用程式。
> * 執行應用程式。
> * 檢查專案檔。

在本教學課程結尾，您將會有一個工作 Razor 頁面 web 應用程式，您將在稍後的教學課程中加以增強。

![Home 或：：：非 loc (Index) ：：： page](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a>必要條件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a>CreateRazor頁面 web 應用程式

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 開始 Visual Studio，然後選取 **Create 新專案**。 如需詳細資訊，請參閱[ Create Visual Studio 中的新專案](/visualstudio/ide/create-new-project)。

   ![：：：非 loc (從開始視窗建立) ：：：新專案](razor-pages-start/_static/5/start-window-create-new-project.png)

1. 在 [ **Create 新增專案**] 對話方塊中，選取 [ **ASP.NET Core Web 應用程式**]，然後選取 **[下一步]**。

    ![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/5/np.png)
    
1. 在 [ **設定您的新專案** ] 對話方塊中，輸入 [ `RazorPagesMovie` **專案名稱**]。 請務必將專案命名為 *Razor PagesMovie*，包括符合大小寫，如此一來，當您複製並貼上範例程式碼時，命名空間會相符。

1. 選取 [Create]。

    ![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/config.png)

1. 在 **Create 新的 ASP.NET Core web 應用程式**] 對話方塊中，選取：
    1. 下拉式清單中的 **.Net Core** 和 **ASP.NET Core 5.0** 。
    1. **Web 應用程式**。
    1. **Create**.

     ![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/5/npx.png)

    下列起始專案會隨即建立：

    ![方案總管](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。

1. 變更為將包含專案的目錄 (`cd`)。

1. 執行下列命令：

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * 此 `dotnet new` 命令會 Razor 在 *Razor PagesMovie* 資料夾中建立新的頁面專案。
   * 此 `code` 命令會在 Visual Studio Code 的目前實例中開啟 [ *Razor PagesMovie* ] 資料夾。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 選取 [檔案]**[新增解決方案]** > 。

    ![macOS 新增方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. 在8.6 版之前的 Visual Studio for Mac 中，請選取 [ **.net Core**  >  **應用**  >  **程式 Web 應用程式**  >  **]**。 在8.6 版或更新版本中，請選取 [ **web] 和 [主控台**  >  **應用**  >  **程式 web 應用程式**  >  **]**。

    ![macOS web 應用程式範本選取專案](razor-pages-start/_static/web_app_template_vsmac.png)

1. 在 [ **設定新的 Web 應用程式** ] 對話方塊中：

    1. 確認 [ **驗證** ] 設定為 [ **無驗證**]。
    1. 如果有選取 **目標 Framework** 的選項，請選取最新的 .net 5.x 版本。
    1. 選取 [下一步]。

1. 將專案命名為 *Razor PagesMovie* ，然後選取 [] **Create** 。

    ![macOS 為專案命名](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>執行應用程式

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>檢查專案檔

以下概要說明主要專案資料夾和檔案，您將在稍後的教學課程中使用這些資料夾和檔案。

### <a name="pages-folder"></a>Pages 資料夾

包含 Razor 頁面和支援檔案。 每個 Razor 頁面都是一對檔案：

* 使用語法以 c # 程式碼撰寫 HTML 標籤的 *cshtml 檔案。* Razor
* 具有處理頁面事件之 c # 程式碼的 *cshtml.cs 檔案。*

支援檔案的名稱以底線開頭。 例如， *_Layout cshtml* 檔案會設定所有頁面通用的 UI 元素。 此檔案會設定頁面頂端的導覽功能表和頁面底部的著作權標示。 如需詳細資訊，請參閱<xref:mvc/views/layout>。

### <a name="wwwroot-folder"></a>wwwroot 資料夾

包含靜態資產，例如 HTML 檔案、JavaScript 檔案和 CSS 檔案。 如需詳細資訊，請參閱<xref:fundamentals/static-files>。

### appsettings.json

包含設定資料，例如連接字串。 如需詳細資訊，請參閱<xref:fundamentals/configuration/index>。

### <a name="programcs"></a>Program.cs

包含應用程式的進入點。 如需詳細資訊，請參閱<xref:fundamentals/host/generic-host>。

### <a name="startupcs"></a>Startup.cs

包含設定應用程式行為的程式碼。 如需詳細資訊，請參閱<xref:fundamentals/startup>。

## <a name="next-steps"></a>後續步驟

> [!div class="step-by-step"]
> [下一步：新增模型](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

這是一個系列的第一個教學課程，教您建立 ASP.NET Core Razor 頁面 web 應用程式的基本概念。

如需更高階的簡介，適用于熟悉控制器和 views 的開發人員，請參閱 [ Razor 頁面簡介](xref:razor-pages/index)。

在本系列結束時，您將會有一個可管理電影資料庫的應用程式。  

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([如何下載](xref:index#how-to-download-a-sample))。

在本教學課程中，您：

> [!div class="checklist"]
> * CreateRazor頁面 web 應用程式。
> * 執行應用程式。
> * 檢查專案檔。

在本教學課程結尾，您將會有一個工作 Razor 頁面 web 應用程式，您將在稍後的教學課程中建立此應用程式。

![Home 或：：：非 loc (Index) ：：： page](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>必要條件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a>CreateRazor頁面 web 應用程式

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 從 Visual Studio 的 [檔案] 功能表中，選取 [新增]**[專案]** >  。
* Create 新的 ASP.NET Core Web 應用程式，然後選取 **[下一步]**。
  ![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/np_2.1.png)
* 將專案命名為 **Razor PagesMovie**。 請務必將專案命名為 *Razor PagesMovie* ，如此一來，當您複製並貼上程式碼時，命名空間才會相符。
  ![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/config.png)

* 在 [ **Web 應用程式**] 下拉式清單中選取 **ASP.NET Core 3.1** ，然後選取 [] **Create** 。

![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/3/npx.png)

  下列起始專案會隨即建立：

  ![方案總管](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。

* 變更為將包含專案的目錄 (`cd`)。

* 執行下列命令：

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * 此 `dotnet new` 命令會 Razor 在 *Razor PagesMovie* 資料夾中建立新的頁面專案。
  * 此 `code` 命令會在 Visual Studio Code 的目前實例中開啟 [ *Razor PagesMovie* ] 資料夾。

* 在狀態列的 OmniSharp 火焰圖示變成綠色之後，會有一個對話方塊要求 **從 ' PagesMovie ' 中找不到建立和偵測所需的資產 Razor 。要新增它們嗎？** 選取 [是]。

  *.vscode* 目錄 (其中包含 *launch.json* 和 *tasks.json* 檔案) 會被新增至專案的根目錄。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 選取 [檔案]**[新增解決方案]** > 。

  ![macOS 新增方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* 在8.6 版之前的 Visual Studio for Mac 中，請選取 [ **.net Core**  >  **應用**  >  **程式 Web 應用程式**  >  **]**。 在8.6 版或更新版本中，請選取 [ **web] 和 [主控台**  >  **應用**  >  **程式 web 應用程式**  >  **]**。

  ![macOS web 應用程式範本選取專案](razor-pages-start/_static/web_app_template_vsmac.png)

* 在 [ **設定新的 Web 應用程式** ] 對話方塊中：

  * 確認 [ **驗證** ] 設定為 [ **無驗證**]。
  * 如果有選取 **目標 Framework** 的選項，請選取最新的3.x 版。

  選取 [下一步]。

* 將專案命名為 **Razor PagesMovie**，然後選取 **Create** 。

  ![macOS 為專案命名](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>執行應用程式

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>檢查專案檔

以下概要說明主要專案資料夾和檔案，您將在稍後的教學課程中使用這些資料夾和檔案。

### <a name="pages-folder"></a>Pages 資料夾

包含 Razor 頁面和支援檔案。 每個 Razor 頁面都是一對檔案：

* 使用語法以 c # 程式碼撰寫 HTML 標籤的 *cshtml 檔案。* Razor
* 具有處理頁面事件之 c # 程式碼的 *cshtml.cs 檔案。*

支援檔案的名稱以底線開頭。 例如， *_Layout cshtml* 檔案會設定所有頁面通用的 UI 元素。 此檔案會設定頁面頂端的導覽功能表和頁面底部的著作權標示。 如需詳細資訊，請參閱<xref:mvc/views/layout>。

### <a name="wwwroot-folder"></a>wwwroot 資料夾

包含靜態檔案，例如 HTML 檔案、JavaScript 檔案和 CSS 檔案。 如需詳細資訊，請參閱<xref:fundamentals/static-files>。

### <a name="appsettingsjson"></a>appSettings.json

包含設定資料，例如連接字串。 如需詳細資訊，請參閱<xref:fundamentals/configuration/index>。

### <a name="programcs"></a>Program.cs

包含程式的進入點。 如需詳細資訊，請參閱<xref:fundamentals/host/generic-host>。

### <a name="startupcs"></a>Startup.cs

包含設定應用程式行為的程式碼。 如需詳細資訊，請參閱<xref:fundamentals/startup>。

## <a name="next-steps"></a>後續步驟

> [!div class="step-by-step"]
> [下一步：新增模型](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

這是本系列的第一個教學課程。 [此系列](xref:tutorials/razor-pages/index) 會教您建立 ASP.NET Core Razor 頁面 web 應用程式的基本概念。

如需更高階的簡介，適用于熟悉控制器和 views 的開發人員，請參閱 [ Razor 頁面簡介](xref:razor-pages/index)。

在本系列結束時，您將會有一個可管理電影資料庫的應用程式。  

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([如何下載](xref:index#how-to-download-a-sample))。

在本教學課程中，您：

> [!div class="checklist"]
> * CreateRazor頁面 web 應用程式。
> * 執行應用程式。
> * 檢查專案檔。

在本教學課程結尾，您將會有一個工作 Razor 頁面 web 應用程式，您將在稍後的教學課程中建立此應用程式。

![Home 或：：：非 loc (Index) ：：： page](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>必要條件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a>CreateRazor頁面 web 應用程式

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 從 Visual Studio 的 [檔案] 功能表中，選取 [新增]**[專案]** >  。

* Create 新的 ASP.NET Core Web 應用程式，然後選取 **[下一步]**。

  ![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/np_2.1.png)

* 將專案命名為 **Razor PagesMovie**。 請務必將專案命名為 *Razor PagesMovie* ，如此一來，當您複製並貼上程式碼時，命名空間才會相符。

  ![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/config.png)

* 在 [ **Web 應用程式**] 下拉式清單中選取 **ASP.NET Core 2.2** ，然後選取 [] **Create** 。

![新增 ASP.NET Core Web 應用程式](razor-pages-start/_static/np_2_2.2.png)

  下列起始專案會隨即建立：

  ![方案總管](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。

* 變更為將包含專案的目錄 (`cd`)。

* 執行下列命令：

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * 此 `dotnet new` 命令會 Razor 在 *Razor PagesMovie* 資料夾中建立新的頁面專案。
  * 此 `code` 命令會在 Visual Studio Code 的目前實例中開啟 [ *Razor PagesMovie* ] 資料夾。

* 在狀態列的 OmniSharp 火焰圖示變成綠色之後，會有一個對話方塊要求 **從 ' PagesMovie ' 中找不到建立和偵測所需的資產 Razor 。要新增它們嗎？** 選取 [是]。

  *.vscode* 目錄 (其中包含 *launch.json* 和 *tasks.json* 檔案) 會被新增至專案的根目錄。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 選取 [檔案]**[新增解決方案]** > 。

![macOS 新增方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* 在8.6 版之前的 Visual Studio for Mac 中，請選取 [ **.net Core**  >  **應用**  >  **程式 Web 應用程式**  >  **]**。 在8.6 版或更新版本中，請選取 [ **web] 和 [主控台**  >  **應用**  >  **程式 web 應用程式**  >  **]**。

* 在 [ **設定新的 Web 應用程式** ] 對話方塊中：

  * 確認 [ **驗證** ] 設定為 [ **無驗證**]。
  * 如果有選取 **目標 Framework** 的選項，請選取最新的2.x 版本。

  選取 [下一步]。

* 將專案命名為 **Razor PagesMovie**，然後選取 **Create** 。

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>執行應用程式

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 按 Ctrl+F5 即可執行而不使用偵錯工具。

  使用 <kbd>Ctrl + F5</kbd> 啟動應用程式 (非 debug 模式) 可讓您進行程式碼變更、儲存檔案、重新整理瀏覽器，以及查看程式碼變更。 許多開發人員偏好使用非偵錯模式來快速啟動應用程式並檢視變更。

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio 會啟動 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)，並執行應用程式。 位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。 這是因為 `localhost` 是本機電腦的標準主機名稱。 Localhost 只會為來自本機電腦的 Web 要求提供服務。 當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。

* 在應用程式的首頁上，選取 [接受] 同意追蹤。

  此應用程式不會追蹤個人資訊，但專案範本會包含同意功能，以防您需要同意才符合歐盟的[一般資料保護規定 (GDPR)](xref:security/gdpr)。

  ![Home 或：：：非 loc (Index) ：：： page](razor-pages-start/_static/homeGDPR2.2.png)

  下圖顯示同意追蹤之後的應用程式：

  ![Home 或：：：非 loc (Index) ：：： page](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* 按 <kbd>Ctrl + F5</kbd> ，在沒有偵錯工具的情況下執行。

  使用 <kbd>Ctrl + F5</kbd> 啟動應用程式 (非 debug 模式) 可讓您進行程式碼變更、儲存檔案、重新整理瀏覽器，以及查看程式碼變更。 許多開發人員偏好使用非偵錯模式來快速啟動應用程式並檢視變更。

  Visual Studio Code 開始 [Kestrel](xref:fundamentals/servers/kestrel)、啟動瀏覽器，然後移至 `http://localhost:5001` 。 位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。 這是因為 `localhost` 是本機電腦的標準主機名稱。 Localhost 只會為來自本機電腦的 Web 要求提供服務。

* 在應用程式的首頁上，選取 [接受] 同意追蹤。

  此應用程式不會追蹤個人資訊，但專案範本會包含同意功能，以防您需要同意才符合歐盟的[一般資料保護規定 (GDPR)](xref:security/gdpr)。

  ![Home 或：：：非 loc (Index) ：：： page](razor-pages-start/_static/homeGDPR2.2.png)

  下圖顯示您同意追蹤之後的應用程式：

  ![Home 或：：：非 loc (Index) ：：： page](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* 按 **Cmd-Opt-F5** 以在不使用偵錯工具的情況下執行。

  使用 <kbd>Cmd + Opt + F5</kbd> (非偵測模式啟動應用程式) 可讓您進行程式碼變更、儲存檔案、重新整理瀏覽器，以及查看程式碼變更。 許多開發人員偏好使用非偵錯模式來快速啟動應用程式並檢視變更。

  Visual Studio 開始 [Kestrel](xref:fundamentals/servers/kestrel)、啟動瀏覽器，然後移至 `http://localhost:5001` 。

* 在應用程式的首頁上，選取 [接受] 同意追蹤。

  此應用程式不會追蹤個人資訊，但專案範本會包含同意功能，以防您需要同意才符合歐盟的[一般資料保護規定 (GDPR)](xref:security/gdpr)。

  ![Home 或：：：非 loc (Index) ：：： page](razor-pages-start/_static/homeGDPR2.2_safari.png)

  下圖顯示同意追蹤之後的應用程式：

  ![Home 或：：：非 loc (Index) ：：： page](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>檢查專案檔

以下概要說明主要專案資料夾和檔案，您將在稍後的教學課程中使用這些資料夾和檔案。

### <a name="pages-folder"></a>Pages 資料夾

包含 Razor 頁面和支援檔案。 每個 Razor 頁面都是一對檔案：

* 使用語法以 c # 程式碼撰寫 HTML 標籤的 *cshtml 檔案。* Razor
* 具有處理頁面事件之 c # 程式碼的 *cshtml.cs 檔案。*

支援檔案的名稱以底線開頭。 例如， *_Layout cshtml* 檔案會設定所有頁面通用的 UI 元素。 此檔案會設定頁面頂端的導覽功能表和頁面底部的著作權標示。 如需詳細資訊，請參閱<xref:mvc/views/layout>。

Razor 頁面衍生自 `PageModel` 。 依照慣例， `PageModel` 衍生的類別會命名為 `<PageName>Model` 。

### <a name="wwwroot-folder"></a>wwwroot 資料夾

包含靜態檔案，例如 HTML 檔案、JavaScript 檔案和 CSS 檔案。 如需詳細資訊，請參閱<xref:fundamentals/static-files>。

### <a name="appsettingsjson"></a>appSettings.json

包含設定資料，例如連接字串。 如需詳細資訊，請參閱<xref:fundamentals/configuration/index>。

### <a name="programcs"></a>Program.cs

包含程式的進入點。 如需詳細資訊，請參閱<xref:fundamentals/host/generic-host>。

### <a name="startupcs"></a>Startup.cs

包含設定應用程式行為的程式碼，例如是否需要的同意 cookie 。 如需詳細資訊，請參閱<xref:fundamentals/startup>。

## <a name="additional-resources"></a>其他資源

* [本教學課程的 YouTube 版本](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>後續步驟

> [!div class="step-by-step"]
> [下一步：新增模型](xref:tutorials/razor-pages/model)

::: moniker-end
