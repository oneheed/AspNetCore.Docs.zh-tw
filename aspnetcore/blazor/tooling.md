---
title: ASP.NET Core 的工具 Blazor
author: guardrex
description: 瞭解可用來建立 Blazor 應用程式的工具。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: d7e3743d12c235c20cc27f6a3263e2994a9e160a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625826"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a><span data-ttu-id="d7bd6-103">ASP.NET Core 的工具 Blazor</span><span class="sxs-lookup"><span data-stu-id="d7bd6-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="d7bd6-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d7bd6-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="d7bd6-105">使用**ASP.NET 和網頁程式開發**工作負載，安裝最新版本的[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="d7bd6-106">建立新專案。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-106">Create a new project.</span></span>

1. <span data-ttu-id="d7bd6-107">選取 [ \*\* Blazor 應用程式\*\*]。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-107">Select **Blazor App**.</span></span> <span data-ttu-id="d7bd6-108">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-108">Select **Next**.</span></span>

1. <span data-ttu-id="d7bd6-109">在 [專案名稱]\*\*\*\* 欄位中提供專案名稱，或接受預設專案名稱。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="d7bd6-110">確認 **位置** 專案是正確的，或提供專案的位置。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="d7bd6-111">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-111">Select **Create**.</span></span>

1. <span data-ttu-id="d7bd6-112">如需 Blazor WebAssembly 體驗，請選擇\*\* Blazor WebAssembly 應用程式\*\*範本。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="d7bd6-113">如需 Blazor Server 體驗，請選擇\*\* Blazor Server 應用程式\*\*範本。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="d7bd6-114">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-114">Select **Create**.</span></span>

   <span data-ttu-id="d7bd6-115">如需這兩個裝載模型和的詳細資訊，請 Blazor *Blazor WebAssembly* *Blazor Server* 參閱 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-115">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="d7bd6-116">按下<kbd>Ctrl</kbd> + <kbd>F5</kbd>以執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="d7bd6-117">如需信任 ASP.NET Core HTTPS 開發憑證的詳細資訊，請參閱 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> 。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="d7bd6-118">安裝最新版本的 [.Net Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-118">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="d7bd6-119">如果您先前已安裝 SDK，您可以在命令 shell 中執行下列命令來判斷已安裝的版本：</span><span class="sxs-lookup"><span data-stu-id="d7bd6-119">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="d7bd6-120">安裝最新版本的 [Visual Studio Code](https://code.visualstudio.com/)。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-120">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="d7bd6-121">安裝 Visual Studio Code 擴充功能的最新 [c #](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-121">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="d7bd6-122">如需 Blazor WebAssembly 體驗，請在命令介面中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="d7bd6-122">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="d7bd6-123">如需 Blazor Server 體驗，請在命令介面中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="d7bd6-123">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="d7bd6-124">如需這兩個裝載模型和的詳細資訊，請 Blazor *Blazor WebAssembly* *Blazor Server* 參閱 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-124">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="d7bd6-125">在 Visual Studio Code 中開啟 `WebApplication1` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-125">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="d7bd6-126">IDE 要求您新增資產來建立和偵測專案。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-126">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="d7bd6-127">選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-127">Select **Yes**.</span></span>

1. <span data-ttu-id="d7bd6-128">按下<kbd>Ctrl</kbd> + <kbd>F5</kbd>以執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-128">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="d7bd6-129">信任開發憑證</span><span class="sxs-lookup"><span data-stu-id="d7bd6-129">Trust a development certificate</span></span>

<span data-ttu-id="d7bd6-130">沒有任何集中式方法可以信任 Linux 上的憑證。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-130">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="d7bd6-131">通常會採用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="d7bd6-131">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="d7bd6-132">在瀏覽器的排除清單中排除應用程式的 URL。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-132">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="d7bd6-133">信任的所有自我簽署憑證 `localhost` 。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-133">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="d7bd6-134">將憑證新增至瀏覽器中受信任的憑證清單。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-134">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="d7bd6-135">如需詳細資訊，請參閱瀏覽器和 Linux 發行版本所提供的指引。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-135">For more information, see the guidance provided by your browser and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="d7bd6-136">安裝 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-136">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="d7bd6-137">選取**File**  >  [檔案**新方案**] 或 [**開始] 視窗**中的 [建立**新**專案]。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-137">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="d7bd6-138">在側邊欄中，選取 [ **Web] 和 [主控台**  >  **應用程式**]。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-138">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="d7bd6-139">如需 Blazor WebAssembly 體驗，請選擇\*\* Blazor WebAssembly 應用程式\*\*範本。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-139">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="d7bd6-140">如需 Blazor Server 體驗，請選擇\*\* Blazor Server 應用程式\*\*範本。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-140">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="d7bd6-141">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-141">Select **Next**.</span></span>

   <span data-ttu-id="d7bd6-142">如需這兩個裝載模型和的詳細資訊，請 Blazor *Blazor WebAssembly* *Blazor Server* 參閱 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-142">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="d7bd6-143">確認 [ **驗證** ] 設定為 [ **無驗證**]。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-143">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="d7bd6-144">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-144">Select **Next**.</span></span>

1. <span data-ttu-id="d7bd6-145">在 [ **專案名稱** ] 欄位中，為應用程式命名 `WebApplication1` 。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="d7bd6-146">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-146">Select **Create**.</span></span>

1. <span data-ttu-id="d7bd6-147">選取**Run**  >  [在不進行偵錯工具的**情況下**執行] 以執行應用程式*而不需偵錯工具*</span><span class="sxs-lookup"><span data-stu-id="d7bd6-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="d7bd6-148">使用 [**執行**  >  **開始調試**程式] 或 [執行 ( # A0) ] 按鈕執行應用程式，以*使用調試*程式來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="d7bd6-149">如果出現提示信任開發憑證，請信任憑證並繼續。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="d7bd6-150">需要使用者和 keychain 密碼才能信任憑證。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-150">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="d7bd6-151">如需信任 ASP.NET Core HTTPS 開發憑證的詳細資訊，請參閱 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> 。</span><span class="sxs-lookup"><span data-stu-id="d7bd6-151">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end
