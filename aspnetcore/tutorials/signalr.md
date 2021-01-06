---
title: 開始使用 ASP.NET Core SignalR
author: bradygaster
description: 在本教學課程中，您會建立使用 ASP.NET Core 的聊天應用程式 SignalR 。
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
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
uid: tutorials/signalr
ms.openlocfilehash: 1dad2264250bf43ec6c1df679b8754a82a5addfd
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "95417665"
---
# <a name="tutorial-get-started-with-aspnet-core-no-locsignalr"></a>教學課程：開始使用 ASP.NET Core SignalR

::: moniker range=">= aspnetcore-3.0"

本教學課程會教您使用建立即時應用程式的基本概念 SignalR 。 您會了解如何：

> [!div class="checklist"]
> * 建立 Web 專案。
> * 新增 SignalR 用戶端程式庫。
> * 建立 SignalR 中樞。
> * 設定要使用的專案 SignalR 。
> * 新增程式碼，以將訊息從任何用戶端傳送至所有連線的用戶端。

最後，您會有一個運作正常的聊天應用程式：

![：：：非 loc (SignalR) ：：：範例應用程式](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a>必要條件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app-project"></a>建立 Web 應用程式專案

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

* 從功能表中選取 [檔案] > [新增專案] 。

* 在 [建立新專案] 對話方塊中，選取 [ASP.NET Core Web 應用程式]，然後選取 [下一步]。

* 在 [**設定您的新專案**] 對話方塊中，為專案 *SignalR 聊天* 命名，然後選取 [**建立**]。

* 在 [ **建立新的 ASP.NET Core Web 應用程式** ] 對話方塊中，選取 [ **.net Core** ] 和 [ **ASP.NET Core 3.1**]。 

* 選取 [ **Web 應用程式** ] 以建立使用 Razor 頁面的專案，然後選取 [ **建立**]。

  ![Visual Studio 的 [新增專案] 對話方塊](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* 對要建立新專案資料夾所在的資料夾，開啟[整合式終端機](https://code.visualstudio.com/docs/editor/integrated-terminal)。

* 執行下列命令：

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   cd SignalRChat
   code -r .
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 從功能表中選取 [檔案] > [新增方案] 。

* 選取 [.NET Core] > [應用程式] > [Web 應用程式] (不選取 [Web 應用程式 (模型-檢視-控制器)])，然後選取 [下一步]。

* 請確定 **目標 Framework** 設定為 **.net Core 3.1**，然後選取 **[下一步]**。

* 為專案 *SignalR 聊天* 命名，然後選取 [**建立**]。

---

## <a name="add-the-no-locsignalr-client-library"></a>新增 SignalR 用戶端程式庫

SignalR伺服器程式庫包含在 ASP.NET Core 3.1 共用架構中。 JavaScript 用戶端程式庫不會自動包括在專案中。 針對此教學課程，您會使用程式庫管理員 (LibMan) 從 *unpkg* 取得用戶端程式庫。 unpkg 是 (CDN) 的內容傳遞網路，可傳遞在 npm 中找到的任何內容（Node.js 套件管理員）。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

* 在 [方案總管] 中，以滑鼠右鍵按一下專案，然後選取 [新增]**[用戶端程式庫]** > 。

* 在 [新增用戶端程式庫] 對話方塊中，針對 [提供者] 選取 [unpkg]。

* 針對 [程式庫] 輸入 `@microsoft/signalr@latest`。

* 選取 [選擇特定檔案]、展開 [散發者/瀏覽器] 資料夾，然後選取 *signalr.js* 與 *signalr.min.js*。

* 將 [ **目標位置** ] 設定為 [ *wwwroot/js/signalr/*]，然後選取 [ **安裝**]。

  ![[新增用戶端程式庫] 對話方塊 - 選取程式庫](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  LibMan 會建立 *wwwroot/js/signalr* 資料夾，並將選取的檔案複製到其中。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* 在整合式終端機中，執行下列命令以安裝 LibMan。

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* 執行下列命令，以 SignalR 使用 LibMan 取得用戶端程式庫。 您可能必須等幾秒鐘，才會看到輸出。

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  參數指定下列選項：
  * 使用 unpkg 提供者。
  * 將檔案複製到 *wwwroot/js/signalr* 目的地。
  * 只複製指定的檔案。

  輸出看起來會像下列範例：

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 在 [終端機] 中執行下列命令以安裝 LibMan。

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* 流覽至專案資料夾， (包含) 的 *SignalR Chat* 檔案的專案資料夾。

* 執行下列命令，以 SignalR 使用 LibMan 取得用戶端程式庫。

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  參數指定下列選項：
  * 使用 unpkg 提供者。
  * 將檔案複製到 *wwwroot/js/signalr* 目的地。
  * 只複製指定的檔案。

  輸出看起來會像下列範例：

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-no-locsignalr-hub"></a>建立 SignalR 中樞

*中樞* 類別可提供作為高階管線，用來處理用戶端/伺服器通訊。

* 在 SignalR 聊天專案資料夾中，建立 *中樞* 資料夾。

* 在 *Hubs* 資料夾中，建立含有下列程式碼的 *ChatHub.cs* 檔案：

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  `ChatHub` 類別繼承自 SignalR `Hub` 類別。 `Hub` 類別管理連線、群組和傳訊。

  連線的用戶端可以呼叫 `SendMessage` 方法將訊息傳送至所有用戶端。 本教學課程稍後將示範呼叫該方法的 JavaScript 用戶端程式碼。 SignalR 程式碼是非同步，可提供最大的擴充性。

## <a name="configure-no-locsignalr"></a>設定 SignalR

SignalR伺服器必須設定為將要求傳遞 SignalR 至 SignalR 。

* 將下列醒目提示的程式碼新增至 *Startup.cs* 檔案。

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  這些變更會新增 SignalR 至 ASP.NET Core 相依性插入和路由系統。

## <a name="add-no-locsignalr-client-code"></a>新增 SignalR 用戶端程式代碼

* 以下列程式碼取代 *Pages\Index.cshtml* 中的程式碼：

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  上述程式碼：

  * 建立名稱和訊息文字的文字方塊，以及提交按鈕。
  * 建立清單，用 `id="messagesList"` 來顯示從中樞接收的訊息 SignalR 。
  * 包含的腳本參考 SignalR ，以及您在下一個步驟中建立的 *chat.js* 應用程式程式碼。

* 在 *wwwroot/js* 資料夾中，建立含有下列程式碼的 *chat.js* 檔案：

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  上述程式碼：

  * 建立並啟動連線。
  * 新增處理常式至提交按鈕，以將訊息傳送至中樞。
  * 新增處理常式至連線物件，以從中樞接收訊息，並將它們新增至清單。

## <a name="run-the-app"></a>執行應用程式

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 按 **CTRL+F5** 即可執行應用程式而不偵錯。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 在整合式終端機中，執行下列命令：

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 從功能表中選取 [執行] > [啟動但不偵錯]。

---

* 從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。

* 選擇任一個瀏覽器，輸入名稱和訊息，然後選取 [傳送訊息] 按鈕。

  名稱和訊息會立即顯示在兩個頁面上。

  ![：：：非 loc (SignalR) ：：：範例應用程式](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * 如果應用程式無法運作，請開啟您的瀏覽器開發人員工具 (F12)，然後移至主控台。 您可能會看到與 HTML 和 JavaScript 程式碼相關的錯誤。 例如，假設您將 *signalr.js* 放置在與指示不同的資料夾中。 在此情況下，該檔案的參考無法運作，您會在主控台中看到 404 錯誤。
>   ![signalr.js 找不到錯誤](signalr/_static/3.x/f12-console.png)
> * 如果您在 Chrome 中遇到錯誤 ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY，請執行下列命令來更新您的開發憑證：
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

本教學課程會教您使用建立即時應用程式的基本概念 SignalR 。 您會了解如何： 

> [!div class="checklist"]  
> * 建立 Web 專案。   
> * 新增 SignalR 用戶端程式庫。   
> * 建立 SignalR 中樞。 
> * 設定要使用的專案 SignalR 。 
> * 新增程式碼，以將訊息從任何用戶端傳送至所有連線的用戶端。  
最後，您將會有一個可運作的聊天應用程式： ![ ：：：非 loc (SignalR) ：：：範例應用程式](signalr/_static/2.x/signalr-get-started-finished.png)   

## <a name="prerequisites"></a>必要條件    

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a>建立 Web 專案 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* 從功能表中選取 [檔案] > [新增專案] 。 

* 在 [新增專案] 對話方塊中，選取 [已安裝] > [Visual C++] > [Web] > [ASP.NET Core Web 應用程式]。 命名專案 *SignalR 聊天*。   

  ![Visual Studio 的 [新增專案] 對話方塊](signalr/_static/2.x/signalr-new-project-dialog.png)    

* 選取 [ **Web 應用程式** ] 以建立使用 Razor 頁面的專案。   

* 選取 **.NET Core** 作為目標 Framework、選取 [ASP.NET Core 2.2]，然後按一下 [確定]。    

  ![Visual Studio 的 [新增專案] 對話方塊](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* 對要建立新專案資料夾所在的資料夾，開啟[整合式終端機](https://code.visualstudio.com/docs/editor/integrated-terminal)。  

* 執行下列命令：   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat   
   code -r SignalRChat    
   ```  

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)   

* 從功能表中選取 [檔案] > [新增方案] 。    

* 選取 [.NET Core] > [應用程式] > [ASP.NET Core Web 應用程式] (不要選取 [ASP.NET Core Web 應用程式 (MVC)])。  

* 選取 [下一步]  。  

* 為專案 *SignalR 聊天* 命名，然後選取 [**建立**]。 

--- 

## <a name="add-the-no-locsignalr-client-library"></a>新增 SignalR 用戶端程式庫 

SignalR伺服器程式庫包含在中繼套件中 `Microsoft.AspNetCore.App` 。 JavaScript 用戶端程式庫不會自動包括在專案中。 針對此教學課程，您會使用程式庫管理員 (LibMan) 從 *unpkg* 取得用戶端程式庫。 unpkg 是 (CDN) 的內容傳遞網路，可傳遞在 npm 中找到的任何內容（Node.js 套件管理員）。   

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* 在 [方案總管] 中，以滑鼠右鍵按一下專案，然後選取 [新增]**[用戶端程式庫]** > 。  

* 在 [新增用戶端程式庫] 對話方塊中，針對 [提供者] 選取 [unpkg]。 

* 針對 [程式庫]，輸入 `@microsoft/signalr@3`，然後選取不是預覽版的最新版本。  

  ![[新增用戶端程式庫] 對話方塊 - 選取程式庫](signalr/_static/2.x/libman1.png)   

* 選取 [選擇特定檔案]、展開 [散發者/瀏覽器] 資料夾，然後選取 *signalr.js* 與 *signalr.min.js*。 

* 將 [目標位置] 設定為 *wwwroot/lib/signalr/*，然後選取 [安裝]。    

  ![[新增用戶端程式庫] 對話方塊 - 選取檔案與目的地](signalr/_static/2.x/libman2.png) 

  LibMan 會建立 *wwwroot/lib/signalr* 資料夾並將選取的檔案複製到其中。    

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* 在整合式終端機中，執行下列命令以安裝 LibMan。  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* 執行下列命令，以 SignalR 使用 LibMan 取得用戶端程式庫。 您可能必須等幾秒鐘，才會看到輸出。 

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  參數指定下列選項： 
  * 使用 unpkg 提供者。 
  * 將檔案複製到 *wwwroot/lib/signalr* 目的地。    
  * 只複製指定的檔案。  

  輸出看起來會像下列範例：  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)   

* 在 [終端機] 中執行下列命令以安裝 LibMan。 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* 流覽至專案資料夾， (包含) 的 *SignalR Chat* 檔案的專案資料夾。   

* 執行下列命令，以 SignalR 使用 LibMan 取得用戶端程式庫。    

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  參數指定下列選項： 
  * 使用 unpkg 提供者。 
  * 將檔案複製到 *wwwroot/lib/signalr* 目的地。    
  * 只複製指定的檔案。  

  輸出看起來會像下列範例：  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-no-locsignalr-hub"></a>建立 SignalR 中樞   

*中樞* 類別可提供作為高階管線，用來處理用戶端/伺服器通訊。   

* 在 SignalR 聊天專案資料夾中，建立 *中樞* 資料夾。  

* 在 *Hubs* 資料夾中，建立含有下列程式碼的 *ChatHub.cs* 檔案： 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  `ChatHub` 類別繼承自 SignalR `Hub` 類別。 `Hub` 類別管理連線、群組和傳訊。  

  連線的用戶端可以呼叫 `SendMessage` 方法將訊息傳送至所有用戶端。 本教學課程稍後將示範呼叫該方法的 JavaScript 用戶端程式碼。 SignalR 程式碼是非同步，可提供最大的擴充性。    

## <a name="configure-no-locsignalr"></a>設定 SignalR  

SignalR伺服器必須設定為將要求傳遞 SignalR 至 SignalR 。    

* 將下列醒目提示的程式碼新增至 *Startup.cs* 檔案。  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  這些變更會新增 SignalR 至 ASP.NET Core 相依性插入系統和中介軟體管線。  

## <a name="add-no-locsignalr-client-code"></a>新增 SignalR 用戶端程式代碼    

* 以下列程式碼取代 *Pages\Index.cshtml* 中的程式碼：  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  上述程式碼：   

  * 建立名稱和訊息文字的文字方塊，以及提交按鈕。  
  * 建立清單，用 `id="messagesList"` 來顯示從中樞接收的訊息 SignalR 。   
  * 包含的腳本參考 SignalR ，以及您在下一個步驟中建立的 *chat.js* 應用程式程式碼。    

* 在 *wwwroot/js* 資料夾中，建立含有下列程式碼的 *chat.js* 檔案：  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  上述程式碼：   

  * 建立並啟動連線。    
  * 新增處理常式至提交按鈕，以將訊息傳送至中樞。 
  * 新增處理常式至連線物件，以從中樞接收訊息，並將它們新增至清單。  

## <a name="run-the-app"></a>執行應用程式  

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

* 按 **CTRL+F5** 即可執行應用程式而不偵錯。   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* 在整合式終端機中，執行下列命令：    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 從功能表中選取 [執行] > [啟動但不偵錯]。

---

* 從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。

* 選擇任一個瀏覽器，輸入名稱和訊息，然後選取 [傳送訊息] 按鈕。  

  名稱和訊息會立即顯示在兩個頁面上。   

  ![：：：非 loc (SignalR) ：：：範例應用程式](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> 如果應用程式無法運作，請開啟您的瀏覽器開發人員工具 (F12)，然後移至主控台。 您可能會看到與 HTML 和 JavaScript 程式碼相關的錯誤。 例如，假設您將 *signalr.js* 放置在與指示不同的資料夾中。 在此情況下，該檔案的參考無法運作，您會在主控台中看到 404 錯誤。   
> ![signalr.js 找不到錯誤](signalr/_static/2.x/f12-console.png)    
## <a name="additional-resources"></a>其他資源 
* [這個教學課程的 YouTube 版本](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
