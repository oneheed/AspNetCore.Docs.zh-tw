---
title: '搭配使用 :::no-loc(SignalR)::: ASP.NET Core :::no-loc(Blazor WebAssembly):::'
author: guardrex
description: '建立搭配使用 ASP.NET Core 的聊天應用 :::no-loc(SignalR)::: 程式 :::no-loc(Blazor WebAssembly)::: 。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: afbfc44db8cb833df7a79061f9bd73052859fec2
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508171"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a><span data-ttu-id="8ab34-103">搭配使用 :::no-loc(SignalR)::: ASP.NET Core :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="8ab34-103">Use ASP.NET Core :::no-loc(SignalR)::: with :::no-loc(Blazor WebAssembly):::</span></span>

<span data-ttu-id="8ab34-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8ab34-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8ab34-105">本教學課程會教您使用與建立即時應用程式的基本概念 :::no-loc(SignalR)::: :::no-loc(Blazor WebAssembly)::: 。</span><span class="sxs-lookup"><span data-stu-id="8ab34-105">This tutorial teaches the basics of building a real-time app using :::no-loc(SignalR)::: with :::no-loc(Blazor WebAssembly):::.</span></span> <span data-ttu-id="8ab34-106">您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="8ab34-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8ab34-107">建立 :::no-loc(Blazor WebAssembly)::: 託管應用程式專案</span><span class="sxs-lookup"><span data-stu-id="8ab34-107">Create a :::no-loc(Blazor WebAssembly)::: Hosted app project</span></span>
> * <span data-ttu-id="8ab34-108">新增 :::no-loc(SignalR)::: 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="8ab34-108">Add the :::no-loc(SignalR)::: client library</span></span>
> * <span data-ttu-id="8ab34-109">新增 :::no-loc(SignalR)::: 中樞</span><span class="sxs-lookup"><span data-stu-id="8ab34-109">Add a :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="8ab34-110">新增 :::no-loc(SignalR)::: 中樞的服務和端點 :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="8ab34-110">Add :::no-loc(SignalR)::: services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="8ab34-111">新增 :::no-loc(Razor)::: 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="8ab34-111">Add :::no-loc(Razor)::: component code for chat</span></span>

<span data-ttu-id="8ab34-112">在本教學課程結尾，您將會有一個可運作的聊天應用程式。</span><span class="sxs-lookup"><span data-stu-id="8ab34-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="8ab34-113">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="8ab34-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8ab34-114">必要條件</span><span class="sxs-lookup"><span data-stu-id="8ab34-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="8ab34-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ab34-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8ab34-116">使用 **ASP.NET 和 網頁程式開發** 工作負載 [Visual Studio 2019 16.8 或更新版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="8ab34-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8ab34-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ab34-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8ab34-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8ab34-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="8ab34-119">Visual Studio for Mac 8.8 版或更新版本</span><span class="sxs-lookup"><span data-stu-id="8ab34-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="8ab34-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8ab34-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="8ab34-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ab34-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8ab34-122">使用 **ASP.NET 和 網頁程式開發** 工作負載 [Visual Studio 2019 16.6 或更新版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="8ab34-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8ab34-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ab34-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8ab34-124">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8ab34-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="8ab34-125">Visual Studio for Mac 8.6 版或更新版本</span><span class="sxs-lookup"><span data-stu-id="8ab34-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="8ab34-126">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8ab34-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="8ab34-127">建立託管 :::no-loc(Blazor WebAssembly)::: 應用程式專案</span><span class="sxs-lookup"><span data-stu-id="8ab34-127">Create a hosted :::no-loc(Blazor WebAssembly)::: app project</span></span>

<span data-ttu-id="8ab34-128">遵循您選擇的工具指導方針：</span><span class="sxs-lookup"><span data-stu-id="8ab34-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8ab34-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ab34-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="8ab34-130">需要 Visual Studio 16.8 或更新版本，以及 .NET Core SDK 5.0.0 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="8ab34-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="8ab34-131">需要 Visual Studio 16.6 或更新版本，以及 .NET Core SDK 3.1.300 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="8ab34-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="8ab34-132">建立新專案。</span><span class="sxs-lookup"><span data-stu-id="8ab34-132">Create a new project.</span></span>

1. <span data-ttu-id="8ab34-133">選取 **:::no-loc(Blazor)::: 應用程式** ，然後選取 **[下一步]** 。</span><span class="sxs-lookup"><span data-stu-id="8ab34-133">Select **:::no-loc(Blazor)::: App** and select **Next**.</span></span>

1. <span data-ttu-id="8ab34-134">輸入 `:::no-loc(Blazor)::::::no-loc(SignalR):::App` [ **專案名稱** ] 欄位。</span><span class="sxs-lookup"><span data-stu-id="8ab34-134">Type `:::no-loc(Blazor)::::::no-loc(SignalR):::App` in the **Project name** field.</span></span> <span data-ttu-id="8ab34-135">確認 **位置** 專案是正確的，或提供專案的位置。</span><span class="sxs-lookup"><span data-stu-id="8ab34-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="8ab34-136">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="8ab34-136">Select **Create**.</span></span>

1. <span data-ttu-id="8ab34-137">選擇 **:::no-loc(Blazor WebAssembly)::: 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="8ab34-137">Choose the **:::no-loc(Blazor WebAssembly)::: App** template.</span></span>

1. <span data-ttu-id="8ab34-138">在 [ **Advanced** ] 底下，選取 [ **hosted ASP.NET Core** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="8ab34-138">Under **Advanced** , select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="8ab34-139">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="8ab34-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8ab34-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ab34-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="8ab34-141">在命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="8ab34-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output :::no-loc(Blazor)::::::no-loc(SignalR):::App
   ```

1. <span data-ttu-id="8ab34-142">在 Visual Studio Code 中，開啟應用程式的專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="8ab34-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="8ab34-143">當對話方塊出現時，若要新增資產以建立和偵測應用程式，請選取 [ **是** ]。</span><span class="sxs-lookup"><span data-stu-id="8ab34-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="8ab34-144">Visual Studio Code 會自動加入 `.vscode` 具有所產生檔案和檔案的資料夾 `launch.json` `tasks.json` 。</span><span class="sxs-lookup"><span data-stu-id="8ab34-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8ab34-145">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8ab34-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="8ab34-146">安裝最新版本的 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) ，然後執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="8ab34-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="8ab34-147">選取 **File**  >  [檔案 **新方案** ] 或 [ **開始] 視窗** 中的 [建立 **新** 專案]。</span><span class="sxs-lookup"><span data-stu-id="8ab34-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="8ab34-148">在側邊欄中，選取 [ **Web] 和 [主控台**  >  **應用程式** ]。</span><span class="sxs-lookup"><span data-stu-id="8ab34-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="8ab34-149">選擇 **:::no-loc(Blazor WebAssembly)::: 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="8ab34-149">Choose the **:::no-loc(Blazor WebAssembly)::: App** template.</span></span> <span data-ttu-id="8ab34-150">選取 [下一步]。</span><span class="sxs-lookup"><span data-stu-id="8ab34-150">Select **Next**.</span></span>

1. <span data-ttu-id="8ab34-151">確認 [ **驗證** ] 設定為 [ **無驗證** ]。</span><span class="sxs-lookup"><span data-stu-id="8ab34-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="8ab34-152">選取 [ **主控 ASP.NET Core** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="8ab34-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="8ab34-153">選取 [下一步]。</span><span class="sxs-lookup"><span data-stu-id="8ab34-153">Select **Next**.</span></span>

1. <span data-ttu-id="8ab34-154">在 [ **專案名稱** ] 欄位中，為應用程式命名 `:::no-loc(Blazor)::::::no-loc(SignalR):::App` 。</span><span class="sxs-lookup"><span data-stu-id="8ab34-154">In the **Project Name** field, name the app `:::no-loc(Blazor)::::::no-loc(SignalR):::App`.</span></span> <span data-ttu-id="8ab34-155">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="8ab34-155">Select **Create**.</span></span>

   <span data-ttu-id="8ab34-156">如果出現提示信任開發憑證，請信任憑證並繼續。</span><span class="sxs-lookup"><span data-stu-id="8ab34-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="8ab34-157">需要使用者和 keychain 密碼才能信任憑證。</span><span class="sxs-lookup"><span data-stu-id="8ab34-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="8ab34-158">流覽至專案資料夾，然後開啟專案的方案檔 () 來開啟專案 `.sln` 。</span><span class="sxs-lookup"><span data-stu-id="8ab34-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8ab34-159">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8ab34-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="8ab34-160">在命令 shell 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="8ab34-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output :::no-loc(Blazor)::::::no-loc(SignalR):::App
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="8ab34-161">新增 :::no-loc(SignalR)::: 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="8ab34-161">Add the :::no-loc(SignalR)::: client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8ab34-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ab34-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="8ab34-163">在 **方案總管** 中，以滑鼠右鍵按一下 `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` 專案，然後選取 [ **管理 NuGet 套件** ]。</span><span class="sxs-lookup"><span data-stu-id="8ab34-163">In **Solution Explorer** , right-click the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="8ab34-164">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [ **封裝來源** ] 設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="8ab34-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="8ab34-165">選取 **[流覽]** 之後， `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="8ab34-165">With **Browse** selected, type `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` in the search box.</span></span>

1. <span data-ttu-id="8ab34-166">在搜尋結果中選取封裝， [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) 然後選取 [ **安裝** ]。</span><span class="sxs-lookup"><span data-stu-id="8ab34-166">In the search results, select the [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="8ab34-167">如果出現 [ **預覽變更** ] 對話方塊，請選取 **[確定]** 。</span><span class="sxs-lookup"><span data-stu-id="8ab34-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="8ab34-168">如果出現 [ **接受授權** ] 對話方塊，請選取 [如果您同意授權條款即 **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="8ab34-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8ab34-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ab34-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="8ab34-170">在 **整合式終端** 機 **View** 中  >  ，從工具列)  (查看 **終端** 機，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="8ab34-170">In the **Integrated Terminal** ( **View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.:::no-loc(SignalR):::.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8ab34-171">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8ab34-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="8ab34-172">在 [ **方案** ] 提要欄位中，以滑鼠右鍵按一下 `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` 專案，然後選取 [ **管理 NuGet 套件** ]。</span><span class="sxs-lookup"><span data-stu-id="8ab34-172">In the **Solution** sidebar, right-click the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="8ab34-173">在 [ **管理 NuGet 封裝** ] 對話方塊中，確認 [來源] 下拉式清單設定為 `nuget.org` 。</span><span class="sxs-lookup"><span data-stu-id="8ab34-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="8ab34-174">選取 **[流覽]** 之後， `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` 在 [搜尋] 方塊中輸入。</span><span class="sxs-lookup"><span data-stu-id="8ab34-174">With **Browse** selected, type `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` in the search box.</span></span>

1. <span data-ttu-id="8ab34-175">在搜尋結果中，選取套件旁的核取方塊 [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) ，然後選取 [ **新增套件** ]。</span><span class="sxs-lookup"><span data-stu-id="8ab34-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="8ab34-176">如果出現 [ **接受授權** ] 對話方塊，請在您同意授權條款時選取 [ **接受** ]。</span><span class="sxs-lookup"><span data-stu-id="8ab34-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8ab34-177">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8ab34-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="8ab34-178">在命令列介面中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="8ab34-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd :::no-loc(Blazor)::::::no-loc(SignalR):::App
dotnet add Client package Microsoft.AspNetCore.:::no-loc(SignalR):::.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="8ab34-179">新增 :::no-loc(SignalR)::: 中樞</span><span class="sxs-lookup"><span data-stu-id="8ab34-179">Add a :::no-loc(SignalR)::: hub</span></span>

<span data-ttu-id="8ab34-180">在 `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` 專案中，建立 `Hubs` (複數) 資料夾，並 () 新增下列 `ChatHub` 類別 `Hubs/ChatHub.cs` ：</span><span class="sxs-lookup"><span data-stu-id="8ab34-180">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="8ab34-181">新增中樞的服務和端點 :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="8ab34-181">Add services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>

1. <span data-ttu-id="8ab34-182">在 `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` 專案中，開啟 `Startup.cs` 檔案。</span><span class="sxs-lookup"><span data-stu-id="8ab34-182">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="8ab34-183">將類別的命名空間新增 `ChatHub` 至檔案頂端：</span><span class="sxs-lookup"><span data-stu-id="8ab34-183">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using :::no-loc(Blazor)::::::no-loc(SignalR):::App.Server.Hubs;
   ```

1. <span data-ttu-id="8ab34-184">新增 :::no-loc(SignalR)::: 和回應壓縮中介軟體服務至 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="8ab34-184">Add :::no-loc(SignalR)::: and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

1. <span data-ttu-id="8ab34-185">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="8ab34-185">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="8ab34-186">在處理管線的設定頂端使用回應壓縮中介軟體。</span><span class="sxs-lookup"><span data-stu-id="8ab34-186">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="8ab34-187">在控制器的端點和用戶端的回復之間，新增中樞的端點。</span><span class="sxs-lookup"><span data-stu-id="8ab34-187">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="8ab34-188">新增 :::no-loc(Razor)::: 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="8ab34-188">Add :::no-loc(Razor)::: component code for chat</span></span>

1. <span data-ttu-id="8ab34-189">在 `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` 專案中，開啟 `Pages/Index.razor` 檔案。</span><span class="sxs-lookup"><span data-stu-id="8ab34-189">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="8ab34-190">以下列程式碼取代標記：</span><span class="sxs-lookup"><span data-stu-id="8ab34-190">Replace the markup with the following code:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="8ab34-191">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="8ab34-191">Run the app</span></span>

1. <span data-ttu-id="8ab34-192">遵循您工具的指導方針：</span><span class="sxs-lookup"><span data-stu-id="8ab34-192">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8ab34-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ab34-193">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="8ab34-194">在 **方案總管** 中，選取 `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` 專案。</span><span class="sxs-lookup"><span data-stu-id="8ab34-194">In **Solution Explorer** , select the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project.</span></span> <span data-ttu-id="8ab34-195">按下<kbd>f5</kbd>鍵以執行應用程式的偵錯工具，或按<kbd>Ctrl</kbd> + <kbd>F5</kbd>執行應用程式，而不進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="8ab34-195">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="8ab34-196">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="8ab34-196">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="8ab34-197">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="8ab34-197">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="8ab34-198">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="8ab34-198">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor WebAssembly) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="8ab34-200">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="8ab34-200">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8ab34-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ab34-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="8ab34-202">當 VS Code 提供來為伺服器應用程式 () 建立啟動設定檔時 `.vscode/launch.json` ， `program` 專案會顯示如下，以指向應用程式的元件 (`{APPLICATION NAME}.Server.dll`) ：</span><span class="sxs-lookup"><span data-stu-id="8ab34-202">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

::: moniker range=">= aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="8ab34-203">按下<kbd>f5</kbd>鍵以執行應用程式的偵錯工具，或按<kbd>Ctrl</kbd> + <kbd>F5</kbd>執行應用程式，而不進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="8ab34-203">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="8ab34-204">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="8ab34-204">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="8ab34-205">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="8ab34-205">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="8ab34-206">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="8ab34-206">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor WebAssembly) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="8ab34-208">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="8ab34-208">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8ab34-209">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8ab34-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="8ab34-210">在 [ **方案** ] 側邊欄中，選取 `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` 專案。</span><span class="sxs-lookup"><span data-stu-id="8ab34-210">In the **Solution** sidebar, select the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project.</span></span> <span data-ttu-id="8ab34-211">按<kbd>⌘</kbd> + <kbd>↩</kbd>以執行應用程式的偵錯工具或<kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd>執行應用程式，而不需進行任何偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="8ab34-211">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="8ab34-212">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="8ab34-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="8ab34-213">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="8ab34-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="8ab34-214">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="8ab34-214">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor WebAssembly) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="8ab34-216">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="8ab34-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8ab34-217">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8ab34-217">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="8ab34-218">在命令列介面中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="8ab34-218">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="8ab34-219">從網址列複製 URL，開啟另一個瀏覽器執行個體或索引標籤，然後將 URL 貼入網址列。</span><span class="sxs-lookup"><span data-stu-id="8ab34-219">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="8ab34-220">選擇任一個瀏覽器，輸入名稱和訊息，然後選取要傳送訊息的按鈕。</span><span class="sxs-lookup"><span data-stu-id="8ab34-220">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="8ab34-221">名稱和訊息會立即顯示在兩個頁面上：</span><span class="sxs-lookup"><span data-stu-id="8ab34-221">The name and message are displayed on both pages instantly:</span></span>

   ![：：：非 loc (SignalR) ：：：：：：非 loc (Blazor WebAssembly) ：：：在顯示交換訊息的兩個瀏覽器視窗中開啟範例應用程式。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="8ab34-223">引號： *星星 TREK VI：未探索的國家/地區* &copy; 1991 最 [重要](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="8ab34-223">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="8ab34-224">後續步驟</span><span class="sxs-lookup"><span data-stu-id="8ab34-224">Next steps</span></span>

<span data-ttu-id="8ab34-225">在本教學課程中，您已了解如何：</span><span class="sxs-lookup"><span data-stu-id="8ab34-225">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8ab34-226">建立 :::no-loc(Blazor WebAssembly)::: 託管應用程式專案</span><span class="sxs-lookup"><span data-stu-id="8ab34-226">Create a :::no-loc(Blazor WebAssembly)::: Hosted app project</span></span>
> * <span data-ttu-id="8ab34-227">新增 :::no-loc(SignalR)::: 用戶端程式庫</span><span class="sxs-lookup"><span data-stu-id="8ab34-227">Add the :::no-loc(SignalR)::: client library</span></span>
> * <span data-ttu-id="8ab34-228">新增 :::no-loc(SignalR)::: 中樞</span><span class="sxs-lookup"><span data-stu-id="8ab34-228">Add a :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="8ab34-229">新增 :::no-loc(SignalR)::: 中樞的服務和端點 :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="8ab34-229">Add :::no-loc(SignalR)::: services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="8ab34-230">新增 :::no-loc(Razor)::: 聊天的元件程式碼</span><span class="sxs-lookup"><span data-stu-id="8ab34-230">Add :::no-loc(Razor)::: component code for chat</span></span>

<span data-ttu-id="8ab34-231">若要深入瞭解如何建立 :::no-loc(Blazor)::: 應用程式，請參閱 :::no-loc(Blazor)::: 檔：</span><span class="sxs-lookup"><span data-stu-id="8ab34-231">To learn more about building :::no-loc(Blazor)::: apps, see the :::no-loc(Blazor)::: documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="8ab34-232"><xref:blazor/index>
> [使用 :::no-loc(Identity)::: 伺服器、websocket 和 Server-Sent 事件的持有人權杖驗證](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="8ab34-232"><xref:blazor/index>
[Bearer token authentication with :::no-loc(Identity)::: Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8ab34-233">其他資源</span><span class="sxs-lookup"><span data-stu-id="8ab34-233">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="8ab34-234">:::no-loc(SignalR)::: 驗證的跨原始來源協商</span><span class="sxs-lookup"><span data-stu-id="8ab34-234">:::no-loc(SignalR)::: cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
