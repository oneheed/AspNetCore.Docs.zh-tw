---
title: 裝載套件組合
author: rick-anderson
description: 瞭解如何設定 .NET Core 裝載套件組合。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: ecf3dd45575390eee263a275e7f1fb9ec50011bb
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058437"
---
# <a name="the-net-core-hosting-bundle"></a><span data-ttu-id="73e8b-103">.NET Core 裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="73e8b-103">The .NET Core Hosting Bundle</span></span>

<span data-ttu-id="73e8b-104">.NET Core 裝載套件組合是 .NET Core 執行時間和 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="73e8b-104">The .NET Core Hosting bundle is an installer for the .NET Core Runtime and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="73e8b-105">套件組合可讓 ASP.NET Core 的應用程式與 IIS 搭配執行。</span><span class="sxs-lookup"><span data-stu-id="73e8b-105">The bundle allows ASP.NET Core apps to run with IIS.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="73e8b-106">安裝 .NET Core 裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="73e8b-106">Install the .NET Core Hosting Bundle</span></span>

> [!IMPORTANT]
> <span data-ttu-id="73e8b-107">若裝載套件組合在 IIS 之前安裝，則必須對該套件組合安裝進行修復。</span><span class="sxs-lookup"><span data-stu-id="73e8b-107">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="73e8b-108">請在安裝 IIS 之後，再次執行裝載套件組合安裝程式。</span><span class="sxs-lookup"><span data-stu-id="73e8b-108">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="73e8b-109">如果在安裝 64 位元 (x64) 版本的 .NET Core 後才安裝裝載套件組合，那麼可能會遺漏 SDK ([未偵測到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="73e8b-109">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="73e8b-110">若要解決此問題，請參閱 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="73e8b-110">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="73e8b-111">直接下載 (目前版本)</span><span class="sxs-lookup"><span data-stu-id="73e8b-111">Direct download (current version)</span></span>

<span data-ttu-id="73e8b-112">使用下列連結下載安裝程式：</span><span class="sxs-lookup"><span data-stu-id="73e8b-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="73e8b-113">目前的 .NET Core 裝載套件組合安裝程式 (直接下載)</span><span class="sxs-lookup"><span data-stu-id="73e8b-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="73e8b-114">安裝程式的先前版本</span><span class="sxs-lookup"><span data-stu-id="73e8b-114">Earlier versions of the installer</span></span>

<span data-ttu-id="73e8b-115">若要取得安裝程式的先前版本：</span><span class="sxs-lookup"><span data-stu-id="73e8b-115">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="73e8b-116">流覽至 [ [下載 .Net Core](https://dotnet.microsoft.com/download/dotnet-core) ] 頁面。</span><span class="sxs-lookup"><span data-stu-id="73e8b-116">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="73e8b-117">選取所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="73e8b-117">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="73e8b-118">在 [執行應用程式 - 執行階段]  欄中，尋找想要的 .NET Core 執行階段版本列。</span><span class="sxs-lookup"><span data-stu-id="73e8b-118">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="73e8b-119">使用 **裝載** 套件組合連結來下載安裝程式。</span><span class="sxs-lookup"><span data-stu-id="73e8b-119">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="73e8b-120">某些安裝程式包含已達到期生命週期結束 (EOL) 的發行版本，這些發行版本已不受 Microsoft 支援。</span><span class="sxs-lookup"><span data-stu-id="73e8b-120">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="73e8b-121">如需詳細資訊，請參閱[支援原則](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) \(英文 \)。</span><span class="sxs-lookup"><span data-stu-id="73e8b-121">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="73e8b-122">安裝裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="73e8b-122">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="73e8b-123">在伺服器上執行安裝程式。</span><span class="sxs-lookup"><span data-stu-id="73e8b-123">Run the installer on the server.</span></span> <span data-ttu-id="73e8b-124">從系統管理員命令殼層執行安裝程式時，有 下列參數可用：</span><span class="sxs-lookup"><span data-stu-id="73e8b-124">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="73e8b-125">`OPT_NO_ANCM=1`：略過安裝 ASP.NET Core 模組。</span><span class="sxs-lookup"><span data-stu-id="73e8b-125">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="73e8b-126">`OPT_NO_RUNTIME=1`：略過安裝 .NET Core 執行時間。</span><span class="sxs-lookup"><span data-stu-id="73e8b-126">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="73e8b-127">當伺服器僅裝載 [獨立部署 (SCD) ](/dotnet/core/deploying/#self-contained-deployments-scd)時使用。</span><span class="sxs-lookup"><span data-stu-id="73e8b-127">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="73e8b-128">`OPT_NO_SHAREDFX=1`：略過安裝 ASP.NET 共用架構 (ASP.NET 執行時間) 。</span><span class="sxs-lookup"><span data-stu-id="73e8b-128">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="73e8b-129">當伺服器僅裝載 [獨立部署 (SCD) ](/dotnet/core/deploying/#self-contained-deployments-scd)時使用。</span><span class="sxs-lookup"><span data-stu-id="73e8b-129">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="73e8b-130">`OPT_NO_X86=1`：略過安裝 x86 執行時間。</span><span class="sxs-lookup"><span data-stu-id="73e8b-130">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="73e8b-131">當您確定不會裝載 32 位元應用程式時，請使用此參數。</span><span class="sxs-lookup"><span data-stu-id="73e8b-131">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="73e8b-132">如果將來有可能同時裝載 32 位元和 64 位元應用程式，請不要使用此參數並安裝這兩個執行階段。</span><span class="sxs-lookup"><span data-stu-id="73e8b-132">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="73e8b-133">`OPT_NO_SHARED_CONFIG_CHECK=1`：當共用設定 (`applicationHost.config`) 位於與 IIS 安裝相同的電腦上時，請停用 Iis 共用設定的檢查。</span><span class="sxs-lookup"><span data-stu-id="73e8b-133">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="73e8b-134">*只在 ASP.NET Core 2.2 或更新版本的裝載套件組合安裝程式上可用。*</span><span class="sxs-lookup"><span data-stu-id="73e8b-134">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="73e8b-135">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="73e8b-135">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="73e8b-136">重新開機系統，或在命令 shell 中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="73e8b-136">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="73e8b-137">重新啟動 IIS 將能偵測到由安裝程式對系統路徑 (此為環境變數) 所做出的變更。</span><span class="sxs-lookup"><span data-stu-id="73e8b-137">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="73e8b-138">ASP.NET Core 不會針對共用架構套件的修補程式版本採用向前復原行為。</span><span class="sxs-lookup"><span data-stu-id="73e8b-138">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="73e8b-139">藉由安裝新的裝載套件組合升級共用架構之後，請重新開機系統，或在命令 shell 中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="73e8b-139">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="73e8b-140">如需 IIS 共用組態的資訊，請參閱[使用 IIS 共用組態的 ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="73e8b-140">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="73e8b-141">模組版本和裝載套件組合安裝程式記錄檔</span><span class="sxs-lookup"><span data-stu-id="73e8b-141">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="73e8b-142">判斷已安裝的 ASP.NET Core 模組版本：</span><span class="sxs-lookup"><span data-stu-id="73e8b-142">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="73e8b-143">在主控系統上，流覽至 `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2` 。</span><span class="sxs-lookup"><span data-stu-id="73e8b-143">On the hosting system, navigate to `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span></span>
1. <span data-ttu-id="73e8b-144">找出檔案 `aspnetcorev2.dll` 。</span><span class="sxs-lookup"><span data-stu-id="73e8b-144">Locate the `aspnetcorev2.dll` file.</span></span>
1. <span data-ttu-id="73e8b-145">在該檔案上按一下滑鼠右鍵，然後從關聯式功能表中選取 [內容]  。</span><span class="sxs-lookup"><span data-stu-id="73e8b-145">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="73e8b-146">選取 [ **詳細資料** ] 索引標籤。檔案 **版本** 和 **產品版本** 代表已安裝的模組版本。</span><span class="sxs-lookup"><span data-stu-id="73e8b-146">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="73e8b-147">模組的裝載套件組合安裝程式記錄檔位於 `C:\Users\%UserName%\AppData\Local\Temp` 。</span><span class="sxs-lookup"><span data-stu-id="73e8b-147">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="73e8b-148">檔案的名稱 `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` ，其中預留位置是檔案的 `{TIMESTAMP}` 時間戳記。</span><span class="sxs-lookup"><span data-stu-id="73e8b-148">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp of the file.</span></span>
