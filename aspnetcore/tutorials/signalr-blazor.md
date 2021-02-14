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
# <a name="use-aspnet-core-signalr-with-blazor"></a><span data-ttu-id="4f13f-103">搭配使用 SignalR ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="4f13f-103">Use ASP.NET Core SignalR with Blazor</span></span>

<span data-ttu-id="4f13f-104">本教學課程會教您使用與建立即時應用程式的基本概念 SignalR Blazor 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-104">This tutorial teaches the basics of building a real-time app using SignalR with Blazor.</span></span> <span data-ttu-id="4f13f-105">您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="4f13f-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4f13f-106">建立 Blazor 專案</span><span class="sxs-lookup"><span data-stu-id="4f13f-106">Create a Blazor project</span></span>
> * <span data-ttu-id="4f13f-107">新增 SignalR 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="4f13f-107">Add the SignalR client library</span></span>
> * <span data-ttu-id="4f13f-108">新增 SignalR 中樞</span><span class="sxs-lookup"><span data-stu-id="4f13f-108">Add a SignalR hub</span></span>
> * <span data-ttu-id="4f13f-109">新增 SignalR 中樞的服務和端點 SignalR</span><span class="sxs-lookup"><span data-stu-id="4f13f-109">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="4f13f-110">新增 Razor 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="4f13f-110">Add Razor component code for chat</span></span>

<span data-ttu-id="4f13f-111">在本教學課程結尾，您將會有一個可運作的聊天應用程式。</span><span class="sxs-lookup"><span data-stu-id="4f13f-111">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="4f13f-112">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="4f13f-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4f13f-113">必要條件</span><span class="sxs-lookup"><span data-stu-id="4f13f-113">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="4f13f-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f13f-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4f13f-115">使用 **ASP.NET 和 網頁程式開發** 工作負載 [Visual Studio 2019 16.8 或更新版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="4f13f-115">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f13f-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f13f-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f13f-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4f13f-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="4f13f-118">Visual Studio for Mac 8.8 版或更新版本</span><span class="sxs-lookup"><span data-stu-id="4f13f-118">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="4f13f-119">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4f13f-119">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="4f13f-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f13f-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4f13f-121">使用 **ASP.NET 和 網頁程式開發** 工作負載 [Visual Studio 2019 16.6 或更新版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="4f13f-121">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f13f-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f13f-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f13f-123">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4f13f-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="4f13f-124">Visual Studio for Mac 8.6 版或更新版本</span><span class="sxs-lookup"><span data-stu-id="4f13f-124">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="4f13f-125">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4f13f-125">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="4f13f-126">建立託管 Blazor WebAssembly 應用程式</span><span class="sxs-lookup"><span data-stu-id="4f13f-126">Create a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="4f13f-127">遵循您選擇的工具指導方針：</span><span class="sxs-lookup"><span data-stu-id="4f13f-127">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f13f-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f13f-128">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="4f13f-129">需要 Visual Studio 16.8 或更新版本，以及 .NET Core SDK 5.0.0 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="4f13f-129">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="4f13f-130">需要 Visual Studio 16.6 或更新版本，以及 .NET Core SDK 3.1.300 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="4f13f-130">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="4f13f-131">建立新專案。</span><span class="sxs-lookup"><span data-stu-id="4f13f-131">Create a new project.</span></span>

1. <span data-ttu-id="4f13f-132">選取 **Blazor 應用程式**，然後選取 **[下一步]**。</span><span class="sxs-lookup"><span data-stu-id="4f13f-132">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="4f13f-133">輸入 `BlazorWebAssemblySignalRApp` [ **專案名稱** ] 欄位。</span><span class="sxs-lookup"><span data-stu-id="4f13f-133">Type `BlazorWebAssemblySignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="4f13f-134">確認 **位置** 專案是正確的，或提供專案的位置。</span><span class="sxs-lookup"><span data-stu-id="4f13f-134">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="4f13f-135">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="4f13f-135">Select **Create**.</span></span>

1. <span data-ttu-id="4f13f-136">選擇 **Blazor WebAssembly 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="4f13f-136">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="4f13f-137">在 [ **Advanced**] 底下，選取 [ **hosted ASP.NET Core** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="4f13f-137">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="4f13f-138">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="4f13f-138">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f13f-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f13f-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="4f13f-140">在命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="4f13f-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   <span data-ttu-id="4f13f-141">`-ho|--hosted`選項會建立託管 Blazor WebAssembly 方案。</span><span class="sxs-lookup"><span data-stu-id="4f13f-141">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

   <span data-ttu-id="4f13f-142">`-o|--output`選項會建立解決方案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="4f13f-142">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="4f13f-143">如果您已建立解決方案的資料夾，並在該資料夾中開啟命令 shell，請省略 `-o|--output` 選項和值以建立解決方案。</span><span class="sxs-lookup"><span data-stu-id="4f13f-143">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

1. <span data-ttu-id="4f13f-144">在 Visual Studio Code 中，開啟應用程式的專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="4f13f-144">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="4f13f-145">當對話方塊出現時，若要新增資產以建立和偵測應用程式，請選取 [ **是**]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-145">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="4f13f-146">Visual Studio Code 會自動加入 `.vscode` 具有所產生檔案和檔案的資料夾 `launch.json` `tasks.json` 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-146">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f13f-147">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4f13f-147">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4f13f-148">安裝最新版本的 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) ，然後執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="4f13f-148">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="4f13f-149">選取  >  [檔案 **新方案**] 或 [**開始] 視窗** 中的 [建立 **新** 專案]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-149">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="4f13f-150">在側邊欄中，選取 [ **Web] 和 [主控台**  >  **應用程式**]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-150">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="4f13f-151">選擇 **Blazor WebAssembly 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="4f13f-151">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="4f13f-152">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-152">Select **Next**.</span></span>

1. <span data-ttu-id="4f13f-153">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-153">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="4f13f-154">選取 [ **主控 ASP.NET Core** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="4f13f-154">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="4f13f-155">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-155">Select **Next**.</span></span>

1. <span data-ttu-id="4f13f-156">在 [ **專案名稱** ] 欄位中，為應用程式命名 `BlazorWebAssemblySignalRApp` 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-156">In the **Project Name** field, name the app `BlazorWebAssemblySignalRApp`.</span></span> <span data-ttu-id="4f13f-157">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="4f13f-157">Select **Create**.</span></span>

   <span data-ttu-id="4f13f-158">如果出現提示信任開發憑證，請信任憑證並繼續。</span><span class="sxs-lookup"><span data-stu-id="4f13f-158">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="4f13f-159">需要使用者和 keychain 密碼才能信任憑證。</span><span class="sxs-lookup"><span data-stu-id="4f13f-159">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="4f13f-160">流覽至專案資料夾，然後開啟專案的方案檔 () 來開啟專案 `.sln` 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-160">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4f13f-161">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4f13f-161">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="4f13f-162">在命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="4f13f-162">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

<span data-ttu-id="4f13f-163">`-ho|--hosted`選項會建立託管 Blazor WebAssembly 方案。</span><span class="sxs-lookup"><span data-stu-id="4f13f-163">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

<span data-ttu-id="4f13f-164">`-o|--output`選項會建立解決方案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="4f13f-164">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="4f13f-165">如果您已建立解決方案的資料夾，並在該資料夾中開啟命令 shell，請省略 `-o|--output` 選項和值以建立解決方案。</span><span class="sxs-lookup"><span data-stu-id="4f13f-165">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="4f13f-166">新增 SignalR 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="4f13f-166">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f13f-167">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f13f-167">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="4f13f-168">在 **方案總管** 中，以滑鼠右鍵按一下 `BlazorWebAssemblySignalRApp.Client` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-168">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4f13f-169">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [ **封裝來源** ] 設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-169">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="4f13f-170">選取 **[流覽]** 之後， `Microsoft.AspNetCore.SignalR.Client` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="4f13f-170">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="4f13f-171">在搜尋結果中，選取 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 套件。</span><span class="sxs-lookup"><span data-stu-id="4f13f-171">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="4f13f-172">設定版本，使其符合應用程式的共用架構。</span><span class="sxs-lookup"><span data-stu-id="4f13f-172">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="4f13f-173">選取 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-173">Select **Install**.</span></span>

1. <span data-ttu-id="4f13f-174">如果出現 [ **預覽變更** ] 對話方塊，請選取 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="4f13f-174">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="4f13f-175">如果出現 [ **接受授權** ] 對話方塊，請選取 [如果您同意授權條款即 **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-175">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f13f-176">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f13f-176">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="4f13f-177">在 **整合式終端** 機中  >  ，從工具列)  (視圖 **終端** 機，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="4f13f-177">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="4f13f-178">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="4f13f-178">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f13f-179">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4f13f-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4f13f-180">在 [ **方案** ] 提要欄位中，以滑鼠右鍵按一下 `BlazorWebAssemblySignalRApp.Client` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-180">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4f13f-181">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [來源] 下拉式清單設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-181">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="4f13f-182">選取 **[流覽]** 之後， `Microsoft.AspNetCore.SignalR.Client` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="4f13f-182">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="4f13f-183">在搜尋結果中，選取套件旁的核取方塊 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-183">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="4f13f-184">設定版本，使其符合應用程式的共用架構。</span><span class="sxs-lookup"><span data-stu-id="4f13f-184">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="4f13f-185">選取 [ **新增套件**]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-185">Select **Add Package**.</span></span>

1. <span data-ttu-id="4f13f-186">如果出現 [ **接受授權** ] 對話方塊，請在您同意授權條款時選取 [ **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-186">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4f13f-187">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4f13f-187">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="4f13f-188">從解決方案資料夾的命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="4f13f-188">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="4f13f-189">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="4f13f-189">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="4f13f-190">新增 system.string. Web 封裝</span><span class="sxs-lookup"><span data-stu-id="4f13f-190">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="4f13f-191">*本節僅適用于 ASP.NET Core 3.x 版的應用程式。*</span><span class="sxs-lookup"><span data-stu-id="4f13f-191">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="4f13f-192">由於在 ASP.NET Core 3.x 應用程式中使用5.x 時發生套件解析問題 [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) ，因此 `BlazorWebAssemblySignalRApp.Server` 專案需要的封裝參考 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-192">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the `BlazorWebAssemblySignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="4f13f-193">在 .NET 5 的未來修補程式版本中，將會解決基礎問題。</span><span class="sxs-lookup"><span data-stu-id="4f13f-193">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="4f13f-194">如需詳細資訊，請參閱 [System.Text.Json 定義沒有相依性的 netcoreapp 3.0 (dotnet/runtime #45560) ](https://github.com/dotnet/runtime/issues/45560)。</span><span class="sxs-lookup"><span data-stu-id="4f13f-194">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="4f13f-195">若要加入 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) `BlazorWebAssemblySignalRApp.Server` ASP.NET Core 3.1 裝載解決方案的專案 Blazor ，請遵循您選擇的工具指導方針：</span><span class="sxs-lookup"><span data-stu-id="4f13f-195">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorWebAssemblySignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f13f-196">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f13f-196">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="4f13f-197">在 **方案總管** 中，以滑鼠右鍵按一下 `BlazorWebAssemblySignalRApp.Server` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-197">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4f13f-198">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [ **封裝來源** ] 設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-198">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="4f13f-199">選取 **[流覽]** 之後， `System.Text.Encodings.Web` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="4f13f-199">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="4f13f-200">在搜尋結果中，選取 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 套件。</span><span class="sxs-lookup"><span data-stu-id="4f13f-200">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="4f13f-201">選取符合使用中共用架構的套件版本。</span><span class="sxs-lookup"><span data-stu-id="4f13f-201">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="4f13f-202">選取 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-202">Select **Install**.</span></span>

1. <span data-ttu-id="4f13f-203">如果出現 [ **預覽變更** ] 對話方塊，請選取 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="4f13f-203">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="4f13f-204">如果出現 [ **接受授權** ] 對話方塊，請選取 [如果您同意授權條款即 **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-204">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f13f-205">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f13f-205">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="4f13f-206">在 **整合式終端** 機中 > ，從工具列)  (查看 **終端** 機，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="4f13f-206">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="4f13f-207">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="4f13f-207">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f13f-208">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4f13f-208">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4f13f-209">在 [ **方案** ] 提要欄位中，以滑鼠右鍵按一下 `BlazorWebAssemblySignalRApp.Server` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-209">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4f13f-210">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [來源] 下拉式清單設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-210">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="4f13f-211">選取 **[流覽]** 之後， `System.Text.Encodings.Web` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="4f13f-211">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="4f13f-212">在搜尋結果中，選取套件旁的核取方塊 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) ，選取符合使用中共用架構的正確套件版本，然後選取 [ **新增套件**]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-212">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="4f13f-213">如果出現 [ **接受授權** ] 對話方塊，請在您同意授權條款時選取 [ **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-213">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4f13f-214">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4f13f-214">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="4f13f-215">從解決方案資料夾的命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="4f13f-215">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="4f13f-216">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="4f13f-216">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="4f13f-217">新增 SignalR 中樞</span><span class="sxs-lookup"><span data-stu-id="4f13f-217">Add a SignalR hub</span></span>

<span data-ttu-id="4f13f-218">在 `BlazorWebAssemblySignalRApp.Server` 專案中，建立 `Hubs` (複數) 資料夾，並 () 新增下列 `ChatHub` 類別 `Hubs/ChatHub.cs` ：</span><span class="sxs-lookup"><span data-stu-id="4f13f-218">In the `BlazorWebAssemblySignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="4f13f-219">新增中樞的服務和端點 SignalR</span><span class="sxs-lookup"><span data-stu-id="4f13f-219">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="4f13f-220">在 `BlazorWebAssemblySignalRApp.Server` 專案中，開啟 `Startup.cs` 檔案。</span><span class="sxs-lookup"><span data-stu-id="4f13f-220">In the `BlazorWebAssemblySignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="4f13f-221">將類別的命名空間新增 `ChatHub` 至檔案頂端：</span><span class="sxs-lookup"><span data-stu-id="4f13f-221">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="4f13f-222">新增 SignalR 和回應壓縮中介軟體服務至 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="4f13f-222">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. <span data-ttu-id="4f13f-223">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="4f13f-223">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="4f13f-224">在處理管線的設定頂端使用回應壓縮中介軟體。</span><span class="sxs-lookup"><span data-stu-id="4f13f-224">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="4f13f-225">在控制器的端點和用戶端的回復之間，新增中樞的端點。</span><span class="sxs-lookup"><span data-stu-id="4f13f-225">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="4f13f-226">新增 SignalR 和回應壓縮中介軟體服務至 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="4f13f-226">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="4f13f-227">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="4f13f-227">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="4f13f-228">在處理管線的設定頂端使用回應壓縮中介軟體。</span><span class="sxs-lookup"><span data-stu-id="4f13f-228">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="4f13f-229">在控制器的端點和用戶端的回復之間，新增中樞的端點。</span><span class="sxs-lookup"><span data-stu-id="4f13f-229">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="4f13f-230">新增 Razor 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="4f13f-230">Add Razor component code for chat</span></span>

1. <span data-ttu-id="4f13f-231">在 `BlazorWebAssemblySignalRApp.Client` 專案中，開啟 `Pages/Index.razor` 檔案。</span><span class="sxs-lookup"><span data-stu-id="4f13f-231">In the `BlazorWebAssemblySignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="4f13f-232">以下列程式碼取代標記：</span><span class="sxs-lookup"><span data-stu-id="4f13f-232">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="4f13f-233">以下列程式碼取代標記：</span><span class="sxs-lookup"><span data-stu-id="4f13f-233">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="4f13f-234">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="4f13f-234">Run the app</span></span>

<span data-ttu-id="4f13f-235">遵循您工具的指導方針：</span><span class="sxs-lookup"><span data-stu-id="4f13f-235">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f13f-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f13f-236">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="4f13f-237">在 **方案總管** 中，選取 `BlazorWebAssemblySignalRApp.Server` 專案。</span><span class="sxs-lookup"><span data-stu-id="4f13f-237">In **Solution Explorer**, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="4f13f-238">按下<kbd>f5</kbd>鍵以執行應用程式的偵錯工具，或按<kbd>Ctrl</kbd> + <kbd>F5</kbd>執行應用程式，而不進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="4f13f-238">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="4f13f-239">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="4f13f-239">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4f13f-240">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="4f13f-240">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="4f13f-241">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="4f13f-241">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="4f13f-243">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4f13f-243">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f13f-244">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f13f-244">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="4f13f-245">按下<kbd>f5</kbd>鍵以執行應用程式的偵錯工具，或按<kbd>Ctrl</kbd> + <kbd>F5</kbd>執行應用程式，而不進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="4f13f-245">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="4f13f-246">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="4f13f-246">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4f13f-247">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="4f13f-247">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="4f13f-248">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="4f13f-248">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="4f13f-250">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4f13f-250">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f13f-251">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4f13f-251">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4f13f-252">在 [ **方案** ] 側邊欄中，選取 `BlazorWebAssemblySignalRApp.Server` 專案。</span><span class="sxs-lookup"><span data-stu-id="4f13f-252">In the **Solution** sidebar, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="4f13f-253">按<kbd>⌘</kbd> + <kbd>↩</kbd>以執行應用程式的偵錯工具或<kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd>執行應用程式，而不需進行任何偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="4f13f-253">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="4f13f-254">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="4f13f-254">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4f13f-255">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="4f13f-255">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="4f13f-256">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="4f13f-256">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="4f13f-258">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4f13f-258">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4f13f-259">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4f13f-259">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="4f13f-260">從解決方案資料夾的命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="4f13f-260">In a command shell from the solution's folder, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="4f13f-261">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="4f13f-261">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4f13f-262">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="4f13f-262">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="4f13f-263">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="4f13f-263">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="4f13f-265">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4f13f-265">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a><span data-ttu-id="4f13f-266">建立 Blazor Server 應用程式</span><span class="sxs-lookup"><span data-stu-id="4f13f-266">Create a Blazor Server app</span></span>

<span data-ttu-id="4f13f-267">遵循您選擇的工具指導方針：</span><span class="sxs-lookup"><span data-stu-id="4f13f-267">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f13f-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f13f-268">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="4f13f-269">需要 Visual Studio 16.8 或更新版本，以及 .NET Core SDK 5.0.0 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="4f13f-269">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="4f13f-270">需要 Visual Studio 16.6 或更新版本，以及 .NET Core SDK 3.1.300 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="4f13f-270">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="4f13f-271">建立新專案。</span><span class="sxs-lookup"><span data-stu-id="4f13f-271">Create a new project.</span></span>

1. <span data-ttu-id="4f13f-272">選取 **Blazor 應用程式**，然後選取 **[下一步]**。</span><span class="sxs-lookup"><span data-stu-id="4f13f-272">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="4f13f-273">輸入 `BlazorServerSignalRApp` [ **專案名稱** ] 欄位。</span><span class="sxs-lookup"><span data-stu-id="4f13f-273">Type `BlazorServerSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="4f13f-274">確認 **位置** 專案是正確的，或提供專案的位置。</span><span class="sxs-lookup"><span data-stu-id="4f13f-274">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="4f13f-275">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="4f13f-275">Select **Create**.</span></span>

1. <span data-ttu-id="4f13f-276">選擇 **Blazor Server 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="4f13f-276">Choose the **Blazor Server App** template.</span></span>

1. <span data-ttu-id="4f13f-277">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="4f13f-277">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f13f-278">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f13f-278">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="4f13f-279">在命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="4f13f-279">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   <span data-ttu-id="4f13f-280">`-o|--output`選項會建立專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="4f13f-280">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="4f13f-281">如果您已建立專案的資料夾，並在該資料夾中開啟命令 shell，請省略 `-o|--output` 選項和值以建立專案。</span><span class="sxs-lookup"><span data-stu-id="4f13f-281">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

1. <span data-ttu-id="4f13f-282">在 Visual Studio Code 中，開啟應用程式的專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="4f13f-282">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="4f13f-283">當對話方塊出現時，若要新增資產以建立和偵測應用程式，請選取 [ **是**]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-283">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="4f13f-284">Visual Studio Code 會自動加入 `.vscode` 具有所產生檔案和檔案的資料夾 `launch.json` `tasks.json` 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-284">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f13f-285">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4f13f-285">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4f13f-286">安裝最新版本的 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) ，然後執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="4f13f-286">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="4f13f-287">選取  >  [檔案 **新方案**] 或 [**開始] 視窗** 中的 [建立 **新** 專案]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-287">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="4f13f-288">在側邊欄中，選取 [ **Web] 和 [主控台**  >  **應用程式**]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-288">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="4f13f-289">選擇 **Blazor Server 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="4f13f-289">Choose the **Blazor Server App** template.</span></span> <span data-ttu-id="4f13f-290">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-290">Select **Next**.</span></span>

1. <span data-ttu-id="4f13f-291">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-291">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="4f13f-292">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-292">Select **Next**.</span></span>

1. <span data-ttu-id="4f13f-293">在 [ **專案名稱** ] 欄位中，為應用程式命名 `BlazorServerSignalRApp` 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-293">In the **Project Name** field, name the app `BlazorServerSignalRApp`.</span></span> <span data-ttu-id="4f13f-294">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="4f13f-294">Select **Create**.</span></span>

   <span data-ttu-id="4f13f-295">如果出現提示信任開發憑證，請信任憑證並繼續。</span><span class="sxs-lookup"><span data-stu-id="4f13f-295">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="4f13f-296">需要使用者和 keychain 密碼才能信任憑證。</span><span class="sxs-lookup"><span data-stu-id="4f13f-296">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="4f13f-297">流覽至專案資料夾，然後開啟專案的方案檔 () 來開啟專案 `.sln` 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-297">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4f13f-298">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4f13f-298">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="4f13f-299">在命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="4f13f-299">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

<span data-ttu-id="4f13f-300">`-o|--output`選項會建立專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="4f13f-300">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="4f13f-301">如果您已建立專案的資料夾，並在該資料夾中開啟命令 shell，請省略 `-o|--output` 選項和值以建立專案。</span><span class="sxs-lookup"><span data-stu-id="4f13f-301">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="4f13f-302">新增 SignalR 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="4f13f-302">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f13f-303">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f13f-303">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="4f13f-304">在 **方案總管** 中，以滑鼠右鍵按一下 `BlazorServerSignalRApp` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-304">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4f13f-305">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [ **封裝來源** ] 設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-305">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="4f13f-306">選取 **[流覽]** 之後， `Microsoft.AspNetCore.SignalR.Client` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="4f13f-306">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="4f13f-307">在搜尋結果中，選取 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 套件。</span><span class="sxs-lookup"><span data-stu-id="4f13f-307">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="4f13f-308">設定版本，使其符合應用程式的共用架構。</span><span class="sxs-lookup"><span data-stu-id="4f13f-308">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="4f13f-309">選取 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-309">Select **Install**.</span></span>

1. <span data-ttu-id="4f13f-310">如果出現 [ **預覽變更** ] 對話方塊，請選取 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="4f13f-310">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="4f13f-311">如果出現 [ **接受授權** ] 對話方塊，請選取 [如果您同意授權條款即 **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-311">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f13f-312">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f13f-312">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="4f13f-313">在 **整合式終端** 機中  >  ，從工具列)  (視圖 **終端** 機，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="4f13f-313">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="4f13f-314">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="4f13f-314">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f13f-315">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4f13f-315">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4f13f-316">在 [ **方案** ] 提要欄位中，以滑鼠右鍵按一下 `BlazorServerSignalRApp` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-316">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4f13f-317">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [來源] 下拉式清單設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-317">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="4f13f-318">選取 **[流覽]** 之後， `Microsoft.AspNetCore.SignalR.Client` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="4f13f-318">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="4f13f-319">在搜尋結果中，選取套件旁的核取方塊 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-319">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="4f13f-320">設定版本，使其符合應用程式的共用架構。</span><span class="sxs-lookup"><span data-stu-id="4f13f-320">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="4f13f-321">選取 [ **新增套件**]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-321">Select **Add Package**.</span></span>

1. <span data-ttu-id="4f13f-322">如果出現 [ **接受授權** ] 對話方塊，請在您同意授權條款時選取 [ **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-322">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4f13f-323">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4f13f-323">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="4f13f-324">在來自專案資料夾的命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="4f13f-324">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="4f13f-325">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="4f13f-325">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="4f13f-326">新增 system.string. Web 封裝</span><span class="sxs-lookup"><span data-stu-id="4f13f-326">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="4f13f-327">*本節僅適用于 ASP.NET Core 3.x 版的應用程式。*</span><span class="sxs-lookup"><span data-stu-id="4f13f-327">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="4f13f-328">由於在 ASP.NET Core 3.x 應用程式中使用5.x 時發生套件解析問題 [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) ，因此專案需要的封裝參考 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-328">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="4f13f-329">在 .NET 5 的未來修補程式版本中，將會解決基礎問題。</span><span class="sxs-lookup"><span data-stu-id="4f13f-329">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="4f13f-330">如需詳細資訊，請參閱 [System.Text.Json 定義沒有相依性的 netcoreapp 3.0 (dotnet/runtime #45560) ](https://github.com/dotnet/runtime/issues/45560)。</span><span class="sxs-lookup"><span data-stu-id="4f13f-330">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="4f13f-331">若要加入 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 至專案，請遵循您選擇的工具指導方針：</span><span class="sxs-lookup"><span data-stu-id="4f13f-331">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the project, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f13f-332">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f13f-332">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="4f13f-333">在 **方案總管** 中，以滑鼠右鍵按一下 `BlazorServerSignalRApp` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-333">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4f13f-334">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [ **封裝來源** ] 設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-334">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="4f13f-335">選取 **[流覽]** 之後， `System.Text.Encodings.Web` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="4f13f-335">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="4f13f-336">在搜尋結果中，選取 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 套件。</span><span class="sxs-lookup"><span data-stu-id="4f13f-336">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="4f13f-337">選取符合使用中共用架構的套件版本。</span><span class="sxs-lookup"><span data-stu-id="4f13f-337">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="4f13f-338">選取 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-338">Select **Install**.</span></span>

1. <span data-ttu-id="4f13f-339">如果出現 [ **預覽變更** ] 對話方塊，請選取 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="4f13f-339">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="4f13f-340">如果出現 [ **接受授權** ] 對話方塊，請選取 [如果您同意授權條款即 **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-340">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f13f-341">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f13f-341">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="4f13f-342">在 **整合式終端** 機中 > ，從工具列)  (查看 **終端** 機，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="4f13f-342">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="4f13f-343">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="4f13f-343">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f13f-344">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4f13f-344">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4f13f-345">在 [ **方案** ] 提要欄位中，以滑鼠右鍵按一下 `BlazorServerSignalRApp` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-345">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4f13f-346">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [來源] 下拉式清單設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="4f13f-346">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="4f13f-347">選取 **[流覽]** 之後， `System.Text.Encodings.Web` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="4f13f-347">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="4f13f-348">在搜尋結果中，選取套件旁的核取方塊 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) ，選取符合使用中共用架構的正確套件版本，然後選取 [ **新增套件**]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-348">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="4f13f-349">如果出現 [ **接受授權** ] 對話方塊，請在您同意授權條款時選取 [ **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="4f13f-349">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4f13f-350">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4f13f-350">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="4f13f-351">在來自專案資料夾的命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="4f13f-351">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="4f13f-352">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="4f13f-352">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="4f13f-353">新增 SignalR 中樞</span><span class="sxs-lookup"><span data-stu-id="4f13f-353">Add a SignalR hub</span></span>

<span data-ttu-id="4f13f-354">建立 `Hubs` (複數) 資料夾，並 `ChatHub` () 新增下列類別 `Hubs/ChatHub.cs` ：</span><span class="sxs-lookup"><span data-stu-id="4f13f-354">Create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="4f13f-355">新增中樞的服務和端點 SignalR</span><span class="sxs-lookup"><span data-stu-id="4f13f-355">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="4f13f-356">開啟 `Startup.cs` 檔案。</span><span class="sxs-lookup"><span data-stu-id="4f13f-356">Open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="4f13f-357">將和類別的命名空間加入 <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> `ChatHub` 至檔案頂端：</span><span class="sxs-lookup"><span data-stu-id="4f13f-357">Add the namespaces for <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> and the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="4f13f-358">將回應壓縮中介軟體服務新增至 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="4f13f-358">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="4f13f-359">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="4f13f-359">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="4f13f-360">在處理管線的設定頂端使用回應壓縮中介軟體。</span><span class="sxs-lookup"><span data-stu-id="4f13f-360">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="4f13f-361">在對應中樞和用戶端回復的端點之間 Blazor ，新增中樞的端點。</span><span class="sxs-lookup"><span data-stu-id="4f13f-361">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="4f13f-362">將回應壓縮中介軟體服務新增至 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="4f13f-362">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="4f13f-363">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="4f13f-363">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="4f13f-364">在處理管線的設定頂端使用回應壓縮中介軟體。</span><span class="sxs-lookup"><span data-stu-id="4f13f-364">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="4f13f-365">在對應中樞和用戶端回復的端點之間 Blazor ，新增中樞的端點。</span><span class="sxs-lookup"><span data-stu-id="4f13f-365">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="4f13f-366">新增 Razor 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="4f13f-366">Add Razor component code for chat</span></span>

1. <span data-ttu-id="4f13f-367">開啟 `Pages/Index.razor` 檔案。</span><span class="sxs-lookup"><span data-stu-id="4f13f-367">Open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="4f13f-368">以下列程式碼取代標記：</span><span class="sxs-lookup"><span data-stu-id="4f13f-368">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="4f13f-369">以下列程式碼取代標記：</span><span class="sxs-lookup"><span data-stu-id="4f13f-369">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="4f13f-370">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="4f13f-370">Run the app</span></span>

<span data-ttu-id="4f13f-371">遵循您工具的指導方針：</span><span class="sxs-lookup"><span data-stu-id="4f13f-371">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f13f-372">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f13f-372">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="4f13f-373">按下<kbd>f5</kbd>鍵以執行應用程式的偵錯工具，或按<kbd>Ctrl</kbd> + <kbd>F5</kbd>執行應用程式，而不進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="4f13f-373">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="4f13f-374">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="4f13f-374">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4f13f-375">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="4f13f-375">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="4f13f-376">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="4f13f-376">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="4f13f-378">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4f13f-378">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f13f-379">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f13f-379">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="4f13f-380">按下<kbd>f5</kbd>鍵以執行應用程式的偵錯工具，或按<kbd>Ctrl</kbd> + <kbd>F5</kbd>執行應用程式，而不進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="4f13f-380">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="4f13f-381">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="4f13f-381">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4f13f-382">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="4f13f-382">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="4f13f-383">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="4f13f-383">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="4f13f-385">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4f13f-385">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f13f-386">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4f13f-386">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4f13f-387">按<kbd>⌘</kbd> + <kbd>↩</kbd>以執行應用程式的偵錯工具或<kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd>執行應用程式，而不需進行任何偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="4f13f-387">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="4f13f-388">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="4f13f-388">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4f13f-389">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="4f13f-389">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="4f13f-390">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="4f13f-390">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="4f13f-392">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4f13f-392">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4f13f-393">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4f13f-393">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="4f13f-394">在來自專案資料夾的命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="4f13f-394">In a command shell from the project's folder, execute the following commands:</span></span>

   ```dotnetcli
   dotnet run
   ```

1. <span data-ttu-id="4f13f-395">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="4f13f-395">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4f13f-396">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="4f13f-396">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="4f13f-397">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="4f13f-397">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="4f13f-399">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4f13f-399">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

## <a name="next-steps"></a><span data-ttu-id="4f13f-400">後續步驟</span><span class="sxs-lookup"><span data-stu-id="4f13f-400">Next steps</span></span>

<span data-ttu-id="4f13f-401">在本教學課程中，您已了解如何：</span><span class="sxs-lookup"><span data-stu-id="4f13f-401">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4f13f-402">建立 Blazor 專案</span><span class="sxs-lookup"><span data-stu-id="4f13f-402">Create a Blazor project</span></span>
> * <span data-ttu-id="4f13f-403">新增 SignalR 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="4f13f-403">Add the SignalR client library</span></span>
> * <span data-ttu-id="4f13f-404">新增 SignalR 中樞</span><span class="sxs-lookup"><span data-stu-id="4f13f-404">Add a SignalR hub</span></span>
> * <span data-ttu-id="4f13f-405">新增 SignalR 中樞的服務和端點 SignalR</span><span class="sxs-lookup"><span data-stu-id="4f13f-405">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="4f13f-406">新增 Razor 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="4f13f-406">Add Razor component code for chat</span></span>

<span data-ttu-id="4f13f-407">若要深入瞭解如何建立 Blazor 應用程式，請參閱 Blazor 檔：</span><span class="sxs-lookup"><span data-stu-id="4f13f-407">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="4f13f-408"><xref:blazor/index>
> [使用 Identity 伺服器、websocket 和 Server-Sent 事件的持有人權杖驗證](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="4f13f-408"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4f13f-409">其他資源</span><span class="sxs-lookup"><span data-stu-id="4f13f-409">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="4f13f-410">SignalR 驗證的跨原始來源協商</span><span class="sxs-lookup"><span data-stu-id="4f13f-410">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
