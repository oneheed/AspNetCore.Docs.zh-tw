---
title: SignalR使用 ASP.NET Core 搭配Blazor
author: guardrex
description: 建立使用 ASP.NET Core 搭配的聊天應用 SignalR 程式 Blazor 。
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
ms.openlocfilehash: 50e5f2d5b1f9d0bf229bc57e6a1f599f9574b27c
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395223"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a><span data-ttu-id="9eece-103">SignalR使用 ASP.NET Core 搭配Blazor</span><span class="sxs-lookup"><span data-stu-id="9eece-103">Use ASP.NET Core SignalR with Blazor</span></span>

<span data-ttu-id="9eece-104">本教學課程會教您使用與建立即時應用程式的基本概念 SignalR Blazor 。</span><span class="sxs-lookup"><span data-stu-id="9eece-104">This tutorial teaches the basics of building a real-time app using SignalR with Blazor.</span></span> <span data-ttu-id="9eece-105">您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="9eece-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9eece-106">建立 Blazor 專案</span><span class="sxs-lookup"><span data-stu-id="9eece-106">Create a Blazor project</span></span>
> * <span data-ttu-id="9eece-107">新增 SignalR 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="9eece-107">Add the SignalR client library</span></span>
> * <span data-ttu-id="9eece-108">新增 SignalR 中樞</span><span class="sxs-lookup"><span data-stu-id="9eece-108">Add a SignalR hub</span></span>
> * <span data-ttu-id="9eece-109">新增 SignalR 中樞的服務和端點 SignalR</span><span class="sxs-lookup"><span data-stu-id="9eece-109">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="9eece-110">新增 Razor 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="9eece-110">Add Razor component code for chat</span></span>

<span data-ttu-id="9eece-111">在本教學課程結尾，您將會有一個可運作的聊天應用程式。</span><span class="sxs-lookup"><span data-stu-id="9eece-111">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="9eece-112">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="9eece-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9eece-113">必要條件</span><span class="sxs-lookup"><span data-stu-id="9eece-113">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="9eece-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9eece-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9eece-115">具有 **ASP.NET 和 網頁程式開發** 工作負載的 [Visual Studio 2019 16.8 或更新版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="9eece-115">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9eece-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9eece-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9eece-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9eece-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="9eece-118">Visual Studio for Mac 8.8 版或更新版本</span><span class="sxs-lookup"><span data-stu-id="9eece-118">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="9eece-119">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9eece-119">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="9eece-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9eece-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9eece-121">具有 **ASP.NET 和 網頁程式開發** 工作負載的 [Visual Studio 2019 16.6 或更新版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="9eece-121">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9eece-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9eece-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9eece-123">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9eece-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="9eece-124">Visual Studio for Mac 8.6 版或更新版本</span><span class="sxs-lookup"><span data-stu-id="9eece-124">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="9eece-125">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9eece-125">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="9eece-126">建立託管 Blazor WebAssembly 應用程式</span><span class="sxs-lookup"><span data-stu-id="9eece-126">Create a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="9eece-127">遵循您選擇的工具指導方針：</span><span class="sxs-lookup"><span data-stu-id="9eece-127">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9eece-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9eece-128">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="9eece-129">需要 Visual Studio 16.8 或更新版本，以及 .NET Core SDK 5.0.0 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="9eece-129">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="9eece-130">需要 Visual Studio 16.6 或更新版本，以及 .NET Core SDK 3.1.300 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="9eece-130">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="9eece-131">建立新專案。</span><span class="sxs-lookup"><span data-stu-id="9eece-131">Create a new project.</span></span>

1. <span data-ttu-id="9eece-132">選取 **Blazor 應用程式**，然後選取 **[下一步]**。</span><span class="sxs-lookup"><span data-stu-id="9eece-132">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="9eece-133">輸入 `BlazorWebAssemblySignalRApp` [ **專案名稱** ] 欄位。</span><span class="sxs-lookup"><span data-stu-id="9eece-133">Type `BlazorWebAssemblySignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="9eece-134">確認 **位置** 專案是正確的，或提供專案的位置。</span><span class="sxs-lookup"><span data-stu-id="9eece-134">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="9eece-135">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="9eece-135">Select **Create**.</span></span>

1. <span data-ttu-id="9eece-136">選擇 **Blazor WebAssembly 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="9eece-136">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="9eece-137">在 [ **Advanced**] 底下，選取 [ **ASP.NET Core hosted** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="9eece-137">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="9eece-138">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="9eece-138">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9eece-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9eece-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="9eece-140">在命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9eece-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   <span data-ttu-id="9eece-141">`-ho|--hosted`選項會建立託管 Blazor WebAssembly 方案。</span><span class="sxs-lookup"><span data-stu-id="9eece-141">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="9eece-142">如需在資料夾中設定 VS Code 資產的相關資訊 `.vscode` ，請參閱中的 **Linux** 作業系統指引 <xref:blazor/tooling> 。</span><span class="sxs-lookup"><span data-stu-id="9eece-142">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

   <span data-ttu-id="9eece-143">`-o|--output`選項會建立解決方案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="9eece-143">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="9eece-144">如果您已建立解決方案的資料夾，並在該資料夾中開啟命令 shell，請省略 `-o|--output` 選項和值以建立解決方案。</span><span class="sxs-lookup"><span data-stu-id="9eece-144">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

1. <span data-ttu-id="9eece-145">在 Visual Studio Code 中，開啟應用程式的專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="9eece-145">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="9eece-146">當對話方塊出現時，若要新增資產以建立和偵測應用程式，請選取 [ **是**]。</span><span class="sxs-lookup"><span data-stu-id="9eece-146">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="9eece-147">Visual Studio Code 會自動加入 `.vscode` 具有所產生檔案和檔案的資料夾 `launch.json` `tasks.json` 。</span><span class="sxs-lookup"><span data-stu-id="9eece-147">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9eece-148">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9eece-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9eece-149">安裝最新版本的 [Visual Studio For Mac](https://visualstudio.microsoft.com/vs/mac/) ，然後執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="9eece-149">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="9eece-150">選取  >  [檔案 **新方案**] 或 [**開始] 視窗** 中的 [建立 **新** 專案]。</span><span class="sxs-lookup"><span data-stu-id="9eece-150">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="9eece-151">在側邊欄中，選取 [ **Web] 和 [主控台**  >  **應用程式**]。</span><span class="sxs-lookup"><span data-stu-id="9eece-151">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="9eece-152">選擇 **Blazor WebAssembly 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="9eece-152">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="9eece-153">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="9eece-153">Select **Next**.</span></span>

1. <span data-ttu-id="9eece-154">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="9eece-154">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="9eece-155">選取 [ **ASP.NET Core Hosted** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="9eece-155">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="9eece-156">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="9eece-156">Select **Next**.</span></span>

1. <span data-ttu-id="9eece-157">在 [ **專案名稱** ] 欄位中，為應用程式命名 `BlazorWebAssemblySignalRApp` 。</span><span class="sxs-lookup"><span data-stu-id="9eece-157">In the **Project Name** field, name the app `BlazorWebAssemblySignalRApp`.</span></span> <span data-ttu-id="9eece-158">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="9eece-158">Select **Create**.</span></span>

   <span data-ttu-id="9eece-159">如果出現提示信任開發憑證，請信任憑證並繼續。</span><span class="sxs-lookup"><span data-stu-id="9eece-159">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="9eece-160">需要使用者和 keychain 密碼才能信任憑證。</span><span class="sxs-lookup"><span data-stu-id="9eece-160">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="9eece-161">流覽至專案資料夾，然後開啟專案的方案檔 () 來開啟專案 `.sln` 。</span><span class="sxs-lookup"><span data-stu-id="9eece-161">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9eece-162">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9eece-162">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="9eece-163">在命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9eece-163">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

<span data-ttu-id="9eece-164">`-ho|--hosted`選項會建立託管 Blazor WebAssembly 方案。</span><span class="sxs-lookup"><span data-stu-id="9eece-164">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

<span data-ttu-id="9eece-165">`-o|--output`選項會建立解決方案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="9eece-165">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="9eece-166">如果您已建立解決方案的資料夾，並在該資料夾中開啟命令 shell，請省略 `-o|--output` 選項和值以建立解決方案。</span><span class="sxs-lookup"><span data-stu-id="9eece-166">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="9eece-167">新增 SignalR 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="9eece-167">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9eece-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9eece-168">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="9eece-169">在 [ **方案 Explorer**] 中，以滑鼠右鍵按一下 `BlazorWebAssemblySignalRApp.Client` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="9eece-169">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="9eece-170">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [ **封裝來源** ] 設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="9eece-170">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="9eece-171">選取 **[流覽]** 之後， `Microsoft.AspNetCore.SignalR.Client` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="9eece-171">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="9eece-172">在搜尋結果中，選取 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 套件。</span><span class="sxs-lookup"><span data-stu-id="9eece-172">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="9eece-173">設定版本，使其符合應用程式的共用架構。</span><span class="sxs-lookup"><span data-stu-id="9eece-173">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="9eece-174">選取 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="9eece-174">Select **Install**.</span></span>

1. <span data-ttu-id="9eece-175">如果出現 [ **預覽變更** ] 對話方塊，請選取 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="9eece-175">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="9eece-176">如果出現 [ **接受授權** ] 對話方塊，請選取 [如果您同意授權條款即 **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="9eece-176">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9eece-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9eece-177">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="9eece-178">在 **整合式終端** 機中  >  ，從工具列)  (視圖 **終端** 機，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9eece-178">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="9eece-179">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="9eece-179">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9eece-180">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9eece-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9eece-181">在 [ **方案** ] 提要欄位中，以滑鼠右鍵按一下 `BlazorWebAssemblySignalRApp.Client` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="9eece-181">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="9eece-182">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [來源] 下拉式清單設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="9eece-182">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="9eece-183">選取 **[流覽]** 之後， `Microsoft.AspNetCore.SignalR.Client` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="9eece-183">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="9eece-184">在搜尋結果中，選取套件旁的核取方塊 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 。</span><span class="sxs-lookup"><span data-stu-id="9eece-184">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="9eece-185">設定版本，使其符合應用程式的共用架構。</span><span class="sxs-lookup"><span data-stu-id="9eece-185">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="9eece-186">選取 [ **新增套件**]。</span><span class="sxs-lookup"><span data-stu-id="9eece-186">Select **Add Package**.</span></span>

1. <span data-ttu-id="9eece-187">如果出現 [ **接受授權** ] 對話方塊，請在您同意授權條款時選取 [ **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="9eece-187">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9eece-188">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9eece-188">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="9eece-189">從解決方案資料夾的命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9eece-189">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="9eece-190">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="9eece-190">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="9eece-191">新增 system.string. Web 封裝</span><span class="sxs-lookup"><span data-stu-id="9eece-191">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="9eece-192">*本節僅適用于 ASP.NET Core 3.x 版的應用程式。*</span><span class="sxs-lookup"><span data-stu-id="9eece-192">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="9eece-193">由於在 ASP.NET Core 3.x 應用程式中使用5.x 時發生套件解析問題 [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) ，因此 `BlazorWebAssemblySignalRApp.Server` 專案需要的封裝參考 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 。</span><span class="sxs-lookup"><span data-stu-id="9eece-193">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the `BlazorWebAssemblySignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="9eece-194">在 .NET 5 的未來修補程式版本中，將會解決基礎問題。</span><span class="sxs-lookup"><span data-stu-id="9eece-194">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="9eece-195">如需詳細資訊，請參閱 [System.Text.Json 定義沒有相依性的 netcoreapp 3.0 (dotnet/runtime #45560) ](https://github.com/dotnet/runtime/issues/45560)。</span><span class="sxs-lookup"><span data-stu-id="9eece-195">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="9eece-196">若要新增 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 至 `BlazorWebAssemblySignalRApp.Server` ASP.NET Core 3.1 裝載解決方案的專案 Blazor ，請遵循您選擇的工具指導方針：</span><span class="sxs-lookup"><span data-stu-id="9eece-196">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorWebAssemblySignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9eece-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9eece-197">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="9eece-198">在 [ **方案 Explorer**] 中，以滑鼠右鍵按一下 `BlazorWebAssemblySignalRApp.Server` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="9eece-198">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="9eece-199">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [ **封裝來源** ] 設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="9eece-199">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="9eece-200">選取 **[流覽]** 之後， `System.Text.Encodings.Web` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="9eece-200">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="9eece-201">在搜尋結果中，選取 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 套件。</span><span class="sxs-lookup"><span data-stu-id="9eece-201">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="9eece-202">選取符合使用中共用架構的套件版本。</span><span class="sxs-lookup"><span data-stu-id="9eece-202">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="9eece-203">選取 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="9eece-203">Select **Install**.</span></span>

1. <span data-ttu-id="9eece-204">如果出現 [ **預覽變更** ] 對話方塊，請選取 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="9eece-204">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="9eece-205">如果出現 [ **接受授權** ] 對話方塊，請選取 [如果您同意授權條款即 **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="9eece-205">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9eece-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9eece-206">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="9eece-207">在 **整合式終端** 機中 > ，從工具列)  (查看 **終端** 機，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9eece-207">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="9eece-208">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="9eece-208">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9eece-209">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9eece-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9eece-210">在 [ **方案** ] 提要欄位中，以滑鼠右鍵按一下 `BlazorWebAssemblySignalRApp.Server` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="9eece-210">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="9eece-211">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [來源] 下拉式清單設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="9eece-211">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="9eece-212">選取 **[流覽]** 之後， `System.Text.Encodings.Web` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="9eece-212">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="9eece-213">在搜尋結果中，選取套件旁的核取方塊 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) ，選取符合使用中共用架構的正確套件版本，然後選取 [ **新增套件**]。</span><span class="sxs-lookup"><span data-stu-id="9eece-213">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="9eece-214">如果出現 [ **接受授權** ] 對話方塊，請在您同意授權條款時選取 [ **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="9eece-214">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9eece-215">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9eece-215">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="9eece-216">從解決方案資料夾的命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9eece-216">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="9eece-217">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="9eece-217">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="9eece-218">新增 SignalR 中樞</span><span class="sxs-lookup"><span data-stu-id="9eece-218">Add a SignalR hub</span></span>

<span data-ttu-id="9eece-219">在 `BlazorWebAssemblySignalRApp.Server` 專案中，建立 `Hubs` (複數) 資料夾，並 () 新增下列 `ChatHub` 類別 `Hubs/ChatHub.cs` ：</span><span class="sxs-lookup"><span data-stu-id="9eece-219">In the `BlazorWebAssemblySignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="9eece-220">新增中樞的服務和端點 SignalR</span><span class="sxs-lookup"><span data-stu-id="9eece-220">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="9eece-221">在 `BlazorWebAssemblySignalRApp.Server` 專案中，開啟 `Startup.cs` 檔案。</span><span class="sxs-lookup"><span data-stu-id="9eece-221">In the `BlazorWebAssemblySignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="9eece-222">將類別的命名空間新增 `ChatHub` 至檔案頂端：</span><span class="sxs-lookup"><span data-stu-id="9eece-222">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="9eece-223">新增 SignalR 和回應壓縮中介軟體服務至 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="9eece-223">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. <span data-ttu-id="9eece-224">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="9eece-224">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="9eece-225">在處理管線的設定頂端使用回應壓縮中介軟體。</span><span class="sxs-lookup"><span data-stu-id="9eece-225">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="9eece-226">在控制器的端點和用戶端的回復之間，新增中樞的端點。</span><span class="sxs-lookup"><span data-stu-id="9eece-226">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="9eece-227">新增 SignalR 和回應壓縮中介軟體服務至 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="9eece-227">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="9eece-228">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="9eece-228">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="9eece-229">在處理管線的設定頂端使用回應壓縮中介軟體。</span><span class="sxs-lookup"><span data-stu-id="9eece-229">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="9eece-230">在控制器的端點和用戶端的回復之間，新增中樞的端點。</span><span class="sxs-lookup"><span data-stu-id="9eece-230">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="9eece-231">新增 Razor 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="9eece-231">Add Razor component code for chat</span></span>

1. <span data-ttu-id="9eece-232">在 `BlazorWebAssemblySignalRApp.Client` 專案中，開啟 `Pages/Index.razor` 檔案。</span><span class="sxs-lookup"><span data-stu-id="9eece-232">In the `BlazorWebAssemblySignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="9eece-233">以下列程式碼取代標記：</span><span class="sxs-lookup"><span data-stu-id="9eece-233">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="9eece-234">以下列程式碼取代標記：</span><span class="sxs-lookup"><span data-stu-id="9eece-234">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="9eece-235">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="9eece-235">Run the app</span></span>

<span data-ttu-id="9eece-236">遵循您工具的指導方針：</span><span class="sxs-lookup"><span data-stu-id="9eece-236">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9eece-237">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9eece-237">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="9eece-238">在 [ **方案瀏覽器**] 中，選取 `BlazorWebAssemblySignalRApp.Server` 專案。</span><span class="sxs-lookup"><span data-stu-id="9eece-238">In **Solution Explorer**, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="9eece-239">按下<kbd>f5</kbd>鍵以執行應用程式的偵錯工具，或按<kbd>Ctrl</kbd> + <kbd>F5</kbd>執行應用程式，而不進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="9eece-239">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="9eece-240">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="9eece-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="9eece-241">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="9eece-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="9eece-242">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="9eece-242">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="9eece-244">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="9eece-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9eece-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9eece-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9eece-246">如需在資料夾中設定 VS Code 資產的相關資訊 `.vscode` ，請參閱中的 **Linux** 作業系統指引 <xref:blazor/tooling> 。</span><span class="sxs-lookup"><span data-stu-id="9eece-246">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="9eece-247">按下<kbd>f5</kbd>鍵以執行應用程式的偵錯工具，或按<kbd>Ctrl</kbd> + <kbd>F5</kbd>執行應用程式，而不進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="9eece-247">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="9eece-248">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="9eece-248">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="9eece-249">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="9eece-249">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="9eece-250">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="9eece-250">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="9eece-252">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="9eece-252">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9eece-253">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9eece-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9eece-254">在 [ **方案** ] 側邊欄中，選取 `BlazorWebAssemblySignalRApp.Server` 專案。</span><span class="sxs-lookup"><span data-stu-id="9eece-254">In the **Solution** sidebar, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="9eece-255">按<kbd>⌘</kbd> + <kbd>↩</kbd>以執行應用程式的偵錯工具或<kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd>執行應用程式，而不需進行任何偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="9eece-255">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="9eece-256">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="9eece-256">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="9eece-257">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="9eece-257">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="9eece-258">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="9eece-258">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="9eece-260">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="9eece-260">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9eece-261">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9eece-261">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="9eece-262">從解決方案資料夾的命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9eece-262">In a command shell from the solution's folder, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="9eece-263">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="9eece-263">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="9eece-264">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="9eece-264">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="9eece-265">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="9eece-265">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="9eece-267">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="9eece-267">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a><span data-ttu-id="9eece-268">建立 Blazor Server 應用程式</span><span class="sxs-lookup"><span data-stu-id="9eece-268">Create a Blazor Server app</span></span>

<span data-ttu-id="9eece-269">遵循您選擇的工具指導方針：</span><span class="sxs-lookup"><span data-stu-id="9eece-269">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9eece-270">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9eece-270">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="9eece-271">需要 Visual Studio 16.8 或更新版本，以及 .NET Core SDK 5.0.0 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="9eece-271">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="9eece-272">需要 Visual Studio 16.6 或更新版本，以及 .NET Core SDK 3.1.300 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="9eece-272">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="9eece-273">建立新專案。</span><span class="sxs-lookup"><span data-stu-id="9eece-273">Create a new project.</span></span>

1. <span data-ttu-id="9eece-274">選取 **Blazor 應用程式**，然後選取 **[下一步]**。</span><span class="sxs-lookup"><span data-stu-id="9eece-274">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="9eece-275">輸入 `BlazorServerSignalRApp` [ **專案名稱** ] 欄位。</span><span class="sxs-lookup"><span data-stu-id="9eece-275">Type `BlazorServerSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="9eece-276">確認 **位置** 專案是正確的，或提供專案的位置。</span><span class="sxs-lookup"><span data-stu-id="9eece-276">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="9eece-277">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="9eece-277">Select **Create**.</span></span>

1. <span data-ttu-id="9eece-278">選擇 **Blazor Server 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="9eece-278">Choose the **Blazor Server App** template.</span></span>

1. <span data-ttu-id="9eece-279">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="9eece-279">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9eece-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9eece-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="9eece-281">在命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9eece-281">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   <span data-ttu-id="9eece-282">`-o|--output`選項會建立專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="9eece-282">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="9eece-283">如果您已建立專案的資料夾，並在該資料夾中開啟命令 shell，請省略 `-o|--output` 選項和值以建立專案。</span><span class="sxs-lookup"><span data-stu-id="9eece-283">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

1. <span data-ttu-id="9eece-284">在 Visual Studio Code 中，開啟應用程式的專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="9eece-284">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="9eece-285">當對話方塊出現時，若要新增資產以建立和偵測應用程式，請選取 [ **是**]。</span><span class="sxs-lookup"><span data-stu-id="9eece-285">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="9eece-286">Visual Studio Code 會自動加入 `.vscode` 具有所產生檔案和檔案的資料夾 `launch.json` `tasks.json` 。</span><span class="sxs-lookup"><span data-stu-id="9eece-286">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span> <span data-ttu-id="9eece-287">如需在資料夾中設定 VS Code 資產的相關資訊 `.vscode` ，請參閱中的 **Linux** 作業系統指引 <xref:blazor/tooling> 。</span><span class="sxs-lookup"><span data-stu-id="9eece-287">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9eece-288">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9eece-288">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9eece-289">安裝最新版本的 [Visual Studio For Mac](https://visualstudio.microsoft.com/vs/mac/) ，然後執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="9eece-289">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="9eece-290">選取  >  [檔案 **新方案**] 或 [**開始] 視窗** 中的 [建立 **新** 專案]。</span><span class="sxs-lookup"><span data-stu-id="9eece-290">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="9eece-291">在側邊欄中，選取 [ **Web] 和 [主控台**  >  **應用程式**]。</span><span class="sxs-lookup"><span data-stu-id="9eece-291">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="9eece-292">選擇 **Blazor Server 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="9eece-292">Choose the **Blazor Server App** template.</span></span> <span data-ttu-id="9eece-293">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="9eece-293">Select **Next**.</span></span>

1. <span data-ttu-id="9eece-294">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="9eece-294">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="9eece-295">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="9eece-295">Select **Next**.</span></span>

1. <span data-ttu-id="9eece-296">在 [ **專案名稱** ] 欄位中，為應用程式命名 `BlazorServerSignalRApp` 。</span><span class="sxs-lookup"><span data-stu-id="9eece-296">In the **Project Name** field, name the app `BlazorServerSignalRApp`.</span></span> <span data-ttu-id="9eece-297">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="9eece-297">Select **Create**.</span></span>

   <span data-ttu-id="9eece-298">如果出現提示信任開發憑證，請信任憑證並繼續。</span><span class="sxs-lookup"><span data-stu-id="9eece-298">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="9eece-299">需要使用者和 keychain 密碼才能信任憑證。</span><span class="sxs-lookup"><span data-stu-id="9eece-299">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="9eece-300">流覽至專案資料夾，然後開啟專案的方案檔 () 來開啟專案 `.sln` 。</span><span class="sxs-lookup"><span data-stu-id="9eece-300">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9eece-301">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9eece-301">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="9eece-302">在命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9eece-302">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

<span data-ttu-id="9eece-303">`-o|--output`選項會建立專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="9eece-303">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="9eece-304">如果您已建立專案的資料夾，並在該資料夾中開啟命令 shell，請省略 `-o|--output` 選項和值以建立專案。</span><span class="sxs-lookup"><span data-stu-id="9eece-304">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="9eece-305">新增 SignalR 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="9eece-305">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9eece-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9eece-306">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="9eece-307">在 [ **方案 Explorer**] 中，以滑鼠右鍵按一下 `BlazorServerSignalRApp` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="9eece-307">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="9eece-308">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [ **封裝來源** ] 設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="9eece-308">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="9eece-309">選取 **[流覽]** 之後， `Microsoft.AspNetCore.SignalR.Client` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="9eece-309">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="9eece-310">在搜尋結果中，選取 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 套件。</span><span class="sxs-lookup"><span data-stu-id="9eece-310">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="9eece-311">設定版本，使其符合應用程式的共用架構。</span><span class="sxs-lookup"><span data-stu-id="9eece-311">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="9eece-312">選取 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="9eece-312">Select **Install**.</span></span>

1. <span data-ttu-id="9eece-313">如果出現 [ **預覽變更** ] 對話方塊，請選取 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="9eece-313">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="9eece-314">如果出現 [ **接受授權** ] 對話方塊，請選取 [如果您同意授權條款即 **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="9eece-314">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9eece-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9eece-315">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="9eece-316">在 **整合式終端** 機中  >  ，從工具列)  (視圖 **終端** 機，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9eece-316">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="9eece-317">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="9eece-317">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9eece-318">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9eece-318">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9eece-319">在 [ **方案** ] 提要欄位中，以滑鼠右鍵按一下 `BlazorServerSignalRApp` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="9eece-319">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="9eece-320">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [來源] 下拉式清單設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="9eece-320">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="9eece-321">選取 **[流覽]** 之後， `Microsoft.AspNetCore.SignalR.Client` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="9eece-321">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="9eece-322">在搜尋結果中，選取套件旁的核取方塊 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 。</span><span class="sxs-lookup"><span data-stu-id="9eece-322">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="9eece-323">設定版本，使其符合應用程式的共用架構。</span><span class="sxs-lookup"><span data-stu-id="9eece-323">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="9eece-324">選取 [ **新增套件**]。</span><span class="sxs-lookup"><span data-stu-id="9eece-324">Select **Add Package**.</span></span>

1. <span data-ttu-id="9eece-325">如果出現 [ **接受授權** ] 對話方塊，請在您同意授權條款時選取 [ **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="9eece-325">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9eece-326">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9eece-326">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="9eece-327">在來自專案資料夾的命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9eece-327">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="9eece-328">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="9eece-328">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="9eece-329">新增 system.string. Web 封裝</span><span class="sxs-lookup"><span data-stu-id="9eece-329">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="9eece-330">*本節僅適用于 ASP.NET Core 3.x 版的應用程式。*</span><span class="sxs-lookup"><span data-stu-id="9eece-330">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="9eece-331">由於在 ASP.NET Core 3.x 應用程式中使用5.x 時發生套件解析問題 [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) ，因此專案需要的封裝參考 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 。</span><span class="sxs-lookup"><span data-stu-id="9eece-331">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="9eece-332">在 .NET 5 的未來修補程式版本中，將會解決基礎問題。</span><span class="sxs-lookup"><span data-stu-id="9eece-332">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="9eece-333">如需詳細資訊，請參閱 [System.Text.Json 定義沒有相依性的 netcoreapp 3.0 (dotnet/runtime #45560) ](https://github.com/dotnet/runtime/issues/45560)。</span><span class="sxs-lookup"><span data-stu-id="9eece-333">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="9eece-334">若要加入 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 至專案，請遵循您選擇的工具指導方針：</span><span class="sxs-lookup"><span data-stu-id="9eece-334">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the project, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9eece-335">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9eece-335">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="9eece-336">在 [ **方案 Explorer**] 中，以滑鼠右鍵按一下 `BlazorServerSignalRApp` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="9eece-336">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="9eece-337">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [ **封裝來源** ] 設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="9eece-337">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="9eece-338">選取 **[流覽]** 之後， `System.Text.Encodings.Web` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="9eece-338">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="9eece-339">在搜尋結果中，選取 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 套件。</span><span class="sxs-lookup"><span data-stu-id="9eece-339">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="9eece-340">選取符合使用中共用架構的套件版本。</span><span class="sxs-lookup"><span data-stu-id="9eece-340">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="9eece-341">選取 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="9eece-341">Select **Install**.</span></span>

1. <span data-ttu-id="9eece-342">如果出現 [ **預覽變更** ] 對話方塊，請選取 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="9eece-342">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="9eece-343">如果出現 [ **接受授權** ] 對話方塊，請選取 [如果您同意授權條款即 **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="9eece-343">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9eece-344">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9eece-344">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="9eece-345">在 **整合式終端** 機中 > ，從工具列)  (查看 **終端** 機，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9eece-345">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="9eece-346">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="9eece-346">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9eece-347">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9eece-347">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9eece-348">在 [ **方案** ] 提要欄位中，以滑鼠右鍵按一下 `BlazorServerSignalRApp` 專案，然後選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="9eece-348">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="9eece-349">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [來源] 下拉式清單設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="9eece-349">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="9eece-350">選取 **[流覽]** 之後， `System.Text.Encodings.Web` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="9eece-350">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="9eece-351">在搜尋結果中，選取套件旁的核取方塊 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) ，選取符合使用中共用架構的正確套件版本，然後選取 [ **新增套件**]。</span><span class="sxs-lookup"><span data-stu-id="9eece-351">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="9eece-352">如果出現 [ **接受授權** ] 對話方塊，請在您同意授權條款時選取 [ **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="9eece-352">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9eece-353">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9eece-353">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="9eece-354">在來自專案資料夾的命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9eece-354">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="9eece-355">若要加入舊版封裝，請提供 `--version {VERSION}` 選項，其中 `{VERSION}` 預留位置是要加入之封裝的版本。</span><span class="sxs-lookup"><span data-stu-id="9eece-355">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="9eece-356">新增 SignalR 中樞</span><span class="sxs-lookup"><span data-stu-id="9eece-356">Add a SignalR hub</span></span>

<span data-ttu-id="9eece-357">建立 `Hubs` (複數) 資料夾，並 `ChatHub` () 新增下列類別 `Hubs/ChatHub.cs` ：</span><span class="sxs-lookup"><span data-stu-id="9eece-357">Create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="9eece-358">新增中樞的服務和端點 SignalR</span><span class="sxs-lookup"><span data-stu-id="9eece-358">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="9eece-359">開啟 `Startup.cs` 檔案。</span><span class="sxs-lookup"><span data-stu-id="9eece-359">Open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="9eece-360">將和類別的命名空間加入 <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> `ChatHub` 至檔案頂端：</span><span class="sxs-lookup"><span data-stu-id="9eece-360">Add the namespaces for <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> and the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="9eece-361">將回應壓縮中介軟體服務新增至 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="9eece-361">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="9eece-362">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="9eece-362">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="9eece-363">在處理管線的設定頂端使用回應壓縮中介軟體。</span><span class="sxs-lookup"><span data-stu-id="9eece-363">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="9eece-364">在對應中樞和用戶端回復的端點之間 Blazor ，新增中樞的端點。</span><span class="sxs-lookup"><span data-stu-id="9eece-364">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="9eece-365">將回應壓縮中介軟體服務新增至 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="9eece-365">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="9eece-366">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="9eece-366">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="9eece-367">在處理管線的設定頂端使用回應壓縮中介軟體。</span><span class="sxs-lookup"><span data-stu-id="9eece-367">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="9eece-368">在對應中樞和用戶端回復的端點之間 Blazor ，新增中樞的端點。</span><span class="sxs-lookup"><span data-stu-id="9eece-368">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="9eece-369">新增 Razor 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="9eece-369">Add Razor component code for chat</span></span>

1. <span data-ttu-id="9eece-370">開啟 `Pages/Index.razor` 檔案。</span><span class="sxs-lookup"><span data-stu-id="9eece-370">Open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="9eece-371">以下列程式碼取代標記：</span><span class="sxs-lookup"><span data-stu-id="9eece-371">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="9eece-372">以下列程式碼取代標記：</span><span class="sxs-lookup"><span data-stu-id="9eece-372">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="9eece-373">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="9eece-373">Run the app</span></span>

<span data-ttu-id="9eece-374">遵循您工具的指導方針：</span><span class="sxs-lookup"><span data-stu-id="9eece-374">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9eece-375">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9eece-375">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="9eece-376">按下<kbd>f5</kbd>鍵以執行應用程式的偵錯工具，或按<kbd>Ctrl</kbd> + <kbd>F5</kbd>執行應用程式，而不進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="9eece-376">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="9eece-377">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="9eece-377">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="9eece-378">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="9eece-378">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="9eece-379">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="9eece-379">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="9eece-381">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="9eece-381">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9eece-382">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9eece-382">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="9eece-383">按下<kbd>f5</kbd>鍵以執行應用程式的偵錯工具，或按<kbd>Ctrl</kbd> + <kbd>F5</kbd>執行應用程式，而不進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="9eece-383">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="9eece-384">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="9eece-384">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="9eece-385">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="9eece-385">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="9eece-386">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="9eece-386">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="9eece-388">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="9eece-388">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9eece-389">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9eece-389">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9eece-390">按<kbd>⌘</kbd> + <kbd>↩</kbd>以執行應用程式的偵錯工具或<kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd>執行應用程式，而不需進行任何偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="9eece-390">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="9eece-391">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="9eece-391">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="9eece-392">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="9eece-392">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="9eece-393">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="9eece-393">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="9eece-395">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="9eece-395">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9eece-396">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9eece-396">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="9eece-397">在來自專案資料夾的命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9eece-397">In a command shell from the project's folder, execute the following commands:</span></span>

   ```dotnetcli
   dotnet run
   ```

1. <span data-ttu-id="9eece-398">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="9eece-398">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="9eece-399">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="9eece-399">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="9eece-400">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="9eece-400">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="9eece-402">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="9eece-402">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

## <a name="next-steps"></a><span data-ttu-id="9eece-403">後續步驟</span><span class="sxs-lookup"><span data-stu-id="9eece-403">Next steps</span></span>

<span data-ttu-id="9eece-404">在本教學課程中，您已了解如何：</span><span class="sxs-lookup"><span data-stu-id="9eece-404">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9eece-405">建立 Blazor 專案</span><span class="sxs-lookup"><span data-stu-id="9eece-405">Create a Blazor project</span></span>
> * <span data-ttu-id="9eece-406">新增 SignalR 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="9eece-406">Add the SignalR client library</span></span>
> * <span data-ttu-id="9eece-407">新增 SignalR 中樞</span><span class="sxs-lookup"><span data-stu-id="9eece-407">Add a SignalR hub</span></span>
> * <span data-ttu-id="9eece-408">新增 SignalR 中樞的服務和端點 SignalR</span><span class="sxs-lookup"><span data-stu-id="9eece-408">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="9eece-409">新增 Razor 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="9eece-409">Add Razor component code for chat</span></span>

<span data-ttu-id="9eece-410">若要深入瞭解如何建立 Blazor 應用程式，請參閱 Blazor 檔：</span><span class="sxs-lookup"><span data-stu-id="9eece-410">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="9eece-411"><xref:blazor/index>
> [使用 Identity 伺服器、websocket 和 Server-Sent 事件的持有人權杖驗證](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="9eece-411"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9eece-412">其他資源</span><span class="sxs-lookup"><span data-stu-id="9eece-412">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="9eece-413">SignalR 驗證的跨原始來源協商</span><span class="sxs-lookup"><span data-stu-id="9eece-413">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
