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
# <a name="get-started-with-aspnet-core-mvc"></a>ASP.NET Core MVC 使用者入門

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

這是一個系列的第一個教學課程，教您使用控制器和 views ASP.NET 核心 MVC 網頁程式開發。

在系列結束時，您將會有一個可管理及顯示電影資料的應用程式。 您會了解如何：

> [!div class="checklist"]
> * 建立 Web 應用程式。
> * 新增並建構模型。
> * 使用資料庫。
> * 新增搜尋和驗證。

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([如何下載](xref:index#how-to-download-a-sample))。

## <a name="prerequisites"></a>必要條件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a>建立 Web 應用程式

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 啟動 Visual Studio，然後選取 [建立新專案]。
* 在 [ **建立新專案** ] 對話方塊中，選取 [下一步] **ASP.NET Core Web 應用程式** > ****。
* 在 [ **設定您的新專案** ] 對話方塊中，輸入 [ `MvcMovie` **專案名稱**]。 請務必將專案命名為 *MvcMovie*。 複製程式碼時，大小寫必須符合每個 `namespace` 相符專案。
* 選取 [建立]  。
* 在 [ **建立新的 ASP.NET Core web 應用程式** ] 對話方塊中，選取：
  * 下拉式清單中的 **.Net core** 和 **ASP.NET core 5.0** 。
  * **ASP.NET Core Web 應用程式 (模型-視圖控制器)**。
  * **Create**。

![建立新的 ASP.NET Core web 應用程式 ](start-mvc/_static/mvcVS19v16.9.png)

如需建立專案的替代方法，請參閱 [在 Visual Studio 中建立新專案](/visualstudio/ide/create-new-project)。

Visual Studio 已針對建立的 MVC 專案使用預設專案範本。 建立的專案：

* 是正常運作的應用程式。
* 是基本的入門專案。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

本教學課程假設您已熟悉 VS Code。 如需詳細資訊，請參閱 [開始使用 VS Code](https://code.visualstudio.com/docs) 和 [Visual Studio Code](#visual-studio-code-help)說明。

* 開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。
* 變更至 `cd` 將包含專案的目錄 () 。
* 執行以下命令：

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * 如果出現一個對話方塊，其中包含 **要建立的必要資產，且 ' MvcMovie ' 中遺漏了 debug 錯。要新增它們嗎？**，請選取 **[是]**

  * `dotnet new mvc -o MvcMovie`：在 *MvcMovie* 資料夾中建立新的 ASP.NET Core MVC 專案。
  * `code -r MvcMovie`：載入 Visual Studio Code 中的 *MvcMovie .csproj* 專案檔。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 選取 [檔案]**[新增解決方案]** > 。

  ![macOS 新增方案](start-mvc/_static/new_project_vsmac.png)

* 在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用**  >  **程式 Web 應用程式] (模型-視圖控制器)**  >  **下一步]**。 在8.6 版或更新版本中，選取 [ **web] 和 [主控台**  >  **應用**  >  **程式 web 應用程式] (模型-視圖控制器)**  >  **下一步]**。

  ![macOS web 應用程式範本選取專案](start-mvc/_static/web_app_template_vsmac.png)

* 在 [ **設定新的 Web 應用程式** ] 對話方塊中：

  * 確認 [ **驗證** ] 設定為 [ **無驗證**]。
  * 如果出現選取 **目標 Framework** 的選項，請選取最新的5.x 版。
  * 選取 [下一步] 。

* 將專案命名為 **MvcMovie**，然後選取 [建立]。

  ![macOS 為專案命名](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>執行應用程式

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 選取 Ctrl + F5 以執行應用程式，而不執行偵錯工具。

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio：

  * 啟動 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)。
  * 執行應用程式。

  位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。 本機電腦的標準主機名稱為 `localhost` 。 當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。

藉由選取 Ctrl + F5 啟動應用程式而不進行偵錯工具，可讓您：

* 變更程式碼。
* 儲存檔案。
* 快速重新整理瀏覽器並查看程式碼變更。

您可以從 [偵錯] 功能表項目的偵錯或非偵錯模式中啟動應用程式：

![[偵錯] 功能表](start-mvc/_static/debug_menu50.png)

您可以選取 [IIS Express] 按鈕偵錯應用程式

![IIS Express](start-mvc/_static/iis_express50.png)

下圖顯示應用程式：

![Home 或 Index 頁面](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 選取 Ctrl + F5 以在不執行偵錯工具的情況下執行。

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code：

  * 開始 [Kestrel](xref:fundamentals/servers/kestrel)
  * 啟動瀏覽器。
  * 流覽至 `https://localhost:5001` 。

  位址列會顯示 `localhost:port:5001`，而不是類似於 `example.com` 的內容。 本機電腦的標準主機名稱為 `localhost` 。 Localhost 只會為來自本機電腦的 Web 要求提供服務。

藉由選取 Ctrl + F5 啟動應用程式而不進行偵錯工具，可讓您：

* 變更程式碼。
* 儲存檔案。
* 快速重新整理瀏覽器並查看程式碼變更。

  ![Home 或 Index 頁面](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 選取 [執行]**[啟動但不偵錯]** >  來啟動應用程式。

  Visual Studio for Mac：

  * 啟動 [Kestrel](xref:fundamentals/servers/index#kestrel) 伺服器。
  * 啟動瀏覽器。
  * 流覽至 `http://localhost:port` ，其中 *port* 是隨機播放的埠號碼。

  [!INCLUDE[](~/includes/trustCertMac.md)]

  位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。 本機電腦的標準主機名稱為 `localhost` 。 當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。

您可以從 [執行] 功能表的偵錯或非偵錯模式中啟動應用程式。

下圖顯示應用程式：

![Home 或 Index 頁面](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

在本教學課程的下一個部分中，您會了解 MVC，並開始撰寫一些程式碼。

> [!div class="step-by-step"]
> [下一步：新增控制器](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

這是一個系列的第一個教學課程，教您使用控制器和 views ASP.NET 核心 MVC 網頁程式開發。

在系列結束時，您將會有一個可管理及顯示電影資料的應用程式。 您會了解如何：

> [!div class="checklist"]
> * 建立 Web 應用程式。
> * 新增並建構模型。
> * 使用資料庫。
> * 新增搜尋和驗證。

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([如何下載](xref:index#how-to-download-a-sample))。

## <a name="prerequisites"></a>必要條件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a>建立 Web 應用程式

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 從 Visual Studio 中，選取 [ **建立新專案**]。

* 選取 [下一步 **ASP.NET Core Web 應用程式** > **]**。

  ![建立新的 ASP.NET Core Web 應用程式專案](start-mvc/_static/np_2.1.png)

* 將專案命名為 **MvcMovie**，然後選取 [建立]。 請務必將專案命名為 **MvcMovie**，以便在複製程式碼時，命名空間得以相符。

  ![設定您的新專案](start-mvc/_static/config.png)

* 選取 **Web 應用程式 (模型-視圖控制器)**。 從下拉式方塊中，選取 [ **.Net core** ] 和 [ **ASP.NET Core 3.1**]，然後選取 [ **建立**]。

  ![[新增專案] 對話方塊、左窗格中的 [.Net Core]、ASP.NET Core Web ](start-mvc/_static/new_project30.png)

Visual Studio 已針對建立的 MVC 專案使用預設專案範本。 建立的專案：

* 是正常運作的應用程式。
* 是基本的入門專案。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

本教學課程假設您已熟悉 VS Code。 如需詳細資訊，請參閱 [開始使用 VS Code](https://code.visualstudio.com/docs) 和 [Visual Studio Code](#visual-studio-code-help)說明。

* 開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。
* 將目錄 (`cd`) 變更為包含專案的資料夾。
* 執行以下命令：

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * 會出現一個對話方塊，其中包含 **建立和偵測所需的資產，但缺少 ' MvcMovie '。要加入它們嗎？**，請選取 **[是]**。

  * `dotnet new mvc -o MvcMovie`：在 *MvcMovie* 資料夾中建立新的 ASP.NET Core MVC 專案。
  * `code -r MvcMovie`：載入 Visual Studio Code 中的 *MvcMovie .csproj* 專案檔。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 選取 [檔案]**[新增解決方案]** > 。

  ![macOS 新增方案](start-mvc/_static/new_project_vsmac.png)

* 在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用**  >  **程式 Web 應用程式] (模型-視圖控制器)**  >  **下一步]**。 在8.6 版或更新版本中，選取 [ **web] 和 [主控台**  >  **應用**  >  **程式 web 應用程式] (模型-視圖控制器)**  >  **下一步]**。

  ![macOS web 應用程式範本選取專案](start-mvc/_static/web_app_template_vsmac.png)

* 在 [ **設定新的 Web 應用程式** ] 對話方塊中：

  * 確認 [ **驗證** ] 設定為 [ **無驗證**]。
  * 如果出現選取 **目標 Framework** 的選項，請選取最新的3.x 版。
  * 選取 [下一步] 。

* 將專案命名為 **MvcMovie**，然後選取 [建立]。

  ![macOS 為專案命名](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>執行應用程式

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 選取 Ctrl + F5 以執行應用程式，而不進行任何偵錯工具。

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio：

  * 啟動 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)。
  * 執行應用程式。

  位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。 本機電腦的標準主機名稱為 `localhost` 。 當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。

藉由選取 Ctrl + F5 啟動應用程式而不進行偵錯工具，可讓您：

* 變更程式碼。
* 儲存檔案。
* 快速重新整理瀏覽器並查看程式碼變更。

您可以從 [偵錯] 功能表項目的偵錯或非偵錯模式中啟動應用程式：

![[偵錯] 功能表](start-mvc/_static/debug_menu.png)

您可以選取 [IIS Express] 按鈕偵錯應用程式

![IIS Express](start-mvc/_static/iis_express.png)

下圖顯示應用程式：

![Home 或 Index 頁面](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 選取 Ctrl + F5 以執行應用程式，而不進行任何偵錯工具。

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code：

  * 開始 [Kestrel](xref:fundamentals/servers/kestrel)
  * 啟動瀏覽器。
  * 流覽至 `https://localhost:5001` 。

  位址列會顯示 `localhost:port:5001`，而不是類似於 `example.com` 的內容。 本機電腦的標準主機名稱為 `localhost` 。 Localhost 只會為來自本機電腦的 Web 要求提供服務。

藉由選取 Ctrl + F5 啟動應用程式而不進行偵錯工具，可讓您：

* 變更程式碼。
* 儲存檔案。
* 快速重新整理瀏覽器並查看程式碼變更。

  ![Home 或 Index 頁面](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 選取 [執行]**[啟動但不偵錯]** >  來啟動應用程式。

  Visual Studio for Mac：啟動 [Kestrel](xref:fundamentals/servers/index#kestrel) 伺服器、啟動瀏覽器，然後流覽至 `http://localhost:port` ，其中 *埠* 是隨機播放的埠號碼。

[!INCLUDE[](~/includes/trustCertMac.md)]

位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。 本機電腦的標準主機名稱為 `localhost` 。 當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。 當您執行應用程式時，會看到不同的連接埠編號。

您可以從 [執行] 功能表的偵錯或非偵錯模式中啟動應用程式。

下圖顯示應用程式：

![Home 或 Index 頁面](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

在本教學課程的下一個部分中，您會了解 MVC，並開始撰寫一些程式碼。

> [!div class="step-by-step"]
> [下一步](adding-controller.md)

::: moniker-end
