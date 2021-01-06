---
title: ASP.NET Core 的工具 Blazor
author: guardrex
description: 瞭解可用來建立 Blazor 應用程式的工具。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
ms.openlocfilehash: 4813668f5278473fbaae36d572e69700b3fe771a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97764233"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a><span data-ttu-id="563fa-103">ASP.NET Core 的工具 Blazor</span><span class="sxs-lookup"><span data-stu-id="563fa-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="563fa-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="563fa-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="563fa-105">使用 **ASP.NET 和網頁程式開發** 工作負載，安裝最新版本的 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 。</span><span class="sxs-lookup"><span data-stu-id="563fa-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="563fa-106">建立新專案。</span><span class="sxs-lookup"><span data-stu-id="563fa-106">Create a new project.</span></span>

1. <span data-ttu-id="563fa-107">選取 [ **Blazor 應用程式**]。</span><span class="sxs-lookup"><span data-stu-id="563fa-107">Select **Blazor App**.</span></span> <span data-ttu-id="563fa-108">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="563fa-108">Select **Next**.</span></span>

1. <span data-ttu-id="563fa-109">在 [專案名稱] 欄位中提供專案名稱，或接受預設專案名稱。</span><span class="sxs-lookup"><span data-stu-id="563fa-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="563fa-110">確認 **位置** 專案是正確的，或提供專案的位置。</span><span class="sxs-lookup"><span data-stu-id="563fa-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="563fa-111">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="563fa-111">Select **Create**.</span></span>

1. <span data-ttu-id="563fa-112">如需 Blazor WebAssembly 體驗，請選擇 **Blazor WebAssembly 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="563fa-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="563fa-113">如需 Blazor Server 體驗，請選擇 **Blazor Server 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="563fa-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="563fa-114">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="563fa-114">Select **Create**.</span></span>

   <span data-ttu-id="563fa-115">針對裝載 Blazor WebAssembly 體驗，請選取 [裝載 **ASP.NET Core** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="563fa-115">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="563fa-116">如需這兩個 Blazor 裝載模型的相關資訊， *Blazor WebAssembly* (獨立和裝載) 和 *Blazor Server* ，請參閱 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="563fa-116">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="563fa-117">按下<kbd>Ctrl</kbd> + <kbd>F5</kbd>以執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="563fa-117">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="563fa-118">如需信任 ASP.NET Core HTTPS 開發憑證的詳細資訊，請參閱 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> 。</span><span class="sxs-lookup"><span data-stu-id="563fa-118">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="563fa-119">安裝最新版本的 [.NET Core SDK](https://dotnet.microsoft.com/download)。</span><span class="sxs-lookup"><span data-stu-id="563fa-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="563fa-120">如果您先前已安裝 SDK，您可以在命令 shell 中執行下列命令來判斷已安裝的版本：</span><span class="sxs-lookup"><span data-stu-id="563fa-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="563fa-121">安裝最新版本的 [Visual Studio Code](https://code.visualstudio.com)。</span><span class="sxs-lookup"><span data-stu-id="563fa-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="563fa-122">安裝 Visual Studio Code 擴充功能的最新 [c #](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。</span><span class="sxs-lookup"><span data-stu-id="563fa-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="563fa-123">如需 Blazor WebAssembly 體驗，請在命令介面中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="563fa-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="563fa-124">針對託管 Blazor WebAssembly 體驗，請在命令中加入 hosted 選項 (`-ho` 或 `--hosted`) 選項：</span><span class="sxs-lookup"><span data-stu-id="563fa-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>
   
   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```
   
   <span data-ttu-id="563fa-125">如需 Blazor Server 體驗，請在命令介面中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="563fa-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="563fa-126">如需這兩個 Blazor 裝載模型的相關資訊， *Blazor WebAssembly* (獨立和裝載) 和 *Blazor Server* ，請參閱 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="563fa-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="563fa-127">在 Visual Studio Code 中開啟 `WebApplication1` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="563fa-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="563fa-128">IDE 要求您新增資產來建立和偵測專案。</span><span class="sxs-lookup"><span data-stu-id="563fa-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="563fa-129">選取 [是]  。</span><span class="sxs-lookup"><span data-stu-id="563fa-129">Select **Yes**.</span></span>

1. <span data-ttu-id="563fa-130">按下<kbd>Ctrl</kbd> + <kbd>F5</kbd>以執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="563fa-130">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="563fa-131">信任開發憑證</span><span class="sxs-lookup"><span data-stu-id="563fa-131">Trust a development certificate</span></span>

<span data-ttu-id="563fa-132">沒有任何集中式方法可以信任 Linux 上的憑證。</span><span class="sxs-lookup"><span data-stu-id="563fa-132">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="563fa-133">通常會採用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="563fa-133">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="563fa-134">在瀏覽器的排除清單中排除應用程式的 URL。</span><span class="sxs-lookup"><span data-stu-id="563fa-134">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="563fa-135">信任的所有自我簽署憑證 `localhost` 。</span><span class="sxs-lookup"><span data-stu-id="563fa-135">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="563fa-136">將憑證新增至瀏覽器中受信任的憑證清單。</span><span class="sxs-lookup"><span data-stu-id="563fa-136">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="563fa-137">如需詳細資訊，請參閱瀏覽器製造商和 Linux 發行版本所提供的指引。</span><span class="sxs-lookup"><span data-stu-id="563fa-137">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="563fa-138">安裝 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="563fa-138">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="563fa-139">選取  >  [檔案 **新方案**] 或 [**開始] 視窗** 中的 [建立 **新** 專案]。</span><span class="sxs-lookup"><span data-stu-id="563fa-139">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="563fa-140">在側邊欄中，選取 [ **Web] 和 [主控台**  >  **應用程式**]。</span><span class="sxs-lookup"><span data-stu-id="563fa-140">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="563fa-141">如需 Blazor WebAssembly 體驗，請選擇 **Blazor WebAssembly 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="563fa-141">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="563fa-142">如需 Blazor Server 體驗，請選擇 **Blazor Server 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="563fa-142">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="563fa-143">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="563fa-143">Select **Next**.</span></span>

   <span data-ttu-id="563fa-144">如需這兩個 Blazor 裝載模型的相關資訊， *Blazor WebAssembly* (獨立和裝載) 和 *Blazor Server* ，請參閱 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="563fa-144">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="563fa-145">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="563fa-145">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="563fa-146">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="563fa-146">Select **Next**.</span></span>

1. <span data-ttu-id="563fa-147">針對裝載 Blazor WebAssembly 體驗，請選取 [裝載 **ASP.NET Core** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="563fa-147">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="563fa-148">在 [ **專案名稱** ] 欄位中，為應用程式命名 `WebApplication1` 。</span><span class="sxs-lookup"><span data-stu-id="563fa-148">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="563fa-149">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="563fa-149">Select **Create**.</span></span>

1. <span data-ttu-id="563fa-150">選取  >  [在不進行偵錯工具的 **情況下** 執行] 以執行應用程式 *而不需偵錯工具*</span><span class="sxs-lookup"><span data-stu-id="563fa-150">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="563fa-151">使用 [**執行**  >  **開始調試** 程式] 或 [執行 ( # A0) ] 按鈕執行應用程式，以 *使用調試* 程式來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="563fa-151">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="563fa-152">如果出現提示信任開發憑證，請信任憑證並繼續。</span><span class="sxs-lookup"><span data-stu-id="563fa-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="563fa-153">需要使用者和 keychain 密碼才能信任憑證。</span><span class="sxs-lookup"><span data-stu-id="563fa-153">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="563fa-154">如需信任 ASP.NET Core HTTPS 開發憑證的詳細資訊，請參閱 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> 。</span><span class="sxs-lookup"><span data-stu-id="563fa-154">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-no-locblazor-development"></a><span data-ttu-id="563fa-155">使用 Visual Studio Code 進行跨平臺 Blazor 開發</span><span class="sxs-lookup"><span data-stu-id="563fa-155">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="563fa-156">[Visual Studio Code](https://code.visualstudio.com/) 是一種開放原始碼、跨平臺的整合式開發環境， (可用來開發應用程式的 IDE) Blazor 。</span><span class="sxs-lookup"><span data-stu-id="563fa-156">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="563fa-157">使用 .NET CLI 來建立新的 Blazor 應用程式，以 Visual Studio Code 的開發。</span><span class="sxs-lookup"><span data-stu-id="563fa-157">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="563fa-158">如需詳細資訊，請參閱本文的 [Linux 版本](/aspnet/core/blazor/tooling?pivots=linux)。</span><span class="sxs-lookup"><span data-stu-id="563fa-158">For more information, see the [Linux version of this article](/aspnet/core/blazor/tooling?pivots=linux).</span></span>

## <a name="no-locblazor-template-options"></a><span data-ttu-id="563fa-159">Blazor 範本選項</span><span class="sxs-lookup"><span data-stu-id="563fa-159">Blazor template options</span></span>

<span data-ttu-id="563fa-160">Blazor架構會提供範本，以針對兩個裝載模型建立新的應用程式 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="563fa-160">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="563fa-161">範本可用來建立新的 Blazor 專案和方案，不論您為 Blazor 開發 (Visual Studio、Visual Studio for Mac、Visual Studio Code 或 .net CLI) 選取的工具為何：</span><span class="sxs-lookup"><span data-stu-id="563fa-161">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="563fa-162">Blazor WebAssembly 專案範本： `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="563fa-162">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="563fa-163">Blazor Server 專案範本： `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="563fa-163">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="563fa-164">如需裝載模型的詳細資訊 Blazor ，請參閱 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="563fa-164">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="563fa-165">您可以藉由將 [說明] 選項 (`-h` 或 `--help`) 傳送至 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令 shell 中的 CLI 命令，來取得範本選項：</span><span class="sxs-lookup"><span data-stu-id="563fa-165">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```
