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
ms.openlocfilehash: 5bf80bd9147b6210fd2b115f7c009cc518f6f5cb
ms.sourcegitcommit: 20a41c8e40a2e69e99291e2fe18caa04c02e7109
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2021
ms.locfileid: "99579102"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a><span data-ttu-id="cac14-103">搭配使用 SignalR ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="cac14-103">Use ASP.NET Core SignalR with Blazor</span></span>

<span data-ttu-id="cac14-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cac14-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cac14-105">本教學課程會教您使用與建立即時應用程式的基本概念 SignalR Blazor 。</span><span class="sxs-lookup"><span data-stu-id="cac14-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor.</span></span> <span data-ttu-id="cac14-106">您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="cac14-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cac14-107">建立 Blazor 專案</span><span class="sxs-lookup"><span data-stu-id="cac14-107">Create a Blazor project</span></span>
> * <span data-ttu-id="cac14-108">新增 SignalR 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="cac14-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="cac14-109">新增 SignalR 中樞</span><span class="sxs-lookup"><span data-stu-id="cac14-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="cac14-110">新增 SignalR 中樞的服務和端點 SignalR</span><span class="sxs-lookup"><span data-stu-id="cac14-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="cac14-111">新增 Razor 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="cac14-111">Add Razor component code for chat</span></span>

<span data-ttu-id="cac14-112">在本教學課程結尾，您將會有一個可運作的聊天應用程式。</span><span class="sxs-lookup"><span data-stu-id="cac14-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="cac14-113">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="cac14-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cac14-114">必要條件</span><span class="sxs-lookup"><span data-stu-id="cac14-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="cac14-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cac14-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cac14-116">使用 **ASP.NET 和 網頁程式開發** 工作負載 [Visual Studio 2019 16.8 或更新版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="cac14-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cac14-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cac14-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cac14-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cac14-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="cac14-119">Visual Studio for Mac 8.8 版或更新版本</span><span class="sxs-lookup"><span data-stu-id="cac14-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="cac14-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cac14-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="cac14-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cac14-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cac14-122">使用 **ASP.NET 和 網頁程式開發** 工作負載 [Visual Studio 2019 16.6 或更新版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="cac14-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cac14-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cac14-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cac14-124">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cac14-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="cac14-125">Visual Studio for Mac 8.6 版或更新版本</span><span class="sxs-lookup"><span data-stu-id="cac14-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="cac14-126">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cac14-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="cac14-127">建立託管 Blazor WebAssembly 應用程式</span><span class="sxs-lookup"><span data-stu-id="cac14-127">Create a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="cac14-128">遵循您選擇的工具指導方針：</span><span class="sxs-lookup"><span data-stu-id="cac14-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cac14-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cac14-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="cac14-130">需要 Visual Studio 16.8 或更新版本，以及 .NET Core SDK 5.0.0 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="cac14-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="cac14-131">需要 Visual Studio 16.6 或更新版本，以及 .NET Core SDK 3.1.300 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="cac14-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="cac14-132">建立新專案。</span><span class="sxs-lookup"><span data-stu-id="cac14-132">Create a new project.</span></span>

1. <span data-ttu-id="cac14-133">選取 **Blazor 應用程式**，然後選取 **[下一步]**。</span><span class="sxs-lookup"><span data-stu-id="cac14-133">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="cac14-134">輸入 `BlazorWebAssemblySignalRApp` [ **專案名稱** ] 欄位。</span><span class="sxs-lookup"><span data-stu-id="cac14-134">Type `BlazorWebAssemblySignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="cac14-135">確認 **位置** 專案是正確的，或提供專案的位置。</span><span class="sxs-lookup"><span data-stu-id="cac14-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="cac14-136">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="cac14-136">Select **Create**.</span></span>

1. <span data-ttu-id="cac14-137">選擇 **Blazor WebAssembly 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="cac14-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="cac14-138">在 [ **Advanced**] 底下，選取 [ **hosted ASP.NET Core** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="cac14-138">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="cac14-139">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="cac14-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cac14-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cac14-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="cac14-141">在命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="cac14-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   <span data-ttu-id="cac14-142">`-ho|--hosted`選項會建立託管 Blazor WebAssembly 方案。</span><span class="sxs-lookup"><span data-stu-id="cac14-142">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

   <span data-ttu-id="cac14-143">`-o|--output`選項會建立解決方案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="cac14-143">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="cac14-144">如果您已建立解決方案的資料夾，並在該資料夾中開啟命令 shell，請省略 `-o|--output` 選項和值以建立解決方案。</span><span class="sxs-lookup"><span data-stu-id="cac14-144">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

1. <span data-ttu-id="cac14-145">在 Visual Studio Code 中，開啟應用程式的專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="cac14-145">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="cac14-146">當對話方塊出現時，若要新增資產以建立和偵測應用程式，請選取 [ **是**]。</span><span class="sxs-lookup"><span data-stu-id="cac14-146">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="cac14-147">Visual Studio Code 會自動加入 `.vscode` 具有所產生檔案和檔案的資料夾 `launch.json` `tasks.json` 。</span><span class="sxs-lookup"><span data-stu-id="cac14-147">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cac14-148">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cac14-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="cac14-149">安裝最新版本的 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) ，然後執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="cac14-149">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="cac14-150">選取  >  [檔案 **新方案**] 或 [**開始] 視窗** 中的 [建立 **新** 專案]。</span><span class="sxs-lookup"><span data-stu-id="cac14-150">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="cac14-151">在側邊欄中，選取 [ **Web] 和 [主控台**  >  **應用程式**]。</span><span class="sxs-lookup"><span data-stu-id="cac14-151">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="cac14-152">選擇 **Blazor WebAssembly 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="cac14-152">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="cac14-153">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="cac14-153">Select **Next**.</span></span>

1. <span data-ttu-id="cac14-154">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="cac14-154">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="cac14-155">選取 [ **主控 ASP.NET Core** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="cac14-155">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="cac14-156">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="cac14-156">Select **Next**.</span></span>

1. <span data-ttu-id="cac14-157">在 [ **專案名稱** ] 欄位中，為應用程式命名 `BlazorWebAssemblySignalRApp` 。</span><span class="sxs-lookup"><span data-stu-id="cac14-157">In the **Project Name** field, name the app `BlazorWebAssemblySignalRApp`.</span></span> <span data-ttu-id="cac14-158">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="cac14-158">Select **Create**.</span></span>

   <span data-ttu-id="cac14-159">如果出現提示信任開發憑證，請信任憑證並繼續。</span><span class="sxs-lookup"><span data-stu-id="cac14-159">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="cac14-160">需要使用者和 keychain 密碼才能信任憑證。</span><span class="sxs-lookup"><span data-stu-id="cac14-160">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="cac14-161">流覽至專案資料夾，然後開啟專案的方案檔 () 來開啟專案 `.sln` 。</span><span class="sxs-lookup"><span data-stu-id="cac14-161">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cac14-162">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cac14-162">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="cac14-163">在命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="cac14-163">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

<span data-ttu-id="cac14-164">`-ho|--hosted`選項會建立託管 Blazor WebAssembly 方案。</span><span class="sxs-lookup"><span data-stu-id="cac14-164">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

<span data-ttu-id="cac14-165">`-o|--output`選項會建立解決方案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="cac14-165">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="cac14-166">如果您已建立解決方案的資料夾，並在該資料夾中開啟命令 shell，請省略 `-o|--output` 選項和值以建立解決方案。</span><span class="sxs-lookup"><span data-stu-id="cac14-166">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="cac14-167">新增 SignalR 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="cac14-167">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cac14-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cac14-168">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="cac14-169">在 **方案總管** 中，以滑鼠右鍵按一下 `BlazorWebAssemblySignalRApp.Client` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="cac14-169">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="cac14-170">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [ **封裝來源** ] 設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="cac14-170">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="cac14-171">選取 **[流覽]** 之後， `Microsoft.AspNetCore.SignalR.Client` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="cac14-171">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="cac14-172">在搜尋結果中，選取 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 套件。</span><span class="sxs-lookup"><span data-stu-id="cac14-172">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="cac14-173">設定版本，使其符合應用程式的共用架構。</span><span class="sxs-lookup"><span data-stu-id="cac14-173">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="cac14-174">選取 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="cac14-174">Select **Install**.</span></span>

1. <span data-ttu-id="cac14-175">如果出現 [ **預覽變更** ] 對話方塊，請選取 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="cac14-175">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="cac14-176">如果出現 [ **接受授權** ] 對話方塊，請選取 [如果您同意授權條款即 **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="cac14-176">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cac14-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cac14-177">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="cac14-178">在 **整合式終端** 機中  >  ，從工具列)  (視圖 **終端** 機，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="cac14-178">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="cac14-179">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="cac14-179">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cac14-180">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cac14-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="cac14-181">在 [ **方案** ] 提要欄位中，以滑鼠右鍵按一下 `BlazorWebAssemblySignalRApp.Client` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="cac14-181">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="cac14-182">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [來源] 下拉式清單設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="cac14-182">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="cac14-183">選取 **[流覽]** 之後， `Microsoft.AspNetCore.SignalR.Client` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="cac14-183">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="cac14-184">在搜尋結果中，選取套件旁的核取方塊 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 。</span><span class="sxs-lookup"><span data-stu-id="cac14-184">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="cac14-185">設定版本，使其符合應用程式的共用架構。</span><span class="sxs-lookup"><span data-stu-id="cac14-185">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="cac14-186">選取 [ **新增套件**]。</span><span class="sxs-lookup"><span data-stu-id="cac14-186">Select **Add Package**.</span></span>

1. <span data-ttu-id="cac14-187">如果出現 [ **接受授權** ] 對話方塊，請在您同意授權條款時選取 [ **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="cac14-187">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cac14-188">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cac14-188">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="cac14-189">從解決方案資料夾的命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="cac14-189">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="cac14-190">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="cac14-190">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="cac14-191">新增 system.string. Web 封裝</span><span class="sxs-lookup"><span data-stu-id="cac14-191">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="cac14-192">*本節僅適用于 ASP.NET Core 3.x 版的應用程式。*</span><span class="sxs-lookup"><span data-stu-id="cac14-192">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="cac14-193">由於在 ASP.NET Core 3.x 應用程式中使用5.x 時發生套件解析問題 [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) ，因此 `BlazorWebAssemblySignalRApp.Server` 專案需要的封裝參考 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 。</span><span class="sxs-lookup"><span data-stu-id="cac14-193">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the `BlazorWebAssemblySignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="cac14-194">在 .NET 5 的未來修補程式版本中，將會解決基礎問題。</span><span class="sxs-lookup"><span data-stu-id="cac14-194">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="cac14-195">如需詳細資訊，請參閱 [System.Text.Json 定義沒有相依性的 netcoreapp 3.0 (dotnet/runtime #45560) ](https://github.com/dotnet/runtime/issues/45560)。</span><span class="sxs-lookup"><span data-stu-id="cac14-195">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="cac14-196">若要加入 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) `BlazorWebAssemblySignalRApp.Server` ASP.NET Core 3.1 裝載解決方案的專案 Blazor ，請遵循您選擇的工具指導方針：</span><span class="sxs-lookup"><span data-stu-id="cac14-196">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorWebAssemblySignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cac14-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cac14-197">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="cac14-198">在 **方案總管** 中，以滑鼠右鍵按一下 `BlazorWebAssemblySignalRApp.Server` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="cac14-198">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="cac14-199">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [ **封裝來源** ] 設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="cac14-199">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="cac14-200">選取 **[流覽]** 之後， `System.Text.Encodings.Web` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="cac14-200">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="cac14-201">在搜尋結果中，選取 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 套件。</span><span class="sxs-lookup"><span data-stu-id="cac14-201">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="cac14-202">選取符合使用中共用架構的套件版本。</span><span class="sxs-lookup"><span data-stu-id="cac14-202">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="cac14-203">選取 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="cac14-203">Select **Install**.</span></span>

1. <span data-ttu-id="cac14-204">如果出現 [ **預覽變更** ] 對話方塊，請選取 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="cac14-204">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="cac14-205">如果出現 [ **接受授權** ] 對話方塊，請選取 [如果您同意授權條款即 **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="cac14-205">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cac14-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cac14-206">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="cac14-207">在 **整合式終端** 機中 > ，從工具列)  (查看 **終端** 機，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="cac14-207">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="cac14-208">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="cac14-208">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cac14-209">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cac14-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="cac14-210">在 [ **方案** ] 提要欄位中，以滑鼠右鍵按一下 `BlazorWebAssemblySignalRApp.Server` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="cac14-210">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="cac14-211">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [來源] 下拉式清單設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="cac14-211">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="cac14-212">選取 **[流覽]** 之後， `System.Text.Encodings.Web` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="cac14-212">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="cac14-213">在搜尋結果中，選取套件旁的核取方塊 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) ，選取符合使用中共用架構的正確套件版本，然後選取 [ **新增套件**]。</span><span class="sxs-lookup"><span data-stu-id="cac14-213">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="cac14-214">如果出現 [ **接受授權** ] 對話方塊，請在您同意授權條款時選取 [ **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="cac14-214">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cac14-215">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cac14-215">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="cac14-216">從解決方案資料夾的命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="cac14-216">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="cac14-217">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="cac14-217">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="cac14-218">新增 SignalR 中樞</span><span class="sxs-lookup"><span data-stu-id="cac14-218">Add a SignalR hub</span></span>

<span data-ttu-id="cac14-219">在 `BlazorWebAssemblySignalRApp.Server` 專案中，建立 `Hubs` (複數) 資料夾，並 () 新增下列 `ChatHub` 類別 `Hubs/ChatHub.cs` ：</span><span class="sxs-lookup"><span data-stu-id="cac14-219">In the `BlazorWebAssemblySignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="cac14-220">新增中樞的服務和端點 SignalR</span><span class="sxs-lookup"><span data-stu-id="cac14-220">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="cac14-221">在 `BlazorWebAssemblySignalRApp.Server` 專案中，開啟 `Startup.cs` 檔案。</span><span class="sxs-lookup"><span data-stu-id="cac14-221">In the `BlazorWebAssemblySignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="cac14-222">將類別的命名空間新增 `ChatHub` 至檔案頂端：</span><span class="sxs-lookup"><span data-stu-id="cac14-222">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="cac14-223">新增 SignalR 和回應壓縮中介軟體服務至 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="cac14-223">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. <span data-ttu-id="cac14-224">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="cac14-224">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="cac14-225">在處理管線的設定頂端使用回應壓縮中介軟體。</span><span class="sxs-lookup"><span data-stu-id="cac14-225">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="cac14-226">在控制器的端點和用戶端的回復之間，新增中樞的端點。</span><span class="sxs-lookup"><span data-stu-id="cac14-226">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="cac14-227">新增 SignalR 和回應壓縮中介軟體服務至 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="cac14-227">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="cac14-228">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="cac14-228">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="cac14-229">在處理管線的設定頂端使用回應壓縮中介軟體。</span><span class="sxs-lookup"><span data-stu-id="cac14-229">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="cac14-230">在控制器的端點和用戶端的回復之間，新增中樞的端點。</span><span class="sxs-lookup"><span data-stu-id="cac14-230">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="cac14-231">新增 Razor 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="cac14-231">Add Razor component code for chat</span></span>

1. <span data-ttu-id="cac14-232">在 `BlazorWebAssemblySignalRApp.Client` 專案中，開啟 `Pages/Index.razor` 檔案。</span><span class="sxs-lookup"><span data-stu-id="cac14-232">In the `BlazorWebAssemblySignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="cac14-233">以下列程式碼取代標記：</span><span class="sxs-lookup"><span data-stu-id="cac14-233">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="cac14-234">以下列程式碼取代標記：</span><span class="sxs-lookup"><span data-stu-id="cac14-234">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="cac14-235">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="cac14-235">Run the app</span></span>

<span data-ttu-id="cac14-236">遵循您工具的指導方針：</span><span class="sxs-lookup"><span data-stu-id="cac14-236">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cac14-237">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cac14-237">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="cac14-238">在 **方案總管** 中，選取 `BlazorWebAssemblySignalRApp.Server` 專案。</span><span class="sxs-lookup"><span data-stu-id="cac14-238">In **Solution Explorer**, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="cac14-239">按下<kbd>f5</kbd>鍵以執行應用程式的偵錯工具，或按<kbd>Ctrl</kbd> + <kbd>F5</kbd>執行應用程式，而不進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="cac14-239">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="cac14-240">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="cac14-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="cac14-241">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="cac14-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="cac14-242">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="cac14-242">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="cac14-244">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="cac14-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cac14-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cac14-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="cac14-246">按下<kbd>f5</kbd>鍵以執行應用程式的偵錯工具，或按<kbd>Ctrl</kbd> + <kbd>F5</kbd>執行應用程式，而不進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="cac14-246">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="cac14-247">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="cac14-247">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="cac14-248">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="cac14-248">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="cac14-249">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="cac14-249">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="cac14-251">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="cac14-251">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cac14-252">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cac14-252">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="cac14-253">在 [ **方案** ] 側邊欄中，選取 `BlazorWebAssemblySignalRApp.Server` 專案。</span><span class="sxs-lookup"><span data-stu-id="cac14-253">In the **Solution** sidebar, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="cac14-254">按<kbd>⌘</kbd> + <kbd>↩</kbd>以執行應用程式的偵錯工具或<kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd>執行應用程式，而不需進行任何偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="cac14-254">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="cac14-255">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="cac14-255">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="cac14-256">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="cac14-256">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="cac14-257">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="cac14-257">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="cac14-259">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="cac14-259">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cac14-260">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cac14-260">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="cac14-261">從解決方案資料夾的命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="cac14-261">In a command shell from the solution's folder, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="cac14-262">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="cac14-262">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="cac14-263">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="cac14-263">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="cac14-264">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="cac14-264">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="cac14-266">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="cac14-266">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a><span data-ttu-id="cac14-267">建立 Blazor Server 應用程式</span><span class="sxs-lookup"><span data-stu-id="cac14-267">Create a Blazor Server app</span></span>

<span data-ttu-id="cac14-268">遵循您選擇的工具指導方針：</span><span class="sxs-lookup"><span data-stu-id="cac14-268">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cac14-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cac14-269">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="cac14-270">需要 Visual Studio 16.8 或更新版本，以及 .NET Core SDK 5.0.0 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="cac14-270">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="cac14-271">需要 Visual Studio 16.6 或更新版本，以及 .NET Core SDK 3.1.300 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="cac14-271">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="cac14-272">建立新專案。</span><span class="sxs-lookup"><span data-stu-id="cac14-272">Create a new project.</span></span>

1. <span data-ttu-id="cac14-273">選取 **Blazor 應用程式**，然後選取 **[下一步]**。</span><span class="sxs-lookup"><span data-stu-id="cac14-273">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="cac14-274">輸入 `BlazorServerSignalRApp` [ **專案名稱** ] 欄位。</span><span class="sxs-lookup"><span data-stu-id="cac14-274">Type `BlazorServerSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="cac14-275">確認 **位置** 專案是正確的，或提供專案的位置。</span><span class="sxs-lookup"><span data-stu-id="cac14-275">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="cac14-276">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="cac14-276">Select **Create**.</span></span>

1. <span data-ttu-id="cac14-277">選擇 **Blazor Server 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="cac14-277">Choose the **Blazor Server App** template.</span></span>

1. <span data-ttu-id="cac14-278">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="cac14-278">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cac14-279">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cac14-279">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="cac14-280">在命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="cac14-280">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   <span data-ttu-id="cac14-281">`-o|--output`選項會建立專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="cac14-281">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="cac14-282">如果您已建立專案的資料夾，並在該資料夾中開啟命令 shell，請省略 `-o|--output` 選項和值以建立專案。</span><span class="sxs-lookup"><span data-stu-id="cac14-282">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

1. <span data-ttu-id="cac14-283">在 Visual Studio Code 中，開啟應用程式的專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="cac14-283">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="cac14-284">當對話方塊出現時，若要新增資產以建立和偵測應用程式，請選取 [ **是**]。</span><span class="sxs-lookup"><span data-stu-id="cac14-284">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="cac14-285">Visual Studio Code 會自動加入 `.vscode` 具有所產生檔案和檔案的資料夾 `launch.json` `tasks.json` 。</span><span class="sxs-lookup"><span data-stu-id="cac14-285">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cac14-286">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cac14-286">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="cac14-287">安裝最新版本的 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) ，然後執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="cac14-287">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="cac14-288">選取  >  [檔案 **新方案**] 或 [**開始] 視窗** 中的 [建立 **新** 專案]。</span><span class="sxs-lookup"><span data-stu-id="cac14-288">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="cac14-289">在側邊欄中，選取 [ **Web] 和 [主控台**  >  **應用程式**]。</span><span class="sxs-lookup"><span data-stu-id="cac14-289">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="cac14-290">選擇 **Blazor Server 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="cac14-290">Choose the **Blazor Server App** template.</span></span> <span data-ttu-id="cac14-291">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="cac14-291">Select **Next**.</span></span>

1. <span data-ttu-id="cac14-292">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="cac14-292">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="cac14-293">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="cac14-293">Select **Next**.</span></span>

1. <span data-ttu-id="cac14-294">在 [ **專案名稱** ] 欄位中，為應用程式命名 `BlazorServerSignalRApp` 。</span><span class="sxs-lookup"><span data-stu-id="cac14-294">In the **Project Name** field, name the app `BlazorServerSignalRApp`.</span></span> <span data-ttu-id="cac14-295">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="cac14-295">Select **Create**.</span></span>

   <span data-ttu-id="cac14-296">如果出現提示信任開發憑證，請信任憑證並繼續。</span><span class="sxs-lookup"><span data-stu-id="cac14-296">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="cac14-297">需要使用者和 keychain 密碼才能信任憑證。</span><span class="sxs-lookup"><span data-stu-id="cac14-297">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="cac14-298">流覽至專案資料夾，然後開啟專案的方案檔 () 來開啟專案 `.sln` 。</span><span class="sxs-lookup"><span data-stu-id="cac14-298">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cac14-299">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cac14-299">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="cac14-300">在命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="cac14-300">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

<span data-ttu-id="cac14-301">`-o|--output`選項會建立專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="cac14-301">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="cac14-302">如果您已建立專案的資料夾，並在該資料夾中開啟命令 shell，請省略 `-o|--output` 選項和值以建立專案。</span><span class="sxs-lookup"><span data-stu-id="cac14-302">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="cac14-303">新增 SignalR 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="cac14-303">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cac14-304">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cac14-304">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="cac14-305">在 **方案總管** 中，以滑鼠右鍵按一下 `BlazorServerSignalRApp` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="cac14-305">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="cac14-306">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [ **封裝來源** ] 設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="cac14-306">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="cac14-307">選取 **[流覽]** 之後， `Microsoft.AspNetCore.SignalR.Client` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="cac14-307">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="cac14-308">在搜尋結果中，選取 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 套件。</span><span class="sxs-lookup"><span data-stu-id="cac14-308">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="cac14-309">設定版本，使其符合應用程式的共用架構。</span><span class="sxs-lookup"><span data-stu-id="cac14-309">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="cac14-310">選取 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="cac14-310">Select **Install**.</span></span>

1. <span data-ttu-id="cac14-311">如果出現 [ **預覽變更** ] 對話方塊，請選取 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="cac14-311">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="cac14-312">如果出現 [ **接受授權** ] 對話方塊，請選取 [如果您同意授權條款即 **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="cac14-312">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cac14-313">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cac14-313">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="cac14-314">在 **整合式終端** 機中  >  ，從工具列)  (視圖 **終端** 機，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="cac14-314">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="cac14-315">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="cac14-315">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cac14-316">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cac14-316">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="cac14-317">在 [ **方案** ] 提要欄位中，以滑鼠右鍵按一下 `BlazorServerSignalRApp` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="cac14-317">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="cac14-318">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [來源] 下拉式清單設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="cac14-318">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="cac14-319">選取 **[流覽]** 之後， `Microsoft.AspNetCore.SignalR.Client` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="cac14-319">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="cac14-320">在搜尋結果中，選取套件旁的核取方塊 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 。</span><span class="sxs-lookup"><span data-stu-id="cac14-320">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="cac14-321">設定版本，使其符合應用程式的共用架構。</span><span class="sxs-lookup"><span data-stu-id="cac14-321">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="cac14-322">選取 [ **新增套件**]。</span><span class="sxs-lookup"><span data-stu-id="cac14-322">Select **Add Package**.</span></span>

1. <span data-ttu-id="cac14-323">如果出現 [ **接受授權** ] 對話方塊，請在您同意授權條款時選取 [ **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="cac14-323">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cac14-324">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cac14-324">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="cac14-325">在來自專案資料夾的命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="cac14-325">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="cac14-326">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="cac14-326">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="cac14-327">新增 system.string. Web 封裝</span><span class="sxs-lookup"><span data-stu-id="cac14-327">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="cac14-328">*本節僅適用于 ASP.NET Core 3.x 版的應用程式。*</span><span class="sxs-lookup"><span data-stu-id="cac14-328">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="cac14-329">由於在 ASP.NET Core 3.x 應用程式中使用5.x 時發生套件解析問題 [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) ，因此專案需要的封裝參考 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 。</span><span class="sxs-lookup"><span data-stu-id="cac14-329">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="cac14-330">在 .NET 5 的未來修補程式版本中，將會解決基礎問題。</span><span class="sxs-lookup"><span data-stu-id="cac14-330">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="cac14-331">如需詳細資訊，請參閱 [System.Text.Json 定義沒有相依性的 netcoreapp 3.0 (dotnet/runtime #45560) ](https://github.com/dotnet/runtime/issues/45560)。</span><span class="sxs-lookup"><span data-stu-id="cac14-331">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="cac14-332">若要加入 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 至專案，請遵循您選擇的工具指導方針：</span><span class="sxs-lookup"><span data-stu-id="cac14-332">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the project, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cac14-333">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cac14-333">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="cac14-334">在 **方案總管** 中，以滑鼠右鍵按一下 `BlazorServerSignalRApp` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="cac14-334">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="cac14-335">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [ **封裝來源** ] 設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="cac14-335">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="cac14-336">選取 **[流覽]** 之後， `System.Text.Encodings.Web` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="cac14-336">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="cac14-337">在搜尋結果中，選取 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 套件。</span><span class="sxs-lookup"><span data-stu-id="cac14-337">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="cac14-338">選取符合使用中共用架構的套件版本。</span><span class="sxs-lookup"><span data-stu-id="cac14-338">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="cac14-339">選取 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="cac14-339">Select **Install**.</span></span>

1. <span data-ttu-id="cac14-340">如果出現 [ **預覽變更** ] 對話方塊，請選取 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="cac14-340">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="cac14-341">如果出現 [ **接受授權** ] 對話方塊，請選取 [如果您同意授權條款即 **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="cac14-341">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cac14-342">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cac14-342">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="cac14-343">在 **整合式終端** 機中 > ，從工具列)  (查看 **終端** 機，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="cac14-343">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="cac14-344">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="cac14-344">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cac14-345">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cac14-345">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="cac14-346">在 [ **方案** ] 提要欄位中，以滑鼠右鍵按一下 `BlazorServerSignalRApp` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="cac14-346">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="cac14-347">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [來源] 下拉式清單設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="cac14-347">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="cac14-348">選取 **[流覽]** 之後， `System.Text.Encodings.Web` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="cac14-348">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="cac14-349">在搜尋結果中，選取套件旁的核取方塊 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) ，選取符合使用中共用架構的正確套件版本，然後選取 [ **新增套件**]。</span><span class="sxs-lookup"><span data-stu-id="cac14-349">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="cac14-350">如果出現 [ **接受授權** ] 對話方塊，請在您同意授權條款時選取 [ **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="cac14-350">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cac14-351">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cac14-351">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="cac14-352">在來自專案資料夾的命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="cac14-352">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="cac14-353">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="cac14-353">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="cac14-354">新增 SignalR 中樞</span><span class="sxs-lookup"><span data-stu-id="cac14-354">Add a SignalR hub</span></span>

<span data-ttu-id="cac14-355">建立 `Hubs` (複數) 資料夾，並 `ChatHub` () 新增下列類別 `Hubs/ChatHub.cs` ：</span><span class="sxs-lookup"><span data-stu-id="cac14-355">Create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="cac14-356">新增中樞的服務和端點 SignalR</span><span class="sxs-lookup"><span data-stu-id="cac14-356">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="cac14-357">開啟 `Startup.cs` 檔案。</span><span class="sxs-lookup"><span data-stu-id="cac14-357">Open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="cac14-358">將和類別的命名空間加入 <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> `ChatHub` 至檔案頂端：</span><span class="sxs-lookup"><span data-stu-id="cac14-358">Add the namespaces for <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> and the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="cac14-359">將回應壓縮中介軟體服務新增至 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="cac14-359">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="cac14-360">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="cac14-360">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="cac14-361">在處理管線的設定頂端使用回應壓縮中介軟體。</span><span class="sxs-lookup"><span data-stu-id="cac14-361">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="cac14-362">在對應中樞和用戶端回復的端點之間 Blazor ，新增中樞的端點。</span><span class="sxs-lookup"><span data-stu-id="cac14-362">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="cac14-363">將回應壓縮中介軟體服務新增至 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="cac14-363">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="cac14-364">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="cac14-364">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="cac14-365">在處理管線的設定頂端使用回應壓縮中介軟體。</span><span class="sxs-lookup"><span data-stu-id="cac14-365">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="cac14-366">在對應中樞和用戶端回復的端點之間 Blazor ，新增中樞的端點。</span><span class="sxs-lookup"><span data-stu-id="cac14-366">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="cac14-367">新增 Razor 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="cac14-367">Add Razor component code for chat</span></span>

1. <span data-ttu-id="cac14-368">開啟 `Pages/Index.razor` 檔案。</span><span class="sxs-lookup"><span data-stu-id="cac14-368">Open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="cac14-369">以下列程式碼取代標記：</span><span class="sxs-lookup"><span data-stu-id="cac14-369">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="cac14-370">以下列程式碼取代標記：</span><span class="sxs-lookup"><span data-stu-id="cac14-370">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="cac14-371">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="cac14-371">Run the app</span></span>

<span data-ttu-id="cac14-372">遵循您工具的指導方針：</span><span class="sxs-lookup"><span data-stu-id="cac14-372">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cac14-373">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cac14-373">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="cac14-374">按下<kbd>f5</kbd>鍵以執行應用程式的偵錯工具，或按<kbd>Ctrl</kbd> + <kbd>F5</kbd>執行應用程式，而不進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="cac14-374">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="cac14-375">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="cac14-375">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="cac14-376">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="cac14-376">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="cac14-377">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="cac14-377">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="cac14-379">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="cac14-379">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cac14-380">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cac14-380">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="cac14-381">按下<kbd>f5</kbd>鍵以執行應用程式的偵錯工具，或按<kbd>Ctrl</kbd> + <kbd>F5</kbd>執行應用程式，而不進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="cac14-381">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="cac14-382">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="cac14-382">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="cac14-383">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="cac14-383">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="cac14-384">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="cac14-384">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="cac14-386">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="cac14-386">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cac14-387">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cac14-387">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="cac14-388">按<kbd>⌘</kbd> + <kbd>↩</kbd>以執行應用程式的偵錯工具或<kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd>執行應用程式，而不需進行任何偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="cac14-388">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="cac14-389">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="cac14-389">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="cac14-390">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="cac14-390">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="cac14-391">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="cac14-391">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="cac14-393">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="cac14-393">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cac14-394">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cac14-394">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="cac14-395">在來自專案資料夾的命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="cac14-395">In a command shell from the project's folder, execute the following commands:</span></span>

   ```dotnetcli
   dotnet run
   ```

1. <span data-ttu-id="cac14-396">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="cac14-396">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="cac14-397">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="cac14-397">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="cac14-398">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="cac14-398">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="cac14-400">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="cac14-400">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

## <a name="next-steps"></a><span data-ttu-id="cac14-401">後續步驟</span><span class="sxs-lookup"><span data-stu-id="cac14-401">Next steps</span></span>

<span data-ttu-id="cac14-402">在本教學課程中，您已了解如何：</span><span class="sxs-lookup"><span data-stu-id="cac14-402">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cac14-403">建立 Blazor 專案</span><span class="sxs-lookup"><span data-stu-id="cac14-403">Create a Blazor project</span></span>
> * <span data-ttu-id="cac14-404">新增 SignalR 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="cac14-404">Add the SignalR client library</span></span>
> * <span data-ttu-id="cac14-405">新增 SignalR 中樞</span><span class="sxs-lookup"><span data-stu-id="cac14-405">Add a SignalR hub</span></span>
> * <span data-ttu-id="cac14-406">新增 SignalR 中樞的服務和端點 SignalR</span><span class="sxs-lookup"><span data-stu-id="cac14-406">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="cac14-407">新增 Razor 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="cac14-407">Add Razor component code for chat</span></span>

<span data-ttu-id="cac14-408">若要深入瞭解如何建立 Blazor 應用程式，請參閱 Blazor 檔：</span><span class="sxs-lookup"><span data-stu-id="cac14-408">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="cac14-409"><xref:blazor/index>
> [使用 Identity 伺服器、websocket 和 Server-Sent 事件的持有人權杖驗證](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="cac14-409"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cac14-410">其他資源</span><span class="sxs-lookup"><span data-stu-id="cac14-410">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="cac14-411">SignalR 驗證的跨原始來源協商</span><span class="sxs-lookup"><span data-stu-id="cac14-411">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
