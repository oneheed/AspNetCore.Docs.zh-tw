---
title: 搭配使用 SignalR ASP.NET Core Blazor WebAssembly
author: guardrex
description: 建立搭配使用 ASP.NET Core 的聊天應用 SignalR 程式 Blazor WebAssembly 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 4d33e99ceb8273487144447eae324469df67c9ff
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633379"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a><span data-ttu-id="a4b17-103">搭配使用 SignalR ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="a4b17-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="a4b17-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a4b17-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a4b17-105">本教學課程會教您使用與建立即時應用程式的基本概念 SignalR Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="a4b17-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="a4b17-106">您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="a4b17-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a4b17-107">建立 Blazor WebAssembly 託管應用程式專案</span><span class="sxs-lookup"><span data-stu-id="a4b17-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="a4b17-108">新增 SignalR 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="a4b17-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="a4b17-109">新增 SignalR 中樞</span><span class="sxs-lookup"><span data-stu-id="a4b17-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="a4b17-110">新增 SignalR 中樞的服務和端點 SignalR</span><span class="sxs-lookup"><span data-stu-id="a4b17-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="a4b17-111">新增 Razor 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="a4b17-111">Add Razor component code for chat</span></span>

<span data-ttu-id="a4b17-112">在本教學課程結尾，您將會有一個可運作的聊天應用程式。</span><span class="sxs-lookup"><span data-stu-id="a4b17-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="a4b17-113">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="a4b17-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a4b17-114">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="a4b17-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4b17-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4b17-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a4b17-116">使用**ASP.NET 和 網頁程式開發**工作負載[Visual Studio 2019 16.6 或更新版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="a4b17-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4b17-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4b17-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4b17-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4b17-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="a4b17-119">Visual Studio for Mac 8.6 版或更新版本</span><span class="sxs-lookup"><span data-stu-id="a4b17-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="a4b17-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a4b17-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="a4b17-121">建立託管 Blazor WebAssembly 應用程式專案</span><span class="sxs-lookup"><span data-stu-id="a4b17-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="a4b17-122">遵循您選擇的工具指導方針：</span><span class="sxs-lookup"><span data-stu-id="a4b17-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4b17-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4b17-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="a4b17-124">需要 Visual Studio 16.6 或更新版本，以及 .NET Core SDK 3.1.300 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="a4b17-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="a4b17-125">建立新專案。</span><span class="sxs-lookup"><span data-stu-id="a4b17-125">Create a new project.</span></span>

1. <span data-ttu-id="a4b17-126">選取\*\* Blazor 應用程式\*\*，然後選取 **[下一步]**。</span><span class="sxs-lookup"><span data-stu-id="a4b17-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="a4b17-127">輸入 `BlazorSignalRApp` [ **專案名稱** ] 欄位。</span><span class="sxs-lookup"><span data-stu-id="a4b17-127">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="a4b17-128">確認 **位置** 專案是正確的，或提供專案的位置。</span><span class="sxs-lookup"><span data-stu-id="a4b17-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="a4b17-129">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="a4b17-129">Select **Create**.</span></span>

1. <span data-ttu-id="a4b17-130">選擇\*\* Blazor WebAssembly 應用程式\*\*範本。</span><span class="sxs-lookup"><span data-stu-id="a4b17-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="a4b17-131">在 [ **Advanced**] 底下，選取 [ **hosted ASP.NET Core** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="a4b17-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="a4b17-132">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="a4b17-132">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4b17-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4b17-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="a4b17-134">在命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="a4b17-134">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="a4b17-135">在 Visual Studio Code 中，開啟應用程式的專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="a4b17-135">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="a4b17-136">當對話方塊出現時，若要新增資產以建立和偵測應用程式，請選取 [ **是**]。</span><span class="sxs-lookup"><span data-stu-id="a4b17-136">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="a4b17-137">Visual Studio Code 會自動加入 `.vscode` 具有所產生檔案和檔案的資料夾 `launch.json` `tasks.json` 。</span><span class="sxs-lookup"><span data-stu-id="a4b17-137">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4b17-138">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4b17-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="a4b17-139">安裝最新版本的 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) ，然後執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="a4b17-139">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="a4b17-140">選取**File**  >  [檔案**新方案**] 或 [**開始] 視窗**中的 [建立**新**專案]。</span><span class="sxs-lookup"><span data-stu-id="a4b17-140">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="a4b17-141">在側邊欄中，選取 [ **Web] 和 [主控台**  >  **應用程式**]。</span><span class="sxs-lookup"><span data-stu-id="a4b17-141">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="a4b17-142">選擇\*\* Blazor WebAssembly 應用程式\*\*範本。</span><span class="sxs-lookup"><span data-stu-id="a4b17-142">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="a4b17-143">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="a4b17-143">Select **Next**.</span></span>

1. <span data-ttu-id="a4b17-144">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="a4b17-144">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="a4b17-145">選取 [ **主控 ASP.NET Core** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="a4b17-145">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="a4b17-146">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="a4b17-146">Select **Next**.</span></span>

1. <span data-ttu-id="a4b17-147">在 [ **專案名稱** ] 欄位中，為應用程式命名 `BlazorSignalRApp` 。</span><span class="sxs-lookup"><span data-stu-id="a4b17-147">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="a4b17-148">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="a4b17-148">Select **Create**.</span></span>

   <span data-ttu-id="a4b17-149">如果出現提示信任開發憑證，請信任憑證並繼續。</span><span class="sxs-lookup"><span data-stu-id="a4b17-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="a4b17-150">需要使用者和 keychain 密碼才能信任憑證。</span><span class="sxs-lookup"><span data-stu-id="a4b17-150">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="a4b17-151">流覽至專案資料夾，然後開啟專案的方案檔 () 來開啟專案 `.sln` 。</span><span class="sxs-lookup"><span data-stu-id="a4b17-151">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a4b17-152">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a4b17-152">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="a4b17-153">在命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="a4b17-153">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="a4b17-154">新增 SignalR 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="a4b17-154">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4b17-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4b17-155">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="a4b17-156">在 **方案總管**中，以滑鼠右鍵按一下 `BlazorSignalRApp.Client` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="a4b17-156">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="a4b17-157">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [ **封裝來源** ] 設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="a4b17-157">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="a4b17-158">選取 **[流覽]** 之後， `Microsoft.AspNetCore.SignalR.Client` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="a4b17-158">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="a4b17-159">在搜尋結果中選取封裝， [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 然後選取 [ **安裝**]。</span><span class="sxs-lookup"><span data-stu-id="a4b17-159">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="a4b17-160">如果出現 [ **預覽變更** ] 對話方塊，請選取 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="a4b17-160">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="a4b17-161">如果出現 [ **接受授權** ] 對話方塊，請選取 [如果您同意授權條款即 **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="a4b17-161">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4b17-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4b17-162">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="a4b17-163">在**整合式終端**機**View**中  >  ，從工具列)  (查看**終端**機，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="a4b17-163">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4b17-164">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4b17-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="a4b17-165">在 [ **方案** ] 提要欄位中，以滑鼠右鍵按一下 `BlazorSignalRApp.Client` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="a4b17-165">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="a4b17-166">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [來源] 下拉式清單設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="a4b17-166">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="a4b17-167">選取 **[流覽]** 之後， `Microsoft.AspNetCore.SignalR.Client` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="a4b17-167">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="a4b17-168">在搜尋結果中，選取套件旁的核取方塊 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) ，然後選取 [ **新增套件**]。</span><span class="sxs-lookup"><span data-stu-id="a4b17-168">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="a4b17-169">如果出現 [ **接受授權** ] 對話方塊，請在您同意授權條款時選取 [ **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="a4b17-169">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a4b17-170">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a4b17-170">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="a4b17-171">在命令列介面中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="a4b17-171">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="a4b17-172">新增 SignalR 中樞</span><span class="sxs-lookup"><span data-stu-id="a4b17-172">Add a SignalR hub</span></span>

<span data-ttu-id="a4b17-173">在 `BlazorSignalRApp.Server` 專案中，建立 `Hubs` (複數) 資料夾，並 () 新增下列 `ChatHub` 類別 `Hubs/ChatHub.cs` ：</span><span class="sxs-lookup"><span data-stu-id="a4b17-173">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="a4b17-174">新增中樞的服務和端點 SignalR</span><span class="sxs-lookup"><span data-stu-id="a4b17-174">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="a4b17-175">在 `BlazorSignalRApp.Server` 專案中，開啟 `Startup.cs` 檔案。</span><span class="sxs-lookup"><span data-stu-id="a4b17-175">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="a4b17-176">將類別的命名空間新增 `ChatHub` 至檔案頂端：</span><span class="sxs-lookup"><span data-stu-id="a4b17-176">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="a4b17-177">新增 SignalR 和回應壓縮中介軟體服務至 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="a4b17-177">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="a4b17-178">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="a4b17-178">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="a4b17-179">在處理管線的設定頂端使用回應壓縮中介軟體。</span><span class="sxs-lookup"><span data-stu-id="a4b17-179">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="a4b17-180">在控制器的端點和用戶端的回復之間，新增中樞的端點。</span><span class="sxs-lookup"><span data-stu-id="a4b17-180">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="a4b17-181">新增 Razor 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="a4b17-181">Add Razor component code for chat</span></span>

1. <span data-ttu-id="a4b17-182">在 `BlazorSignalRApp.Client` 專案中，開啟 `Pages/Index.razor` 檔案。</span><span class="sxs-lookup"><span data-stu-id="a4b17-182">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="a4b17-183">以下列程式碼取代標記：</span><span class="sxs-lookup"><span data-stu-id="a4b17-183">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="a4b17-184">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="a4b17-184">Run the app</span></span>

1. <span data-ttu-id="a4b17-185">遵循您工具的指導方針：</span><span class="sxs-lookup"><span data-stu-id="a4b17-185">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4b17-186">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4b17-186">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a4b17-187">在 **方案總管**中，選取 `BlazorSignalRApp.Server` 專案。</span><span class="sxs-lookup"><span data-stu-id="a4b17-187">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="a4b17-188">按下<kbd>f5</kbd>鍵以執行應用程式的偵錯工具，或按<kbd>Ctrl</kbd> + <kbd>F5</kbd>執行應用程式，而不進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="a4b17-188">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="a4b17-189">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="a4b17-189">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="a4b17-190">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="a4b17-190">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="a4b17-191">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="a4b17-191">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor WebAssembly) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="a4b17-193">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="a4b17-193">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4b17-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4b17-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="a4b17-195">當 VS Code 提供來為伺服器應用程式 () 建立啟動設定檔時 `.vscode/launch.json` ， `program` 專案會顯示如下，以指向應用程式的元件 (`{APPLICATION NAME}.Server.dll`) ：</span><span class="sxs-lookup"><span data-stu-id="a4b17-195">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="a4b17-196">按下<kbd>f5</kbd>鍵以執行應用程式的偵錯工具，或按<kbd>Ctrl</kbd> + <kbd>F5</kbd>執行應用程式，而不進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="a4b17-196">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="a4b17-197">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="a4b17-197">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="a4b17-198">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="a4b17-198">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="a4b17-199">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="a4b17-199">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor WebAssembly) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="a4b17-201">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="a4b17-201">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4b17-202">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4b17-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="a4b17-203">在 [ **方案** ] 側邊欄中，選取 `BlazorSignalRApp.Server` 專案。</span><span class="sxs-lookup"><span data-stu-id="a4b17-203">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="a4b17-204">按<kbd>⌘</kbd> + <kbd>↩</kbd>以執行應用程式的偵錯工具或<kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd>執行應用程式，而不需進行任何偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="a4b17-204">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="a4b17-205">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="a4b17-205">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="a4b17-206">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="a4b17-206">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="a4b17-207">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="a4b17-207">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor WebAssembly) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="a4b17-209">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="a4b17-209">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a4b17-210">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a4b17-210">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="a4b17-211">在命令列介面中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="a4b17-211">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="a4b17-212">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="a4b17-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="a4b17-213">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="a4b17-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="a4b17-214">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="a4b17-214">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor WebAssembly) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="a4b17-216">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="a4b17-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="a4b17-217">後續步驟</span><span class="sxs-lookup"><span data-stu-id="a4b17-217">Next steps</span></span>

<span data-ttu-id="a4b17-218">在本教學課程中，您已了解如何：</span><span class="sxs-lookup"><span data-stu-id="a4b17-218">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a4b17-219">建立 Blazor WebAssembly 託管應用程式專案</span><span class="sxs-lookup"><span data-stu-id="a4b17-219">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="a4b17-220">新增 SignalR 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="a4b17-220">Add the SignalR client library</span></span>
> * <span data-ttu-id="a4b17-221">新增 SignalR 中樞</span><span class="sxs-lookup"><span data-stu-id="a4b17-221">Add a SignalR hub</span></span>
> * <span data-ttu-id="a4b17-222">新增 SignalR 中樞的服務和端點 SignalR</span><span class="sxs-lookup"><span data-stu-id="a4b17-222">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="a4b17-223">新增 Razor 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="a4b17-223">Add Razor component code for chat</span></span>

<span data-ttu-id="a4b17-224">若要深入瞭解如何建立 Blazor 應用程式，請參閱 Blazor 檔：</span><span class="sxs-lookup"><span data-stu-id="a4b17-224">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="a4b17-225">其他資源</span><span class="sxs-lookup"><span data-stu-id="a4b17-225">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="a4b17-226">SignalR 驗證的跨原始來源協商</span><span class="sxs-lookup"><span data-stu-id="a4b17-226">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
