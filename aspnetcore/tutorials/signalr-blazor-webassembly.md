---
title: SignalR使用 ASP.NET Core 搭配Blazor WebAssembly
author: guardrex
description: 建立使用 ASP.NET Core 搭配的聊天應用 SignalR 程式 Blazor WebAssembly 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
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
ms.openlocfilehash: 3d7ae49142849c589a1a20c33d30e87747ad1935
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021623"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a><span data-ttu-id="73a70-103">SignalR使用 ASP.NET Core 搭配Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="73a70-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="73a70-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="73a70-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="73a70-105">本教學課程將教您使用與建立即時應用程式的基本概念 SignalR Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="73a70-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="73a70-106">您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="73a70-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="73a70-107">建立 Blazor WebAssembly 託管應用程式專案</span><span class="sxs-lookup"><span data-stu-id="73a70-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="73a70-108">新增 SignalR 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="73a70-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="73a70-109">新增 SignalR 中樞</span><span class="sxs-lookup"><span data-stu-id="73a70-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="73a70-110">新增 SignalR 服務和中樞的端點 SignalR</span><span class="sxs-lookup"><span data-stu-id="73a70-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="73a70-111">新增 Razor 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="73a70-111">Add Razor component code for chat</span></span>

<span data-ttu-id="73a70-112">在本教學課程結尾，您將會有一個可運作的聊天應用程式。</span><span class="sxs-lookup"><span data-stu-id="73a70-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="73a70-113">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="73a70-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="73a70-114">必要條件</span><span class="sxs-lookup"><span data-stu-id="73a70-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="73a70-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73a70-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="73a70-116">使用**ASP.NET 和 網頁程式開發**工作負載[Visual Studio 2019 16.6 或更新版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="73a70-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="73a70-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="73a70-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="73a70-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="73a70-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="73a70-119">Visual Studio for Mac 8.6 版或更新版本</span><span class="sxs-lookup"><span data-stu-id="73a70-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="73a70-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="73a70-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="73a70-121">建立託管 Blazor WebAssembly 應用程式專案</span><span class="sxs-lookup"><span data-stu-id="73a70-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="73a70-122">遵循您選擇的工具的指導方針：</span><span class="sxs-lookup"><span data-stu-id="73a70-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="73a70-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73a70-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="73a70-124">Visual Studio 16.6 或更新版本，而且需要 .NET Core SDK 3.1.300 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="73a70-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="73a70-125">建立新專案。</span><span class="sxs-lookup"><span data-stu-id="73a70-125">Create a new project.</span></span>

1. <span data-ttu-id="73a70-126">選取\*\* Blazor 應用程式\*\*，然後選取 **[下一步]**。</span><span class="sxs-lookup"><span data-stu-id="73a70-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="73a70-127">`BlazorSignalRApp`在 [**專案名稱**] 欄位中輸入。</span><span class="sxs-lookup"><span data-stu-id="73a70-127">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="73a70-128">確認 [**位置**] 專案正確，或提供專案的 [位置]。</span><span class="sxs-lookup"><span data-stu-id="73a70-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="73a70-129">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="73a70-129">Select **Create**.</span></span>

1. <span data-ttu-id="73a70-130">選擇 [ \*\* Blazor WebAssembly 應用程式\*\*] 範本。</span><span class="sxs-lookup"><span data-stu-id="73a70-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="73a70-131">在 [ **Advanced**] 底下，選取 [ **ASP.NET Core**裝載] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="73a70-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="73a70-132">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="73a70-132">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="73a70-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="73a70-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="73a70-134">在命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="73a70-134">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="73a70-135">在 Visual Studio Code 中，開啟應用程式的專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="73a70-135">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="73a70-136">當對話方塊出現以新增資產以建立和偵錯工具時，請選取 **[是**]。</span><span class="sxs-lookup"><span data-stu-id="73a70-136">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="73a70-137">Visual Studio Code 會自動新增 `.vscode` 具有所產生檔案和檔案的資料夾 `launch.json` `tasks.json` 。</span><span class="sxs-lookup"><span data-stu-id="73a70-137">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="73a70-138">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="73a70-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="73a70-139">安裝最新版本的[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) ，並執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="73a70-139">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="73a70-140">**File**  >  在 [**開始] 視窗**中選取 [檔案] [**新增方案**] 或 [建立**新**專案]。</span><span class="sxs-lookup"><span data-stu-id="73a70-140">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="73a70-141">在提要欄位中，選取 [ **Web 和主控台**  >  **應用程式**]。</span><span class="sxs-lookup"><span data-stu-id="73a70-141">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="73a70-142">選擇 [ \*\* Blazor WebAssembly 應用程式\*\*] 範本。</span><span class="sxs-lookup"><span data-stu-id="73a70-142">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="73a70-143">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="73a70-143">Select **Next**.</span></span>

1. <span data-ttu-id="73a70-144">確認 [**驗證**] 已設為 [**無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="73a70-144">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="73a70-145">選取 [**主控 ASP.NET Core** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="73a70-145">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="73a70-146">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="73a70-146">Select **Next**.</span></span>

1. <span data-ttu-id="73a70-147">在 [**專案名稱**] 欄位中，將應用程式命名為 `BlazorSignalRApp` 。</span><span class="sxs-lookup"><span data-stu-id="73a70-147">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="73a70-148">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="73a70-148">Select **Create**.</span></span>

   <span data-ttu-id="73a70-149">如果出現會信任開發憑證的提示，請信任憑證並繼續。</span><span class="sxs-lookup"><span data-stu-id="73a70-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="73a70-150">需要使用者和 keychain 密碼，才能信任憑證。</span><span class="sxs-lookup"><span data-stu-id="73a70-150">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="73a70-151">流覽至專案資料夾，然後開啟專案的方案檔 () ，以開啟專案 `.sln` 。</span><span class="sxs-lookup"><span data-stu-id="73a70-151">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="73a70-152">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="73a70-152">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="73a70-153">在命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="73a70-153">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="73a70-154">新增 SignalR 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="73a70-154">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="73a70-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73a70-155">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="73a70-156">在**方案總管**中，以滑鼠右鍵按一下 `BlazorSignalRApp.Client` 專案，然後選取 [**管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="73a70-156">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="73a70-157">在 [**管理 NuGet 封裝**] 對話方塊中，確認 [**套件來源**] 設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="73a70-157">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="73a70-158">在選取 **[流覽]** 的情況下，于 `Microsoft.AspNetCore.SignalR.Client` 搜尋方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="73a70-158">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="73a70-159">在搜尋結果中選取套件， [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 然後選取 [**安裝**]。</span><span class="sxs-lookup"><span data-stu-id="73a70-159">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="73a70-160">如果出現 [**預覽變更**] 對話方塊，請選取 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="73a70-160">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="73a70-161">如果 [**接受授權**] 對話方塊出現，如果您同意授權條款，請選取 [**我接受**]。</span><span class="sxs-lookup"><span data-stu-id="73a70-161">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="73a70-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="73a70-162">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="73a70-163">在**整合式終端**機**View**中 ( >  從工具列) 查看**終端**機]，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="73a70-163">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="73a70-164">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="73a70-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="73a70-165">在 [**解決方案**] 提要欄位中，以滑鼠右鍵按一下 `BlazorSignalRApp.Client` 專案，然後選取 [**管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="73a70-165">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="73a70-166">在 [**管理 NuGet 封裝**] 對話方塊中，確認 [來源] 下拉式設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="73a70-166">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="73a70-167">在選取 **[流覽]** 的情況下，于 `Microsoft.AspNetCore.SignalR.Client` 搜尋方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="73a70-167">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="73a70-168">在搜尋結果中，選取封裝旁的核取方塊 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) ，然後選取 [**新增套件**]。</span><span class="sxs-lookup"><span data-stu-id="73a70-168">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="73a70-169">如果 [**接受授權**] 對話方塊出現，請選取 [**接受**] （如果您同意授權條款）。</span><span class="sxs-lookup"><span data-stu-id="73a70-169">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="73a70-170">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="73a70-170">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="73a70-171">在命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="73a70-171">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="73a70-172">新增 SignalR 中樞</span><span class="sxs-lookup"><span data-stu-id="73a70-172">Add a SignalR hub</span></span>

<span data-ttu-id="73a70-173">在 `BlazorSignalRApp.Server` 專案中，建立 `Hubs` (複數) 資料夾，並 () 加入下列 `ChatHub` 類別 `Hubs/ChatHub.cs` ：</span><span class="sxs-lookup"><span data-stu-id="73a70-173">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="73a70-174">新增服務和中樞的端點 SignalR</span><span class="sxs-lookup"><span data-stu-id="73a70-174">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="73a70-175">在 `BlazorSignalRApp.Server` 專案中，開啟 `Startup.cs` 檔案。</span><span class="sxs-lookup"><span data-stu-id="73a70-175">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="73a70-176">將類別的命名空間新增 `ChatHub` 至檔案頂端：</span><span class="sxs-lookup"><span data-stu-id="73a70-176">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="73a70-177">將 SignalR 和回應壓縮中介軟體服務新增至 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="73a70-177">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="73a70-178">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="73a70-178">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="73a70-179">使用處理管線設定頂端的回應壓縮中介軟體。</span><span class="sxs-lookup"><span data-stu-id="73a70-179">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="73a70-180">在控制器的端點和用戶端的回退之間，新增中樞的端點。</span><span class="sxs-lookup"><span data-stu-id="73a70-180">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="73a70-181">新增 Razor 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="73a70-181">Add Razor component code for chat</span></span>

1. <span data-ttu-id="73a70-182">在 `BlazorSignalRApp.Client` 專案中，開啟 `Pages/Index.razor` 檔案。</span><span class="sxs-lookup"><span data-stu-id="73a70-182">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="73a70-183">將標記取代為下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="73a70-183">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="73a70-184">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="73a70-184">Run the app</span></span>

1. <span data-ttu-id="73a70-185">遵循您工具的指導方針：</span><span class="sxs-lookup"><span data-stu-id="73a70-185">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="73a70-186">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73a70-186">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="73a70-187">在 [**方案總管**中，選取 `BlazorSignalRApp.Server` 專案。</span><span class="sxs-lookup"><span data-stu-id="73a70-187">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="73a70-188">按<kbd>f5</kbd>鍵以執行應用程式的偵測或<kbd>Ctrl</kbd> + <kbd>F5</kbd> ，以執行應用程式而不進行偵測。</span><span class="sxs-lookup"><span data-stu-id="73a70-188">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="73a70-189">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="73a70-189">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="73a70-190">選擇任一個瀏覽器、輸入名稱和訊息，然後選取按鈕來傳送訊息。</span><span class="sxs-lookup"><span data-stu-id="73a70-190">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="73a70-191">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="73a70-191">The name and message are displayed on both pages instantly:</span></span>

   ![：：：無-loc (SignalR) ：：：：：： no-loc (Blazor WebAssembly) ：：：範例應用程式在兩個顯示交換訊息的瀏覽器視窗中開啟。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="73a70-193">引號：*星星 TREK VI：未發現的國家/地區* &copy; 1991[重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="73a70-193">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="73a70-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="73a70-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="73a70-195">當 VS Code 提供來建立伺服器應用程式 () 的啟動設定檔時 `.vscode/launch.json` ， `program` 專案看起來會像下面這樣，以指向應用程式的元件 (`{APPLICATION NAME}.Server.dll`) ：</span><span class="sxs-lookup"><span data-stu-id="73a70-195">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="73a70-196">按<kbd>f5</kbd>鍵以執行應用程式的偵測或<kbd>Ctrl</kbd> + <kbd>F5</kbd> ，以執行應用程式而不進行偵測。</span><span class="sxs-lookup"><span data-stu-id="73a70-196">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="73a70-197">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="73a70-197">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="73a70-198">選擇任一個瀏覽器、輸入名稱和訊息，然後選取按鈕來傳送訊息。</span><span class="sxs-lookup"><span data-stu-id="73a70-198">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="73a70-199">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="73a70-199">The name and message are displayed on both pages instantly:</span></span>

   ![：：：無-loc (SignalR) ：：：：：： no-loc (Blazor WebAssembly) ：：：範例應用程式在兩個顯示交換訊息的瀏覽器視窗中開啟。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="73a70-201">引號：*星星 TREK VI：未發現的國家/地區* &copy; 1991[重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="73a70-201">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="73a70-202">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="73a70-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="73a70-203">在 [**方案**] 提要欄位中，選取 `BlazorSignalRApp.Server` 專案。</span><span class="sxs-lookup"><span data-stu-id="73a70-203">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="73a70-204">按<kbd>則是⌘</kbd> + <kbd>↩</kbd>以執行應用程式的偵錯工具，或<kbd>⌥</kbd> + <kbd>則是⌘</kbd> + <kbd>↩</kbd>以執行應用程式而不進行偵測。</span><span class="sxs-lookup"><span data-stu-id="73a70-204">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="73a70-205">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="73a70-205">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="73a70-206">選擇任一個瀏覽器、輸入名稱和訊息，然後選取按鈕來傳送訊息。</span><span class="sxs-lookup"><span data-stu-id="73a70-206">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="73a70-207">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="73a70-207">The name and message are displayed on both pages instantly:</span></span>

   ![：：：無-loc (SignalR) ：：：：：： no-loc (Blazor WebAssembly) ：：：範例應用程式在兩個顯示交換訊息的瀏覽器視窗中開啟。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="73a70-209">引號：*星星 TREK VI：未發現的國家/地區* &copy; 1991[重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="73a70-209">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="73a70-210">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="73a70-210">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="73a70-211">在命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="73a70-211">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="73a70-212">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="73a70-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="73a70-213">選擇任一個瀏覽器、輸入名稱和訊息，然後選取按鈕來傳送訊息。</span><span class="sxs-lookup"><span data-stu-id="73a70-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="73a70-214">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="73a70-214">The name and message are displayed on both pages instantly:</span></span>

   ![：：：無-loc (SignalR) ：：：：：： no-loc (Blazor WebAssembly) ：：：範例應用程式在兩個顯示交換訊息的瀏覽器視窗中開啟。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="73a70-216">引號：*星星 TREK VI：未發現的國家/地區* &copy; 1991[重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="73a70-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="73a70-217">後續步驟</span><span class="sxs-lookup"><span data-stu-id="73a70-217">Next steps</span></span>

<span data-ttu-id="73a70-218">在本教學課程中，您已了解如何：</span><span class="sxs-lookup"><span data-stu-id="73a70-218">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="73a70-219">建立 Blazor WebAssembly 託管應用程式專案</span><span class="sxs-lookup"><span data-stu-id="73a70-219">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="73a70-220">新增 SignalR 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="73a70-220">Add the SignalR client library</span></span>
> * <span data-ttu-id="73a70-221">新增 SignalR 中樞</span><span class="sxs-lookup"><span data-stu-id="73a70-221">Add a SignalR hub</span></span>
> * <span data-ttu-id="73a70-222">新增 SignalR 服務和中樞的端點 SignalR</span><span class="sxs-lookup"><span data-stu-id="73a70-222">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="73a70-223">新增 Razor 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="73a70-223">Add Razor component code for chat</span></span>

<span data-ttu-id="73a70-224">若要深入瞭解如何建立 Blazor 應用程式，請參閱 Blazor 檔：</span><span class="sxs-lookup"><span data-stu-id="73a70-224">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="73a70-225">其他資源</span><span class="sxs-lookup"><span data-stu-id="73a70-225">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="73a70-226">SignalR用於驗證的跨原始來源協調</span><span class="sxs-lookup"><span data-stu-id="73a70-226">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
