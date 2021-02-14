---
title: 搭配使用 SignalR ASP.NET Core Blazor
author: guardrex
description: 建立搭配使用 ASP.NET Core 的聊天應用 SignalR 程式 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/25/2021
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
uid: tutorials/signalr-blazor
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 355db5ad5462747be0058096bc0132ed1071f290
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280996"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a>搭配使用 SignalR ASP.NET Core Blazor

本教學課程會教您使用與建立即時應用程式的基本概念 SignalR Blazor 。 您會了解如何：

> [!div class="checklist"]
> * 建立 Blazor 專案
> * 新增 SignalR 用戶端程式庫
> * 新增 SignalR 中樞
> * 新增 SignalR 中樞的服務和端點 SignalR
> * 新增 Razor 聊天的元件程式碼

在本教學課程結尾，您將會有一個可運作的聊天應用程式。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="prerequisites"></a>必要條件

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 使用 **ASP.NET 和 網頁程式開發** 工作負載 [Visual Studio 2019 16.8 或更新版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* [Visual Studio for Mac 8.8 版或更新版本](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 使用 **ASP.NET 和 網頁程式開發** 工作負載 [Visual Studio 2019 16.6 或更新版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* [Visual Studio for Mac 8.6 版或更新版本](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a>建立託管 Blazor WebAssembly 應用程式

遵循您選擇的工具指導方針：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> 需要 Visual Studio 16.8 或更新版本，以及 .NET Core SDK 5.0.0 或更新版本。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> 需要 Visual Studio 16.6 或更新版本，以及 .NET Core SDK 3.1.300 或更新版本。

::: moniker-end

1. 建立新專案。

1. 選取 **Blazor 應用程式**，然後選取 **[下一步]**。

1. 輸入 `BlazorWebAssemblySignalRApp` [ **專案名稱** ] 欄位。 確認 **位置** 專案是正確的，或提供專案的位置。 選取 [建立]  。

1. 選擇 **Blazor WebAssembly 應用程式** 範本。

1. 在 [ **Advanced**] 底下，選取 [ **hosted ASP.NET Core** ] 核取方塊。

1. 選取 [建立]  。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 在命令 shell 中，執行下列命令：

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   `-ho|--hosted`選項會建立託管 Blazor WebAssembly 方案。

   `-o|--output`選項會建立解決方案的資料夾。 如果您已建立解決方案的資料夾，並在該資料夾中開啟命令 shell，請省略 `-o|--output` 選項和值以建立解決方案。

1. 在 Visual Studio Code 中，開啟應用程式的專案資料夾。

1. 當對話方塊出現時，若要新增資產以建立和偵測應用程式，請選取 [ **是**]。 Visual Studio Code 會自動加入 `.vscode` 具有所產生檔案和檔案的資料夾 `launch.json` `tasks.json` 。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 安裝最新版本的 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) ，然後執行下列步驟：

1. 選取  >  [檔案 **新方案**] 或 [**開始] 視窗** 中的 [建立 **新** 專案]。

1. 在側邊欄中，選取 [ **Web] 和 [主控台**  >  **應用程式**]。

1. 選擇 **Blazor WebAssembly 應用程式** 範本。 選取 [下一步] 。

1. 確認 [ **驗證** ] 設定為 [ **無驗證**]。 選取 [ **主控 ASP.NET Core** ] 核取方塊。 選取 [下一步] 。

1. 在 [ **專案名稱** ] 欄位中，為應用程式命名 `BlazorWebAssemblySignalRApp` 。 選取 [建立]  。

   如果出現提示信任開發憑證，請信任憑證並繼續。 需要使用者和 keychain 密碼才能信任憑證。

1. 流覽至專案資料夾，然後開啟專案的方案檔 () 來開啟專案 `.sln` 。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

在命令 shell 中，執行下列命令：

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

`-ho|--hosted`選項會建立託管 Blazor WebAssembly 方案。

`-o|--output`選項會建立解決方案的資料夾。 如果您已建立解決方案的資料夾，並在該資料夾中開啟命令 shell，請省略 `-o|--output` 選項和值以建立解決方案。

---

## <a name="add-the-signalr-client-library"></a>新增 SignalR 用戶端程式庫

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. 在 **方案總管** 中，以滑鼠右鍵按一下 `BlazorWebAssemblySignalRApp.Client` 專案，然後選取 [ **管理 NuGet 套件**]。

1. 在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [ **封裝來源** ] 設定為 `nuget.org` 。

1. 選取 **[流覽]** 之後， `Microsoft.AspNetCore.SignalR.Client` 在 [搜尋] 方塊中輸入。

1. 在搜尋結果中，選取 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 套件。 設定版本，使其符合應用程式的共用架構。 選取 [安裝]。

1. 如果出現 [ **預覽變更** ] 對話方塊，請選取 **[確定]**。

1. 如果出現 [ **接受授權** ] 對話方塊，請選取 [如果您同意授權條款即 **接受** ]。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

在 **整合式終端** 機中  >  ，從工具列)  (視圖 **終端** 機，請執行下列命令：

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 在 [ **方案** ] 提要欄位中，以滑鼠右鍵按一下 `BlazorWebAssemblySignalRApp.Client` 專案，然後選取 [ **管理 NuGet 套件**]。

1. 在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [來源] 下拉式清單設定為 `nuget.org` 。

1. 選取 **[流覽]** 之後， `Microsoft.AspNetCore.SignalR.Client` 在 [搜尋] 方塊中輸入。

1. 在搜尋結果中，選取套件旁的核取方塊 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 。 設定版本，使其符合應用程式的共用架構。 選取 [ **新增套件**]。

1. 如果出現 [ **接受授權** ] 對話方塊，請在您同意授權條款時選取 [ **接受** ]。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

從解決方案資料夾的命令 shell 中，執行下列命令：

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a>新增 system.string. Web 封裝

*本節僅適用于 ASP.NET Core 3.x 版的應用程式。*

由於在 ASP.NET Core 3.x 應用程式中使用5.x 時發生套件解析問題 [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) ，因此 `BlazorWebAssemblySignalRApp.Server` 專案需要的封裝參考 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 。 在 .NET 5 的未來修補程式版本中，將會解決基礎問題。 如需詳細資訊，請參閱 [System.Text.Json 定義沒有相依性的 netcoreapp 3.0 (dotnet/runtime #45560) ](https://github.com/dotnet/runtime/issues/45560)。

若要加入 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) `BlazorWebAssemblySignalRApp.Server` ASP.NET Core 3.1 裝載解決方案的專案 Blazor ，請遵循您選擇的工具指導方針：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. 在 **方案總管** 中，以滑鼠右鍵按一下 `BlazorWebAssemblySignalRApp.Server` 專案，然後選取 [ **管理 NuGet 套件**]。

1. 在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [ **封裝來源** ] 設定為 `nuget.org` 。

1. 選取 **[流覽]** 之後， `System.Text.Encodings.Web` 在 [搜尋] 方塊中輸入。

1. 在搜尋結果中，選取 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 套件。 選取符合使用中共用架構的套件版本。 選取 [安裝]。

1. 如果出現 [ **預覽變更** ] 對話方塊，請選取 **[確定]**。

1. 如果出現 [ **接受授權** ] 對話方塊，請選取 [如果您同意授權條款即 **接受** ]。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

在 **整合式終端** 機中 > ，從工具列)  (查看 **終端** 機，請執行下列命令：

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 在 [ **方案** ] 提要欄位中，以滑鼠右鍵按一下 `BlazorWebAssemblySignalRApp.Server` 專案，然後選取 [ **管理 NuGet 套件**]。

1. 在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [來源] 下拉式清單設定為 `nuget.org` 。

1. 選取 **[流覽]** 之後， `System.Text.Encodings.Web` 在 [搜尋] 方塊中輸入。

1. 在搜尋結果中，選取套件旁的核取方塊 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) ，選取符合使用中共用架構的正確套件版本，然後選取 [ **新增套件**]。

1. 如果出現 [ **接受授權** ] 對話方塊，請在您同意授權條款時選取 [ **接受** ]。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

從解決方案資料夾的命令 shell 中，執行下列命令：

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。

---

::: moniker-end

## <a name="add-a-signalr-hub"></a>新增 SignalR 中樞

在 `BlazorWebAssemblySignalRApp.Server` 專案中，建立 `Hubs` (複數) 資料夾，並 () 新增下列 `ChatHub` 類別 `Hubs/ChatHub.cs` ：

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>新增中樞的服務和端點 SignalR

1. 在 `BlazorWebAssemblySignalRApp.Server` 專案中，開啟 `Startup.cs` 檔案。

1. 將類別的命名空間新增 `ChatHub` 至檔案頂端：

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. 新增 SignalR 和回應壓縮中介軟體服務至 `Startup.ConfigureServices` ：

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. 在 `Startup.Configure` 中：

   * 在處理管線的設定頂端使用回應壓縮中介軟體。
   * 在控制器的端點和用戶端的回復之間，新增中樞的端點。

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. 新增 SignalR 和回應壓縮中介軟體服務至 `Startup.ConfigureServices` ：

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. 在 `Startup.Configure` 中：

   * 在處理管線的設定頂端使用回應壓縮中介軟體。
   * 在控制器的端點和用戶端的回復之間，新增中樞的端點。

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a>新增 Razor 聊天的元件程式碼

1. 在 `BlazorWebAssemblySignalRApp.Client` 專案中，開啟 `Pages/Index.razor` 檔案。

::: moniker range=">= aspnetcore-5.0"

1. 以下列程式碼取代標記：

   [!code-razor[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. 以下列程式碼取代標記：

   [!code-razor[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>執行應用程式

遵循您工具的指導方針：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 在 **方案總管** 中，選取 `BlazorWebAssemblySignalRApp.Server` 專案。 按下<kbd>f5</kbd>鍵以執行應用程式的偵錯工具，或按<kbd>Ctrl</kbd> + <kbd>F5</kbd>執行應用程式，而不進行偵錯工具。

1. 從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。

1. 選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。 名稱和訊息會立即顯示在兩個頁面上：

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 按下<kbd>f5</kbd>鍵以執行應用程式的偵錯工具，或按<kbd>Ctrl</kbd> + <kbd>F5</kbd>執行應用程式，而不進行偵錯工具。

1. 從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。

1. 選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。 名稱和訊息會立即顯示在兩個頁面上：

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 在 [ **方案** ] 側邊欄中，選取 `BlazorWebAssemblySignalRApp.Server` 專案。 按<kbd>⌘</kbd> + <kbd>↩</kbd>以執行應用程式的偵錯工具或<kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd>執行應用程式，而不需進行任何偵錯工具。

1. 從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。

1. 選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。 名稱和訊息會立即顯示在兩個頁面上：

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. 從解決方案資料夾的命令 shell 中，執行下列命令：

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. 從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。

1. 選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。 名稱和訊息會立即顯示在兩個頁面上：

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a>建立 Blazor Server 應用程式

遵循您選擇的工具指導方針：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> 需要 Visual Studio 16.8 或更新版本，以及 .NET Core SDK 5.0.0 或更新版本。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> 需要 Visual Studio 16.6 或更新版本，以及 .NET Core SDK 3.1.300 或更新版本。

::: moniker-end

1. 建立新專案。

1. 選取 **Blazor 應用程式**，然後選取 **[下一步]**。

1. 輸入 `BlazorServerSignalRApp` [ **專案名稱** ] 欄位。 確認 **位置** 專案是正確的，或提供專案的位置。 選取 [建立]  。

1. 選擇 **Blazor Server 應用程式** 範本。

1. 選取 [建立]  。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 在命令 shell 中，執行下列命令：

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   `-o|--output`選項會建立專案的資料夾。 如果您已建立專案的資料夾，並在該資料夾中開啟命令 shell，請省略 `-o|--output` 選項和值以建立專案。

1. 在 Visual Studio Code 中，開啟應用程式的專案資料夾。

1. 當對話方塊出現時，若要新增資產以建立和偵測應用程式，請選取 [ **是**]。 Visual Studio Code 會自動加入 `.vscode` 具有所產生檔案和檔案的資料夾 `launch.json` `tasks.json` 。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 安裝最新版本的 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) ，然後執行下列步驟：

1. 選取  >  [檔案 **新方案**] 或 [**開始] 視窗** 中的 [建立 **新** 專案]。

1. 在側邊欄中，選取 [ **Web] 和 [主控台**  >  **應用程式**]。

1. 選擇 **Blazor Server 應用程式** 範本。 選取 [下一步] 。

1. 確認 [ **驗證** ] 設定為 [ **無驗證**]。 選取 [下一步] 。

1. 在 [ **專案名稱** ] 欄位中，為應用程式命名 `BlazorServerSignalRApp` 。 選取 [建立]  。

   如果出現提示信任開發憑證，請信任憑證並繼續。 需要使用者和 keychain 密碼才能信任憑證。

1. 流覽至專案資料夾，然後開啟專案的方案檔 () 來開啟專案 `.sln` 。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

在命令 shell 中，執行下列命令：

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

`-o|--output`選項會建立專案的資料夾。 如果您已建立專案的資料夾，並在該資料夾中開啟命令 shell，請省略 `-o|--output` 選項和值以建立專案。

---

## <a name="add-the-signalr-client-library"></a>新增 SignalR 用戶端程式庫

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. 在 **方案總管** 中，以滑鼠右鍵按一下 `BlazorServerSignalRApp` 專案，然後選取 [ **管理 NuGet 套件**]。

1. 在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [ **封裝來源** ] 設定為 `nuget.org` 。

1. 選取 **[流覽]** 之後， `Microsoft.AspNetCore.SignalR.Client` 在 [搜尋] 方塊中輸入。

1. 在搜尋結果中，選取 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 套件。 設定版本，使其符合應用程式的共用架構。 選取 [安裝]。

1. 如果出現 [ **預覽變更** ] 對話方塊，請選取 **[確定]**。

1. 如果出現 [ **接受授權** ] 對話方塊，請選取 [如果您同意授權條款即 **接受** ]。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

在 **整合式終端** 機中  >  ，從工具列)  (視圖 **終端** 機，請執行下列命令：

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 在 [ **方案** ] 提要欄位中，以滑鼠右鍵按一下 `BlazorServerSignalRApp` 專案，然後選取 [ **管理 NuGet 套件**]。

1. 在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [來源] 下拉式清單設定為 `nuget.org` 。

1. 選取 **[流覽]** 之後， `Microsoft.AspNetCore.SignalR.Client` 在 [搜尋] 方塊中輸入。

1. 在搜尋結果中，選取套件旁的核取方塊 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 。 設定版本，使其符合應用程式的共用架構。 選取 [ **新增套件**]。

1. 如果出現 [ **接受授權** ] 對話方塊，請在您同意授權條款時選取 [ **接受** ]。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

在來自專案資料夾的命令 shell 中，執行下列命令：

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a>新增 system.string. Web 封裝

*本節僅適用于 ASP.NET Core 3.x 版的應用程式。*

由於在 ASP.NET Core 3.x 應用程式中使用5.x 時發生套件解析問題 [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) ，因此專案需要的封裝參考 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 。 在 .NET 5 的未來修補程式版本中，將會解決基礎問題。 如需詳細資訊，請參閱 [System.Text.Json 定義沒有相依性的 netcoreapp 3.0 (dotnet/runtime #45560) ](https://github.com/dotnet/runtime/issues/45560)。

若要加入 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 至專案，請遵循您選擇的工具指導方針：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. 在 **方案總管** 中，以滑鼠右鍵按一下 `BlazorServerSignalRApp` 專案，然後選取 [ **管理 NuGet 套件**]。

1. 在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [ **封裝來源** ] 設定為 `nuget.org` 。

1. 選取 **[流覽]** 之後， `System.Text.Encodings.Web` 在 [搜尋] 方塊中輸入。

1. 在搜尋結果中，選取 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 套件。 選取符合使用中共用架構的套件版本。 選取 [安裝]。

1. 如果出現 [ **預覽變更** ] 對話方塊，請選取 **[確定]**。

1. 如果出現 [ **接受授權** ] 對話方塊，請選取 [如果您同意授權條款即 **接受** ]。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

在 **整合式終端** 機中 > ，從工具列)  (查看 **終端** 機，請執行下列命令：

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 在 [ **方案** ] 提要欄位中，以滑鼠右鍵按一下 `BlazorServerSignalRApp` 專案，然後選取 [ **管理 NuGet 套件**]。

1. 在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [來源] 下拉式清單設定為 `nuget.org` 。

1. 選取 **[流覽]** 之後， `System.Text.Encodings.Web` 在 [搜尋] 方塊中輸入。

1. 在搜尋結果中，選取套件旁的核取方塊 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) ，選取符合使用中共用架構的正確套件版本，然後選取 [ **新增套件**]。

1. 如果出現 [ **接受授權** ] 對話方塊，請在您同意授權條款時選取 [ **接受** ]。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

在來自專案資料夾的命令 shell 中，執行下列命令：

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。

---

::: moniker-end

## <a name="add-a-signalr-hub"></a>新增 SignalR 中樞

建立 `Hubs` (複數) 資料夾，並 `ChatHub` () 新增下列類別 `Hubs/ChatHub.cs` ：

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>新增中樞的服務和端點 SignalR

1. 開啟 `Startup.cs` 檔案。

1. 將和類別的命名空間加入 <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> `ChatHub` 至檔案頂端：

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. 將回應壓縮中介軟體服務新增至 `Startup.ConfigureServices` ：

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. 在 `Startup.Configure` 中：

   * 在處理管線的設定頂端使用回應壓縮中介軟體。
   * 在對應中樞和用戶端回復的端點之間 Blazor ，新增中樞的端點。

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. 將回應壓縮中介軟體服務新增至 `Startup.ConfigureServices` ：

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. 在 `Startup.Configure` 中：

   * 在處理管線的設定頂端使用回應壓縮中介軟體。
   * 在對應中樞和用戶端回復的端點之間 Blazor ，新增中樞的端點。

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a>新增 Razor 聊天的元件程式碼

1. 開啟 `Pages/Index.razor` 檔案。

::: moniker range=">= aspnetcore-5.0"

1. 以下列程式碼取代標記：

   [!code-razor[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. 以下列程式碼取代標記：

   [!code-razor[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>執行應用程式

遵循您工具的指導方針：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 按下<kbd>f5</kbd>鍵以執行應用程式的偵錯工具，或按<kbd>Ctrl</kbd> + <kbd>F5</kbd>執行應用程式，而不進行偵錯工具。

1. 從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。

1. 選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。 名稱和訊息會立即顯示在兩個頁面上：

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 按下<kbd>f5</kbd>鍵以執行應用程式的偵錯工具，或按<kbd>Ctrl</kbd> + <kbd>F5</kbd>執行應用程式，而不進行偵錯工具。

1. 從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。

1. 選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。 名稱和訊息會立即顯示在兩個頁面上：

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 按<kbd>⌘</kbd> + <kbd>↩</kbd>以執行應用程式的偵錯工具或<kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd>執行應用程式，而不需進行任何偵錯工具。

1. 從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。

1. 選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。 名稱和訊息會立即顯示在兩個頁面上：

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. 在來自專案資料夾的命令 shell 中，執行下列命令：

   ```dotnetcli
   dotnet run
   ```

1. 從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。

1. 選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。 名稱和訊息會立即顯示在兩個頁面上：

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

::: zone-end

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立 Blazor 專案
> * 新增 SignalR 用戶端程式庫
> * 新增 SignalR 中樞
> * 新增 SignalR 中樞的服務和端點 SignalR
> * 新增 Razor 聊天的元件程式碼

若要深入瞭解如何建立 Blazor 應用程式，請參閱 Blazor 檔：

> [!div class="nextstepaction"]
> <xref:blazor/index>
> [使用 Identity 伺服器、websocket 和 Server-Sent 事件的持有人權杖驗證](xref:signalr/authn-and-authz#bearer-token-authentication)

## <a name="additional-resources"></a>其他資源

* <xref:signalr/introduction>
* [SignalR 驗證的跨原始來源協商](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
