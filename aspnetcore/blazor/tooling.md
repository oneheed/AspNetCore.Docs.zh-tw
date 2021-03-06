---
title: 適用于 ASP.NET Core 的工具 Blazor
author: guardrex
description: 瞭解可用來建立 Blazor 應用程式的工具。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2021
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 19270bb74326dccfee9466b7c1fa61daeab805a2
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394456"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="701d6-103">適用于 ASP.NET Core 的工具 Blazor</span><span class="sxs-lookup"><span data-stu-id="701d6-103">Tooling for ASP.NET Core Blazor</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="701d6-104">使用 **ASP.NET 和網頁程式開發** 工作負載，安裝最新版本的 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 。</span><span class="sxs-lookup"><span data-stu-id="701d6-104">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="701d6-105">建立新專案。</span><span class="sxs-lookup"><span data-stu-id="701d6-105">Create a new project.</span></span>

1. <span data-ttu-id="701d6-106">選取 [ **Blazor 應用程式**]。</span><span class="sxs-lookup"><span data-stu-id="701d6-106">Select **Blazor App**.</span></span> <span data-ttu-id="701d6-107">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="701d6-107">Select **Next**.</span></span>

1. <span data-ttu-id="701d6-108">在 [專案名稱] 欄位中提供專案名稱，或接受預設專案名稱。</span><span class="sxs-lookup"><span data-stu-id="701d6-108">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="701d6-109">確認 **位置** 專案是正確的，或提供專案的位置。</span><span class="sxs-lookup"><span data-stu-id="701d6-109">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="701d6-110">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="701d6-110">Select **Create**.</span></span>

1. <span data-ttu-id="701d6-111">如需 Blazor WebAssembly 體驗，請選擇 **Blazor WebAssembly 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="701d6-111">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="701d6-112">如需 Blazor Server 體驗，請選擇 **Blazor Server 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="701d6-112">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="701d6-113">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="701d6-113">Select **Create**.</span></span>

   <span data-ttu-id="701d6-114">如需託管 Blazor WebAssembly 體驗，請選取 [ **ASP.NET Core hosted** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="701d6-114">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="701d6-115">如需這兩個 Blazor 裝載模型的相關資訊， *Blazor WebAssembly* (獨立和裝載) 和 *Blazor Server* ，請參閱 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="701d6-115">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="701d6-116">按下<kbd>Ctrl</kbd> + <kbd>F5</kbd>以執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="701d6-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="701d6-117">如需信任 ASP.NET Core HTTPS 開發憑證的詳細資訊，請參閱 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> 。</span><span class="sxs-lookup"><span data-stu-id="701d6-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

<span data-ttu-id="701d6-118">執行託管 Blazor WebAssembly 應用程式時，請從解決方案的專案執行應用程式 **`Server`** 。</span><span class="sxs-lookup"><span data-stu-id="701d6-118">When executing a hosted Blazor WebAssembly app, run the app from the solution's **`Server`** project.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="701d6-119">安裝最新版本的 [.Net CORE SDK](https://dotnet.microsoft.com/download)。</span><span class="sxs-lookup"><span data-stu-id="701d6-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="701d6-120">如果您先前已安裝 SDK，您可以在命令 shell 中執行下列命令來判斷已安裝的版本：</span><span class="sxs-lookup"><span data-stu-id="701d6-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="701d6-121">安裝最新版本的 [Visual Studio Code](https://code.visualstudio.com)。</span><span class="sxs-lookup"><span data-stu-id="701d6-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="701d6-122">安裝最新的 [c # For Visual Studio Code 延伸](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)模組。</span><span class="sxs-lookup"><span data-stu-id="701d6-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="701d6-123">如需 Blazor WebAssembly 體驗，請在命令介面中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="701d6-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="701d6-124">針對託管 Blazor WebAssembly 體驗，請在命令中加入 hosted 選項 (`-ho` 或 `--hosted`) 選項：</span><span class="sxs-lookup"><span data-stu-id="701d6-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```

   <span data-ttu-id="701d6-125">如需 Blazor Server 體驗，請在命令介面中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="701d6-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="701d6-126">如需這兩個 Blazor 裝載模型的相關資訊， *Blazor WebAssembly* (獨立和裝載) 和 *Blazor Server* ，請參閱 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="701d6-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="701d6-127">在 Visual Studio Code 中開啟 `WebApplication1` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="701d6-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="701d6-128">IDE 要求您新增資產來建立和偵測專案。</span><span class="sxs-lookup"><span data-stu-id="701d6-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="701d6-129">選取 [是]  。</span><span class="sxs-lookup"><span data-stu-id="701d6-129">Select **Yes**.</span></span>

   <span data-ttu-id="701d6-130">**託管 Blazor WebAssembly 啟動和工作設定**</span><span class="sxs-lookup"><span data-stu-id="701d6-130">**Hosted Blazor WebAssembly launch and task configuration**</span></span>

   <span data-ttu-id="701d6-131">若為裝載的 Blazor WebAssembly 方案，請將 (或移動) `.vscode` 資料夾與和檔案移 `launch.json` `tasks.json` 至方案的父資料夾，也就是包含、和的一般專案資料夾名稱的資料夾 `Client` `Server` `Shared` 。</span><span class="sxs-lookup"><span data-stu-id="701d6-131">For hosted Blazor WebAssembly solutions, add (or move) the `.vscode` folder with `launch.json` and `tasks.json` files to the solution's parent folder, which is the folder that contains the typical project folder names of `Client`, `Server`, and `Shared`.</span></span> <span data-ttu-id="701d6-132">更新或確認和檔案中的設定 `launch.json` 會 `tasks.json` 從專案執行託管 Blazor WebAssembly 應用程式 **`Server`** 。</span><span class="sxs-lookup"><span data-stu-id="701d6-132">Update or confirm that the configuration in the `launch.json` and `tasks.json` files execute a hosted Blazor WebAssembly app from the **`Server`** project.</span></span>

   <span data-ttu-id="701d6-133">**`.vscode/launch.json`** (設定 `launch`) ：</span><span class="sxs-lookup"><span data-stu-id="701d6-133">**`.vscode/launch.json`** (`launch` configuration):</span></span>

   ```json
   ...
   "cwd": "${workspaceFolder}/{SERVER APP FOLDER}",
   ...
   ```

   <span data-ttu-id="701d6-134">在先前設定中， () 的目前工作目錄 `cwd` ， `{SERVER APP FOLDER}` 預留位置是 **`Server`** 專案的資料夾，通常是 " `Server` "。</span><span class="sxs-lookup"><span data-stu-id="701d6-134">In the preceding configuration for the current working directory (`cwd`), the `{SERVER APP FOLDER}` placeholder is the **`Server`** project's folder, typically "`Server`".</span></span>

   <span data-ttu-id="701d6-135">如果使用 Microsoft Edge，且系統上未安裝 Google Chrome，請在設定中新增的額外屬性 `"browser": "edge"` 。</span><span class="sxs-lookup"><span data-stu-id="701d6-135">If Microsoft Edge is used and Google Chrome isn't installed on the system, add an additional property of `"browser": "edge"` to the configuration.</span></span>

   <span data-ttu-id="701d6-136">的專案資料夾範例， `Server` 會將 Microsoft Edge 以瀏覽器的形式產生，以進行 debug，而不是使用預設的瀏覽器 Google Chrome：</span><span class="sxs-lookup"><span data-stu-id="701d6-136">Example for a project folder of `Server` and that spawns Microsoft Edge as the browser for debug runs instead of the default browser Google Chrome:</span></span>

   ```json
   ...
   "cwd": "${workspaceFolder}/Server",
   "browser": "edge"
   ...
   ```

   <span data-ttu-id="701d6-137">**`.vscode/tasks.json`** ([ `dotnet` 命令](/dotnet/core/tools/dotnet)引數) ：</span><span class="sxs-lookup"><span data-stu-id="701d6-137">**`.vscode/tasks.json`** ([`dotnet` command](/dotnet/core/tools/dotnet) arguments):</span></span>

   ```json
   ...
   "${workspaceFolder}/{SERVER APP FOLDER}/{PROJECT NAME}.csproj",
   ...
   ```

   <span data-ttu-id="701d6-138">在前面的引數中：</span><span class="sxs-lookup"><span data-stu-id="701d6-138">In the preceding argument:</span></span>

   * <span data-ttu-id="701d6-139">`{SERVER APP FOLDER}`預留位置是 **`Server`** 專案的資料夾，通常是 " `Server` "。</span><span class="sxs-lookup"><span data-stu-id="701d6-139">The `{SERVER APP FOLDER}` placeholder is the **`Server`** project's folder, typically "`Server`".</span></span>
   * <span data-ttu-id="701d6-140">`{PROJECT NAME}`預留位置是應用程式的名稱，通常是根據解決方案的名稱，然後是根據 `.Server` [ Blazor 專案範本](xref:blazor/project-structure)所產生之應用程式中的 ""。</span><span class="sxs-lookup"><span data-stu-id="701d6-140">The `{PROJECT NAME}` placeholder is the app's name, typically based on the solution's name followed by "`.Server`" in an app generated from the [Blazor project template](xref:blazor/project-structure).</span></span>

   <span data-ttu-id="701d6-141">下列教學課程中 [使用的 SignalR Blazor WebAssembly 應用程式](xref:tutorials/signalr-blazor) 範例會使用的專案資料夾名稱 `Server` 和專案名稱 `BlazorWebAssemblySignalRApp.Server` ：</span><span class="sxs-lookup"><span data-stu-id="701d6-141">The following example from the [tutorial for using SignalR with a Blazor WebAssembly app](xref:tutorials/signalr-blazor) uses a project folder name of `Server` and a project name of `BlazorWebAssemblySignalRApp.Server`:</span></span>

   ```json
   ...
   "args": [
     "build",
       "${workspaceFolder}/Server/BlazorWebAssemblySignalRApp.Server.csproj",
       "/property:GenerateFullPaths=true",
       "/consoleloggerparameters:NoSummary"
   ],
   ...
   ```

1. <span data-ttu-id="701d6-142">按下<kbd>Ctrl</kbd> + <kbd>F5</kbd>以執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="701d6-142">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="701d6-143">信任開發憑證</span><span class="sxs-lookup"><span data-stu-id="701d6-143">Trust a development certificate</span></span>

<span data-ttu-id="701d6-144">沒有任何集中式方法可以信任 Linux 上的憑證。</span><span class="sxs-lookup"><span data-stu-id="701d6-144">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="701d6-145">通常會採用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="701d6-145">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="701d6-146">在瀏覽器的排除清單中排除應用程式的 URL。</span><span class="sxs-lookup"><span data-stu-id="701d6-146">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="701d6-147">信任的所有自我簽署憑證 `localhost` 。</span><span class="sxs-lookup"><span data-stu-id="701d6-147">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="701d6-148">將憑證新增至瀏覽器中受信任的憑證清單。</span><span class="sxs-lookup"><span data-stu-id="701d6-148">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="701d6-149">如需詳細資訊，請參閱瀏覽器製造商和 Linux 發行版本所提供的指引。</span><span class="sxs-lookup"><span data-stu-id="701d6-149">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="701d6-150">安裝 [Visual Studio For Mac](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="701d6-150">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="701d6-151">選取  >  [檔案 **新方案**] 或 [**開始] 視窗** 中的 [建立 **新** 專案]。</span><span class="sxs-lookup"><span data-stu-id="701d6-151">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="701d6-152">在側邊欄中，選取 [ **Web] 和 [主控台**  >  **應用程式**]。</span><span class="sxs-lookup"><span data-stu-id="701d6-152">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="701d6-153">如需 Blazor WebAssembly 體驗，請選擇 **Blazor WebAssembly 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="701d6-153">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="701d6-154">如需 Blazor Server 體驗，請選擇 **Blazor Server 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="701d6-154">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="701d6-155">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="701d6-155">Select **Next**.</span></span>

   <span data-ttu-id="701d6-156">如需這兩個 Blazor 裝載模型的相關資訊， *Blazor WebAssembly* (獨立和裝載) 和 *Blazor Server* ，請參閱 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="701d6-156">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="701d6-157">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="701d6-157">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="701d6-158">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="701d6-158">Select **Next**.</span></span>

1. <span data-ttu-id="701d6-159">如需託管 Blazor WebAssembly 體驗，請選取 [ **ASP.NET Core hosted** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="701d6-159">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="701d6-160">在 [ **專案名稱** ] 欄位中，為應用程式命名 `WebApplication1` 。</span><span class="sxs-lookup"><span data-stu-id="701d6-160">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="701d6-161">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="701d6-161">Select **Create**.</span></span>

1. <span data-ttu-id="701d6-162">選取  >  [在不進行偵錯工具的 **情況下** 執行] 以執行應用程式 *而不需偵錯工具*</span><span class="sxs-lookup"><span data-stu-id="701d6-162">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="701d6-163">使用 [**執行**  >  **開始調試** 程式] 或 [執行 (&#9654;]) 按鈕來執行應用程式，以 *使用調試* 程式來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="701d6-163">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="701d6-164">如果出現提示信任開發憑證，請信任憑證並繼續。</span><span class="sxs-lookup"><span data-stu-id="701d6-164">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="701d6-165">需要使用者和 keychain 密碼才能信任憑證。</span><span class="sxs-lookup"><span data-stu-id="701d6-165">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="701d6-166">如需信任 ASP.NET Core HTTPS 開發憑證的詳細資訊，請參閱 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> 。</span><span class="sxs-lookup"><span data-stu-id="701d6-166">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

<span data-ttu-id="701d6-167">執行託管 Blazor WebAssembly 應用程式時，請從解決方案的專案執行應用程式 **`Server`** 。</span><span class="sxs-lookup"><span data-stu-id="701d6-167">When executing a hosted Blazor WebAssembly app, run the app from the solution's **`Server`** project.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a><span data-ttu-id="701d6-168">使用 Visual Studio Code 進行跨平臺 Blazor 開發</span><span class="sxs-lookup"><span data-stu-id="701d6-168">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="701d6-169">[Visual Studio Code](https://code.visualstudio.com/) 是開放原始碼、跨平臺的整合式開發環境 (IDE) ，可用來開發 Blazor 應用程式。</span><span class="sxs-lookup"><span data-stu-id="701d6-169">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="701d6-170">使用 .NET CLI 來建立新的 Blazor 應用程式，以使用 Visual Studio Code 進行開發。</span><span class="sxs-lookup"><span data-stu-id="701d6-170">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="701d6-171">如需詳細資訊，請參閱本文的 [Linux 版本](?pivots=linux)。</span><span class="sxs-lookup"><span data-stu-id="701d6-171">For more information, see the [Linux version of this article](?pivots=linux).</span></span>

## <a name="blazor-template-options"></a><span data-ttu-id="701d6-172">Blazor 範本選項</span><span class="sxs-lookup"><span data-stu-id="701d6-172">Blazor template options</span></span>

<span data-ttu-id="701d6-173">Blazor架構會提供範本，以針對兩個裝載模型建立新的應用程式 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="701d6-173">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="701d6-174">Blazor無論您針對 Blazor 開發 (visual Studio、visual Studio for Mac、Visual studio Code 或 .net CLI) 所選取的工具為何，都可以使用這些範本來建立新的專案和方案：</span><span class="sxs-lookup"><span data-stu-id="701d6-174">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="701d6-175">Blazor WebAssembly 專案範本： `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="701d6-175">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="701d6-176">Blazor Server 專案範本： `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="701d6-176">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="701d6-177">如需裝載模型的詳細資訊 Blazor ，請參閱 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="701d6-177">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span> <span data-ttu-id="701d6-178">如需專案範本的詳細資訊 Blazor ，請參閱 <xref:blazor/project-structure> 。</span><span class="sxs-lookup"><span data-stu-id="701d6-178">For more information on Blazor project templates, see <xref:blazor/project-structure>.</span></span>

<span data-ttu-id="701d6-179">您可以藉由將 [說明] 選項 (`-h` 或 `--help`) 傳送至 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令 shell 中的 CLI 命令，來取得範本選項：</span><span class="sxs-lookup"><span data-stu-id="701d6-179">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```

## <a name="additional-resources"></a><span data-ttu-id="701d6-180">其他資源</span><span class="sxs-lookup"><span data-stu-id="701d6-180">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/project-structure>
