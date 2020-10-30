---
title: 工具和下載-使用 ASP.NET Core 和 Azure DevOps
author: CamSoper
description: 使用 ASP.NET Core 和 Azure DevOps 所需的工具和下載。
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
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
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 8c7f10a6b6f8504efaba6054533aba034982820c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056565"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="a877c-103">工具及下載</span><span class="sxs-lookup"><span data-stu-id="a877c-103">Tools and downloads</span></span>

<span data-ttu-id="a877c-104">Azure 提供數種介面來布建和管理資源，例如 [Azure 入口網站](https://portal.azure.com)、 [Azure CLI](/cli/azure/)、 [Azure PowerShell](/powershell/azure/overview)、 [Azure Cloud Shell](https://shell.azure.com/bash)和 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="a877c-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="a877c-105">本指南採用極簡方法，並盡可能使用 Azure Cloud Shell，以減少所需的步驟。</span><span class="sxs-lookup"><span data-stu-id="a877c-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="a877c-106">不過，Azure 入口網站必須用於某些部分。</span><span class="sxs-lookup"><span data-stu-id="a877c-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a877c-107">必要條件</span><span class="sxs-lookup"><span data-stu-id="a877c-107">Prerequisites</span></span>

<span data-ttu-id="a877c-108">需要下列訂用帳戶：</span><span class="sxs-lookup"><span data-stu-id="a877c-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="a877c-109">Azure &mdash; 如果您沒有帳戶，請 [取得免費試用版](https://azure.microsoft.com/free/dotnet/)。</span><span class="sxs-lookup"><span data-stu-id="a877c-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/dotnet/).</span></span>
* <span data-ttu-id="a877c-110">Azure DevOps Services &mdash; 您的 Azure DevOps 訂用帳戶和組織會在第4章中建立。</span><span class="sxs-lookup"><span data-stu-id="a877c-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="a877c-111">GitHub &mdash; 如果您沒有帳戶，請 [免費註冊](https://github.com/join)。</span><span class="sxs-lookup"><span data-stu-id="a877c-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="a877c-112">需要下列工具：</span><span class="sxs-lookup"><span data-stu-id="a877c-112">The following tools are required:</span></span>

* <span data-ttu-id="a877c-113">[Git](https://git-scm.com/downloads) &mdash; 本指南建議您對 Git 有基本的瞭解。</span><span class="sxs-lookup"><span data-stu-id="a877c-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="a877c-114">請參閱 [git 檔](https://git-scm.com/doc)，特別是 [git remote](https://git-scm.com/docs/git-remote) 和 [git push](https://git-scm.com/docs/git-push)。</span><span class="sxs-lookup"><span data-stu-id="a877c-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="a877c-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; 需要版本2.1.300 或更新版本，才能建立並執行範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="a877c-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="a877c-116">如果 Visual Studio 是隨著 **.Net Core 跨平臺開發** 工作負載一起安裝，則已安裝 .NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="a877c-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="a877c-117">確認您的 .NET Core SDK 安裝。</span><span class="sxs-lookup"><span data-stu-id="a877c-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="a877c-118">開啟命令 shell，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="a877c-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="a877c-119">僅 (Windows 的建議工具) </span><span class="sxs-lookup"><span data-stu-id="a877c-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="a877c-120">[Visual Studio](https://visualstudio.microsoft.com)的強大 Azure 工具提供了一個 GUI，適用于本指南中所述的大部分功能。</span><span class="sxs-lookup"><span data-stu-id="a877c-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="a877c-121">任何版本的 Visual Studio 都可運作，包括免費的 Visual Studio Community 版本。</span><span class="sxs-lookup"><span data-stu-id="a877c-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="a877c-122">本教學課程的撰寫是為了示範開發、部署和 DevOps （不論是否有 Visual Studio）。</span><span class="sxs-lookup"><span data-stu-id="a877c-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="a877c-123">確認 Visual Studio 已安裝下列 [工作負載](/visualstudio/install/modify-visual-studio) ：</span><span class="sxs-lookup"><span data-stu-id="a877c-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="a877c-124">ASP.NET 和 Web 開發</span><span class="sxs-lookup"><span data-stu-id="a877c-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="a877c-125">Azure 開發</span><span class="sxs-lookup"><span data-stu-id="a877c-125">Azure development</span></span>
  * <span data-ttu-id="a877c-126">.NET Core 跨平台開發</span><span class="sxs-lookup"><span data-stu-id="a877c-126">.NET Core cross-platform development</span></span>
