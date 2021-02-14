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
ms.openlocfilehash: a17b16563ac12d634e6bdc32638991f45e2a66d5
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280685"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="f4227-103">ASP.NET Core 的工具 Blazor</span><span class="sxs-lookup"><span data-stu-id="f4227-103">Tooling for ASP.NET Core Blazor</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="f4227-104">使用 **ASP.NET 和網頁程式開發** 工作負載，安裝最新版本的 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 。</span><span class="sxs-lookup"><span data-stu-id="f4227-104">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="f4227-105">建立新專案。</span><span class="sxs-lookup"><span data-stu-id="f4227-105">Create a new project.</span></span>

1. <span data-ttu-id="f4227-106">選取 [ **Blazor 應用程式**]。</span><span class="sxs-lookup"><span data-stu-id="f4227-106">Select **Blazor App**.</span></span> <span data-ttu-id="f4227-107">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="f4227-107">Select **Next**.</span></span>

1. <span data-ttu-id="f4227-108">在 [專案名稱] 欄位中提供專案名稱，或接受預設專案名稱。</span><span class="sxs-lookup"><span data-stu-id="f4227-108">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="f4227-109">確認 **位置** 專案是正確的，或提供專案的位置。</span><span class="sxs-lookup"><span data-stu-id="f4227-109">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="f4227-110">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="f4227-110">Select **Create**.</span></span>

1. <span data-ttu-id="f4227-111">如需 Blazor WebAssembly 體驗，請選擇 **Blazor WebAssembly 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="f4227-111">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="f4227-112">如需 Blazor Server 體驗，請選擇 **Blazor Server 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="f4227-112">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="f4227-113">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="f4227-113">Select **Create**.</span></span>

   <span data-ttu-id="f4227-114">針對裝載 Blazor WebAssembly 體驗，請選取 [裝載 **ASP.NET Core** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="f4227-114">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="f4227-115">如需這兩個 Blazor 裝載模型的相關資訊， *Blazor WebAssembly* (獨立和裝載) 和 *Blazor Server* ，請參閱 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="f4227-115">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f4227-116">按下<kbd>Ctrl</kbd> + <kbd>F5</kbd>以執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="f4227-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="f4227-117">如需信任 ASP.NET Core HTTPS 開發憑證的詳細資訊，請參閱 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> 。</span><span class="sxs-lookup"><span data-stu-id="f4227-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="f4227-118">安裝最新版本的 [.NET Core SDK](https://dotnet.microsoft.com/download)。</span><span class="sxs-lookup"><span data-stu-id="f4227-118">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="f4227-119">如果您先前已安裝 SDK，您可以在命令 shell 中執行下列命令來判斷已安裝的版本：</span><span class="sxs-lookup"><span data-stu-id="f4227-119">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="f4227-120">安裝最新版本的 [Visual Studio Code](https://code.visualstudio.com)。</span><span class="sxs-lookup"><span data-stu-id="f4227-120">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="f4227-121">安裝 Visual Studio Code 擴充功能的最新 [c #](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。</span><span class="sxs-lookup"><span data-stu-id="f4227-121">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="f4227-122">如需 Blazor WebAssembly 體驗，請在命令介面中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="f4227-122">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="f4227-123">針對託管 Blazor WebAssembly 體驗，請在命令中加入 hosted 選項 (`-ho` 或 `--hosted`) 選項：</span><span class="sxs-lookup"><span data-stu-id="f4227-123">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>
   
   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```
   
   <span data-ttu-id="f4227-124">如需 Blazor Server 體驗，請在命令介面中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="f4227-124">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="f4227-125">如需這兩個 Blazor 裝載模型的相關資訊， *Blazor WebAssembly* (獨立和裝載) 和 *Blazor Server* ，請參閱 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="f4227-125">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f4227-126">在 Visual Studio Code 中開啟 `WebApplication1` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="f4227-126">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="f4227-127">IDE 要求您新增資產來建立和偵測專案。</span><span class="sxs-lookup"><span data-stu-id="f4227-127">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="f4227-128">選取 [是]  。</span><span class="sxs-lookup"><span data-stu-id="f4227-128">Select **Yes**.</span></span>

1. <span data-ttu-id="f4227-129">按下<kbd>Ctrl</kbd> + <kbd>F5</kbd>以執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="f4227-129">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="f4227-130">信任開發憑證</span><span class="sxs-lookup"><span data-stu-id="f4227-130">Trust a development certificate</span></span>

<span data-ttu-id="f4227-131">沒有任何集中式方法可以信任 Linux 上的憑證。</span><span class="sxs-lookup"><span data-stu-id="f4227-131">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="f4227-132">通常會採用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="f4227-132">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="f4227-133">在瀏覽器的排除清單中排除應用程式的 URL。</span><span class="sxs-lookup"><span data-stu-id="f4227-133">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="f4227-134">信任的所有自我簽署憑證 `localhost` 。</span><span class="sxs-lookup"><span data-stu-id="f4227-134">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="f4227-135">將憑證新增至瀏覽器中受信任的憑證清單。</span><span class="sxs-lookup"><span data-stu-id="f4227-135">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="f4227-136">如需詳細資訊，請參閱瀏覽器製造商和 Linux 發行版本所提供的指引。</span><span class="sxs-lookup"><span data-stu-id="f4227-136">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="f4227-137">安裝 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="f4227-137">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="f4227-138">選取  >  [檔案 **新方案**] 或 [**開始] 視窗** 中的 [建立 **新** 專案]。</span><span class="sxs-lookup"><span data-stu-id="f4227-138">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="f4227-139">在側邊欄中，選取 [ **Web] 和 [主控台**  >  **應用程式**]。</span><span class="sxs-lookup"><span data-stu-id="f4227-139">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="f4227-140">如需 Blazor WebAssembly 體驗，請選擇 **Blazor WebAssembly 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="f4227-140">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="f4227-141">如需 Blazor Server 體驗，請選擇 **Blazor Server 應用程式** 範本。</span><span class="sxs-lookup"><span data-stu-id="f4227-141">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="f4227-142">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="f4227-142">Select **Next**.</span></span>

   <span data-ttu-id="f4227-143">如需這兩個 Blazor 裝載模型的相關資訊， *Blazor WebAssembly* (獨立和裝載) 和 *Blazor Server* ，請參閱 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="f4227-143">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f4227-144">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="f4227-144">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="f4227-145">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="f4227-145">Select **Next**.</span></span>

1. <span data-ttu-id="f4227-146">針對裝載 Blazor WebAssembly 體驗，請選取 [裝載 **ASP.NET Core** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="f4227-146">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="f4227-147">在 [ **專案名稱** ] 欄位中，為應用程式命名 `WebApplication1` 。</span><span class="sxs-lookup"><span data-stu-id="f4227-147">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="f4227-148">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="f4227-148">Select **Create**.</span></span>

1. <span data-ttu-id="f4227-149">選取  >  [在不進行偵錯工具的 **情況下** 執行] 以執行應用程式 *而不需偵錯工具*</span><span class="sxs-lookup"><span data-stu-id="f4227-149">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="f4227-150">使用 [**執行**  >  **開始調試** 程式] 或 [執行 (&#9654;]) 按鈕來執行應用程式，以 *使用調試* 程式來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="f4227-150">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="f4227-151">如果出現提示信任開發憑證，請信任憑證並繼續。</span><span class="sxs-lookup"><span data-stu-id="f4227-151">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="f4227-152">需要使用者和 keychain 密碼才能信任憑證。</span><span class="sxs-lookup"><span data-stu-id="f4227-152">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="f4227-153">如需信任 ASP.NET Core HTTPS 開發憑證的詳細資訊，請參閱 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> 。</span><span class="sxs-lookup"><span data-stu-id="f4227-153">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a><span data-ttu-id="f4227-154">使用 Visual Studio Code 進行跨平臺 Blazor 開發</span><span class="sxs-lookup"><span data-stu-id="f4227-154">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="f4227-155">[Visual Studio Code](https://code.visualstudio.com/) 是一種開放原始碼、跨平臺的整合式開發環境， (可用來開發應用程式的 IDE) Blazor 。</span><span class="sxs-lookup"><span data-stu-id="f4227-155">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="f4227-156">使用 .NET CLI 來建立新的 Blazor 應用程式，以 Visual Studio Code 的開發。</span><span class="sxs-lookup"><span data-stu-id="f4227-156">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="f4227-157">如需詳細資訊，請參閱本文的 [Linux 版本](?pivots=linux)。</span><span class="sxs-lookup"><span data-stu-id="f4227-157">For more information, see the [Linux version of this article](?pivots=linux).</span></span>

## <a name="blazor-template-options"></a><span data-ttu-id="f4227-158">Blazor 範本選項</span><span class="sxs-lookup"><span data-stu-id="f4227-158">Blazor template options</span></span>

<span data-ttu-id="f4227-159">Blazor架構會提供範本，以針對兩個裝載模型建立新的應用程式 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="f4227-159">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="f4227-160">範本可用來建立新的 Blazor 專案和方案，不論您為 Blazor 開發 (Visual Studio、Visual Studio for Mac、Visual Studio Code 或 .net CLI) 選取的工具為何：</span><span class="sxs-lookup"><span data-stu-id="f4227-160">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="f4227-161">Blazor WebAssembly 專案範本： `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="f4227-161">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="f4227-162">Blazor Server 專案範本： `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="f4227-162">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="f4227-163">如需裝載模型的詳細資訊 Blazor ，請參閱 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="f4227-163">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="f4227-164">您可以藉由將 [說明] 選項 (`-h` 或 `--help`) 傳送至 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令 shell 中的 CLI 命令，來取得範本選項：</span><span class="sxs-lookup"><span data-stu-id="f4227-164">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```