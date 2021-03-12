---
title: 在使用 IIS 的 Windows 上裝載 ASP.NET Core
author: rick-anderson
description: 了解如何在 Windows Server Internet Information Services (IIS) 上裝載 ASP.NET Core 應用程式。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/index
ms.openlocfilehash: 13c4c2e2759355b10a4648b313f4dbed4b3aa482
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588954"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="5a379-103">在使用 IIS 的 Windows 上裝載 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5a379-103">Host ASP.NET Core on Windows with IIS</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="5a379-104">Internet Information Services (IIS) 是一種彈性、安全且可管理的 Web 服務器，可用於裝載 web 應用程式，包括 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="5a379-104">Internet Information Services (IIS) is a flexible, secure and manageable Web Server for hosting web apps, including ASP.NET Core.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="5a379-105">支援的平台</span><span class="sxs-lookup"><span data-stu-id="5a379-105">Supported platforms</span></span>

<span data-ttu-id="5a379-106">以下為支援的作業系統：</span><span class="sxs-lookup"><span data-stu-id="5a379-106">The following operating systems are supported:</span></span>

* <span data-ttu-id="5a379-107">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5a379-107">Windows 7 or later</span></span>
* <span data-ttu-id="5a379-108">Windows Server 2012 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5a379-108">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="5a379-109">支援針對 32 位元 (x86) 或 64 位元 (x64) 部署發行的應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-109">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="5a379-110">使用 32 位元 (x86) .NET Core SDK 部署 32 位元應用程式，除非應用程式：</span><span class="sxs-lookup"><span data-stu-id="5a379-110">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="5a379-111">需要提供給 64 位元應用程式使用的較大虛擬記憶體位址空間。</span><span class="sxs-lookup"><span data-stu-id="5a379-111">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="5a379-112">需要較大的 IIS 堆疊大小。</span><span class="sxs-lookup"><span data-stu-id="5a379-112">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="5a379-113">有 64 位元原生相依性。</span><span class="sxs-lookup"><span data-stu-id="5a379-113">Has 64-bit native dependencies.</span></span>

## <a name="install-the-aspnet-core-modulehosting-bundle"></a><span data-ttu-id="5a379-114">安裝 ASP.NET 核心模組/裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="5a379-114">Install the ASP.NET Core Module/Hosting Bundle</span></span>

<span data-ttu-id="5a379-115">使用下列連結下載安裝程式：</span><span class="sxs-lookup"><span data-stu-id="5a379-115">Download the installer using the following link:</span></span>

[<span data-ttu-id="5a379-116">目前的 .NET Core 裝載套件組合安裝程式 (直接下載)</span><span class="sxs-lookup"><span data-stu-id="5a379-116">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

<span data-ttu-id="5a379-117">如需有關如何安裝 ASP.NET Core 模組或安裝不同版本的詳細資訊，請參閱 [安裝 .Net Core 裝載](xref:host-and-deploy/iis/hosting-bundle)套件組合。</span><span class="sxs-lookup"><span data-stu-id="5a379-117">For more details instructions on how to install the ASP.NET Core Module, or installing different versions, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/hosting-bundle).</span></span>

## <a name="get-started"></a><span data-ttu-id="5a379-118">開始使用</span><span class="sxs-lookup"><span data-stu-id="5a379-118">Get started</span></span>

<span data-ttu-id="5a379-119">如需在 IIS 上裝載網站的入門指南，請參閱使用者入門 [指南](xref:tutorials/publish-to-iis)。</span><span class="sxs-lookup"><span data-stu-id="5a379-119">For getting started with hosting a website on IIS, see our [getting started guide](xref:tutorials/publish-to-iis).</span></span>

<span data-ttu-id="5a379-120">For getting started with hosting a website on Azure App Services, see our [deploying to Azure App Service guide](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="5a379-120">For getting started with hosting a website on Azure App Services, see our [deploying to Azure App Service guide](xref:host-and-deploy/azure-apps/index).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="5a379-121">IIS 系統管理員的部署資源</span><span class="sxs-lookup"><span data-stu-id="5a379-121">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="5a379-122">IIS 文件</span><span class="sxs-lookup"><span data-stu-id="5a379-122">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="5a379-123">IIS 中的 IIS 管理員使用者入門</span><span class="sxs-lookup"><span data-stu-id="5a379-123">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="5a379-124">.NET Core 應用程式部署</span><span class="sxs-lookup"><span data-stu-id="5a379-124">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="overlapped-recycle"></a><span data-ttu-id="5a379-125">重迭回收</span><span class="sxs-lookup"><span data-stu-id="5a379-125">Overlapped recycle</span></span>

<span data-ttu-id="5a379-126">一般來說，我們建議使用 [藍色-綠色部署](https://www.martinfowler.com/bliki/BlueGreenDeployment.html) 之類的模式來進行零停機部署。</span><span class="sxs-lookup"><span data-stu-id="5a379-126">In general, we recommend using a pattern like [blue-green deployments](https://www.martinfowler.com/bliki/BlueGreenDeployment.html) for zero-downtime deployments.</span></span> <span data-ttu-id="5a379-127">重迭回收說明之類的功能，但不保證您可以進行零停機的部署。</span><span class="sxs-lookup"><span data-stu-id="5a379-127">Features like Overlapped Recycle help, but don't guarantee that you can do a zero-downtime deployment.</span></span> <span data-ttu-id="5a379-128">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/aspnetcore/issues/10117)。</span><span class="sxs-lookup"><span data-stu-id="5a379-128">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/10117).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5a379-129">其他資源</span><span class="sxs-lookup"><span data-stu-id="5a379-129">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="5a379-130">Microsoft IIS 官方網站</span><span class="sxs-lookup"><span data-stu-id="5a379-130">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="5a379-131">Windows Server 技術內容庫</span><span class="sxs-lookup"><span data-stu-id="5a379-131">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="5a379-132">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="5a379-132">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="5a379-133">如需將 ASP.NET Core 應用程式發佈至 IIS 伺服器的教學課程體驗，請參閱<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="5a379-133">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="5a379-134">安裝 .NET Core 裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="5a379-134">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="5a379-135">支援的作業系統</span><span class="sxs-lookup"><span data-stu-id="5a379-135">Supported operating systems</span></span>

<span data-ttu-id="5a379-136">以下為支援的作業系統：</span><span class="sxs-lookup"><span data-stu-id="5a379-136">The following operating systems are supported:</span></span>

* <span data-ttu-id="5a379-137">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5a379-137">Windows 7 or later</span></span>
* <span data-ttu-id="5a379-138">Windows Server 2012 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5a379-138">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="5a379-139">[HTTP.sys 伺服器](xref:fundamentals/servers/httpsys) (先前稱為 WebListener) 不適用搭配 IIS 的反向 Proxy 設定。</span><span class="sxs-lookup"><span data-stu-id="5a379-139">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="5a379-140">請使用 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="5a379-140">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="5a379-141">如需在 Azure 中裝載的資訊，請參閱 <xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="5a379-141">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="5a379-142">如需疑難排解指引，請參閱 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="5a379-142">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="5a379-143">支援的平台</span><span class="sxs-lookup"><span data-stu-id="5a379-143">Supported platforms</span></span>

<span data-ttu-id="5a379-144">支援針對 32 位元 (x86) 或 64 位元 (x64) 部署發行的應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-144">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="5a379-145">使用 32 位元 (x86) .NET Core SDK 部署 32 位元應用程式，除非應用程式：</span><span class="sxs-lookup"><span data-stu-id="5a379-145">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="5a379-146">需要提供給 64 位元應用程式使用的較大虛擬記憶體位址空間。</span><span class="sxs-lookup"><span data-stu-id="5a379-146">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="5a379-147">需要較大的 IIS 堆疊大小。</span><span class="sxs-lookup"><span data-stu-id="5a379-147">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="5a379-148">有 64 位元原生相依性。</span><span class="sxs-lookup"><span data-stu-id="5a379-148">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="5a379-149">針對32位 (x86) 發佈的應用程式必須為其 IIS 應用程式集區啟用32位。</span><span class="sxs-lookup"><span data-stu-id="5a379-149">Apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="5a379-150">如需詳細資訊，請參閱 [建立 IIS 網站](#create-the-iis-site) 一節。</span><span class="sxs-lookup"><span data-stu-id="5a379-150">For more information, see the [Create the IIS site](#create-the-iis-site) section.</span></span>

<span data-ttu-id="5a379-151">使用 64 位元 (x64) .NET Core SDK 來發行 64 位元應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-151">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="5a379-152">主機系統上必須有 64 位元執行階段存在。</span><span class="sxs-lookup"><span data-stu-id="5a379-152">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="5a379-153">裝載模型</span><span class="sxs-lookup"><span data-stu-id="5a379-153">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="5a379-154">同處理序裝載模型</span><span class="sxs-lookup"><span data-stu-id="5a379-154">In-process hosting model</span></span>

<span data-ttu-id="5a379-155">使用同處理序裝載，ASP.NET Core 應用程式會在與其 IIS 工作者處理序相同的處理序中執行。</span><span class="sxs-lookup"><span data-stu-id="5a379-155">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="5a379-156">因為要求未透過回送介面卡 (將連出網路流量傳回同一部電腦的網路介面) 進行 proxy 處理，所以同處理序裝載會提供優於跨處理序裝載的效能。</span><span class="sxs-lookup"><span data-stu-id="5a379-156">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="5a379-157">IIS 透過 [Windows 處理序啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 來執行處理程序管理。</span><span class="sxs-lookup"><span data-stu-id="5a379-157">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="5a379-158">[ASP.NET 核心模組](xref:host-and-deploy/aspnet-core-module)：</span><span class="sxs-lookup"><span data-stu-id="5a379-158">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="5a379-159">執行應用程式初始化。</span><span class="sxs-lookup"><span data-stu-id="5a379-159">Performs app initialization.</span></span>
  * <span data-ttu-id="5a379-160">載入 [CoreCLR](/dotnet/standard/glossary#coreclr)。</span><span class="sxs-lookup"><span data-stu-id="5a379-160">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="5a379-161">呼叫 `Program.Main`。</span><span class="sxs-lookup"><span data-stu-id="5a379-161">Calls `Program.Main`.</span></span>
* <span data-ttu-id="5a379-162">處理 IIS 原生要求的存留期。</span><span class="sxs-lookup"><span data-stu-id="5a379-162">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="5a379-163">下圖說明 IIS、ASP.NET Core 模組和同處理序裝載應用程式之間的關聯性：</span><span class="sxs-lookup"><span data-stu-id="5a379-163">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![同處理序代管內的 ASP.NET Core 模組案例](index/_static/ancm-inprocess.png)

1. <span data-ttu-id="5a379-165">要求會從 Web 到達核心模式的 HTTP.sys 驅動程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-165">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="5a379-166">驅動程式會在網站設定的連接埠上將原生要求路由至 IIS，此連接埠通常是 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="5a379-166">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="5a379-167">ASP.NET 核心模組會接收原生要求，並將它傳遞給 IIS HTTP 伺服器 (`IISHttpServer`) 。</span><span class="sxs-lookup"><span data-stu-id="5a379-167">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="5a379-168">IIS HTTP 伺服器是 IIS 的同處理序伺服程式實作，可將要求從原生轉換為受控。</span><span class="sxs-lookup"><span data-stu-id="5a379-168">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="5a379-169">IIS HTTP 伺服器處理要求之後：</span><span class="sxs-lookup"><span data-stu-id="5a379-169">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="5a379-170">要求會傳送至 ASP.NET Core 中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="5a379-170">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="5a379-171">中介軟體管線會處理要求，並將其作為 `HttpContext` 執行個體傳遞至應用程式的邏輯。</span><span class="sxs-lookup"><span data-stu-id="5a379-171">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="5a379-172">應用程式的回應會透過 IIS HTTP 伺服器傳回 IIS。</span><span class="sxs-lookup"><span data-stu-id="5a379-172">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="5a379-173">IIS 會將回應傳送到起始該要求的用戶端。</span><span class="sxs-lookup"><span data-stu-id="5a379-173">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="5a379-174">內含式裝載可加入現有的應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-174">In-process hosting is opt-in for existing apps.</span></span> <span data-ttu-id="5a379-175">ASP.NET Core web 範本會使用同進程裝載模型。</span><span class="sxs-lookup"><span data-stu-id="5a379-175">The ASP.NET Core web templates use the in-process hosting model.</span></span>

<span data-ttu-id="5a379-176">`CreateDefaultBuilder` 藉 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 由呼叫方法來加入實例， <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> 以啟動 [CoreCLR](/dotnet/standard/glossary#coreclr) ，並將應用程式裝載在 IIS 工作者進程內 (`w3wp.exe` 或 `iisexpress.exe`) 。</span><span class="sxs-lookup"><span data-stu-id="5a379-176">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (`w3wp.exe` or `iisexpress.exe`).</span></span> <span data-ttu-id="5a379-177">效能測試指出，相較於將 .NET Core 應用程式裝載於處理序外，並將要求 Proxy 處理至 [Kestrel](xref:fundamentals/servers/kestrel)，裝載於處理序內可提供明顯更高的要求輸送量。</span><span class="sxs-lookup"><span data-stu-id="5a379-177">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="5a379-178">發佈為單一檔案可執行檔的應用程式無法由同處理序裝載模型載入。</span><span class="sxs-lookup"><span data-stu-id="5a379-178">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="5a379-179">跨處理序裝載模型</span><span class="sxs-lookup"><span data-stu-id="5a379-179">Out-of-process hosting model</span></span>

<span data-ttu-id="5a379-180">因為 ASP.NET Core 應用程式會在與 IIS 背景工作進程不同的進程中執行，所以 ASP.NET Core 模組會處理進程管理。</span><span class="sxs-lookup"><span data-stu-id="5a379-180">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="5a379-181">此模組會在第一個要求到達時啟動 ASP.NET Core 應用程式的處理序，並在應用程式關閉或損毀時將它重新啟動。</span><span class="sxs-lookup"><span data-stu-id="5a379-181">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="5a379-182">此行為基本上與執行同處理序，並由 [Windows 處理序啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 所管理的應用程式相同。</span><span class="sxs-lookup"><span data-stu-id="5a379-182">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="5a379-183">下圖說明 IIS、ASP.NET Core 模組和跨處理序裝載應用程式之間的關聯性：</span><span class="sxs-lookup"><span data-stu-id="5a379-183">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![非同處理序代管內的 ASP.NET Core 模組案例](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="5a379-185">要求會從 Web 到達核心模式的 HTTP.sys 驅動程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-185">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="5a379-186">驅動程式會在網站設定的埠上將要求路由傳送至 IIS。</span><span class="sxs-lookup"><span data-stu-id="5a379-186">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="5a379-187">設定的埠通常是 80 (HTTP) 或 443 (HTTPS) 。</span><span class="sxs-lookup"><span data-stu-id="5a379-187">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="5a379-188">模組會將要求轉送到應用程式的隨機埠上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="5a379-188">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="5a379-189">隨機埠不是80或443。</span><span class="sxs-lookup"><span data-stu-id="5a379-189">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="5a379-190">ASP.NET Core 模組會在啟動時透過環境變數指定埠。</span><span class="sxs-lookup"><span data-stu-id="5a379-190">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="5a379-191">延伸模組會設定 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> 要接聽的伺服器 `http://localhost:{PORT}` 。</span><span class="sxs-lookup"><span data-stu-id="5a379-191">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="5a379-192">將會執行額外檢查，不是源自模組的要求都會遭到拒絕。</span><span class="sxs-lookup"><span data-stu-id="5a379-192">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="5a379-193">模組不支援 HTTPS 轉送。</span><span class="sxs-lookup"><span data-stu-id="5a379-193">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="5a379-194">即使 IIS 透過 HTTPS 接收，要求還是會透過 HTTP 轉送。</span><span class="sxs-lookup"><span data-stu-id="5a379-194">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="5a379-195">在 Kestrel 收取來自模組的要求之後，會將要求轉送到 ASP.NET Core 中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="5a379-195">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="5a379-196">中介軟體管線會處理要求，並將其作為 `HttpContext` 執行個體傳遞至應用程式的邏輯。</span><span class="sxs-lookup"><span data-stu-id="5a379-196">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="5a379-197">IIS Integration 新增的中介軟體會更新配置、遠端 IP 和帳戶路徑基底，以將要求轉送至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="5a379-197">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="5a379-198">應用程式的回應會傳回 IIS，然後將其轉送回起始要求的 HTTP 用戶端。</span><span class="sxs-lookup"><span data-stu-id="5a379-198">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="5a379-199">如需 ASP.NET Core 模組組態指南，請參閱 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="5a379-199">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="5a379-200">如需代管的詳細資訊，請參閱[在 ASP.NET Core 中代管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="5a379-200">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="5a379-201">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="5a379-201">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="5a379-202">啟用 IISIntegration 元件</span><span class="sxs-lookup"><span data-stu-id="5a379-202">Enable the IISIntegration components</span></span>

<span data-ttu-id="5a379-203">在 () 中建立主機時 `CreateHostBuilder` `Program.cs` ，請呼叫 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 來啟用 IIS 整合：</span><span class="sxs-lookup"><span data-stu-id="5a379-203">When building a host in `CreateHostBuilder` (`Program.cs`), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="5a379-204">如需 `CreateDefaultBuilder` 的詳細資訊，請參閱 <xref:fundamentals/host/generic-host#default-builder-settings>。</span><span class="sxs-lookup"><span data-stu-id="5a379-204">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="5a379-205">IIS 選項</span><span class="sxs-lookup"><span data-stu-id="5a379-205">IIS options</span></span>

<span data-ttu-id="5a379-206">**同處理序主控模型**</span><span class="sxs-lookup"><span data-stu-id="5a379-206">**In-process hosting model**</span></span>

<span data-ttu-id="5a379-207">若要設定 IIS 伺服器選項，請在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> 中加入 <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 的服務設定。</span><span class="sxs-lookup"><span data-stu-id="5a379-207">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="5a379-208">下列範例會停用 AutomaticAuthentication：</span><span class="sxs-lookup"><span data-stu-id="5a379-208">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="5a379-209">選項</span><span class="sxs-lookup"><span data-stu-id="5a379-209">Option</span></span>                         | <span data-ttu-id="5a379-210">預設</span><span class="sxs-lookup"><span data-stu-id="5a379-210">Default</span></span> | <span data-ttu-id="5a379-211">設定</span><span class="sxs-lookup"><span data-stu-id="5a379-211">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="5a379-212">若為 `true`，IIS 伺服器會設定由 [Windows 驗證](xref:security/authentication/windowsauth)所驗證的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="5a379-212">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="5a379-213">若為 `false`，則伺服器僅會對 `HttpContext.User` 提供身分識別，並在 `AuthenticationScheme` 明確要求時回應挑戰。</span><span class="sxs-lookup"><span data-stu-id="5a379-213">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="5a379-214">必須在 IIS 中啟用 Windows 驗證以讓 `AutomaticAuthentication` 作用。</span><span class="sxs-lookup"><span data-stu-id="5a379-214">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="5a379-215">如需詳細資訊，請參閱 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="5a379-215">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="5a379-216">設定使用者在登入頁面上看到的顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="5a379-216">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO`           | `false` | <span data-ttu-id="5a379-217">和是否允許同步 i/o `HttpContext.Request` `HttpContext.Response` 。</span><span class="sxs-lookup"><span data-stu-id="5a379-217">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize`           | `30000000`  | <span data-ttu-id="5a379-218">取得或設定 `HttpRequest` 的要求本文大小上限。</span><span class="sxs-lookup"><span data-stu-id="5a379-218">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="5a379-219">請注意，IIS 本身具有限制 `maxAllowedContentLength`，此限制將在 `IISServerOptions` 中設定 `MaxRequestBodySize` 時處理。</span><span class="sxs-lookup"><span data-stu-id="5a379-219">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="5a379-220">變更 `MaxRequestBodySize` 將不會影響 `maxAllowedContentLength`。</span><span class="sxs-lookup"><span data-stu-id="5a379-220">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="5a379-221">若要增加 `maxAllowedContentLength` ，請在中加入專案， `web.config` 以將設定 `maxAllowedContentLength` 為較高的值。</span><span class="sxs-lookup"><span data-stu-id="5a379-221">To increase `maxAllowedContentLength`, add an entry in the `web.config` to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="5a379-222">如需更多詳細資料，請參閱[組態](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)。</span><span class="sxs-lookup"><span data-stu-id="5a379-222">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="5a379-223">**跨處理序裝載模型**</span><span class="sxs-lookup"><span data-stu-id="5a379-223">**Out-of-process hosting model**</span></span>

<span data-ttu-id="5a379-224">若要設定 IIS 選項，請在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> 中加入 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服務設定。</span><span class="sxs-lookup"><span data-stu-id="5a379-224">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="5a379-225">下列範例會防止應用程式填入 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="5a379-225">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="5a379-226">選項</span><span class="sxs-lookup"><span data-stu-id="5a379-226">Option</span></span>                         | <span data-ttu-id="5a379-227">預設</span><span class="sxs-lookup"><span data-stu-id="5a379-227">Default</span></span> | <span data-ttu-id="5a379-228">設定</span><span class="sxs-lookup"><span data-stu-id="5a379-228">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="5a379-229">若為 `true`，[IIS 整合中介軟體](#enable-the-iisintegration-components)會設定由 [Windows 驗證](xref:security/authentication/windowsauth)所驗證的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="5a379-229">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="5a379-230">如果為 `false`，則驗證中介軟體僅針對 `HttpContext.User` 提供身分識別，並在游 `AuthenticationScheme` 提出明確要求時回應挑戰。</span><span class="sxs-lookup"><span data-stu-id="5a379-230">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="5a379-231">必須在 IIS 中啟用 Windows 驗證以讓 `AutomaticAuthentication` 作用。</span><span class="sxs-lookup"><span data-stu-id="5a379-231">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="5a379-232">如需詳細資訊，請參閱 [Windows 驗證](xref:security/authentication/windowsauth)主題。</span><span class="sxs-lookup"><span data-stu-id="5a379-232">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="5a379-233">設定使用者在登入頁面上看到的顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="5a379-233">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="5a379-234">如果為 `true` 且 `MS-ASPNETCORE-CLIENTCERT` 要求標頭已存在，則會填入 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="5a379-234">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="5a379-235">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="5a379-235">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="5a379-236">[IIS 整合中介軟體](#enable-the-iisintegration-components)和 ASP.NET 核心模組設定為轉寄：</span><span class="sxs-lookup"><span data-stu-id="5a379-236">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="5a379-237"> (HTTP/HTTPS) 的配置。</span><span class="sxs-lookup"><span data-stu-id="5a379-237">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="5a379-238">發出要求的遠端 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="5a379-238">Remote IP address where the request originated.</span></span>

<span data-ttu-id="5a379-239">[IIS 整合中介軟體](#enable-the-iisintegration-components)會設定轉送的標頭中介軟體。</span><span class="sxs-lookup"><span data-stu-id="5a379-239">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="5a379-240">其他 Proxy 伺服器和負載平衡器後方託管的應用程式可能需要其他設定。</span><span class="sxs-lookup"><span data-stu-id="5a379-240">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="5a379-241">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="5a379-241">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="5a379-242">`web.config` 檔案</span><span class="sxs-lookup"><span data-stu-id="5a379-242">`web.config` file</span></span>

<span data-ttu-id="5a379-243">檔案會設定 `web.config` [ASP.NET 核心模組](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="5a379-243">The `web.config` file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="5a379-244">建立、轉換及發佈檔案 `web.config` 是由 MSBuild 目標 (`_TransformWebConfig`) 在發行專案時處理。</span><span class="sxs-lookup"><span data-stu-id="5a379-244">Creating, transforming, and publishing the `web.config` file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="5a379-245">此目標存在於 Web SDK 目標 (`Microsoft.NET.Sdk.Web`)。</span><span class="sxs-lookup"><span data-stu-id="5a379-245">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="5a379-246">SDK 設定在專案檔的頂端：</span><span class="sxs-lookup"><span data-stu-id="5a379-246">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="5a379-247">如果檔案 `web.config` 不存在於專案中，則會以正確的建立檔案， `processPath` 並 `arguments` 設定 ASP.NET 核心模組並移至 [已發佈的輸出](xref:host-and-deploy/directory-structure)。</span><span class="sxs-lookup"><span data-stu-id="5a379-247">If a `web.config` file isn't present in the project, the file is created with the correct `processPath` and `arguments` to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="5a379-248">如果 `web.config` 專案中有檔案，則檔案會以正確的轉換， `processPath` 並 `arguments` 設定 ASP.NET 核心模組並移至已發佈的輸出。</span><span class="sxs-lookup"><span data-stu-id="5a379-248">If a `web.config` file is present in the project, the file is transformed with the correct `processPath` and `arguments` to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="5a379-249">轉換不會修改檔案中的 IIS 組態設定。</span><span class="sxs-lookup"><span data-stu-id="5a379-249">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="5a379-250">檔案 `web.config` 可能會提供控制作用中 IIS 模組的其他 IIS 設定。</span><span class="sxs-lookup"><span data-stu-id="5a379-250">The `web.config` file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="5a379-251">如需能處理 ASP.NET Core 應用程式要求之 IIS 模組的相關資訊，請參閱 [IIS 模組](xref:host-and-deploy/iis/modules)主題。</span><span class="sxs-lookup"><span data-stu-id="5a379-251">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="5a379-252">若要防止 Web SDK 轉換檔案 `web.config` ，請使用專案檔 `<IsTransformWebConfigDisabled>` 中的屬性：</span><span class="sxs-lookup"><span data-stu-id="5a379-252">To prevent the Web SDK from transforming the `web.config` file, use the `<IsTransformWebConfigDisabled>` property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="5a379-253">停用 Web SDK 以轉換檔案時， `processPath` `arguments` 必須由開發人員手動設定和。</span><span class="sxs-lookup"><span data-stu-id="5a379-253">When disabling the Web SDK from transforming the file, the `processPath` and `arguments` should be manually set by the developer.</span></span> <span data-ttu-id="5a379-254">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="5a379-254">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="5a379-255">`web.config` 檔案位置</span><span class="sxs-lookup"><span data-stu-id="5a379-255">`web.config` file location</span></span>

<span data-ttu-id="5a379-256">為了正確設定 [ASP.NET 核心模組](xref:host-and-deploy/aspnet-core-module) ，檔案 `web.config` 必須存在於 [內容根](xref:fundamentals/index#content-root) 路徑中， (通常是已部署應用程式的應用程式基底路徑) 。</span><span class="sxs-lookup"><span data-stu-id="5a379-256">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the `web.config` file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="5a379-257">這是與提供給 IIS 的網站實體路徑相同的位置。</span><span class="sxs-lookup"><span data-stu-id="5a379-257">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="5a379-258">`web.config`應用程式的根目錄需要檔案，才能使用 Web Deploy 來發佈多個應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-258">The `web.config` file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="5a379-259">機密檔案存在於應用程式的實體路徑上，例如 `{ASSEMBLY}.runtimeconfig.json` `{ASSEMBLY}.xml` (XML 檔批註) 和 `{ASSEMBLY}.deps.json` ，其中預留位置 `{ASSEMBLY}` 是元件名稱。</span><span class="sxs-lookup"><span data-stu-id="5a379-259">Sensitive files exist on the app's physical path, such as `{ASSEMBLY}.runtimeconfig.json`, `{ASSEMBLY}.xml` (XML Documentation comments), and `{ASSEMBLY}.deps.json`, where the placeholder `{ASSEMBLY}` is the assembly name.</span></span> <span data-ttu-id="5a379-260">當檔案 `web.config` 存在且網站正常啟動時，如果要求這些機密檔案，IIS 就不會提供這些機密檔案的服務。</span><span class="sxs-lookup"><span data-stu-id="5a379-260">When the `web.config` file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="5a379-261">如果檔案 `web.config` 遺失、未正確命名，或無法設定網站以進行正常啟動，IIS 可能會公開提供機密檔案。</span><span class="sxs-lookup"><span data-stu-id="5a379-261">If the `web.config` file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="5a379-262">**檔案 `web.config` 必須存在於部署中的任何時間、正確命名，而且能夠設定網站以進行正常啟動。請勿 `web.config` 從生產環境部署中移除該檔案。**</span><span class="sxs-lookup"><span data-stu-id="5a379-262">**The `web.config` file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the `web.config` file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="5a379-263">轉換 web.config</span><span class="sxs-lookup"><span data-stu-id="5a379-263">Transform web.config</span></span>

<span data-ttu-id="5a379-264">如果您需要 `web.config` 在發佈時進行轉換，請參閱 <xref:host-and-deploy/iis/transform-webconfig> 。</span><span class="sxs-lookup"><span data-stu-id="5a379-264">If you need to transform `web.config` on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="5a379-265">您可能需要在 [發行] 上進行轉換， `web.config` 以根據設定、設定檔或環境設定環境變數。</span><span class="sxs-lookup"><span data-stu-id="5a379-265">You might need to transform `web.config` on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="5a379-266">IIS 設定</span><span class="sxs-lookup"><span data-stu-id="5a379-266">IIS configuration</span></span>

<span data-ttu-id="5a379-267">**Windows Server 作業系統**</span><span class="sxs-lookup"><span data-stu-id="5a379-267">**Windows Server operating systems**</span></span>

<span data-ttu-id="5a379-268">啟用 **網頁伺服器 (IIS)** 伺服器角色，並建立角色服務。</span><span class="sxs-lookup"><span data-stu-id="5a379-268">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="5a379-269">使用來自 [管理] 功能表的 [新增角色及功能] 精靈，或是 [伺服器管理員] 中的連結。</span><span class="sxs-lookup"><span data-stu-id="5a379-269">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="5a379-270">在 **伺服器角色** 步驟中，核取 [網頁伺服器 (IIS)] 方塊。</span><span class="sxs-lookup"><span data-stu-id="5a379-270">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在選取伺服器角色步驟中選取網頁伺服器 IIS 角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="5a379-272">在 [功能] 步驟之後，[角色服務] 步驟會針對網頁伺服器 (IIS) 進行載入。</span><span class="sxs-lookup"><span data-stu-id="5a379-272">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="5a379-273">選取所需的 IIS 角色服務或接受所提供的預設角色服務。</span><span class="sxs-lookup"><span data-stu-id="5a379-273">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在選取角色服務步驟中，選取預設的角色服務。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="5a379-275">**Windows 驗證 (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5a379-275">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="5a379-276">若要啟用 Windows 驗證，請展開下列節點：**網頁伺服器**  >  **安全性**。</span><span class="sxs-lookup"><span data-stu-id="5a379-276">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="5a379-277">選取 [Windows 驗證] 功能。</span><span class="sxs-lookup"><span data-stu-id="5a379-277">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="5a379-278">如需詳細資訊，請參閱[Windows 驗證 `<windowsAuthentication>` ](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)和[設定 windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="5a379-278">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="5a379-279">**WebSocket (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5a379-279">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="5a379-280">WebSocket 由 ASP.NET Core 1.1 或更新版本所支援。</span><span class="sxs-lookup"><span data-stu-id="5a379-280">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="5a379-281">若要啟用 websocket，請展開下列節點：**網頁伺服器**  >  **應用程式開發**。</span><span class="sxs-lookup"><span data-stu-id="5a379-281">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="5a379-282">選取 [WebSocket 通訊協定] 功能。</span><span class="sxs-lookup"><span data-stu-id="5a379-282">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="5a379-283">如需詳細資訊，請參閱 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="5a379-283">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="5a379-284">透過 **確認** 步驟繼續作業，安裝網頁伺服器角色和服務。</span><span class="sxs-lookup"><span data-stu-id="5a379-284">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="5a379-285">在安裝 **Web 服務器 (iis)** 角色之後，不需要重新開機伺服器。</span><span class="sxs-lookup"><span data-stu-id="5a379-285">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="5a379-286">**Windows 桌面作業系統**</span><span class="sxs-lookup"><span data-stu-id="5a379-286">**Windows desktop operating systems**</span></span>

<span data-ttu-id="5a379-287">啟用 [IIS 管理主控台] 和 [World Wide Web 服務]。</span><span class="sxs-lookup"><span data-stu-id="5a379-287">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="5a379-288">瀏覽到 [控制台]**[程式]** > **[程式和功能]** >  > **[開啟或關閉 Windows 功能]** \(畫面左側\)。</span><span class="sxs-lookup"><span data-stu-id="5a379-288">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="5a379-289">開啟 [Internet Information Services] 節點。</span><span class="sxs-lookup"><span data-stu-id="5a379-289">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="5a379-290">開啟 [Web 管理工具] 節點。</span><span class="sxs-lookup"><span data-stu-id="5a379-290">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="5a379-291">核取 [IIS 管理主控台] 方塊。</span><span class="sxs-lookup"><span data-stu-id="5a379-291">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="5a379-292">[World Wide Web Services] (全球資訊網服務) 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="5a379-292">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="5a379-293">接受 **全球資訊網服務** 的預設功能，或自訂 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="5a379-293">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="5a379-294">**Windows 驗證 (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5a379-294">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="5a379-295">若要啟用 Windows 驗證，請展開下列節點： **World Wide Web 服務**  >  **安全性**。</span><span class="sxs-lookup"><span data-stu-id="5a379-295">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="5a379-296">選取 [Windows 驗證] 功能。</span><span class="sxs-lookup"><span data-stu-id="5a379-296">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="5a379-297">如需詳細資訊，請參閱[Windows 驗證 \<windowsAuthentication> ](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)和[設定 windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="5a379-297">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="5a379-298">**WebSocket (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5a379-298">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="5a379-299">WebSocket 由 ASP.NET Core 1.1 或更新版本所支援。</span><span class="sxs-lookup"><span data-stu-id="5a379-299">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="5a379-300">若要啟用 websocket，請展開下列節點： **World Wide Web 服務**  >  **應用程式開發功能**。</span><span class="sxs-lookup"><span data-stu-id="5a379-300">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="5a379-301">選取 [WebSocket 通訊協定] 功能。</span><span class="sxs-lookup"><span data-stu-id="5a379-301">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="5a379-302">如需詳細資訊，請參閱 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="5a379-302">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="5a379-303">若 IIS 安裝需要重新啟動，請重新啟動系統。</span><span class="sxs-lookup"><span data-stu-id="5a379-303">If the IIS installation requires a restart, restart the system.</span></span>

![選取 [Windows 功能] 中的 [IIS 管理主控台] 和 [World Wide Web Services] (全球資訊網服務)。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="5a379-305">安裝 .NET Core 裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="5a379-305">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="5a379-306">在主控系統上安裝 .NET Core 裝載套件組合。</span><span class="sxs-lookup"><span data-stu-id="5a379-306">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="5a379-307">套件組合會安裝 .NET Core 執行時間、.NET Core 程式庫和 [ASP.NET 核心模組](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="5a379-307">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="5a379-308">此模組可讓 ASP.NET Core 應用程式在 IIS 背後執行。</span><span class="sxs-lookup"><span data-stu-id="5a379-308">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5a379-309">若裝載套件組合在 IIS 之前安裝，則必須對該套件組合安裝進行修復。</span><span class="sxs-lookup"><span data-stu-id="5a379-309">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="5a379-310">請在安裝 IIS 之後，再次執行裝載套件組合安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-310">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="5a379-311">如果在安裝 64 位元 (x64) 版本的 .NET Core 後才安裝裝載套件組合，那麼可能會遺漏 SDK ([未偵測到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="5a379-311">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="5a379-312">若要解決此問題，請參閱 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="5a379-312">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="5a379-313">直接下載 (目前版本)</span><span class="sxs-lookup"><span data-stu-id="5a379-313">Direct download (current version)</span></span>

<span data-ttu-id="5a379-314">使用下列連結下載安裝程式：</span><span class="sxs-lookup"><span data-stu-id="5a379-314">Download the installer using the following link:</span></span>

[<span data-ttu-id="5a379-315">目前的 .NET Core 裝載套件組合安裝程式 (直接下載)</span><span class="sxs-lookup"><span data-stu-id="5a379-315">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="5a379-316">安裝程式的先前版本</span><span class="sxs-lookup"><span data-stu-id="5a379-316">Earlier versions of the installer</span></span>

<span data-ttu-id="5a379-317">若要取得安裝程式的先前版本：</span><span class="sxs-lookup"><span data-stu-id="5a379-317">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="5a379-318">流覽至 [ [下載 .Net Core](https://dotnet.microsoft.com/download/dotnet-core) ] 頁面。</span><span class="sxs-lookup"><span data-stu-id="5a379-318">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="5a379-319">選取所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="5a379-319">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="5a379-320">在 [執行應用程式 - 執行階段] 欄中，尋找想要的 .NET Core 執行階段版本列。</span><span class="sxs-lookup"><span data-stu-id="5a379-320">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="5a379-321">使用 **裝載** 套件組合連結來下載安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-321">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="5a379-322">某些安裝程式包含已達到期生命週期結束 (EOL) 的發行版本，這些發行版本已不受 Microsoft 支援。</span><span class="sxs-lookup"><span data-stu-id="5a379-322">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="5a379-323">如需詳細資訊，請參閱[支援原則](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) \(英文 \)。</span><span class="sxs-lookup"><span data-stu-id="5a379-323">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="5a379-324">安裝裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="5a379-324">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="5a379-325">在伺服器上執行安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-325">Run the installer on the server.</span></span> <span data-ttu-id="5a379-326">從系統管理員命令殼層執行安裝程式時，有 下列參數可用：</span><span class="sxs-lookup"><span data-stu-id="5a379-326">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="5a379-327">`OPT_NO_ANCM=1`：略過安裝 ASP.NET Core 模組。</span><span class="sxs-lookup"><span data-stu-id="5a379-327">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="5a379-328">`OPT_NO_RUNTIME=1`：略過安裝 .NET Core 執行時間。</span><span class="sxs-lookup"><span data-stu-id="5a379-328">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="5a379-329">當伺服器僅裝載 [獨立部署 (SCD) ](/dotnet/core/deploying/#self-contained-deployments-scd)時使用。</span><span class="sxs-lookup"><span data-stu-id="5a379-329">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="5a379-330">`OPT_NO_SHAREDFX=1`：略過安裝 ASP.NET 共用架構 (ASP.NET 執行時間) 。</span><span class="sxs-lookup"><span data-stu-id="5a379-330">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="5a379-331">當伺服器僅裝載 [獨立部署 (SCD) ](/dotnet/core/deploying/#self-contained-deployments-scd)時使用。</span><span class="sxs-lookup"><span data-stu-id="5a379-331">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="5a379-332">`OPT_NO_X86=1`：略過安裝 x86 執行時間。</span><span class="sxs-lookup"><span data-stu-id="5a379-332">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="5a379-333">當您確定不會裝載 32 位元應用程式時，請使用此參數。</span><span class="sxs-lookup"><span data-stu-id="5a379-333">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="5a379-334">如果將來有可能同時裝載 32 位元和 64 位元應用程式，請不要使用此參數並安裝這兩個執行階段。</span><span class="sxs-lookup"><span data-stu-id="5a379-334">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="5a379-335">`OPT_NO_SHARED_CONFIG_CHECK=1`：當共用設定 (`applicationHost.config`) 位於與 IIS 安裝相同的電腦上時，請停用 Iis 共用設定的檢查。</span><span class="sxs-lookup"><span data-stu-id="5a379-335">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="5a379-336">*只在 ASP.NET Core 2.2 或更新版本的裝載套件組合安裝程式上可用。*</span><span class="sxs-lookup"><span data-stu-id="5a379-336">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="5a379-337">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="5a379-337">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="5a379-338">重新開機系統，或在命令 shell 中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="5a379-338">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="5a379-339">重新啟動 IIS 將能偵測到由安裝程式對系統路徑 (此為環境變數) 所做出的變更。</span><span class="sxs-lookup"><span data-stu-id="5a379-339">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="5a379-340">ASP.NET Core 不會針對共用架構套件的修補程式版本採用向前復原行為。</span><span class="sxs-lookup"><span data-stu-id="5a379-340">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="5a379-341">藉由安裝新的裝載套件組合升級共用架構之後，請重新開機系統，或在命令 shell 中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="5a379-341">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="5a379-342">如需 IIS 共用組態的資訊，請參閱[使用 IIS 共用組態的 ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="5a379-342">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="5a379-343">使用 Visual Studio 發佈時安裝 Web Deploy</span><span class="sxs-lookup"><span data-stu-id="5a379-343">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="5a379-344">將應用程式部署到具有 [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later) 的伺服器時，請在伺服器上安裝最新版的 Web Deploy。</span><span class="sxs-lookup"><span data-stu-id="5a379-344">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="5a379-345">若要安裝 Web Deploy，請使用 [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=43717)直接取得安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-345">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="5a379-346">慣用的方法是使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="5a379-346">The preferred method is to use WebPI.</span></span> <span data-ttu-id="5a379-347">WebPI 提供獨立的安裝程式和組態以裝載提供者。</span><span class="sxs-lookup"><span data-stu-id="5a379-347">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="5a379-348">建立 IIS 網站</span><span class="sxs-lookup"><span data-stu-id="5a379-348">Create the IIS site</span></span>

1. <span data-ttu-id="5a379-349">在主控系統上，請建立資料夾以容納應用程式的已發行資料夾和檔案。</span><span class="sxs-lookup"><span data-stu-id="5a379-349">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="5a379-350">在下列步驟中，您提供資料夾路徑給 IIS，作為應用程式的實體路徑。</span><span class="sxs-lookup"><span data-stu-id="5a379-350">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="5a379-351">如需應用程式之部署資料夾和檔案配置的詳細資訊，請參閱 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="5a379-351">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="5a379-352">在 [IIS 管理員] 中 **，在 [連線] 面板中** 開啟伺服器的節點。</span><span class="sxs-lookup"><span data-stu-id="5a379-352">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="5a379-353">以滑鼠右鍵按一下 [網站] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5a379-353">Right-click the **Sites** folder.</span></span> <span data-ttu-id="5a379-354">從操作功能表選取 [新增網站]。</span><span class="sxs-lookup"><span data-stu-id="5a379-354">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="5a379-355">提供 **網站名稱**，並將 **實體路徑** 設定為應用程式的部署資料夾。</span><span class="sxs-lookup"><span data-stu-id="5a379-355">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="5a379-356">提供系結 **設定，並** 選取 **[確定]** 以建立網站：</span><span class="sxs-lookup"><span data-stu-id="5a379-356">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在新增網站步驟中提供站台名稱、實體路徑和主機名稱。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="5a379-358">請 **勿** 使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="5a379-358">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="5a379-359">最上層萬用字元繫結可能暴露您的應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="5a379-359">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="5a379-360">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="5a379-360">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="5a379-361">請使用明確主機名稱，而非萬用字元。</span><span class="sxs-lookup"><span data-stu-id="5a379-361">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="5a379-362">若您擁有整個父網域 (與具弱點的 `*.com` 相對) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 就沒有此安全性風險。</span><span class="sxs-lookup"><span data-stu-id="5a379-362">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="5a379-363">如需詳細資訊，請參閱 [rfc7230 5.4 節](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="5a379-363">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="5a379-364">在伺服器的節點之下，選取 [應用程式集區]。</span><span class="sxs-lookup"><span data-stu-id="5a379-364">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="5a379-365">以滑鼠右鍵按一下網站的應用程式集區，然後從操作功能表選取 [基本設定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-365">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="5a379-366">在 [編輯應用程式集區] 視窗中，將 [.NET CLR 版本] 設定為 [沒有受控碼]：</span><span class="sxs-lookup"><span data-stu-id="5a379-366">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![將 .NET CLR 版本設為 [沒有受控碼]。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="5a379-368">ASP.NET Core 會在不同的處理序中執行，並管理執行階段。</span><span class="sxs-lookup"><span data-stu-id="5a379-368">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="5a379-369">ASP.NET Core 不依賴載入桌面 CLR ( .NET CLR) 。</span><span class="sxs-lookup"><span data-stu-id="5a379-369">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR).</span></span> <span data-ttu-id="5a379-370">.NET Core 的核心 Common Language Runtime (CoreCLR) 會開機，以在背景工作進程中裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-370">The Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="5a379-371">將 [.NET CLR 版本] 設定為 [沒有受控碼] 是選擇性的，但建議這樣做。</span><span class="sxs-lookup"><span data-stu-id="5a379-371">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="5a379-372">*ASP.NET Core 2.2 或更新版本*：</span><span class="sxs-lookup"><span data-stu-id="5a379-372">*ASP.NET Core 2.2 or later*:</span></span>

   * <span data-ttu-id="5a379-373">若為32位 (x86) [獨立部署](/dotnet/core/deploying/#self-contained-deployments-scd) ，以使用同 [進程裝載模型](#in-process-hosting-model)的32位 SDK 發佈，請啟用應用程式集區以取得32位。</span><span class="sxs-lookup"><span data-stu-id="5a379-373">For a 32-bit (x86) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) published with a 32-bit SDK that uses the [in-process hosting model](#in-process-hosting-model), enable the Application Pool for 32-bit.</span></span> <span data-ttu-id="5a379-374">在 **[IIS** 管理員] 中，流覽至 [連線] 提要欄位中的 **應用程式** 集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-374">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="5a379-375">選取應用程式的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-375">Select the app's Application Pool.</span></span> <span data-ttu-id="5a379-376">在 [ **動作** ] 提要欄位中選取 [ **Advanced Settings**]。</span><span class="sxs-lookup"><span data-stu-id="5a379-376">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="5a379-377">將 [ **啟用32位應用程式** ] 設定為 `True` 。</span><span class="sxs-lookup"><span data-stu-id="5a379-377">Set **Enable 32-Bit Applications** to `True`.</span></span> 

   * <span data-ttu-id="5a379-378">對於使用[同處理序主控模型](#in-process-hosting-model)的 64 位元 (x64) [自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，會停用 32 位元 (x86) 處理序的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-378">For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span> <span data-ttu-id="5a379-379">在 **[IIS** 管理員] 中，流覽至 [連線] 提要欄位中的 **應用程式** 集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-379">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="5a379-380">選取應用程式的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-380">Select the app's Application Pool.</span></span> <span data-ttu-id="5a379-381">在 [ **動作** ] 提要欄位中選取 [ **Advanced Settings**]。</span><span class="sxs-lookup"><span data-stu-id="5a379-381">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="5a379-382">將 [ **啟用32位應用程式** ] 設定為 `False` 。</span><span class="sxs-lookup"><span data-stu-id="5a379-382">Set **Enable 32-Bit Applications** to `False`.</span></span> 

1. <span data-ttu-id="5a379-383">確認處理序模型身分識別具有適當的權限。</span><span class="sxs-lookup"><span data-stu-id="5a379-383">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="5a379-384">如果應用程式集區的預設身分識別 (**進程模型**  >  **Identity**) 從 **ApplicationPool Identity** 變更為另一個身分識別，請確認新的身分識別具有存取應用程式資料夾、資料庫和其他必要資源所需的許可權。</span><span class="sxs-lookup"><span data-stu-id="5a379-384">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="5a379-385">例如，應用程式集區需要針對應用程式讀取和寫入檔案的資料夾取得讀取和寫入權限。</span><span class="sxs-lookup"><span data-stu-id="5a379-385">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="5a379-386">**Windows 驗證設定 (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5a379-386">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="5a379-387">如需詳細資訊，請參閱[設定 Windows 驗證](xref:security/authentication/windowsauth)主題。</span><span class="sxs-lookup"><span data-stu-id="5a379-387">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="5a379-388">部署應用程式</span><span class="sxs-lookup"><span data-stu-id="5a379-388">Deploy the app</span></span>

<span data-ttu-id="5a379-389">將應用程式部署至在 [建立 IIS 網站](#create-the-iis-site)一節中所建立的 IIS **實體路徑** 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5a379-389">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="5a379-390">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) 是建議的部署機制，但有數個選項可將應用程式從專案的 `publish` 資料夾移至裝載系統的部署資料夾。</span><span class="sxs-lookup"><span data-stu-id="5a379-390">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's `publish` folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="5a379-391">使用 Visual Studio 的 Web Deploy</span><span class="sxs-lookup"><span data-stu-id="5a379-391">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="5a379-392">請參閱[適用於 ASP.NET Core 應用程式部署的 Visual Studio 發行設定檔](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)主題，了解如何建立搭配 Web Deploy 使用的發行設定檔。</span><span class="sxs-lookup"><span data-stu-id="5a379-392">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="5a379-393">如果主機服務提供者提供或支援建立發行設定檔，請下載其設定檔，並使用 Visual Studio 的 [發行] 對話方塊匯入其設定檔：</span><span class="sxs-lookup"><span data-stu-id="5a379-393">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![[發佈] 對話方塊頁](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="5a379-395">Visual Studio 外部的 Web Deploy</span><span class="sxs-lookup"><span data-stu-id="5a379-395">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="5a379-396">透過命令列也可以在 Visual Studio 外部使用 [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="5a379-396">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="5a379-397">如需詳細資訊，請參閱 [Web 部署工具](/iis/publish/using-web-deploy/use-the-web-deployment-tool)。</span><span class="sxs-lookup"><span data-stu-id="5a379-397">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="5a379-398">Web Deploy 的替代項目</span><span class="sxs-lookup"><span data-stu-id="5a379-398">Alternatives to Web Deploy</span></span>

<span data-ttu-id="5a379-399">使用數種方法的其中一種將應用程式移到主機系統，例如手動複製、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)。</span><span class="sxs-lookup"><span data-stu-id="5a379-399">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="5a379-400">如需 IIS 的 ASP.NET Core 部署詳細資訊，請參閱 [IIS 系統管理員的部署資源](#deployment-resources-for-iis-administrators)一節。</span><span class="sxs-lookup"><span data-stu-id="5a379-400">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="5a379-401">瀏覽網站</span><span class="sxs-lookup"><span data-stu-id="5a379-401">Browse the website</span></span>

<span data-ttu-id="5a379-402">應用程式部署到主機系統之後，請向其中一個應用程式的公用端點發出要求。</span><span class="sxs-lookup"><span data-stu-id="5a379-402">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="5a379-403">在下列範例中，網站繫結至 IIS 上的 **連接埠** `80`，其 **主機名稱** 為 `www.mysite.com`。</span><span class="sxs-lookup"><span data-stu-id="5a379-403">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="5a379-404">對 `http://www.mysite.com` 發出要求：</span><span class="sxs-lookup"><span data-stu-id="5a379-404">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 瀏覽器已載入 IIS 啟動頁面。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="5a379-406">已鎖定的部署檔案</span><span class="sxs-lookup"><span data-stu-id="5a379-406">Locked deployment files</span></span>

<span data-ttu-id="5a379-407">當應用程式執行時，會鎖定部署資料夾中的檔案。</span><span class="sxs-lookup"><span data-stu-id="5a379-407">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="5a379-408">無法於部署期間覆寫已鎖定的檔案。</span><span class="sxs-lookup"><span data-stu-id="5a379-408">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="5a379-409">若要釋放部署中的已鎖定檔案，請使用下列其中 **一種** 方法停止應用程式集區：</span><span class="sxs-lookup"><span data-stu-id="5a379-409">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="5a379-410">使用 Web Deploy 並參考專案檔中的 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="5a379-410">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="5a379-411">檔案 `app_offline.htm` 會放置在 web 應用程式目錄的根目錄中。</span><span class="sxs-lookup"><span data-stu-id="5a379-411">An `app_offline.htm` file is placed at the root of the web app directory.</span></span> <span data-ttu-id="5a379-412">當檔案存在時，ASP.NET Core 模組會正常地關閉應用程式，並在部署期間提供檔案服務 `app_offline.htm` 。</span><span class="sxs-lookup"><span data-stu-id="5a379-412">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the `app_offline.htm` file during the deployment.</span></span> <span data-ttu-id="5a379-413">如需詳細資訊，請參閱 [ASP.NET Core 模組組態參考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="5a379-413">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="5a379-414">在伺服器上的 IIS 管理員中手動停止應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-414">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="5a379-415">使用 PowerShell 卸載 `app_offline.htm` (需要 PowerShell 5 或更新版本) ：</span><span class="sxs-lookup"><span data-stu-id="5a379-415">Use PowerShell to drop `app_offline.htm` (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm
  ```

## <a name="data-protection"></a><span data-ttu-id="5a379-416">資料保護</span><span class="sxs-lookup"><span data-stu-id="5a379-416">Data protection</span></span>

<span data-ttu-id="5a379-417">[ASP.NET Core 資料保護堆疊](xref:security/data-protection/introduction)是由數個 ASP.NET Core [中介軟體](xref:fundamentals/middleware/index)所使用，包括用於驗證的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="5a379-417">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="5a379-418">即使資料保護 API 不是由使用者程式碼呼叫，仍應使用部署指令碼或是在使用者程式碼中設定資料保護，以建立持續性的密碼編譯[金鑰存放區](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="5a379-418">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="5a379-419">如不設定資料保護，金鑰會保留在記憶體中，並於應用程式重新啟動時捨棄。</span><span class="sxs-lookup"><span data-stu-id="5a379-419">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="5a379-420">如果 Keyring 儲存在記憶體中，則當應用程式重新啟動時：</span><span class="sxs-lookup"><span data-stu-id="5a379-420">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="5a379-421">所有 cookie 的驗證權杖都會失效。</span><span class="sxs-lookup"><span data-stu-id="5a379-421">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="5a379-422">當使用者提出下一個要求時，需要再次登入。</span><span class="sxs-lookup"><span data-stu-id="5a379-422">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="5a379-423">所有以 Keyring 保護的資料都無法再解密。</span><span class="sxs-lookup"><span data-stu-id="5a379-423">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="5a379-424">這可能包括 [CSRF 權杖](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 和 [ASP.NET Core MVC TempData cookie ](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="5a379-424">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="5a379-425">若要在 IIS 下設定資料保護以保存 Keyring，請使用下列其中 **一種** 方法：</span><span class="sxs-lookup"><span data-stu-id="5a379-425">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="5a379-426">**建立資料保護登錄機碼**</span><span class="sxs-lookup"><span data-stu-id="5a379-426">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="5a379-427">ASP.NET Core 應用程式所使用的資料保護金鑰會儲存在應用程式外部的登錄中。</span><span class="sxs-lookup"><span data-stu-id="5a379-427">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="5a379-428">若要保存指定應用程式的金鑰，請為應用程式集區建立登錄機碼。</span><span class="sxs-lookup"><span data-stu-id="5a379-428">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="5a379-429">若為獨立的非Web 伺服陣列 IIS 安裝，請針對搭配使用 ASP.NET Core 應用程式的每個應用程式集區，使用[資料保護 Provision-AutoGenKeys.ps1 PowerShell 指令碼](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="5a379-429">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="5a379-430">此指令碼會在 HKLM 登錄中建立登錄機碼，只有應用程式之應用程式集區的背景工作處理序帳戶可以存取它。</span><span class="sxs-lookup"><span data-stu-id="5a379-430">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="5a379-431">在待用期間使用 DPAPI 和全電腦金鑰加密金鑰。</span><span class="sxs-lookup"><span data-stu-id="5a379-431">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="5a379-432">在 Web 伺服陣列案例中，應用程式可以設定成使用 UNC 路徑來儲存其資料保護 Keyring。</span><span class="sxs-lookup"><span data-stu-id="5a379-432">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="5a379-433">根據預設，資料保護金鑰不予加密。</span><span class="sxs-lookup"><span data-stu-id="5a379-433">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="5a379-434">請確保網路共用的檔案權限僅限於執行應用程式的 Windows 帳戶。</span><span class="sxs-lookup"><span data-stu-id="5a379-434">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="5a379-435">可以使用 X509 憑證來保護待用的金鑰。</span><span class="sxs-lookup"><span data-stu-id="5a379-435">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="5a379-436">請考慮下列讓使用者上傳憑證的機制：將憑證放入使用者的受信任憑證存放區，並確保在執行使用者應用程式的所有電腦上都能使用這些憑證。</span><span class="sxs-lookup"><span data-stu-id="5a379-436">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="5a379-437">如需詳細資訊，請參閱[設定 ASP.NET Core 資料保護](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="5a379-437">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="5a379-438">**設定 IIS 應用程式集區載入使用者設定檔**</span><span class="sxs-lookup"><span data-stu-id="5a379-438">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="5a379-439">此設定位在應用程式集區 [進階設定] 下的 [處理序模型] 區段中。</span><span class="sxs-lookup"><span data-stu-id="5a379-439">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="5a379-440">將 [載入使用者設定檔] 設為 `True`。</span><span class="sxs-lookup"><span data-stu-id="5a379-440">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="5a379-441">當設定為 `True` 時，金鑰會儲存在使用者設定檔目錄中，且使用具有使用者帳戶專屬金鑰的 DPAPI 保護。</span><span class="sxs-lookup"><span data-stu-id="5a379-441">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="5a379-442">金鑰會保存到 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5a379-442">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="5a379-443">應用程式集區的 [setProfileEnvironment 屬性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)也必須啟用。</span><span class="sxs-lookup"><span data-stu-id="5a379-443">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="5a379-444">`setProfileEnvironment` 的預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="5a379-444">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="5a379-445">在某些情況下 (例如 Windows OS)，`setProfileEnvironment` 會設為 `false`。</span><span class="sxs-lookup"><span data-stu-id="5a379-445">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="5a379-446">如果金鑰並未如預期地儲存在使用者設定檔目錄中：</span><span class="sxs-lookup"><span data-stu-id="5a379-446">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="5a379-447">瀏覽至 *%windir%/system32/inetsrv/config* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5a379-447">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="5a379-448">開啟 *applicationHost.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="5a379-448">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="5a379-449">找出 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="5a379-449">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="5a379-450">確認 `setProfileEnvironment` 屬性不存在 (其預設值為 `true`)，或明確地將屬性值設為 `true`。</span><span class="sxs-lookup"><span data-stu-id="5a379-450">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="5a379-451">**將檔案系統當作 Keyring 存放區使用**</span><span class="sxs-lookup"><span data-stu-id="5a379-451">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="5a379-452">調整應用程式程式碼，以[將檔案系統當作 Keyring 存放區使用](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="5a379-452">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="5a379-453">使用 X509 憑證來保護 Keyring，並確保憑證是受信任的憑證。</span><span class="sxs-lookup"><span data-stu-id="5a379-453">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="5a379-454">如果憑證為自我簽署，請將憑證放在受信任的根存放區。</span><span class="sxs-lookup"><span data-stu-id="5a379-454">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="5a379-455">在 Web 伺服陣列中使用 IIS 時：</span><span class="sxs-lookup"><span data-stu-id="5a379-455">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="5a379-456">請使用所有電腦都可以存取的檔案共用。</span><span class="sxs-lookup"><span data-stu-id="5a379-456">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="5a379-457">將 X509 憑證部署到每一部電腦。</span><span class="sxs-lookup"><span data-stu-id="5a379-457">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="5a379-458">設定[程式碼中的資料保護](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="5a379-458">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="5a379-459">**設定資料保護的全電腦原則**</span><span class="sxs-lookup"><span data-stu-id="5a379-459">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="5a379-460">針對取用資料保護 API 的所有應用程式，資料保護系統僅支援有限的預設[全電腦原則](xref:security/data-protection/configuration/machine-wide-policy)設定。</span><span class="sxs-lookup"><span data-stu-id="5a379-460">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="5a379-461">如需詳細資訊，請參閱<xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="5a379-461">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="5a379-462">虛擬目錄</span><span class="sxs-lookup"><span data-stu-id="5a379-462">Virtual Directories</span></span>

<span data-ttu-id="5a379-463">ASP.NET Core 應用程式不支援 [IIS 虛擬目錄](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="5a379-463">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="5a379-464">應用程式能以[子應用程式](#sub-applications)的形式裝載。</span><span class="sxs-lookup"><span data-stu-id="5a379-464">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="5a379-465">子應用程式</span><span class="sxs-lookup"><span data-stu-id="5a379-465">Sub-applications</span></span>

<span data-ttu-id="5a379-466">ASP.NET Core 應用程式能以 [IIS 子應用程式](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)的形式裝載。</span><span class="sxs-lookup"><span data-stu-id="5a379-466">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="5a379-467">子應用程式的路徑會成為根應用程式 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="5a379-467">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="5a379-468">子應用程式內的靜態資產連結應該使用波狀符號與斜線 (`~/`) 標記法。</span><span class="sxs-lookup"><span data-stu-id="5a379-468">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="5a379-469">波狀符號與斜線標記法會觸發[標記協助程式](xref:mvc/views/tag-helpers/intro)以將子應用程式的路徑基底附加到轉譯的相對連結前面。</span><span class="sxs-lookup"><span data-stu-id="5a379-469">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="5a379-470">針對位於 `/subapp_path` 的子應用程式，使用 `src="~/image.png"` 連結的影像會轉譯為 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="5a379-470">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="5a379-471">根應用程式的靜態檔案中介軟體不會處理靜態檔案要求。</span><span class="sxs-lookup"><span data-stu-id="5a379-471">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="5a379-472">要求會由子應用程式的靜態檔案中介軟體處理。</span><span class="sxs-lookup"><span data-stu-id="5a379-472">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="5a379-473">若靜態資產的 `src` 屬性是設定為絕對路徑 (例如，`src="/image.png"`)，會以不使用子應用程式路徑基底的方式轉譯連結。</span><span class="sxs-lookup"><span data-stu-id="5a379-473">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="5a379-474">根應用程式的靜態檔案中介軟體會嘗試從根應用程式的 [webroot](xref:fundamentals/index#web-root) 提供資產，這會導致「404 - 找不到」回應 (除非根應用程式可存取靜態資產)。</span><span class="sxs-lookup"><span data-stu-id="5a379-474">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="5a379-475">裝載 ASP.NET Core 應用程式做為另一個 ASP.NET Core 應用程式下的子應用程式：</span><span class="sxs-lookup"><span data-stu-id="5a379-475">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="5a379-476">為子應用程式建立應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-476">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="5a379-477">將 [.NET CLR 版本] 設定為 [沒有受控碼]，因為會將核心通用語言執行平台 (CoreCLR) 開機以在背景工作處理序中裝載應用程式，而非在桌面 CLR (.NET CLR) 中裝載。</span><span class="sxs-lookup"><span data-stu-id="5a379-477">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="5a379-478">使用根網站下資料夾中的子應用程式在 IIS 管理員中新增根網站。</span><span class="sxs-lookup"><span data-stu-id="5a379-478">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="5a379-479">以滑鼠右鍵按一下 IIS 管理員中的子應用程式資料夾，然後選取 [轉換成應用程式]。</span><span class="sxs-lookup"><span data-stu-id="5a379-479">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="5a379-480">在 [新增應用程式] 對話方塊中，使用 [應用程式集區] 的[選取] 按鈕來指派您為子應用程式建立的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-480">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="5a379-481">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-481">Select **OK**.</span></span>

<span data-ttu-id="5a379-482">將不同的應用程式集區指派給子應用程式是使用同處理序裝載模型。</span><span class="sxs-lookup"><span data-stu-id="5a379-482">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="5a379-483">如需有關同進程裝載模型和設定 ASP.NET 核心模組的詳細資訊，請參閱 <xref:host-and-deploy/aspnet-core-module> 。</span><span class="sxs-lookup"><span data-stu-id="5a379-483">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="5a379-484">使用 web.config 的 IIS 組態</span><span class="sxs-lookup"><span data-stu-id="5a379-484">Configuration of IIS with web.config</span></span>

<span data-ttu-id="5a379-485">在對使用了 ASP.NET Core 模組的 ASP.NET Core 有作用的 IIS 情境下，設定會受 *web.config* 的 `<system.webServer>` 區段影響。</span><span class="sxs-lookup"><span data-stu-id="5a379-485">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="5a379-486">舉例來說，IIS 設定對動態壓縮有作用。</span><span class="sxs-lookup"><span data-stu-id="5a379-486">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="5a379-487">如果在伺服器層級將 IIS 設為使用動態壓縮，應用程式 *web.config* 檔案中的 `<urlCompression>` 元素則可為 ASP.NET Core 應用程式予以停用。</span><span class="sxs-lookup"><span data-stu-id="5a379-487">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="5a379-488">如需詳細資訊，請參閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="5a379-488">For more information, see the following topics:</span></span>

* [<span data-ttu-id="5a379-489">的設定參考 \<system.webServer></span><span class="sxs-lookup"><span data-stu-id="5a379-489">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="5a379-490">若要為在隔離的應用程式集區中執行的個別應用程式設定環境變數 (IIS 10.0 或更新版本的) 支援，請參閱 IIS 參考檔中 [環境變數 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe)主題的 *AppCmd.exe 命令* 部分。</span><span class="sxs-lookup"><span data-stu-id="5a379-490">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="5a379-491">web.config 的組態區段</span><span class="sxs-lookup"><span data-stu-id="5a379-491">Configuration sections of web.config</span></span>

<span data-ttu-id="5a379-492">ASP.NET Core 應用程式的設定不使用 *web.config* 中 ASP.NET 4.x 應用程式的設定區段：</span><span class="sxs-lookup"><span data-stu-id="5a379-492">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="5a379-493">使用其他組態提供者設定的 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-493">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="5a379-494">如需詳細資訊，請參閱 [Configuration](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="5a379-494">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="5a379-495">應用程式集區</span><span class="sxs-lookup"><span data-stu-id="5a379-495">Application Pools</span></span>

<span data-ttu-id="5a379-496">應用程式集區隔離取決於裝載模型：</span><span class="sxs-lookup"><span data-stu-id="5a379-496">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="5a379-497">同進程裝載：應用程式必須在不同的應用程式集區中執行。</span><span class="sxs-lookup"><span data-stu-id="5a379-497">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="5a379-498">跨進程裝載：建議您在應用程式本身的應用程式集區中執行每個應用程式，以隔離彼此的應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-498">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="5a379-499">IIS [新增網站] 對話方塊預設每個應用程式皆為單一應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-499">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="5a379-500">當提供 **網站名稱** 時，文字會自動轉移至 [應用程式集區] 文字方塊。</span><span class="sxs-lookup"><span data-stu-id="5a379-500">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="5a379-501">新增網站時，會使用該網站名稱建立新的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-501">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="5a379-502">應用程式集區 Identity</span><span class="sxs-lookup"><span data-stu-id="5a379-502">Application Pool Identity</span></span>

<span data-ttu-id="5a379-503">應用程式集區身分識別帳戶可讓應用程式在唯一的帳戶下執行，不必建立及管理網域或本機帳戶。</span><span class="sxs-lookup"><span data-stu-id="5a379-503">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="5a379-504">在 IIS 8.0 或更新版本中，IIS 管理背景工作處理序 (WAS) 會使用新的應用程式集區名稱建立虛擬帳戶，並預設在此帳戶下執行應用程式集區的背景工作處理序。</span><span class="sxs-lookup"><span data-stu-id="5a379-504">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="5a379-505">在 [IIS 管理主控台] 中，于應用程式集區的 [ **Advanced Settings** ] 底下，確定 **Identity** 已將設定為 [使用 **Identity ApplicationPool**：</span><span class="sxs-lookup"><span data-stu-id="5a379-505">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![應用程式集區進階設定對話方塊](index/_static/apppool-identity.png)

<span data-ttu-id="5a379-507">IIS 管理程序會在 Windows 安全系統中，以應用程式集區的名稱建立安全識別碼。</span><span class="sxs-lookup"><span data-stu-id="5a379-507">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="5a379-508">可使用此身分識別來保護資源。</span><span class="sxs-lookup"><span data-stu-id="5a379-508">Resources can be secured using this identity.</span></span> <span data-ttu-id="5a379-509">不過，此身分識別不是真正的使用者帳戶，也不會顯示在 Windows 使用者管理主控台中。</span><span class="sxs-lookup"><span data-stu-id="5a379-509">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="5a379-510">如果 IIS 背景工作處理序需要提升應用程式的存取權限，請修改包含應用程式的目錄存取控制清單 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="5a379-510">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="5a379-511">開啟 Windows 檔案總管，巡覽至目錄。</span><span class="sxs-lookup"><span data-stu-id="5a379-511">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="5a379-512">以滑鼠右鍵按一下目錄並選取 [屬性]。</span><span class="sxs-lookup"><span data-stu-id="5a379-512">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="5a379-513">依序選取 [安全性] 索引標籤下的 [編輯] 按鈕和 [新增] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="5a379-513">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="5a379-514">選取 [位置] 按鈕，並確定選取系統。</span><span class="sxs-lookup"><span data-stu-id="5a379-514">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="5a379-515">輸入 `IIS AppPool\{APP POOL NAME}` ，其中預留位置 `{APP POOL NAME}` 是應用程式集區名稱，在 [ **輸入物件名稱來選取** ] 區域中。</span><span class="sxs-lookup"><span data-stu-id="5a379-515">Enter `IIS AppPool\{APP POOL NAME}`, where the placeholder `{APP POOL NAME}` is the app pool name, in **Enter the object names to select** area.</span></span> <span data-ttu-id="5a379-516">選取 [檢查名稱] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="5a379-516">Select the **Check Names** button.</span></span> <span data-ttu-id="5a379-517">若為 *DefaultAppPool* ，請使用來檢查名稱 `IIS AppPool\DefaultAppPool` 。</span><span class="sxs-lookup"><span data-stu-id="5a379-517">For the *DefaultAppPool* check the names using `IIS AppPool\DefaultAppPool`.</span></span> <span data-ttu-id="5a379-518">選取 [ **檢查名稱** ] 按鈕時，的值 `DefaultAppPool` 會在 [物件名稱] 區域中指出。</span><span class="sxs-lookup"><span data-stu-id="5a379-518">When the **Check Names** button is selected, a value of `DefaultAppPool` is indicated in the object names area.</span></span> <span data-ttu-id="5a379-519">您無法直接將應用程式集區名稱輸入至物件名稱區域。</span><span class="sxs-lookup"><span data-stu-id="5a379-519">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="5a379-520">`IIS AppPool\{APP POOL NAME}`檢查物件名稱時，請使用格式，其中預留位置 `{APP POOL NAME}` 是應用程式集區名稱。</span><span class="sxs-lookup"><span data-stu-id="5a379-520">Use the `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, when checking for the object name.</span></span>

   ![針對應用程式資料夾選取使用者或群組對話方塊：在選取 [檢查名稱] 之前，"DefaultAppPool" 這個應用程式集區名稱在物件名稱區域中會附加至 "IIS AppPool\"。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="5a379-522">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-522">Select **OK**.</span></span>

   ![針對應用程式資料夾選取使用者或群組對話方塊：選取 [檢查名稱] 之後，物件名稱 "DefaultAppPool" 會顯示在物件名稱區域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="5a379-524">預設應該會授與讀取 &amp; 執行權限。</span><span class="sxs-lookup"><span data-stu-id="5a379-524">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="5a379-525">請視需要提供其他權限。</span><span class="sxs-lookup"><span data-stu-id="5a379-525">Provide additional permissions as needed.</span></span>

<span data-ttu-id="5a379-526">也可使用 **ICACLS** 工具透過命令提示字元授與存取權限。</span><span class="sxs-lookup"><span data-stu-id="5a379-526">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="5a379-527">使用 `DefaultAppPool` 作為範例時，會使用下列命令：</span><span class="sxs-lookup"><span data-stu-id="5a379-527">Using the `DefaultAppPool` as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="5a379-528">如需詳細資訊，請參閱 [icacls](/windows-server/administration/windows-commands/icacls) 主題。</span><span class="sxs-lookup"><span data-stu-id="5a379-528">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="5a379-529">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="5a379-529">HTTP/2 support</span></span>

<span data-ttu-id="5a379-530">在下列 IIS 部署案例中，ASP.NET Core 支援 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="5a379-530">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="5a379-531">內含式</span><span class="sxs-lookup"><span data-stu-id="5a379-531">In-process</span></span>
  * <span data-ttu-id="5a379-532">Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5a379-532">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="5a379-533">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="5a379-533">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="5a379-534">跨處理序</span><span class="sxs-lookup"><span data-stu-id="5a379-534">Out-of-process</span></span>
  * <span data-ttu-id="5a379-535">Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5a379-535">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="5a379-536">公開 Edge Server 連線使用 HTTP/2，但是對 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)的反向 Proxy 連線使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="5a379-536">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="5a379-537">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="5a379-537">TLS 1.2 or later connection</span></span>

<span data-ttu-id="5a379-538">若為建立 HTTP/2 連接時的同進程部署，則為 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 報告 `HTTP/2` 。</span><span class="sxs-lookup"><span data-stu-id="5a379-538">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="5a379-539">若為建立 HTTP/2 連接時的跨進程部署，則為 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 報告 `HTTP/1.1` 。</span><span class="sxs-lookup"><span data-stu-id="5a379-539">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="5a379-540">如需有關同處理序和跨處理序主控模型的詳細資訊，請參閱 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="5a379-540">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="5a379-541">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="5a379-541">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="5a379-542">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="5a379-542">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="5a379-543">如需使用 IIS 部署之 HTTP/2 設定的詳細資訊，請參閱 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="5a379-543">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="5a379-544">CORS 預檢要求</span><span class="sxs-lookup"><span data-stu-id="5a379-544">CORS preflight requests</span></span>

<span data-ttu-id="5a379-545">*此節只適用於以 .NET Framework 為目標的 ASP.NET Core 應用程式。*</span><span class="sxs-lookup"><span data-stu-id="5a379-545">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="5a379-546">針對以 .NET Framework 為目標的 ASP.NET Core 應用程式，在 IIS 中OPTIONS 要求預設不會傳遞到應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-546">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="5a379-547">若要瞭解如何在中設定應用程式的 IIS 處理常式 `web.config` 來傳遞選項要求，請參閱 [在 ASP.NET Web API 2 中啟用跨原始來源要求： CORS 的運作方式](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="5a379-547">To learn how to configure the app's IIS handlers in `web.config` to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="5a379-548">應用程式初始化模組與閒置逾時</span><span class="sxs-lookup"><span data-stu-id="5a379-548">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="5a379-549">在 IIS 中由 ASP.NET Core 模組版本 2 裝載時：</span><span class="sxs-lookup"><span data-stu-id="5a379-549">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="5a379-550">[應用程式初始化模組](#application-initialization-module)：應用程式裝載的同 [進程](#in-process-hosting-model) 或 [跨進程](#out-of-process-hosting-model) 可設定為在背景工作進程重新開機或伺服器重新開機時自動啟動。</span><span class="sxs-lookup"><span data-stu-id="5a379-550">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="5a379-551">[閒置 Timeout](#idle-timeout)：應用程式裝載的同 [進程](#in-process-hosting-model) 可設定為在無活動期間停止超時。</span><span class="sxs-lookup"><span data-stu-id="5a379-551">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="5a379-552">應用程式初始化模組</span><span class="sxs-lookup"><span data-stu-id="5a379-552">Application Initialization Module</span></span>

<span data-ttu-id="5a379-553">*套用到應用程式裝載同處理序與非同處理序。*</span><span class="sxs-lookup"><span data-stu-id="5a379-553">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="5a379-554">[IIS 應用程式初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一項 IIS 功能，可在應用程式集區啟動或回收時，將 HTTP 要求傳送給應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-554">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="5a379-555">要求會觸發應用程式啟動。</span><span class="sxs-lookup"><span data-stu-id="5a379-555">The request triggers the app to start.</span></span> <span data-ttu-id="5a379-556">根據預設，IIS 會發出應用程式根 URL (`/`) 的要求以初始化應用程式 (如需有關設定的詳細資訊，請參閱[額外資源](#application-initialization-module-and-idle-timeout-additional-resources))。</span><span class="sxs-lookup"><span data-stu-id="5a379-556">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="5a379-557">確認已啟用「IIS 應用程式初始化」角色功能：</span><span class="sxs-lookup"><span data-stu-id="5a379-557">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="5a379-558">在 Windows 7 或更新的電腦系統上，當在本機使用 IIS 時：</span><span class="sxs-lookup"><span data-stu-id="5a379-558">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="5a379-559">瀏覽到 [控制台]**[程式]** > **[程式和功能]** >  > **[開啟或關閉 Windows 功能]** \(畫面左側\)。</span><span class="sxs-lookup"><span data-stu-id="5a379-559">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="5a379-560">開啟 [網際網路資訊服務]**[World Wide Web 服務]** > **[應用程式開發功能]** > 。</span><span class="sxs-lookup"><span data-stu-id="5a379-560">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="5a379-561">選取 [應用程式初始化]的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="5a379-561">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="5a379-562">在 Windows Server 2008 R2 或更新版本上：</span><span class="sxs-lookup"><span data-stu-id="5a379-562">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="5a379-563">開啟「新增角色與功能精靈」。</span><span class="sxs-lookup"><span data-stu-id="5a379-563">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="5a379-564">在 [選取角色服務] 面板中，開啟 [應用程式開發] 節點。</span><span class="sxs-lookup"><span data-stu-id="5a379-564">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="5a379-565">選取 [應用程式初始化]的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="5a379-565">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="5a379-566">使用下列任一方式為網站啟用應用程式初始化模組：</span><span class="sxs-lookup"><span data-stu-id="5a379-566">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="5a379-567">使用 IIS 管理員：</span><span class="sxs-lookup"><span data-stu-id="5a379-567">Using IIS Manager:</span></span>

  1. <span data-ttu-id="5a379-568">選取 [連線] 面板中的 [應用程式集區]。</span><span class="sxs-lookup"><span data-stu-id="5a379-568">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="5a379-569">以滑鼠右鍵按一下清單中應用程式的應用程式集區，然後選取 [進階設定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-569">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="5a379-570">預設的 [啟動模式] 是 [OnDemand]。</span><span class="sxs-lookup"><span data-stu-id="5a379-570">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="5a379-571">將 [啟動模式] 設定為 [AlwaysRunning]。</span><span class="sxs-lookup"><span data-stu-id="5a379-571">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="5a379-572">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-572">Select **OK**.</span></span>
  1. <span data-ttu-id="5a379-573">開啟 [連線] 面板中的 [站台] 節點。</span><span class="sxs-lookup"><span data-stu-id="5a379-573">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="5a379-574">以滑鼠右鍵按一下應用程式，然後選取 [管理網站]**[進階設定]** > 。</span><span class="sxs-lookup"><span data-stu-id="5a379-574">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="5a379-575">預設 [預先載入已啟用] 設定是 [False]。</span><span class="sxs-lookup"><span data-stu-id="5a379-575">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="5a379-576">將 [預先載入已啟用] 設定為 [True]。</span><span class="sxs-lookup"><span data-stu-id="5a379-576">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="5a379-577">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-577">Select **OK**.</span></span>

* <span data-ttu-id="5a379-578">使用 `web.config` ，將 `<applicationInitialization>` 設定為的元素加入 `doAppInitAfterRestart` `true` 至 `<system.webServer>` 應用程式 web.config' 檔案中的元素：</span><span class="sxs-lookup"><span data-stu-id="5a379-578">Using `web.config`, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's web.config\` file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="5a379-579">閒置逾時</span><span class="sxs-lookup"><span data-stu-id="5a379-579">Idle Timeout</span></span>

<span data-ttu-id="5a379-580">*僅適用於應用程式裝載同處理序。*</span><span class="sxs-lookup"><span data-stu-id="5a379-580">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="5a379-581">若要防止應用程式閒置，請使用 IIS 管理員設定應用程式集區的閒置逾時：</span><span class="sxs-lookup"><span data-stu-id="5a379-581">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="5a379-582">選取 [連線] 面板中的 [應用程式集區]。</span><span class="sxs-lookup"><span data-stu-id="5a379-582">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="5a379-583">以滑鼠右鍵按一下清單中應用程式的應用程式集區，然後選取 [進階設定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-583">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="5a379-584">預設 [閒置逾時 (分鐘)] 是 **20** 分鐘。</span><span class="sxs-lookup"><span data-stu-id="5a379-584">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="5a379-585">將 [閒置逾時 (分鐘)] 設定為 **0** (零)。</span><span class="sxs-lookup"><span data-stu-id="5a379-585">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="5a379-586">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-586">Select **OK**.</span></span>
1. <span data-ttu-id="5a379-587">回收背景工作處理序。</span><span class="sxs-lookup"><span data-stu-id="5a379-587">Recycle the worker process.</span></span>

<span data-ttu-id="5a379-588">若要防止應用程式裝載[非同處理序](#out-of-process-hosting-model)逾時，請使用下列任一方式：</span><span class="sxs-lookup"><span data-stu-id="5a379-588">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="5a379-589">從外部服務對應用程式執行 Ping 以讓它繼續執行。</span><span class="sxs-lookup"><span data-stu-id="5a379-589">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="5a379-590">若應用程式只裝載背景服務，避免 IIS 裝載並使用 [Windows 服務來裝載 ASP.NET Core 應用程式](xref:host-and-deploy/windows-service)。</span><span class="sxs-lookup"><span data-stu-id="5a379-590">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="5a379-591">應用程式初始化模組與閒置逾時額外資源</span><span class="sxs-lookup"><span data-stu-id="5a379-591">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="5a379-592">IIS 8.0 應用程式初始化</span><span class="sxs-lookup"><span data-stu-id="5a379-592">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="5a379-593">[應用程式 \<applicationInitialization> 初始化](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="5a379-593">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="5a379-594">[應用程式集 \<processModel> 區的進程模型設定](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="5a379-594">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="5a379-595">IIS 系統管理員的部署資源</span><span class="sxs-lookup"><span data-stu-id="5a379-595">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="5a379-596">IIS 文件</span><span class="sxs-lookup"><span data-stu-id="5a379-596">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="5a379-597">IIS 中的 IIS 管理員使用者入門</span><span class="sxs-lookup"><span data-stu-id="5a379-597">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="5a379-598">.NET Core 應用程式部署</span><span class="sxs-lookup"><span data-stu-id="5a379-598">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="5a379-599">其他資源</span><span class="sxs-lookup"><span data-stu-id="5a379-599">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="5a379-600">Microsoft IIS 官方網站</span><span class="sxs-lookup"><span data-stu-id="5a379-600">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="5a379-601">Windows Server 技術內容庫</span><span class="sxs-lookup"><span data-stu-id="5a379-601">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="5a379-602">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="5a379-602">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="5a379-603">如需將 ASP.NET Core 應用程式發佈至 IIS 伺服器的教學課程體驗，請參閱<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="5a379-603">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="5a379-604">安裝 .NET Core 裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="5a379-604">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="5a379-605">支援的作業系統</span><span class="sxs-lookup"><span data-stu-id="5a379-605">Supported operating systems</span></span>

<span data-ttu-id="5a379-606">以下為支援的作業系統：</span><span class="sxs-lookup"><span data-stu-id="5a379-606">The following operating systems are supported:</span></span>

* <span data-ttu-id="5a379-607">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5a379-607">Windows 7 or later</span></span>
* <span data-ttu-id="5a379-608">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5a379-608">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="5a379-609">[HTTP.sys 伺服器](xref:fundamentals/servers/httpsys) (先前稱為 WebListener) 不適用搭配 IIS 的反向 Proxy 設定。</span><span class="sxs-lookup"><span data-stu-id="5a379-609">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="5a379-610">請使用 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="5a379-610">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="5a379-611">如需在 Azure 中裝載的資訊，請參閱 <xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="5a379-611">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="5a379-612">如需疑難排解指引，請參閱 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="5a379-612">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="5a379-613">支援的平台</span><span class="sxs-lookup"><span data-stu-id="5a379-613">Supported platforms</span></span>

<span data-ttu-id="5a379-614">支援針對 32 位元 (x86) 或 64 位元 (x64) 部署發行的應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-614">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="5a379-615">使用 32 位元 (x86) .NET Core SDK 部署 32 位元應用程式，除非應用程式：</span><span class="sxs-lookup"><span data-stu-id="5a379-615">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="5a379-616">需要提供給 64 位元應用程式使用的較大虛擬記憶體位址空間。</span><span class="sxs-lookup"><span data-stu-id="5a379-616">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="5a379-617">需要較大的 IIS 堆疊大小。</span><span class="sxs-lookup"><span data-stu-id="5a379-617">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="5a379-618">有 64 位元原生相依性。</span><span class="sxs-lookup"><span data-stu-id="5a379-618">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="5a379-619">使用 64 位元 (x64) .NET Core SDK 來發行 64 位元應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-619">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="5a379-620">主機系統上必須有 64 位元執行階段存在。</span><span class="sxs-lookup"><span data-stu-id="5a379-620">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="5a379-621">裝載模型</span><span class="sxs-lookup"><span data-stu-id="5a379-621">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="5a379-622">同處理序裝載模型</span><span class="sxs-lookup"><span data-stu-id="5a379-622">In-process hosting model</span></span>

<span data-ttu-id="5a379-623">使用同處理序裝載，ASP.NET Core 應用程式會在與其 IIS 工作者處理序相同的處理序中執行。</span><span class="sxs-lookup"><span data-stu-id="5a379-623">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="5a379-624">內含式裝載可提升跨進程裝載的效能，因為：</span><span class="sxs-lookup"><span data-stu-id="5a379-624">In-process hosting provides improved performance over out-of-process hosting because:</span></span>

* <span data-ttu-id="5a379-625">要求不會透過回送介面卡進行 proxy。</span><span class="sxs-lookup"><span data-stu-id="5a379-625">Requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="5a379-626">回送介面卡是一種網路介面，可將連出的網路流量傳回相同的電腦。</span><span class="sxs-lookup"><span data-stu-id="5a379-626">A loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="5a379-627">IIS 透過 [Windows 處理序啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 來執行處理程序管理。</span><span class="sxs-lookup"><span data-stu-id="5a379-627">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="5a379-628">[ASP.NET 核心模組](xref:host-and-deploy/aspnet-core-module)：</span><span class="sxs-lookup"><span data-stu-id="5a379-628">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="5a379-629">執行應用程式初始化。</span><span class="sxs-lookup"><span data-stu-id="5a379-629">Performs app initialization.</span></span>
  * <span data-ttu-id="5a379-630">載入 [CoreCLR](/dotnet/standard/glossary#coreclr)。</span><span class="sxs-lookup"><span data-stu-id="5a379-630">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="5a379-631">呼叫 `Program.Main`。</span><span class="sxs-lookup"><span data-stu-id="5a379-631">Calls `Program.Main`.</span></span>
* <span data-ttu-id="5a379-632">處理 IIS 原生要求的存留期。</span><span class="sxs-lookup"><span data-stu-id="5a379-632">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="5a379-633">以 .NET Framework 為目標的 ASP.NET Core 應用程式不支援處理序內裝載模型。</span><span class="sxs-lookup"><span data-stu-id="5a379-633">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="5a379-634">下圖說明 IIS、ASP.NET Core 模組和同處理序裝載應用程式之間的關聯性：</span><span class="sxs-lookup"><span data-stu-id="5a379-634">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![同處理序代管內的 ASP.NET Core 模組案例](index/_static/ancm-inprocess.png)

<span data-ttu-id="5a379-636">要求會從 Web 到達核心模式的 HTTP.sys 驅動程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-636">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="5a379-637">驅動程式會在網站設定的連接埠上將原生要求路由至 IIS，此連接埠通常是 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="5a379-637">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="5a379-638">ASP.NET 核心模組會接收原生要求，並將它傳遞給 IIS HTTP 伺服器 (`IISHttpServer`) 。</span><span class="sxs-lookup"><span data-stu-id="5a379-638">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="5a379-639">IIS HTTP 伺服器是 IIS 的同處理序伺服程式實作，可將要求從原生轉換為受控。</span><span class="sxs-lookup"><span data-stu-id="5a379-639">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="5a379-640">IIS HTTP 伺服器處理要求之後，要求會被推送至 ASP.NET Core 中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="5a379-640">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="5a379-641">中介軟體管線會處理要求，並將其作為 `HttpContext` 執行個體傳遞至應用程式的邏輯。</span><span class="sxs-lookup"><span data-stu-id="5a379-641">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="5a379-642">應用程式的回應會透過 IIS HTTP 伺服器傳回 IIS。</span><span class="sxs-lookup"><span data-stu-id="5a379-642">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="5a379-643">IIS 會將回應傳送到起始該要求的用戶端。</span><span class="sxs-lookup"><span data-stu-id="5a379-643">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="5a379-644">現有的應用程式可以選擇同處理序裝載，但 [dotnet new](/dotnet/core/tools/dotnet-new) 範本預設會對所有 IIS 和 IIS Express 案例使用同處理序裝載模型。</span><span class="sxs-lookup"><span data-stu-id="5a379-644">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="5a379-645">`CreateDefaultBuilder` 會透過呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> 方法來啟動 [CoreCLR](/dotnet/standard/glossary#coreclr) 以新增 <xref:Microsoft.AspNetCore.Hosting.Server.IServer>，並在 IIS 工作者處理序 (*w3wp.exe* 或 *iisexpress.exe*) 內裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-645">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="5a379-646">效能測試指出，相較於跨處理序裝載 .NET Core 應用程式，並將要求 Proxy 處理至 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器，裝載於同處理序可提供明顯更高的要求輸送量。</span><span class="sxs-lookup"><span data-stu-id="5a379-646">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="5a379-647">跨處理序裝載模型</span><span class="sxs-lookup"><span data-stu-id="5a379-647">Out-of-process hosting model</span></span>

<span data-ttu-id="5a379-648">因為 ASP.NET Core 應用程式會在與 IIS 背景工作進程不同的進程中執行，所以 ASP.NET Core 模組會處理進程管理。</span><span class="sxs-lookup"><span data-stu-id="5a379-648">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="5a379-649">此模組會在第一個要求到達時啟動 ASP.NET Core 應用程式的處理序，並在應用程式關閉或損毀時將它重新啟動。</span><span class="sxs-lookup"><span data-stu-id="5a379-649">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="5a379-650">此行為基本上與執行同處理序，並由 [Windows 處理序啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 所管理的應用程式相同。</span><span class="sxs-lookup"><span data-stu-id="5a379-650">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="5a379-651">下圖說明 IIS、ASP.NET Core 模組和跨處理序裝載應用程式之間的關聯性：</span><span class="sxs-lookup"><span data-stu-id="5a379-651">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![非同處理序代管內的 ASP.NET Core 模組案例](index/_static/ancm-outofprocess.png)

<span data-ttu-id="5a379-653">要求會從 Web 到達核心模式的 HTTP.sys 驅動程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-653">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="5a379-654">驅動程式會在網站設定的通訊埠上將要求路由至 IIS，此通訊埠通常是 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="5a379-654">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="5a379-655">此模組會在應用程式的隨機通訊埠上將要求轉送至 Kestrel，而且不會是通訊埠 80 或 443。</span><span class="sxs-lookup"><span data-stu-id="5a379-655">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="5a379-656">此模組在啟動時透過環境變數指定連接埠，而 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 延伸模組則會設定伺服器來接聽 `http://localhost:{PORT}`。</span><span class="sxs-lookup"><span data-stu-id="5a379-656">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="5a379-657">將會執行額外檢查，不是源自模組的要求都會遭到拒絕。</span><span class="sxs-lookup"><span data-stu-id="5a379-657">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="5a379-658">此模組不支援 HTTPS 轉送，因此即使由 IIS 透過 HTTPS 接收，要求還是會透過 HTTP 轉送。</span><span class="sxs-lookup"><span data-stu-id="5a379-658">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="5a379-659">Kestrel 收取來自模組的要求之後，要求會被推送至 ASP.NET Core 中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="5a379-659">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="5a379-660">中介軟體管線會處理要求，並將其作為 `HttpContext` 執行個體傳遞至應用程式的邏輯。</span><span class="sxs-lookup"><span data-stu-id="5a379-660">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="5a379-661">IIS Integration 新增的中介軟體會更新配置、遠端 IP 和帳戶路徑基底，以將要求轉送至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="5a379-661">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="5a379-662">應用程式的回應會傳回 IIS，而 IIS 會將其推送回起始要求的 HTTP 用戶端。</span><span class="sxs-lookup"><span data-stu-id="5a379-662">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="5a379-663">如需 ASP.NET Core 模組組態指南，請參閱 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="5a379-663">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="5a379-664">如需代管的詳細資訊，請參閱[在 ASP.NET Core 中代管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="5a379-664">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="5a379-665">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="5a379-665">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="5a379-666">啟用 IISIntegration 元件</span><span class="sxs-lookup"><span data-stu-id="5a379-666">Enable the IISIntegration components</span></span>

<span data-ttu-id="5a379-667">在 `CreateWebHostBuilder` (*Program.cs*) 中建立主機時，請呼叫 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 來啟用 IIS 整合：</span><span class="sxs-lookup"><span data-stu-id="5a379-667">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="5a379-668">如需 `CreateDefaultBuilder` 的詳細資訊，請參閱 <xref:fundamentals/host/web-host#set-up-a-host>。</span><span class="sxs-lookup"><span data-stu-id="5a379-668">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="5a379-669">IIS 選項</span><span class="sxs-lookup"><span data-stu-id="5a379-669">IIS options</span></span>

<span data-ttu-id="5a379-670">**同處理序主控模型**</span><span class="sxs-lookup"><span data-stu-id="5a379-670">**In-process hosting model**</span></span>

<span data-ttu-id="5a379-671">若要設定 IIS 伺服器選項，請在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> 中加入 <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 的服務設定。</span><span class="sxs-lookup"><span data-stu-id="5a379-671">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="5a379-672">下列範例會停用 AutomaticAuthentication：</span><span class="sxs-lookup"><span data-stu-id="5a379-672">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="5a379-673">選項</span><span class="sxs-lookup"><span data-stu-id="5a379-673">Option</span></span>                         | <span data-ttu-id="5a379-674">預設</span><span class="sxs-lookup"><span data-stu-id="5a379-674">Default</span></span> | <span data-ttu-id="5a379-675">設定</span><span class="sxs-lookup"><span data-stu-id="5a379-675">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="5a379-676">若為 `true`，IIS 伺服器會設定由 [Windows 驗證](xref:security/authentication/windowsauth)所驗證的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="5a379-676">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="5a379-677">若為 `false`，則伺服器僅會對 `HttpContext.User` 提供身分識別，並在 `AuthenticationScheme` 明確要求時回應挑戰。</span><span class="sxs-lookup"><span data-stu-id="5a379-677">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="5a379-678">必須在 IIS 中啟用 Windows 驗證以讓 `AutomaticAuthentication` 作用。</span><span class="sxs-lookup"><span data-stu-id="5a379-678">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="5a379-679">如需詳細資訊，請參閱 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="5a379-679">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="5a379-680">設定使用者在登入頁面上看到的顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="5a379-680">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="5a379-681">**跨處理序裝載模型**</span><span class="sxs-lookup"><span data-stu-id="5a379-681">**Out-of-process hosting model**</span></span>

<span data-ttu-id="5a379-682">若要設定 IIS 選項，請在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中加入 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服務設定。</span><span class="sxs-lookup"><span data-stu-id="5a379-682">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="5a379-683">下列範例會防止應用程式填入 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="5a379-683">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="5a379-684">選項</span><span class="sxs-lookup"><span data-stu-id="5a379-684">Option</span></span>                         | <span data-ttu-id="5a379-685">預設</span><span class="sxs-lookup"><span data-stu-id="5a379-685">Default</span></span> | <span data-ttu-id="5a379-686">設定</span><span class="sxs-lookup"><span data-stu-id="5a379-686">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="5a379-687">若為 `true`，[IIS 整合中介軟體](#enable-the-iisintegration-components)會設定由 [Windows 驗證](xref:security/authentication/windowsauth)所驗證的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="5a379-687">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="5a379-688">如果為 `false`，則驗證中介軟體僅針對 `HttpContext.User` 提供身分識別，並在游 `AuthenticationScheme` 提出明確要求時回應挑戰。</span><span class="sxs-lookup"><span data-stu-id="5a379-688">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="5a379-689">必須在 IIS 中啟用 Windows 驗證以讓 `AutomaticAuthentication` 作用。</span><span class="sxs-lookup"><span data-stu-id="5a379-689">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="5a379-690">如需詳細資訊，請參閱 [Windows 驗證](xref:security/authentication/windowsauth)主題。</span><span class="sxs-lookup"><span data-stu-id="5a379-690">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="5a379-691">設定使用者在登入頁面上看到的顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="5a379-691">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="5a379-692">如果為 `true` 且 `MS-ASPNETCORE-CLIENTCERT` 要求標頭已存在，則會填入 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="5a379-692">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="5a379-693">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="5a379-693">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="5a379-694">用來設定轉送標頭中介軟體及 ASP.NET Core 模組的 [IIS 整合中介軟體](#enable-the-iisintegration-components)會設定為轉送配置 (HTTP/HTTPS) 與發出要求的遠端 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="5a379-694">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="5a379-695">其他 Proxy 伺服器和負載平衡器後方託管的應用程式可能需要其他設定。</span><span class="sxs-lookup"><span data-stu-id="5a379-695">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="5a379-696">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="5a379-696">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="5a379-697">web.config 檔案</span><span class="sxs-lookup"><span data-stu-id="5a379-697">web.config file</span></span>

<span data-ttu-id="5a379-698">*web.config* 檔案是用來設定 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="5a379-698">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="5a379-699">發佈專案時，由 MSBuild 目標 (`_TransformWebConfig`) 處理 *web.config* 檔案的建立、轉換及發佈。</span><span class="sxs-lookup"><span data-stu-id="5a379-699">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="5a379-700">此目標存在於 Web SDK 目標 (`Microsoft.NET.Sdk.Web`)。</span><span class="sxs-lookup"><span data-stu-id="5a379-700">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="5a379-701">SDK 設定在專案檔的頂端：</span><span class="sxs-lookup"><span data-stu-id="5a379-701">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="5a379-702">如果專案中沒有 *web.config* 檔案，則系統會使用正確的 *processPath* 和 *arguments* 建立該檔案以設定 ASP.NET Core 模組，並將該檔案移至 [已發行的輸出](xref:host-and-deploy/directory-structure)。</span><span class="sxs-lookup"><span data-stu-id="5a379-702">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="5a379-703">如果 *web.config* 檔案存在於專案中，則系統會使用正確的 *processPath* 和 *arguments* 來轉換該檔案以設定 ASP.NET Core 模組，然後將它移至已發行的輸出。</span><span class="sxs-lookup"><span data-stu-id="5a379-703">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="5a379-704">轉換不會修改檔案中的 IIS 組態設定。</span><span class="sxs-lookup"><span data-stu-id="5a379-704">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="5a379-705">*web.config* 檔案可提供能控制作用中 IIS 模組的額外 IIS 組態設定。</span><span class="sxs-lookup"><span data-stu-id="5a379-705">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="5a379-706">如需能處理 ASP.NET Core 應用程式要求之 IIS 模組的相關資訊，請參閱 [IIS 模組](xref:host-and-deploy/iis/modules)主題。</span><span class="sxs-lookup"><span data-stu-id="5a379-706">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="5a379-707">若要防止 Web SDK 轉換 *web.config* 檔案，請使用專案檔 **\<IsTransformWebConfigDisabled>** 中的屬性：</span><span class="sxs-lookup"><span data-stu-id="5a379-707">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="5a379-708">使 Web SDK 無法轉換檔案時，應該由開發人員手動設定 *processPath* 和 *arguments*。</span><span class="sxs-lookup"><span data-stu-id="5a379-708">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="5a379-709">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="5a379-709">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="5a379-710">web.config 檔案位置</span><span class="sxs-lookup"><span data-stu-id="5a379-710">web.config file location</span></span>

<span data-ttu-id="5a379-711">為了正確設定 [ASP.NET 核心模組](xref:host-and-deploy/aspnet-core-module) ， *web.config* 檔必須出現在 [內容根](xref:fundamentals/index#content-root) 路徑中， (通常是已部署應用程式) 的應用程式基底路徑。</span><span class="sxs-lookup"><span data-stu-id="5a379-711">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="5a379-712">這是與提供給 IIS 的網站實體路徑相同的位置。</span><span class="sxs-lookup"><span data-stu-id="5a379-712">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="5a379-713">應用程式的根目錄需有 *web.config* 檔案，才能使用 Web Deploy 發行多個應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-713">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="5a379-714">機密檔案存在於應用程式的實體路徑上，例如 *\<assembly>.runtimeconfig.json*、 *\<assembly> .xml* (xml 檔批註) 以及 *\<assembly>.deps.js*。</span><span class="sxs-lookup"><span data-stu-id="5a379-714">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="5a379-715">當 *web.config* 檔案存在且網站正常啟動時，如果有人要求機密檔案，IIS 不會予以提供。</span><span class="sxs-lookup"><span data-stu-id="5a379-715">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="5a379-716">若 *web.config* 檔案遺失或沒有正確命名，或是無法設定網站以正常啟動，IIS 可能會公開提供機密檔案。</span><span class="sxs-lookup"><span data-stu-id="5a379-716">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="5a379-717">***web.config* 檔案必須在部署中隨時都存在、正確命名，而且能夠設定網站以正常啟動。請勿從生產環境部署中移除 *web.config* 的檔案。**</span><span class="sxs-lookup"><span data-stu-id="5a379-717">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="5a379-718">轉換 web.config</span><span class="sxs-lookup"><span data-stu-id="5a379-718">Transform web.config</span></span>

<span data-ttu-id="5a379-719">如需在發佈時轉換 *web.config* (例如依據設定、設定檔或環境設定環境變數)，請參閱<xref:host-and-deploy/iis/transform-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="5a379-719">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="5a379-720">IIS 設定</span><span class="sxs-lookup"><span data-stu-id="5a379-720">IIS configuration</span></span>

<span data-ttu-id="5a379-721">**Windows Server 作業系統**</span><span class="sxs-lookup"><span data-stu-id="5a379-721">**Windows Server operating systems**</span></span>

<span data-ttu-id="5a379-722">啟用 **網頁伺服器 (IIS)** 伺服器角色，並建立角色服務。</span><span class="sxs-lookup"><span data-stu-id="5a379-722">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="5a379-723">使用來自 [管理] 功能表的 [新增角色及功能] 精靈，或是 [伺服器管理員] 中的連結。</span><span class="sxs-lookup"><span data-stu-id="5a379-723">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="5a379-724">在 **伺服器角色** 步驟中，核取 [網頁伺服器 (IIS)] 方塊。</span><span class="sxs-lookup"><span data-stu-id="5a379-724">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在選取伺服器角色步驟中選取網頁伺服器 IIS 角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="5a379-726">在 [功能] 步驟之後，[角色服務] 步驟會針對網頁伺服器 (IIS) 進行載入。</span><span class="sxs-lookup"><span data-stu-id="5a379-726">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="5a379-727">選取所需的 IIS 角色服務或接受所提供的預設角色服務。</span><span class="sxs-lookup"><span data-stu-id="5a379-727">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在選取角色服務步驟中，選取預設的角色服務。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="5a379-729">**Windows 驗證 (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5a379-729">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="5a379-730">若要啟用 Windows 驗證，請展開下列節點：**網頁伺服器**  >  **安全性**。</span><span class="sxs-lookup"><span data-stu-id="5a379-730">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="5a379-731">選取 [Windows 驗證] 功能。</span><span class="sxs-lookup"><span data-stu-id="5a379-731">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="5a379-732">如需詳細資訊，請參閱[Windows 驗證 \<windowsAuthentication> ](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)和[設定 windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="5a379-732">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="5a379-733">**WebSocket (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5a379-733">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="5a379-734">WebSocket 由 ASP.NET Core 1.1 或更新版本所支援。</span><span class="sxs-lookup"><span data-stu-id="5a379-734">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="5a379-735">若要啟用 websocket，請展開下列節點：**網頁伺服器**  >  **應用程式開發**。</span><span class="sxs-lookup"><span data-stu-id="5a379-735">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="5a379-736">選取 [WebSocket 通訊協定] 功能。</span><span class="sxs-lookup"><span data-stu-id="5a379-736">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="5a379-737">如需詳細資訊，請參閱 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="5a379-737">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="5a379-738">透過 **確認** 步驟繼續作業，安裝網頁伺服器角色和服務。</span><span class="sxs-lookup"><span data-stu-id="5a379-738">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="5a379-739">在安裝 **Web 服務器 (iis)** 角色之後，不需要重新開機伺服器。</span><span class="sxs-lookup"><span data-stu-id="5a379-739">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="5a379-740">**Windows 桌面作業系統**</span><span class="sxs-lookup"><span data-stu-id="5a379-740">**Windows desktop operating systems**</span></span>

<span data-ttu-id="5a379-741">啟用 [IIS 管理主控台] 和 [World Wide Web 服務]。</span><span class="sxs-lookup"><span data-stu-id="5a379-741">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="5a379-742">瀏覽到 [控制台]**[程式]** > **[程式和功能]** >  > **[開啟或關閉 Windows 功能]** \(畫面左側\)。</span><span class="sxs-lookup"><span data-stu-id="5a379-742">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="5a379-743">開啟 [Internet Information Services] 節點。</span><span class="sxs-lookup"><span data-stu-id="5a379-743">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="5a379-744">開啟 [Web 管理工具] 節點。</span><span class="sxs-lookup"><span data-stu-id="5a379-744">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="5a379-745">核取 [IIS 管理主控台] 方塊。</span><span class="sxs-lookup"><span data-stu-id="5a379-745">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="5a379-746">[World Wide Web Services] (全球資訊網服務) 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="5a379-746">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="5a379-747">接受 **全球資訊網服務** 的預設功能，或自訂 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="5a379-747">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="5a379-748">**Windows 驗證 (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5a379-748">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="5a379-749">若要啟用 Windows 驗證，請展開下列節點： **World Wide Web 服務**  >  **安全性**。</span><span class="sxs-lookup"><span data-stu-id="5a379-749">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="5a379-750">選取 [Windows 驗證] 功能。</span><span class="sxs-lookup"><span data-stu-id="5a379-750">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="5a379-751">如需詳細資訊，請參閱[Windows 驗證 \<windowsAuthentication> ](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)和[設定 windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="5a379-751">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="5a379-752">**WebSocket (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5a379-752">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="5a379-753">WebSocket 由 ASP.NET Core 1.1 或更新版本所支援。</span><span class="sxs-lookup"><span data-stu-id="5a379-753">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="5a379-754">若要啟用 websocket，請展開下列節點： **World Wide Web 服務**  >  **應用程式開發功能**。</span><span class="sxs-lookup"><span data-stu-id="5a379-754">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="5a379-755">選取 [WebSocket 通訊協定] 功能。</span><span class="sxs-lookup"><span data-stu-id="5a379-755">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="5a379-756">如需詳細資訊，請參閱 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="5a379-756">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="5a379-757">若 IIS 安裝需要重新啟動，請重新啟動系統。</span><span class="sxs-lookup"><span data-stu-id="5a379-757">If the IIS installation requires a restart, restart the system.</span></span>

![選取 [Windows 功能] 中的 [IIS 管理主控台] 和 [World Wide Web Services] (全球資訊網服務)。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="5a379-759">安裝 .NET Core 裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="5a379-759">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="5a379-760">在主控系統上安裝 .NET Core 裝載套件組合。</span><span class="sxs-lookup"><span data-stu-id="5a379-760">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="5a379-761">套件組合會安裝 .NET Core 執行時間、.NET Core 程式庫和 [ASP.NET 核心模組](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="5a379-761">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="5a379-762">此模組可讓 ASP.NET Core 應用程式在 IIS 背後執行。</span><span class="sxs-lookup"><span data-stu-id="5a379-762">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5a379-763">若裝載套件組合在 IIS 之前安裝，則必須對該套件組合安裝進行修復。</span><span class="sxs-lookup"><span data-stu-id="5a379-763">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="5a379-764">請在安裝 IIS 之後，再次執行裝載套件組合安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-764">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="5a379-765">如果在安裝 64 位元 (x64) 版本的 .NET Core 後才安裝裝載套件組合，那麼可能會遺漏 SDK ([未偵測到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="5a379-765">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="5a379-766">若要解決此問題，請參閱 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="5a379-766">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="5a379-767">下載</span><span class="sxs-lookup"><span data-stu-id="5a379-767">Download</span></span>

1. <span data-ttu-id="5a379-768">流覽至 [ [下載 .Net Core](https://dotnet.microsoft.com/download/dotnet-core) ] 頁面。</span><span class="sxs-lookup"><span data-stu-id="5a379-768">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="5a379-769">選取所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="5a379-769">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="5a379-770">在 [執行應用程式 - 執行階段] 欄中，尋找想要的 .NET Core 執行階段版本列。</span><span class="sxs-lookup"><span data-stu-id="5a379-770">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="5a379-771">使用 **裝載** 套件組合連結來下載安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-771">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="5a379-772">某些安裝程式包含已達到期生命週期結束 (EOL) 的發行版本，這些發行版本已不受 Microsoft 支援。</span><span class="sxs-lookup"><span data-stu-id="5a379-772">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="5a379-773">如需詳細資訊，請參閱[支援原則](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) \(英文 \)。</span><span class="sxs-lookup"><span data-stu-id="5a379-773">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="5a379-774">安裝裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="5a379-774">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="5a379-775">在伺服器上執行安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-775">Run the installer on the server.</span></span> <span data-ttu-id="5a379-776">從系統管理員命令殼層執行安裝程式時，有 下列參數可用：</span><span class="sxs-lookup"><span data-stu-id="5a379-776">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="5a379-777">`OPT_NO_ANCM=1`：略過安裝 ASP.NET Core 模組。</span><span class="sxs-lookup"><span data-stu-id="5a379-777">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="5a379-778">`OPT_NO_RUNTIME=1`：略過安裝 .NET Core 執行時間。</span><span class="sxs-lookup"><span data-stu-id="5a379-778">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="5a379-779">當伺服器僅裝載 [獨立部署 (SCD) ](/dotnet/core/deploying/#self-contained-deployments-scd)時使用。</span><span class="sxs-lookup"><span data-stu-id="5a379-779">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="5a379-780">`OPT_NO_SHAREDFX=1`：略過安裝 ASP.NET 共用架構 (ASP.NET 執行時間) 。</span><span class="sxs-lookup"><span data-stu-id="5a379-780">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="5a379-781">當伺服器僅裝載 [獨立部署 (SCD) ](/dotnet/core/deploying/#self-contained-deployments-scd)時使用。</span><span class="sxs-lookup"><span data-stu-id="5a379-781">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="5a379-782">`OPT_NO_X86=1`：略過安裝 x86 執行時間。</span><span class="sxs-lookup"><span data-stu-id="5a379-782">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="5a379-783">當您確定不會裝載 32 位元應用程式時，請使用此參數。</span><span class="sxs-lookup"><span data-stu-id="5a379-783">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="5a379-784">如果將來有可能同時裝載 32 位元和 64 位元應用程式，請不要使用此參數並安裝這兩個執行階段。</span><span class="sxs-lookup"><span data-stu-id="5a379-784">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="5a379-785">`OPT_NO_SHARED_CONFIG_CHECK=1`：當共用設定 (*applicationHost.config*) 位於與 IIS 安裝相同的電腦上時，請停用 iis 共用設定的檢查。</span><span class="sxs-lookup"><span data-stu-id="5a379-785">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="5a379-786">*只在 ASP.NET Core 2.2 或更新版本的裝載套件組合安裝程式上可用。*</span><span class="sxs-lookup"><span data-stu-id="5a379-786">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="5a379-787">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="5a379-787">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="5a379-788">重新開機系統，或在命令 shell 中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="5a379-788">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="5a379-789">重新啟動 IIS 將能偵測到由安裝程式對系統路徑 (此為環境變數) 所做出的變更。</span><span class="sxs-lookup"><span data-stu-id="5a379-789">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="5a379-790">安裝裝載套件組合時，不需要在 IIS 中手動停止個別的網站。</span><span class="sxs-lookup"><span data-stu-id="5a379-790">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="5a379-791">IIS 網站 (的託管應用程式) 在 IIS 重新開機時重新開機。</span><span class="sxs-lookup"><span data-stu-id="5a379-791">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="5a379-792">當應用程式收到第一個要求（包括從 [應用程式初始化模組](#application-initialization-module-and-idle-timeout)）時，就會再次啟動。</span><span class="sxs-lookup"><span data-stu-id="5a379-792">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="5a379-793">ASP.NET Core 採用共用架構套件修補程式版本的向前復原行為。</span><span class="sxs-lookup"><span data-stu-id="5a379-793">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="5a379-794">當 IIS 以 IIS 重新開機應用程式時，應用程式會在收到第一個要求時，以其參考封裝的最新修補程式版本進行載入。</span><span class="sxs-lookup"><span data-stu-id="5a379-794">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="5a379-795">如果未重新開機 IIS，應用程式就會重新開機，並在其背景工作進程回收並收到其第一個要求時，呈現向前復原行為。</span><span class="sxs-lookup"><span data-stu-id="5a379-795">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="5a379-796">如需 IIS 共用組態的資訊，請參閱[使用 IIS 共用組態的 ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="5a379-796">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="5a379-797">使用 Visual Studio 發佈時安裝 Web Deploy</span><span class="sxs-lookup"><span data-stu-id="5a379-797">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="5a379-798">將應用程式部署到具有 [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later) 的伺服器時，請在伺服器上安裝最新版的 Web Deploy。</span><span class="sxs-lookup"><span data-stu-id="5a379-798">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="5a379-799">若要安裝 Web Deploy，請使用 [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=43717)直接取得安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-799">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="5a379-800">慣用的方法是使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="5a379-800">The preferred method is to use WebPI.</span></span> <span data-ttu-id="5a379-801">WebPI 提供獨立的安裝程式和組態以裝載提供者。</span><span class="sxs-lookup"><span data-stu-id="5a379-801">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="5a379-802">建立 IIS 網站</span><span class="sxs-lookup"><span data-stu-id="5a379-802">Create the IIS site</span></span>

1. <span data-ttu-id="5a379-803">在主控系統上，請建立資料夾以容納應用程式的已發行資料夾和檔案。</span><span class="sxs-lookup"><span data-stu-id="5a379-803">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="5a379-804">在下列步驟中，您提供資料夾路徑給 IIS，作為應用程式的實體路徑。</span><span class="sxs-lookup"><span data-stu-id="5a379-804">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="5a379-805">如需應用程式之部署資料夾和檔案配置的詳細資訊，請參閱 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="5a379-805">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="5a379-806">在 [IIS 管理員] 中 **，在 [連線] 面板中** 開啟伺服器的節點。</span><span class="sxs-lookup"><span data-stu-id="5a379-806">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="5a379-807">以滑鼠右鍵按一下 [網站] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5a379-807">Right-click the **Sites** folder.</span></span> <span data-ttu-id="5a379-808">從操作功能表選取 [新增網站]。</span><span class="sxs-lookup"><span data-stu-id="5a379-808">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="5a379-809">提供 **網站名稱**，並將 **實體路徑** 設定為應用程式的部署資料夾。</span><span class="sxs-lookup"><span data-stu-id="5a379-809">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="5a379-810">提供系結 **設定，並** 選取 **[確定]** 以建立網站：</span><span class="sxs-lookup"><span data-stu-id="5a379-810">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在新增網站步驟中提供站台名稱、實體路徑和主機名稱。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="5a379-812">請 **勿** 使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="5a379-812">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="5a379-813">最上層萬用字元繫結可能暴露您的應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="5a379-813">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="5a379-814">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="5a379-814">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="5a379-815">請使用明確主機名稱，而非萬用字元。</span><span class="sxs-lookup"><span data-stu-id="5a379-815">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="5a379-816">若您擁有整個父網域 (與具弱點的 `*.com` 相對) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 就沒有此安全性風險。</span><span class="sxs-lookup"><span data-stu-id="5a379-816">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="5a379-817">如需詳細資訊，請參閱 [rfc7230 5.4 節](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="5a379-817">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="5a379-818">在伺服器的節點之下，選取 [應用程式集區]。</span><span class="sxs-lookup"><span data-stu-id="5a379-818">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="5a379-819">以滑鼠右鍵按一下網站的應用程式集區，然後從操作功能表選取 [基本設定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-819">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="5a379-820">在 [編輯應用程式集區] 視窗中，將 [.NET CLR 版本] 設定為 [沒有受控碼]：</span><span class="sxs-lookup"><span data-stu-id="5a379-820">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![將 .NET CLR 版本設為 [沒有受控碼]。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="5a379-822">ASP.NET Core 會在不同的處理序中執行，並管理執行階段。</span><span class="sxs-lookup"><span data-stu-id="5a379-822">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="5a379-823">ASP.NET Core 不仰賴載入桌面 CLR (.NET CLR)&mdash;會使用 .NET Core 的核心通用語言執行平台 (CoreCLR) 來開機以在背景工作處理序中裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-823">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="5a379-824">將 [.NET CLR 版本] 設定為 [沒有受控碼] 是選擇性的，但建議這樣做。</span><span class="sxs-lookup"><span data-stu-id="5a379-824">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="5a379-825">*ASP.NET Core 2.2 或更新版本*：對於使用 [同處理序主控模型](#in-process-hosting-model)的 64 位元 (x64) [獨立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，會停用 32 位元 (x86) 處理序的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-825">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="5a379-826">在 IIS 管理員的 [動作] 資訊看板 > [應用程式集區] 中，選取 [設定應用程式集區預設值] 或 [進階設定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-826">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="5a379-827">找到 [啟用 32 位元應用程式]，然後將其值設定為 `False`。</span><span class="sxs-lookup"><span data-stu-id="5a379-827">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="5a379-828">此設定不會影響為[處理程序外裝載](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)部署的應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-828">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="5a379-829">確認處理序模型身分識別具有適當的權限。</span><span class="sxs-lookup"><span data-stu-id="5a379-829">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="5a379-830">如果應用程式集區的預設身分識別 (**進程模型**  >  **Identity**) 從 **ApplicationPool Identity** 變更為另一個身分識別，請確認新的身分識別具有存取應用程式資料夾、資料庫和其他必要資源所需的許可權。</span><span class="sxs-lookup"><span data-stu-id="5a379-830">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="5a379-831">例如，應用程式集區需要針對應用程式讀取和寫入檔案的資料夾取得讀取和寫入權限。</span><span class="sxs-lookup"><span data-stu-id="5a379-831">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="5a379-832">**Windows 驗證設定 (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5a379-832">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="5a379-833">如需詳細資訊，請參閱[設定 Windows 驗證](xref:security/authentication/windowsauth)主題。</span><span class="sxs-lookup"><span data-stu-id="5a379-833">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="5a379-834">部署應用程式</span><span class="sxs-lookup"><span data-stu-id="5a379-834">Deploy the app</span></span>

<span data-ttu-id="5a379-835">將應用程式部署至在 [建立 IIS 網站](#create-the-iis-site)一節中所建立的 IIS **實體路徑** 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5a379-835">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="5a379-836">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) 是建議的部署機制，但有數個選項可將應用程式從專案的 [publish] 資料夾移動至主機系統的部署資料夾。</span><span class="sxs-lookup"><span data-stu-id="5a379-836">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="5a379-837">使用 Visual Studio 的 Web Deploy</span><span class="sxs-lookup"><span data-stu-id="5a379-837">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="5a379-838">請參閱[適用於 ASP.NET Core 應用程式部署的 Visual Studio 發行設定檔](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)主題，了解如何建立搭配 Web Deploy 使用的發行設定檔。</span><span class="sxs-lookup"><span data-stu-id="5a379-838">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="5a379-839">如果主機服務提供者提供或支援建立發行設定檔，請下載其設定檔，並使用 Visual Studio 的 [發行] 對話方塊匯入其設定檔：</span><span class="sxs-lookup"><span data-stu-id="5a379-839">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![[發佈] 對話方塊頁](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="5a379-841">Visual Studio 外部的 Web Deploy</span><span class="sxs-lookup"><span data-stu-id="5a379-841">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="5a379-842">透過命令列也可以在 Visual Studio 外部使用 [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="5a379-842">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="5a379-843">如需詳細資訊，請參閱 [Web 部署工具](/iis/publish/using-web-deploy/use-the-web-deployment-tool)。</span><span class="sxs-lookup"><span data-stu-id="5a379-843">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="5a379-844">Web Deploy 的替代項目</span><span class="sxs-lookup"><span data-stu-id="5a379-844">Alternatives to Web Deploy</span></span>

<span data-ttu-id="5a379-845">使用數種方法的其中一種將應用程式移到主機系統，例如手動複製、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)。</span><span class="sxs-lookup"><span data-stu-id="5a379-845">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="5a379-846">如需 IIS 的 ASP.NET Core 部署詳細資訊，請參閱 [IIS 系統管理員的部署資源](#deployment-resources-for-iis-administrators)一節。</span><span class="sxs-lookup"><span data-stu-id="5a379-846">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="5a379-847">瀏覽網站</span><span class="sxs-lookup"><span data-stu-id="5a379-847">Browse the website</span></span>

<span data-ttu-id="5a379-848">應用程式部署到主機系統之後，請向其中一個應用程式的公用端點發出要求。</span><span class="sxs-lookup"><span data-stu-id="5a379-848">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="5a379-849">在下列範例中，網站繫結至 IIS 上的 **連接埠** `80`，其 **主機名稱** 為 `www.mysite.com`。</span><span class="sxs-lookup"><span data-stu-id="5a379-849">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="5a379-850">對 `http://www.mysite.com` 發出要求：</span><span class="sxs-lookup"><span data-stu-id="5a379-850">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 瀏覽器已載入 IIS 啟動頁面。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="5a379-852">已鎖定的部署檔案</span><span class="sxs-lookup"><span data-stu-id="5a379-852">Locked deployment files</span></span>

<span data-ttu-id="5a379-853">當應用程式執行時，會鎖定部署資料夾中的檔案。</span><span class="sxs-lookup"><span data-stu-id="5a379-853">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="5a379-854">無法於部署期間覆寫已鎖定的檔案。</span><span class="sxs-lookup"><span data-stu-id="5a379-854">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="5a379-855">若要釋放部署中的已鎖定檔案，請使用下列其中 **一種** 方法停止應用程式集區：</span><span class="sxs-lookup"><span data-stu-id="5a379-855">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="5a379-856">使用 Web Deploy 並參考專案檔中的 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="5a379-856">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="5a379-857">*app_offline.htm* 檔案是放在 Web 應用程式目錄的根目錄中。</span><span class="sxs-lookup"><span data-stu-id="5a379-857">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="5a379-858">當檔案存在時，ASP.NET Core 模組會正常關閉應用程式，並在部署期間提供 *app_offline.htm* 檔案。</span><span class="sxs-lookup"><span data-stu-id="5a379-858">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="5a379-859">如需詳細資訊，請參閱 [ASP.NET Core 模組組態參考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="5a379-859">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="5a379-860">在伺服器上的 IIS 管理員中手動停止應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-860">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="5a379-861">使用 PowerShell 卸載 *app_offline.htm* (需要 PowerShell 5 或更新版本) ：</span><span class="sxs-lookup"><span data-stu-id="5a379-861">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="5a379-862">資料保護</span><span class="sxs-lookup"><span data-stu-id="5a379-862">Data protection</span></span>

<span data-ttu-id="5a379-863">[ASP.NET Core 資料保護堆疊](xref:security/data-protection/introduction)是由數個 ASP.NET Core [中介軟體](xref:fundamentals/middleware/index)所使用，包括用於驗證的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="5a379-863">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="5a379-864">即使資料保護 API 不是由使用者程式碼呼叫，仍應使用部署指令碼或是在使用者程式碼中設定資料保護，以建立持續性的密碼編譯[金鑰存放區](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="5a379-864">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="5a379-865">如不設定資料保護，金鑰會保留在記憶體中，並於應用程式重新啟動時捨棄。</span><span class="sxs-lookup"><span data-stu-id="5a379-865">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="5a379-866">如果 Keyring 儲存在記憶體中，則當應用程式重新啟動時：</span><span class="sxs-lookup"><span data-stu-id="5a379-866">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="5a379-867">所有 cookie 的驗證權杖都會失效。</span><span class="sxs-lookup"><span data-stu-id="5a379-867">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="5a379-868">當使用者提出下一個要求時，需要再次登入。</span><span class="sxs-lookup"><span data-stu-id="5a379-868">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="5a379-869">所有以 Keyring 保護的資料都無法再解密。</span><span class="sxs-lookup"><span data-stu-id="5a379-869">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="5a379-870">這可能包括 [CSRF 權杖](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 和 [ASP.NET Core MVC TempData cookie ](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="5a379-870">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="5a379-871">若要在 IIS 下設定資料保護以保存 Keyring，請使用下列其中 **一種** 方法：</span><span class="sxs-lookup"><span data-stu-id="5a379-871">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="5a379-872">**建立資料保護登錄機碼**</span><span class="sxs-lookup"><span data-stu-id="5a379-872">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="5a379-873">ASP.NET Core 應用程式所使用的資料保護金鑰會儲存在應用程式外部的登錄中。</span><span class="sxs-lookup"><span data-stu-id="5a379-873">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="5a379-874">若要保存指定應用程式的金鑰，請為應用程式集區建立登錄機碼。</span><span class="sxs-lookup"><span data-stu-id="5a379-874">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="5a379-875">若為獨立的非Web 伺服陣列 IIS 安裝，請針對搭配使用 ASP.NET Core 應用程式的每個應用程式集區，使用[資料保護 Provision-AutoGenKeys.ps1 PowerShell 指令碼](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="5a379-875">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="5a379-876">此指令碼會在 HKLM 登錄中建立登錄機碼，只有應用程式之應用程式集區的背景工作處理序帳戶可以存取它。</span><span class="sxs-lookup"><span data-stu-id="5a379-876">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="5a379-877">在待用期間使用 DPAPI 和全電腦金鑰加密金鑰。</span><span class="sxs-lookup"><span data-stu-id="5a379-877">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="5a379-878">在 Web 伺服陣列案例中，應用程式可以設定成使用 UNC 路徑來儲存其資料保護 Keyring。</span><span class="sxs-lookup"><span data-stu-id="5a379-878">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="5a379-879">根據預設，資料保護金鑰不予加密。</span><span class="sxs-lookup"><span data-stu-id="5a379-879">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="5a379-880">請確保網路共用的檔案權限僅限於執行應用程式的 Windows 帳戶。</span><span class="sxs-lookup"><span data-stu-id="5a379-880">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="5a379-881">可以使用 X509 憑證來保護待用的金鑰。</span><span class="sxs-lookup"><span data-stu-id="5a379-881">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="5a379-882">請考慮下列讓使用者上傳憑證的機制：將憑證放入使用者的受信任憑證存放區，並確保在執行使用者應用程式的所有電腦上都能使用這些憑證。</span><span class="sxs-lookup"><span data-stu-id="5a379-882">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="5a379-883">如需詳細資訊，請參閱[設定 ASP.NET Core 資料保護](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="5a379-883">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="5a379-884">**設定 IIS 應用程式集區載入使用者設定檔**</span><span class="sxs-lookup"><span data-stu-id="5a379-884">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="5a379-885">此設定位在應用程式集區 [進階設定] 下的 [處理序模型] 區段中。</span><span class="sxs-lookup"><span data-stu-id="5a379-885">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="5a379-886">將 [載入使用者設定檔] 設為 `True`。</span><span class="sxs-lookup"><span data-stu-id="5a379-886">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="5a379-887">當設定為 `True` 時，金鑰會儲存在使用者設定檔目錄中，且使用具有使用者帳戶專屬金鑰的 DPAPI 保護。</span><span class="sxs-lookup"><span data-stu-id="5a379-887">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="5a379-888">金鑰會保存到 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5a379-888">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="5a379-889">應用程式集區的 [setProfileEnvironment 屬性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)也必須啟用。</span><span class="sxs-lookup"><span data-stu-id="5a379-889">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="5a379-890">`setProfileEnvironment` 的預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="5a379-890">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="5a379-891">在某些情況下 (例如 Windows OS)，`setProfileEnvironment` 會設為 `false`。</span><span class="sxs-lookup"><span data-stu-id="5a379-891">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="5a379-892">如果金鑰並未如預期地儲存在使用者設定檔目錄中：</span><span class="sxs-lookup"><span data-stu-id="5a379-892">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="5a379-893">瀏覽至 *%windir%/system32/inetsrv/config* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5a379-893">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="5a379-894">開啟 *applicationHost.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="5a379-894">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="5a379-895">找出 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="5a379-895">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="5a379-896">確認 `setProfileEnvironment` 屬性不存在 (其預設值為 `true`)，或明確地將屬性值設為 `true`。</span><span class="sxs-lookup"><span data-stu-id="5a379-896">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="5a379-897">**將檔案系統當作 Keyring 存放區使用**</span><span class="sxs-lookup"><span data-stu-id="5a379-897">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="5a379-898">調整應用程式程式碼，以[將檔案系統當作 Keyring 存放區使用](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="5a379-898">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="5a379-899">使用 X509 憑證來保護 Keyring，並確保憑證是受信任的憑證。</span><span class="sxs-lookup"><span data-stu-id="5a379-899">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="5a379-900">如果憑證為自我簽署，請將憑證放在受信任的根存放區。</span><span class="sxs-lookup"><span data-stu-id="5a379-900">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="5a379-901">在 Web 伺服陣列中使用 IIS 時：</span><span class="sxs-lookup"><span data-stu-id="5a379-901">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="5a379-902">請使用所有電腦都可以存取的檔案共用。</span><span class="sxs-lookup"><span data-stu-id="5a379-902">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="5a379-903">將 X509 憑證部署到每一部電腦。</span><span class="sxs-lookup"><span data-stu-id="5a379-903">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="5a379-904">設定[程式碼中的資料保護](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="5a379-904">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="5a379-905">**設定資料保護的全電腦原則**</span><span class="sxs-lookup"><span data-stu-id="5a379-905">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="5a379-906">針對取用資料保護 API 的所有應用程式，資料保護系統僅支援有限的預設[全電腦原則](xref:security/data-protection/configuration/machine-wide-policy)設定。</span><span class="sxs-lookup"><span data-stu-id="5a379-906">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="5a379-907">如需詳細資訊，請參閱<xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="5a379-907">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="5a379-908">虛擬目錄</span><span class="sxs-lookup"><span data-stu-id="5a379-908">Virtual Directories</span></span>

<span data-ttu-id="5a379-909">ASP.NET Core 應用程式不支援 [IIS 虛擬目錄](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="5a379-909">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="5a379-910">應用程式能以[子應用程式](#sub-applications)的形式裝載。</span><span class="sxs-lookup"><span data-stu-id="5a379-910">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="5a379-911">子應用程式</span><span class="sxs-lookup"><span data-stu-id="5a379-911">Sub-applications</span></span>

<span data-ttu-id="5a379-912">ASP.NET Core 應用程式能以 [IIS 子應用程式](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)的形式裝載。</span><span class="sxs-lookup"><span data-stu-id="5a379-912">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="5a379-913">子應用程式的路徑會成為根應用程式 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="5a379-913">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="5a379-914">子應用程式內的靜態資產連結應該使用波狀符號與斜線 (`~/`) 標記法。</span><span class="sxs-lookup"><span data-stu-id="5a379-914">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="5a379-915">波狀符號與斜線標記法會觸發[標記協助程式](xref:mvc/views/tag-helpers/intro)以將子應用程式的路徑基底附加到轉譯的相對連結前面。</span><span class="sxs-lookup"><span data-stu-id="5a379-915">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="5a379-916">針對位於 `/subapp_path` 的子應用程式，使用 `src="~/image.png"` 連結的影像會轉譯為 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="5a379-916">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="5a379-917">根應用程式的靜態檔案中介軟體不會處理靜態檔案要求。</span><span class="sxs-lookup"><span data-stu-id="5a379-917">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="5a379-918">要求會由子應用程式的靜態檔案中介軟體處理。</span><span class="sxs-lookup"><span data-stu-id="5a379-918">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="5a379-919">若靜態資產的 `src` 屬性是設定為絕對路徑 (例如，`src="/image.png"`)，會以不使用子應用程式路徑基底的方式轉譯連結。</span><span class="sxs-lookup"><span data-stu-id="5a379-919">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="5a379-920">根應用程式的靜態檔案中介軟體會嘗試從根應用程式的 [webroot](xref:fundamentals/index#web-root) 提供資產，這會導致「404 - 找不到」回應 (除非根應用程式可存取靜態資產)。</span><span class="sxs-lookup"><span data-stu-id="5a379-920">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="5a379-921">裝載 ASP.NET Core 應用程式做為另一個 ASP.NET Core 應用程式下的子應用程式：</span><span class="sxs-lookup"><span data-stu-id="5a379-921">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="5a379-922">為子應用程式建立應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-922">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="5a379-923">將 [.NET CLR 版本] 設定為 [沒有受控碼]，因為會將核心通用語言執行平台 (CoreCLR) 開機以在背景工作處理序中裝載應用程式，而非在桌面 CLR (.NET CLR) 中裝載。</span><span class="sxs-lookup"><span data-stu-id="5a379-923">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="5a379-924">使用根網站下資料夾中的子應用程式在 IIS 管理員中新增根網站。</span><span class="sxs-lookup"><span data-stu-id="5a379-924">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="5a379-925">以滑鼠右鍵按一下 IIS 管理員中的子應用程式資料夾，然後選取 [轉換成應用程式]。</span><span class="sxs-lookup"><span data-stu-id="5a379-925">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="5a379-926">在 [新增應用程式] 對話方塊中，使用 [應用程式集區] 的[選取] 按鈕來指派您為子應用程式建立的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-926">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="5a379-927">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-927">Select **OK**.</span></span>

<span data-ttu-id="5a379-928">將不同的應用程式集區指派給子應用程式是使用同處理序裝載模型。</span><span class="sxs-lookup"><span data-stu-id="5a379-928">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="5a379-929">如需有關同進程裝載模型和設定 ASP.NET 核心模組的詳細資訊，請參閱 <xref:host-and-deploy/aspnet-core-module> 。</span><span class="sxs-lookup"><span data-stu-id="5a379-929">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="5a379-930">使用 web.config 的 IIS 組態</span><span class="sxs-lookup"><span data-stu-id="5a379-930">Configuration of IIS with web.config</span></span>

<span data-ttu-id="5a379-931">在對使用了 ASP.NET Core 模組的 ASP.NET Core 有作用的 IIS 情境下，設定會受 *web.config* 的 `<system.webServer>` 區段影響。</span><span class="sxs-lookup"><span data-stu-id="5a379-931">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="5a379-932">舉例來說，IIS 設定對動態壓縮有作用。</span><span class="sxs-lookup"><span data-stu-id="5a379-932">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="5a379-933">如果在伺服器層級將 IIS 設為使用動態壓縮，應用程式 *web.config* 檔案中的 `<urlCompression>` 元素則可為 ASP.NET Core 應用程式予以停用。</span><span class="sxs-lookup"><span data-stu-id="5a379-933">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="5a379-934">如需詳細資訊，請參閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="5a379-934">For more information, see the following topics:</span></span>

* [<span data-ttu-id="5a379-935">的設定參考 \<system.webServer></span><span class="sxs-lookup"><span data-stu-id="5a379-935">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="5a379-936">若要為在隔離的應用程式集區中執行的個別應用程式設定環境變數 (IIS 10.0 或更新版本的) 支援，請參閱 IIS 參考檔中 [環境變數 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe)主題的 *AppCmd.exe 命令* 部分。</span><span class="sxs-lookup"><span data-stu-id="5a379-936">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="5a379-937">web.config 的組態區段</span><span class="sxs-lookup"><span data-stu-id="5a379-937">Configuration sections of web.config</span></span>

<span data-ttu-id="5a379-938">ASP.NET Core 應用程式的設定不使用 *web.config* 中 ASP.NET 4.x 應用程式的設定區段：</span><span class="sxs-lookup"><span data-stu-id="5a379-938">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="5a379-939">使用其他組態提供者設定的 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-939">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="5a379-940">如需詳細資訊，請參閱 [Configuration](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="5a379-940">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="5a379-941">應用程式集區</span><span class="sxs-lookup"><span data-stu-id="5a379-941">Application Pools</span></span>

<span data-ttu-id="5a379-942">應用程式集區隔離取決於裝載模型：</span><span class="sxs-lookup"><span data-stu-id="5a379-942">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="5a379-943">同進程裝載：應用程式必須在不同的應用程式集區中執行。</span><span class="sxs-lookup"><span data-stu-id="5a379-943">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="5a379-944">跨進程裝載：建議您在應用程式本身的應用程式集區中執行每個應用程式，以隔離彼此的應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-944">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="5a379-945">IIS [新增網站] 對話方塊預設每個應用程式皆為單一應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-945">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="5a379-946">當提供 **網站名稱** 時，文字會自動轉移至 [應用程式集區] 文字方塊。</span><span class="sxs-lookup"><span data-stu-id="5a379-946">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="5a379-947">新增網站時，會使用該網站名稱建立新的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-947">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="5a379-948">應用程式集區 Identity</span><span class="sxs-lookup"><span data-stu-id="5a379-948">Application Pool Identity</span></span>

<span data-ttu-id="5a379-949">應用程式集區身分識別帳戶可讓應用程式在唯一的帳戶下執行，不必建立及管理網域或本機帳戶。</span><span class="sxs-lookup"><span data-stu-id="5a379-949">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="5a379-950">在 IIS 8.0 或更新版本中，IIS 管理背景工作處理序 (WAS) 會使用新的應用程式集區名稱建立虛擬帳戶，並預設在此帳戶下執行應用程式集區的背景工作處理序。</span><span class="sxs-lookup"><span data-stu-id="5a379-950">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="5a379-951">在 [IIS 管理主控台] 中，于應用程式集區的 [ **Advanced Settings** ] 底下，確定 **Identity** 已將設定為 [使用 **Identity ApplicationPool**：</span><span class="sxs-lookup"><span data-stu-id="5a379-951">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![應用程式集區進階設定對話方塊](index/_static/apppool-identity.png)

<span data-ttu-id="5a379-953">IIS 管理程序會在 Windows 安全系統中，以應用程式集區的名稱建立安全識別碼。</span><span class="sxs-lookup"><span data-stu-id="5a379-953">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="5a379-954">可使用此身分識別來保護資源。</span><span class="sxs-lookup"><span data-stu-id="5a379-954">Resources can be secured using this identity.</span></span> <span data-ttu-id="5a379-955">不過，此身分識別不是真正的使用者帳戶，也不會顯示在 Windows 使用者管理主控台中。</span><span class="sxs-lookup"><span data-stu-id="5a379-955">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="5a379-956">如果 IIS 背景工作處理序需要提升應用程式的存取權限，請修改包含應用程式的目錄存取控制清單 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="5a379-956">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="5a379-957">開啟 Windows 檔案總管，巡覽至目錄。</span><span class="sxs-lookup"><span data-stu-id="5a379-957">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="5a379-958">以滑鼠右鍵按一下目錄並選取 [屬性]。</span><span class="sxs-lookup"><span data-stu-id="5a379-958">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="5a379-959">依序選取 [安全性] 索引標籤下的 [編輯] 按鈕和 [新增] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="5a379-959">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="5a379-960">選取 [位置] 按鈕，並確定選取系統。</span><span class="sxs-lookup"><span data-stu-id="5a379-960">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="5a379-961">在 [輸入要選取的物件名稱] 區域中，輸入 **IIS AppPool\\<app_pool_name>**。</span><span class="sxs-lookup"><span data-stu-id="5a379-961">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="5a379-962">選取 [檢查名稱] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="5a379-962">Select the **Check Names** button.</span></span> <span data-ttu-id="5a379-963">針對 [DefaultAppPool]，請使用 **IIS AppPool\DefaultAppPool** 檢查名稱。</span><span class="sxs-lookup"><span data-stu-id="5a379-963">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="5a379-964">選取 [檢查名稱] 按鈕時，[DefaultAppPool] 的值便會顯示於物件名稱區域中。</span><span class="sxs-lookup"><span data-stu-id="5a379-964">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="5a379-965">您無法直接將應用程式集區名稱輸入至物件名稱區域。</span><span class="sxs-lookup"><span data-stu-id="5a379-965">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="5a379-966">檢查物件名稱時，請使用 **IIS AppPool\\<app_pool_name>** 的格式。</span><span class="sxs-lookup"><span data-stu-id="5a379-966">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![針對應用程式資料夾選取使用者或群組對話方塊：在選取 [檢查名稱] 之前，"DefaultAppPool" 這個應用程式集區名稱在物件名稱區域中會附加至 "IIS AppPool\"。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="5a379-968">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-968">Select **OK**.</span></span>

   ![針對應用程式資料夾選取使用者或群組對話方塊：選取 [檢查名稱] 之後，物件名稱 "DefaultAppPool" 會顯示在物件名稱區域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="5a379-970">預設應該會授與讀取 &amp; 執行權限。</span><span class="sxs-lookup"><span data-stu-id="5a379-970">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="5a379-971">請視需要提供其他權限。</span><span class="sxs-lookup"><span data-stu-id="5a379-971">Provide additional permissions as needed.</span></span>

<span data-ttu-id="5a379-972">也可使用 **ICACLS** 工具透過命令提示字元授與存取權限。</span><span class="sxs-lookup"><span data-stu-id="5a379-972">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="5a379-973">使用 *DefaultAppPool* 作為範例，將會使用下列命令：</span><span class="sxs-lookup"><span data-stu-id="5a379-973">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="5a379-974">如需詳細資訊，請參閱 [icacls](/windows-server/administration/windows-commands/icacls) 主題。</span><span class="sxs-lookup"><span data-stu-id="5a379-974">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="5a379-975">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="5a379-975">HTTP/2 support</span></span>

<span data-ttu-id="5a379-976">在下列 IIS 部署案例中，ASP.NET Core 支援 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="5a379-976">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="5a379-977">內含式</span><span class="sxs-lookup"><span data-stu-id="5a379-977">In-process</span></span>
  * <span data-ttu-id="5a379-978">Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5a379-978">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="5a379-979">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="5a379-979">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="5a379-980">跨處理序</span><span class="sxs-lookup"><span data-stu-id="5a379-980">Out-of-process</span></span>
  * <span data-ttu-id="5a379-981">Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5a379-981">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="5a379-982">公開 Edge Server 連線使用 HTTP/2，但是對 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)的反向 Proxy 連線使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="5a379-982">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="5a379-983">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="5a379-983">TLS 1.2 or later connection</span></span>

<span data-ttu-id="5a379-984">針對建立 HTTP/2 連線時的同處理序部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會回報 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="5a379-984">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="5a379-985">針對建立 HTTP/2 連線時的跨處理序部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會回報 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="5a379-985">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="5a379-986">如需有關同處理序和跨處理序主控模型的詳細資訊，請參閱 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="5a379-986">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="5a379-987">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="5a379-987">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="5a379-988">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="5a379-988">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="5a379-989">如需使用 IIS 部署之 HTTP/2 設定的詳細資訊，請參閱 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="5a379-989">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="5a379-990">CORS 預檢要求</span><span class="sxs-lookup"><span data-stu-id="5a379-990">CORS preflight requests</span></span>

<span data-ttu-id="5a379-991">*此節只適用於以 .NET Framework 為目標的 ASP.NET Core 應用程式。*</span><span class="sxs-lookup"><span data-stu-id="5a379-991">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="5a379-992">針對以 .NET Framework 為目標的 ASP.NET Core 應用程式，在 IIS 中OPTIONS 要求預設不會傳遞到應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-992">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="5a379-993">若要瞭解如何在 *web.config* 中設定應用程式的 IIS 處理常式來傳遞選項要求，請參閱 [在 ASP.NET Web API 2 中啟用跨原始來源要求： CORS 的運作方式](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="5a379-993">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="5a379-994">應用程式初始化模組與閒置逾時</span><span class="sxs-lookup"><span data-stu-id="5a379-994">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="5a379-995">在 IIS 中由 ASP.NET Core 模組版本 2 裝載時：</span><span class="sxs-lookup"><span data-stu-id="5a379-995">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="5a379-996">[應用程式初始化模組](#application-initialization-module)：應用程式裝載的同 [進程](#in-process-hosting-model) 或 [跨進程](#out-of-process-hosting-model) 可設定為在背景工作進程重新開機或伺服器重新開機時自動啟動。</span><span class="sxs-lookup"><span data-stu-id="5a379-996">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="5a379-997">[閒置 Timeout](#idle-timeout)：應用程式裝載的同 [進程](#in-process-hosting-model) 可設定為在無活動期間停止超時。</span><span class="sxs-lookup"><span data-stu-id="5a379-997">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="5a379-998">應用程式初始化模組</span><span class="sxs-lookup"><span data-stu-id="5a379-998">Application Initialization Module</span></span>

<span data-ttu-id="5a379-999">*套用到應用程式裝載同處理序與非同處理序。*</span><span class="sxs-lookup"><span data-stu-id="5a379-999">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="5a379-1000">[IIS 應用程式初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一項 IIS 功能，可在應用程式集區啟動或回收時，將 HTTP 要求傳送給應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-1000">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="5a379-1001">要求會觸發應用程式啟動。</span><span class="sxs-lookup"><span data-stu-id="5a379-1001">The request triggers the app to start.</span></span> <span data-ttu-id="5a379-1002">根據預設，IIS 會發出應用程式根 URL (`/`) 的要求以初始化應用程式 (如需有關設定的詳細資訊，請參閱[額外資源](#application-initialization-module-and-idle-timeout-additional-resources))。</span><span class="sxs-lookup"><span data-stu-id="5a379-1002">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="5a379-1003">確認已啟用「IIS 應用程式初始化」角色功能：</span><span class="sxs-lookup"><span data-stu-id="5a379-1003">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="5a379-1004">在 Windows 7 或更新的電腦系統上，當在本機使用 IIS 時：</span><span class="sxs-lookup"><span data-stu-id="5a379-1004">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="5a379-1005">瀏覽到 [控制台]**[程式]** > **[程式和功能]** >  > **[開啟或關閉 Windows 功能]** \(畫面左側\)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1005">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="5a379-1006">開啟 [網際網路資訊服務]**[World Wide Web 服務]** > **[應用程式開發功能]** > 。</span><span class="sxs-lookup"><span data-stu-id="5a379-1006">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="5a379-1007">選取 [應用程式初始化]的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="5a379-1007">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="5a379-1008">在 Windows Server 2008 R2 或更新版本上：</span><span class="sxs-lookup"><span data-stu-id="5a379-1008">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="5a379-1009">開啟「新增角色與功能精靈」。</span><span class="sxs-lookup"><span data-stu-id="5a379-1009">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="5a379-1010">在 [選取角色服務] 面板中，開啟 [應用程式開發] 節點。</span><span class="sxs-lookup"><span data-stu-id="5a379-1010">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="5a379-1011">選取 [應用程式初始化]的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="5a379-1011">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="5a379-1012">使用下列任一方式為網站啟用應用程式初始化模組：</span><span class="sxs-lookup"><span data-stu-id="5a379-1012">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="5a379-1013">使用 IIS 管理員：</span><span class="sxs-lookup"><span data-stu-id="5a379-1013">Using IIS Manager:</span></span>

  1. <span data-ttu-id="5a379-1014">選取 [連線] 面板中的 [應用程式集區]。</span><span class="sxs-lookup"><span data-stu-id="5a379-1014">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="5a379-1015">以滑鼠右鍵按一下清單中應用程式的應用程式集區，然後選取 [進階設定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-1015">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="5a379-1016">預設的 [啟動模式] 是 [OnDemand]。</span><span class="sxs-lookup"><span data-stu-id="5a379-1016">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="5a379-1017">將 [啟動模式] 設定為 [AlwaysRunning]。</span><span class="sxs-lookup"><span data-stu-id="5a379-1017">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="5a379-1018">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-1018">Select **OK**.</span></span>
  1. <span data-ttu-id="5a379-1019">開啟 [連線] 面板中的 [站台] 節點。</span><span class="sxs-lookup"><span data-stu-id="5a379-1019">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="5a379-1020">以滑鼠右鍵按一下應用程式，然後選取 [管理網站]**[進階設定]** > 。</span><span class="sxs-lookup"><span data-stu-id="5a379-1020">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="5a379-1021">預設 [預先載入已啟用] 設定是 [False]。</span><span class="sxs-lookup"><span data-stu-id="5a379-1021">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="5a379-1022">將 [預先載入已啟用] 設定為 [True]。</span><span class="sxs-lookup"><span data-stu-id="5a379-1022">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="5a379-1023">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-1023">Select **OK**.</span></span>

* <span data-ttu-id="5a379-1024">使用 *web.config*，新增 `<applicationInitialization>` 元素並將 `doAppInitAfterRestart` 設定為 `true` 至應用程式 *web.config* 檔案中的 `<system.webServer>` 元素：</span><span class="sxs-lookup"><span data-stu-id="5a379-1024">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="5a379-1025">閒置逾時</span><span class="sxs-lookup"><span data-stu-id="5a379-1025">Idle Timeout</span></span>

<span data-ttu-id="5a379-1026">*僅適用於應用程式裝載同處理序。*</span><span class="sxs-lookup"><span data-stu-id="5a379-1026">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="5a379-1027">若要防止應用程式閒置，請使用 IIS 管理員設定應用程式集區的閒置逾時：</span><span class="sxs-lookup"><span data-stu-id="5a379-1027">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="5a379-1028">選取 [連線] 面板中的 [應用程式集區]。</span><span class="sxs-lookup"><span data-stu-id="5a379-1028">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="5a379-1029">以滑鼠右鍵按一下清單中應用程式的應用程式集區，然後選取 [進階設定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-1029">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="5a379-1030">預設 [閒置逾時 (分鐘)] 是 **20** 分鐘。</span><span class="sxs-lookup"><span data-stu-id="5a379-1030">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="5a379-1031">將 [閒置逾時 (分鐘)] 設定為 **0** (零)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1031">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="5a379-1032">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-1032">Select **OK**.</span></span>
1. <span data-ttu-id="5a379-1033">回收背景工作處理序。</span><span class="sxs-lookup"><span data-stu-id="5a379-1033">Recycle the worker process.</span></span>

<span data-ttu-id="5a379-1034">若要防止應用程式裝載[非同處理序](#out-of-process-hosting-model)逾時，請使用下列任一方式：</span><span class="sxs-lookup"><span data-stu-id="5a379-1034">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="5a379-1035">從外部服務對應用程式執行 Ping 以讓它繼續執行。</span><span class="sxs-lookup"><span data-stu-id="5a379-1035">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="5a379-1036">若應用程式只裝載背景服務，避免 IIS 裝載並使用 [Windows 服務來裝載 ASP.NET Core 應用程式](xref:host-and-deploy/windows-service)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1036">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="5a379-1037">應用程式初始化模組與閒置逾時額外資源</span><span class="sxs-lookup"><span data-stu-id="5a379-1037">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="5a379-1038">IIS 8.0 應用程式初始化</span><span class="sxs-lookup"><span data-stu-id="5a379-1038">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="5a379-1039">[應用程式 \<applicationInitialization> 初始化](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1039">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="5a379-1040">[應用程式集 \<processModel> 區的進程模型設定](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1040">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="5a379-1041">IIS 系統管理員的部署資源</span><span class="sxs-lookup"><span data-stu-id="5a379-1041">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="5a379-1042">IIS 文件</span><span class="sxs-lookup"><span data-stu-id="5a379-1042">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="5a379-1043">IIS 中的 IIS 管理員使用者入門</span><span class="sxs-lookup"><span data-stu-id="5a379-1043">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="5a379-1044">.NET Core 應用程式部署</span><span class="sxs-lookup"><span data-stu-id="5a379-1044">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="5a379-1045">其他資源</span><span class="sxs-lookup"><span data-stu-id="5a379-1045">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="5a379-1046">Microsoft IIS 官方網站</span><span class="sxs-lookup"><span data-stu-id="5a379-1046">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="5a379-1047">Windows Server 技術內容庫</span><span class="sxs-lookup"><span data-stu-id="5a379-1047">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="5a379-1048">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="5a379-1048">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5a379-1049">如需將 ASP.NET Core 應用程式發佈至 IIS 伺服器的教學課程體驗，請參閱<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="5a379-1049">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="5a379-1050">安裝 .NET Core 裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="5a379-1050">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="5a379-1051">支援的作業系統</span><span class="sxs-lookup"><span data-stu-id="5a379-1051">Supported operating systems</span></span>

<span data-ttu-id="5a379-1052">以下為支援的作業系統：</span><span class="sxs-lookup"><span data-stu-id="5a379-1052">The following operating systems are supported:</span></span>

* <span data-ttu-id="5a379-1053">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5a379-1053">Windows 7 or later</span></span>
* <span data-ttu-id="5a379-1054">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5a379-1054">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="5a379-1055">[HTTP.sys 伺服器](xref:fundamentals/servers/httpsys) (先前稱為 WebListener) 不適用搭配 IIS 的反向 Proxy 設定。</span><span class="sxs-lookup"><span data-stu-id="5a379-1055">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="5a379-1056">請使用 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1056">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="5a379-1057">如需在 Azure 中裝載的資訊，請參閱 <xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="5a379-1057">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="5a379-1058">如需疑難排解指引，請參閱 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="5a379-1058">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="5a379-1059">支援的平台</span><span class="sxs-lookup"><span data-stu-id="5a379-1059">Supported platforms</span></span>

<span data-ttu-id="5a379-1060">支援針對 32 位元 (x86) 或 64 位元 (x64) 部署發行的應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-1060">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="5a379-1061">使用 32 位元 (x86) .NET Core SDK 部署 32 位元應用程式，除非應用程式：</span><span class="sxs-lookup"><span data-stu-id="5a379-1061">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="5a379-1062">需要提供給 64 位元應用程式使用的較大虛擬記憶體位址空間。</span><span class="sxs-lookup"><span data-stu-id="5a379-1062">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="5a379-1063">需要較大的 IIS 堆疊大小。</span><span class="sxs-lookup"><span data-stu-id="5a379-1063">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="5a379-1064">有 64 位元原生相依性。</span><span class="sxs-lookup"><span data-stu-id="5a379-1064">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="5a379-1065">使用 64 位元 (x64) .NET Core SDK 來發行 64 位元應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-1065">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="5a379-1066">主機系統上必須有 64 位元執行階段存在。</span><span class="sxs-lookup"><span data-stu-id="5a379-1066">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="5a379-1067">ASP.NET Core 隨附 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)，其為預設的跨平台 HTTP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="5a379-1067">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="5a379-1068">在使用 [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 時，應用程式會執行於從 IIS 背景工作處理序中分離出的處理序 (跨處理序)，並搭配 [Kestrel 伺服器](xref:fundamentals/servers/index#kestrel)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1068">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="5a379-1069">因為 ASP.NET Core 應用程式執行所在的處理序會與 IIS 工作者處理序分開，所以此模組會執行處理程序管理。</span><span class="sxs-lookup"><span data-stu-id="5a379-1069">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="5a379-1070">此模組會在第一個要求到達時啟動 ASP.NET Core 應用程式的處理序，並在應用程式關閉或損毀時將它重新啟動。</span><span class="sxs-lookup"><span data-stu-id="5a379-1070">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="5a379-1071">此行為基本上與執行同處理序，並由 [Windows 處理序啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 所管理的應用程式相同。</span><span class="sxs-lookup"><span data-stu-id="5a379-1071">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="5a379-1072">下圖說明 IIS、ASP.NET Core 模組和跨處理序裝載應用程式之間的關聯性：</span><span class="sxs-lookup"><span data-stu-id="5a379-1072">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core 模組](index/_static/ancm-outofprocess.png)

<span data-ttu-id="5a379-1074">要求會從 Web 到達核心模式的 HTTP.sys 驅動程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-1074">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="5a379-1075">驅動程式會在網站設定的通訊埠上將要求路由至 IIS，此通訊埠通常是 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1075">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="5a379-1076">此模組會在應用程式的隨機通訊埠上將要求轉送至 Kestrel，而且不會是通訊埠 80 或 443。</span><span class="sxs-lookup"><span data-stu-id="5a379-1076">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="5a379-1077">模組會在啟動時透過環境變數指定埠，而 [IIS 整合中介軟體](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 會設定要接聽的伺服器 `http://localhost:{port}` 。</span><span class="sxs-lookup"><span data-stu-id="5a379-1077">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="5a379-1078">將會執行額外檢查，不是源自模組的要求都會遭到拒絕。</span><span class="sxs-lookup"><span data-stu-id="5a379-1078">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="5a379-1079">此模組不支援 HTTPS 轉送，因此即使由 IIS 透過 HTTPS 接收，要求還是會透過 HTTP 轉送。</span><span class="sxs-lookup"><span data-stu-id="5a379-1079">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="5a379-1080">Kestrel 收取來自模組的要求之後，要求會被推送至 ASP.NET Core 中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="5a379-1080">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="5a379-1081">中介軟體管線會處理要求，並將其作為 `HttpContext` 執行個體傳遞至應用程式的邏輯。</span><span class="sxs-lookup"><span data-stu-id="5a379-1081">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="5a379-1082">IIS Integration 新增的中介軟體會更新配置、遠端 IP 和帳戶路徑基底，以將要求轉送至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="5a379-1082">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="5a379-1083">應用程式的回應會傳回 IIS，而 IIS 會將其推送回起始要求的 HTTP 用戶端。</span><span class="sxs-lookup"><span data-stu-id="5a379-1083">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="5a379-1084">`CreateDefaultBuilder` 會將 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器設為網頁伺服器，並設定 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)的基底路徑與連接埠來啟用 IIS 整合。</span><span class="sxs-lookup"><span data-stu-id="5a379-1084">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="5a379-1085">ASP.NET Core 模組會產生要指派給後端處理序的動態連接埠。</span><span class="sxs-lookup"><span data-stu-id="5a379-1085">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="5a379-1086">`CreateDefaultBuilder` 會呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> 方法。</span><span class="sxs-lookup"><span data-stu-id="5a379-1086">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> method.</span></span> <span data-ttu-id="5a379-1087">`UseIISIntegration` 會將 Kestrel 設定為在位於 localhost IP 位址 (`127.0.0.1`) 的動態連接埠上接聽。</span><span class="sxs-lookup"><span data-stu-id="5a379-1087">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="5a379-1088">若動態連接埠是 1234，Kestrel 會在 `127.0.0.1:1234` 接聽。</span><span class="sxs-lookup"><span data-stu-id="5a379-1088">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="5a379-1089">此設定會取代由下列項目提供的其他 URL 設定：</span><span class="sxs-lookup"><span data-stu-id="5a379-1089">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="5a379-1090">Kestrel 的接聽 API</span><span class="sxs-lookup"><span data-stu-id="5a379-1090">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="5a379-1091">[設定](xref:fundamentals/configuration/index) (或[命令列 --urls 選項](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="5a379-1091">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="5a379-1092">在使用模組時不需要呼叫 `UseUrls` 或 Kestrel 的 `Listen` API。</span><span class="sxs-lookup"><span data-stu-id="5a379-1092">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="5a379-1093">若呼叫 `UseUrls` 或 `Listen`，Kestrel 只會接聽未使用 IIS 執行應用程式時指定的連接埠。</span><span class="sxs-lookup"><span data-stu-id="5a379-1093">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="5a379-1094">如需 ASP.NET Core 模組組態指南，請參閱 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="5a379-1094">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="5a379-1095">如需代管的詳細資訊，請參閱[在 ASP.NET Core 中代管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1095">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="5a379-1096">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="5a379-1096">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="5a379-1097">啟用 IISIntegration 元件</span><span class="sxs-lookup"><span data-stu-id="5a379-1097">Enable the IISIntegration components</span></span>

<span data-ttu-id="5a379-1098">在 `CreateWebHostBuilder` (*Program.cs*) 中建立主機時，請呼叫 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 來啟用 IIS 整合：</span><span class="sxs-lookup"><span data-stu-id="5a379-1098">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="5a379-1099">如需 `CreateDefaultBuilder` 的詳細資訊，請參閱 <xref:fundamentals/host/web-host#set-up-a-host>。</span><span class="sxs-lookup"><span data-stu-id="5a379-1099">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="5a379-1100">IIS 選項</span><span class="sxs-lookup"><span data-stu-id="5a379-1100">IIS options</span></span>

| <span data-ttu-id="5a379-1101">選項</span><span class="sxs-lookup"><span data-stu-id="5a379-1101">Option</span></span>                         | <span data-ttu-id="5a379-1102">預設</span><span class="sxs-lookup"><span data-stu-id="5a379-1102">Default</span></span> | <span data-ttu-id="5a379-1103">設定</span><span class="sxs-lookup"><span data-stu-id="5a379-1103">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="5a379-1104">若為 `true`，IIS 伺服器會設定由 [Windows 驗證](xref:security/authentication/windowsauth)所驗證的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="5a379-1104">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="5a379-1105">若為 `false`，則伺服器僅會對 `HttpContext.User` 提供身分識別，並在 `AuthenticationScheme` 明確要求時回應挑戰。</span><span class="sxs-lookup"><span data-stu-id="5a379-1105">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="5a379-1106">必須在 IIS 中啟用 Windows 驗證以讓 `AutomaticAuthentication` 作用。</span><span class="sxs-lookup"><span data-stu-id="5a379-1106">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="5a379-1107">如需詳細資訊，請參閱 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1107">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="5a379-1108">設定使用者在登入頁面上看到的顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="5a379-1108">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="5a379-1109">若要設定 IIS 選項，請在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中加入 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服務設定。</span><span class="sxs-lookup"><span data-stu-id="5a379-1109">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="5a379-1110">下列範例會防止應用程式填入 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="5a379-1110">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="5a379-1111">選項</span><span class="sxs-lookup"><span data-stu-id="5a379-1111">Option</span></span>                         | <span data-ttu-id="5a379-1112">預設</span><span class="sxs-lookup"><span data-stu-id="5a379-1112">Default</span></span> | <span data-ttu-id="5a379-1113">設定</span><span class="sxs-lookup"><span data-stu-id="5a379-1113">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="5a379-1114">若為 `true`，[IIS 整合中介軟體](#enable-the-iisintegration-components)會設定由 [Windows 驗證](xref:security/authentication/windowsauth)所驗證的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="5a379-1114">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="5a379-1115">如果為 `false`，則驗證中介軟體僅針對 `HttpContext.User` 提供身分識別，並在游 `AuthenticationScheme` 提出明確要求時回應挑戰。</span><span class="sxs-lookup"><span data-stu-id="5a379-1115">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="5a379-1116">必須在 IIS 中啟用 Windows 驗證以讓 `AutomaticAuthentication` 作用。</span><span class="sxs-lookup"><span data-stu-id="5a379-1116">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="5a379-1117">如需詳細資訊，請參閱 [Windows 驗證](xref:security/authentication/windowsauth)主題。</span><span class="sxs-lookup"><span data-stu-id="5a379-1117">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="5a379-1118">設定使用者在登入頁面上看到的顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="5a379-1118">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="5a379-1119">如果為 `true` 且 `MS-ASPNETCORE-CLIENTCERT` 要求標頭已存在，則會填入 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="5a379-1119">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="5a379-1120">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="5a379-1120">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="5a379-1121">用來設定轉送標頭中介軟體及 ASP.NET Core 模組的 [IIS 整合中介軟體](#enable-the-iisintegration-components)會設定為轉送配置 (HTTP/HTTPS) 與發出要求的遠端 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="5a379-1121">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="5a379-1122">其他 Proxy 伺服器和負載平衡器後方託管的應用程式可能需要其他設定。</span><span class="sxs-lookup"><span data-stu-id="5a379-1122">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="5a379-1123">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1123">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="5a379-1124">web.config 檔案</span><span class="sxs-lookup"><span data-stu-id="5a379-1124">web.config file</span></span>

<span data-ttu-id="5a379-1125">*web.config* 檔案是用來設定 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1125">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="5a379-1126">發佈專案時，由 MSBuild 目標 (`_TransformWebConfig`) 處理 *web.config* 檔案的建立、轉換及發佈。</span><span class="sxs-lookup"><span data-stu-id="5a379-1126">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="5a379-1127">此目標存在於 Web SDK 目標 (`Microsoft.NET.Sdk.Web`)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1127">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="5a379-1128">SDK 設定在專案檔的頂端：</span><span class="sxs-lookup"><span data-stu-id="5a379-1128">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="5a379-1129">如果專案中沒有 *web.config* 檔案，則系統會使用正確的 *processPath* 和 *arguments* 建立該檔案以設定 ASP.NET Core 模組，並將該檔案移至 [已發行的輸出](xref:host-and-deploy/directory-structure)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1129">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="5a379-1130">如果 *web.config* 檔案存在於專案中，則系統會使用正確的 *processPath* 和 *arguments* 來轉換該檔案以設定 ASP.NET Core 模組，然後將它移至已發行的輸出。</span><span class="sxs-lookup"><span data-stu-id="5a379-1130">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="5a379-1131">轉換不會修改檔案中的 IIS 組態設定。</span><span class="sxs-lookup"><span data-stu-id="5a379-1131">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="5a379-1132">*web.config* 檔案可提供能控制作用中 IIS 模組的額外 IIS 組態設定。</span><span class="sxs-lookup"><span data-stu-id="5a379-1132">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="5a379-1133">如需能處理 ASP.NET Core 應用程式要求之 IIS 模組的相關資訊，請參閱 [IIS 模組](xref:host-and-deploy/iis/modules)主題。</span><span class="sxs-lookup"><span data-stu-id="5a379-1133">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="5a379-1134">若要防止 Web SDK 轉換 *web.config* 檔案，請使用專案檔 **\<IsTransformWebConfigDisabled>** 中的屬性：</span><span class="sxs-lookup"><span data-stu-id="5a379-1134">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="5a379-1135">使 Web SDK 無法轉換檔案時，應該由開發人員手動設定 *processPath* 和 *arguments*。</span><span class="sxs-lookup"><span data-stu-id="5a379-1135">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="5a379-1136">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="5a379-1136">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="5a379-1137">web.config 檔案位置</span><span class="sxs-lookup"><span data-stu-id="5a379-1137">web.config file location</span></span>

<span data-ttu-id="5a379-1138">為了正確設定 [ASP.NET 核心模組](xref:host-and-deploy/aspnet-core-module) ， *web.config* 檔必須出現在 [內容根](xref:fundamentals/index#content-root) 路徑中， (通常是已部署應用程式) 的應用程式基底路徑。</span><span class="sxs-lookup"><span data-stu-id="5a379-1138">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="5a379-1139">這是與提供給 IIS 的網站實體路徑相同的位置。</span><span class="sxs-lookup"><span data-stu-id="5a379-1139">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="5a379-1140">應用程式的根目錄需有 *web.config* 檔案，才能使用 Web Deploy 發行多個應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-1140">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="5a379-1141">機密檔案存在於應用程式的實體路徑上，例如 *\<assembly>.runtimeconfig.json*、 *\<assembly> .xml* (xml 檔批註) 以及 *\<assembly>.deps.js*。</span><span class="sxs-lookup"><span data-stu-id="5a379-1141">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="5a379-1142">當 *web.config* 檔案存在且網站正常啟動時，如果有人要求機密檔案，IIS 不會予以提供。</span><span class="sxs-lookup"><span data-stu-id="5a379-1142">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="5a379-1143">若 *web.config* 檔案遺失或沒有正確命名，或是無法設定網站以正常啟動，IIS 可能會公開提供機密檔案。</span><span class="sxs-lookup"><span data-stu-id="5a379-1143">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="5a379-1144">***web.config* 檔案必須在部署中隨時都存在、正確命名，而且能夠設定網站以正常啟動。請勿從生產環境部署中移除 *web.config* 的檔案。**</span><span class="sxs-lookup"><span data-stu-id="5a379-1144">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="5a379-1145">轉換 web.config</span><span class="sxs-lookup"><span data-stu-id="5a379-1145">Transform web.config</span></span>

<span data-ttu-id="5a379-1146">如需在發佈時轉換 *web.config* (例如依據設定、設定檔或環境設定環境變數)，請參閱<xref:host-and-deploy/iis/transform-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="5a379-1146">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="5a379-1147">IIS 設定</span><span class="sxs-lookup"><span data-stu-id="5a379-1147">IIS configuration</span></span>

<span data-ttu-id="5a379-1148">**Windows Server 作業系統**</span><span class="sxs-lookup"><span data-stu-id="5a379-1148">**Windows Server operating systems**</span></span>

<span data-ttu-id="5a379-1149">啟用 **網頁伺服器 (IIS)** 伺服器角色，並建立角色服務。</span><span class="sxs-lookup"><span data-stu-id="5a379-1149">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="5a379-1150">使用來自 [管理] 功能表的 [新增角色及功能] 精靈，或是 [伺服器管理員] 中的連結。</span><span class="sxs-lookup"><span data-stu-id="5a379-1150">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="5a379-1151">在 **伺服器角色** 步驟中，核取 [網頁伺服器 (IIS)] 方塊。</span><span class="sxs-lookup"><span data-stu-id="5a379-1151">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在選取伺服器角色步驟中選取網頁伺服器 IIS 角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="5a379-1153">在 [功能] 步驟之後，[角色服務] 步驟會針對網頁伺服器 (IIS) 進行載入。</span><span class="sxs-lookup"><span data-stu-id="5a379-1153">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="5a379-1154">選取所需的 IIS 角色服務或接受所提供的預設角色服務。</span><span class="sxs-lookup"><span data-stu-id="5a379-1154">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在選取角色服務步驟中，選取預設的角色服務。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="5a379-1156">**Windows 驗證 (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5a379-1156">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="5a379-1157">若要啟用 Windows 驗證，請展開下列節點：**網頁伺服器**  >  **安全性**。</span><span class="sxs-lookup"><span data-stu-id="5a379-1157">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="5a379-1158">選取 [Windows 驗證] 功能。</span><span class="sxs-lookup"><span data-stu-id="5a379-1158">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="5a379-1159">如需詳細資訊，請參閱[Windows 驗證 \<windowsAuthentication> ](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)和[設定 windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1159">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="5a379-1160">**WebSocket (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5a379-1160">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="5a379-1161">WebSocket 由 ASP.NET Core 1.1 或更新版本所支援。</span><span class="sxs-lookup"><span data-stu-id="5a379-1161">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="5a379-1162">若要啟用 websocket，請展開下列節點：**網頁伺服器**  >  **應用程式開發**。</span><span class="sxs-lookup"><span data-stu-id="5a379-1162">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="5a379-1163">選取 [WebSocket 通訊協定] 功能。</span><span class="sxs-lookup"><span data-stu-id="5a379-1163">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="5a379-1164">如需詳細資訊，請參閱 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1164">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="5a379-1165">透過 **確認** 步驟繼續作業，安裝網頁伺服器角色和服務。</span><span class="sxs-lookup"><span data-stu-id="5a379-1165">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="5a379-1166">在安裝 **Web 服務器 (iis)** 角色之後，不需要重新開機伺服器。</span><span class="sxs-lookup"><span data-stu-id="5a379-1166">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="5a379-1167">**Windows 桌面作業系統**</span><span class="sxs-lookup"><span data-stu-id="5a379-1167">**Windows desktop operating systems**</span></span>

<span data-ttu-id="5a379-1168">啟用 [IIS 管理主控台] 和 [World Wide Web 服務]。</span><span class="sxs-lookup"><span data-stu-id="5a379-1168">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="5a379-1169">瀏覽到 [控制台]**[程式]** > **[程式和功能]** >  > **[開啟或關閉 Windows 功能]** \(畫面左側\)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1169">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="5a379-1170">開啟 [Internet Information Services] 節點。</span><span class="sxs-lookup"><span data-stu-id="5a379-1170">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="5a379-1171">開啟 [Web 管理工具] 節點。</span><span class="sxs-lookup"><span data-stu-id="5a379-1171">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="5a379-1172">核取 [IIS 管理主控台] 方塊。</span><span class="sxs-lookup"><span data-stu-id="5a379-1172">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="5a379-1173">[World Wide Web Services] (全球資訊網服務) 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="5a379-1173">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="5a379-1174">接受 **全球資訊網服務** 的預設功能，或自訂 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="5a379-1174">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="5a379-1175">**Windows 驗證 (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5a379-1175">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="5a379-1176">若要啟用 Windows 驗證，請展開下列節點： **World Wide Web 服務**  >  **安全性**。</span><span class="sxs-lookup"><span data-stu-id="5a379-1176">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="5a379-1177">選取 [Windows 驗證] 功能。</span><span class="sxs-lookup"><span data-stu-id="5a379-1177">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="5a379-1178">如需詳細資訊，請參閱[Windows 驗證 \<windowsAuthentication> ](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)和[設定 windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1178">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="5a379-1179">**WebSocket (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5a379-1179">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="5a379-1180">WebSocket 由 ASP.NET Core 1.1 或更新版本所支援。</span><span class="sxs-lookup"><span data-stu-id="5a379-1180">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="5a379-1181">若要啟用 websocket，請展開下列節點： **World Wide Web 服務**  >  **應用程式開發功能**。</span><span class="sxs-lookup"><span data-stu-id="5a379-1181">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="5a379-1182">選取 [WebSocket 通訊協定] 功能。</span><span class="sxs-lookup"><span data-stu-id="5a379-1182">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="5a379-1183">如需詳細資訊，請參閱 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1183">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="5a379-1184">若 IIS 安裝需要重新啟動，請重新啟動系統。</span><span class="sxs-lookup"><span data-stu-id="5a379-1184">If the IIS installation requires a restart, restart the system.</span></span>

![選取 [Windows 功能] 中的 [IIS 管理主控台] 和 [World Wide Web Services] (全球資訊網服務)。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="5a379-1186">安裝 .NET Core 裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="5a379-1186">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="5a379-1187">在主控系統上安裝 .NET Core 裝載套件組合。</span><span class="sxs-lookup"><span data-stu-id="5a379-1187">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="5a379-1188">套件組合會安裝 .NET Core 執行時間、.NET Core 程式庫和 [ASP.NET 核心模組](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1188">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="5a379-1189">此模組可讓 ASP.NET Core 應用程式在 IIS 背後執行。</span><span class="sxs-lookup"><span data-stu-id="5a379-1189">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5a379-1190">若裝載套件組合在 IIS 之前安裝，則必須對該套件組合安裝進行修復。</span><span class="sxs-lookup"><span data-stu-id="5a379-1190">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="5a379-1191">請在安裝 IIS 之後，再次執行裝載套件組合安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-1191">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="5a379-1192">如果在安裝 64 位元 (x64) 版本的 .NET Core 後才安裝裝載套件組合，那麼可能會遺漏 SDK ([未偵測到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="5a379-1192">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="5a379-1193">若要解決此問題，請參閱 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="5a379-1193">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="5a379-1194">下載</span><span class="sxs-lookup"><span data-stu-id="5a379-1194">Download</span></span>

1. <span data-ttu-id="5a379-1195">流覽至 [ [下載 .Net Core](https://dotnet.microsoft.com/download/dotnet-core) ] 頁面。</span><span class="sxs-lookup"><span data-stu-id="5a379-1195">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="5a379-1196">選取所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="5a379-1196">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="5a379-1197">在 [執行應用程式 - 執行階段] 欄中，尋找想要的 .NET Core 執行階段版本列。</span><span class="sxs-lookup"><span data-stu-id="5a379-1197">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="5a379-1198">使用 **裝載** 套件組合連結來下載安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-1198">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="5a379-1199">某些安裝程式包含已達到期生命週期結束 (EOL) 的發行版本，這些發行版本已不受 Microsoft 支援。</span><span class="sxs-lookup"><span data-stu-id="5a379-1199">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="5a379-1200">如需詳細資訊，請參閱[支援原則](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) \(英文 \)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1200">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="5a379-1201">安裝裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="5a379-1201">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="5a379-1202">在伺服器上執行安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-1202">Run the installer on the server.</span></span> <span data-ttu-id="5a379-1203">從系統管理員命令殼層執行安裝程式時，有 下列參數可用：</span><span class="sxs-lookup"><span data-stu-id="5a379-1203">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="5a379-1204">`OPT_NO_ANCM=1`：略過安裝 ASP.NET Core 模組。</span><span class="sxs-lookup"><span data-stu-id="5a379-1204">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="5a379-1205">`OPT_NO_RUNTIME=1`：略過安裝 .NET Core 執行時間。</span><span class="sxs-lookup"><span data-stu-id="5a379-1205">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="5a379-1206">當伺服器僅裝載 [獨立部署 (SCD) ](/dotnet/core/deploying/#self-contained-deployments-scd)時使用。</span><span class="sxs-lookup"><span data-stu-id="5a379-1206">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="5a379-1207">`OPT_NO_SHAREDFX=1`：略過安裝 ASP.NET 共用架構 (ASP.NET 執行時間) 。</span><span class="sxs-lookup"><span data-stu-id="5a379-1207">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="5a379-1208">當伺服器僅裝載 [獨立部署 (SCD) ](/dotnet/core/deploying/#self-contained-deployments-scd)時使用。</span><span class="sxs-lookup"><span data-stu-id="5a379-1208">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="5a379-1209">`OPT_NO_X86=1`：略過安裝 x86 執行時間。</span><span class="sxs-lookup"><span data-stu-id="5a379-1209">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="5a379-1210">當您確定不會裝載 32 位元應用程式時，請使用此參數。</span><span class="sxs-lookup"><span data-stu-id="5a379-1210">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="5a379-1211">如果將來有可能同時裝載 32 位元和 64 位元應用程式，請不要使用此參數並安裝這兩個執行階段。</span><span class="sxs-lookup"><span data-stu-id="5a379-1211">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="5a379-1212">`OPT_NO_SHARED_CONFIG_CHECK=1`：當共用設定 (*applicationHost.config*) 位於與 IIS 安裝相同的電腦上時，請停用 iis 共用設定的檢查。</span><span class="sxs-lookup"><span data-stu-id="5a379-1212">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="5a379-1213">*只在 ASP.NET Core 2.2 或更新版本的裝載套件組合安裝程式上可用。*</span><span class="sxs-lookup"><span data-stu-id="5a379-1213">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="5a379-1214">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="5a379-1214">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="5a379-1215">重新開機系統，或在命令 shell 中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="5a379-1215">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="5a379-1216">重新啟動 IIS 將能偵測到由安裝程式對系統路徑 (此為環境變數) 所做出的變更。</span><span class="sxs-lookup"><span data-stu-id="5a379-1216">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="5a379-1217">安裝裝載套件組合時，不需要在 IIS 中手動停止個別的網站。</span><span class="sxs-lookup"><span data-stu-id="5a379-1217">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="5a379-1218">IIS 網站 (的託管應用程式) 在 IIS 重新開機時重新開機。</span><span class="sxs-lookup"><span data-stu-id="5a379-1218">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="5a379-1219">當應用程式收到第一個要求（包括從 [應用程式初始化模組](#application-initialization-module-and-idle-timeout)）時，就會再次啟動。</span><span class="sxs-lookup"><span data-stu-id="5a379-1219">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="5a379-1220">ASP.NET Core 採用共用架構套件修補程式版本的向前復原行為。</span><span class="sxs-lookup"><span data-stu-id="5a379-1220">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="5a379-1221">當 IIS 以 IIS 重新開機應用程式時，應用程式會在收到第一個要求時，以其參考封裝的最新修補程式版本進行載入。</span><span class="sxs-lookup"><span data-stu-id="5a379-1221">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="5a379-1222">如果未重新開機 IIS，應用程式就會重新開機，並在其背景工作進程回收並收到其第一個要求時，呈現向前復原行為。</span><span class="sxs-lookup"><span data-stu-id="5a379-1222">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="5a379-1223">如需 IIS 共用組態的資訊，請參閱[使用 IIS 共用組態的 ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1223">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="5a379-1224">使用 Visual Studio 發佈時安裝 Web Deploy</span><span class="sxs-lookup"><span data-stu-id="5a379-1224">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="5a379-1225">將應用程式部署到具有 [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later) 的伺服器時，請在伺服器上安裝最新版的 Web Deploy。</span><span class="sxs-lookup"><span data-stu-id="5a379-1225">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="5a379-1226">若要安裝 Web Deploy，請使用 [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=43717)直接取得安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-1226">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="5a379-1227">慣用的方法是使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="5a379-1227">The preferred method is to use WebPI.</span></span> <span data-ttu-id="5a379-1228">WebPI 提供獨立的安裝程式和組態以裝載提供者。</span><span class="sxs-lookup"><span data-stu-id="5a379-1228">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="5a379-1229">建立 IIS 網站</span><span class="sxs-lookup"><span data-stu-id="5a379-1229">Create the IIS site</span></span>

1. <span data-ttu-id="5a379-1230">在主控系統上，請建立資料夾以容納應用程式的已發行資料夾和檔案。</span><span class="sxs-lookup"><span data-stu-id="5a379-1230">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="5a379-1231">在下列步驟中，您提供資料夾路徑給 IIS，作為應用程式的實體路徑。</span><span class="sxs-lookup"><span data-stu-id="5a379-1231">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="5a379-1232">如需應用程式之部署資料夾和檔案配置的詳細資訊，請參閱 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="5a379-1232">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="5a379-1233">在 [IIS 管理員] 中 **，在 [連線] 面板中** 開啟伺服器的節點。</span><span class="sxs-lookup"><span data-stu-id="5a379-1233">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="5a379-1234">以滑鼠右鍵按一下 [網站] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5a379-1234">Right-click the **Sites** folder.</span></span> <span data-ttu-id="5a379-1235">從操作功能表選取 [新增網站]。</span><span class="sxs-lookup"><span data-stu-id="5a379-1235">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="5a379-1236">提供 **網站名稱**，並將 **實體路徑** 設定為應用程式的部署資料夾。</span><span class="sxs-lookup"><span data-stu-id="5a379-1236">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="5a379-1237">提供系結 **設定，並** 選取 **[確定]** 以建立網站：</span><span class="sxs-lookup"><span data-stu-id="5a379-1237">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在新增網站步驟中提供站台名稱、實體路徑和主機名稱。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="5a379-1239">請 **勿** 使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1239">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="5a379-1240">最上層萬用字元繫結可能暴露您的應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="5a379-1240">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="5a379-1241">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="5a379-1241">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="5a379-1242">請使用明確主機名稱，而非萬用字元。</span><span class="sxs-lookup"><span data-stu-id="5a379-1242">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="5a379-1243">若您擁有整個父網域 (與具弱點的 `*.com` 相對) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 就沒有此安全性風險。</span><span class="sxs-lookup"><span data-stu-id="5a379-1243">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="5a379-1244">如需詳細資訊，請參閱 [rfc7230 5.4 節](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1244">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="5a379-1245">在伺服器的節點之下，選取 [應用程式集區]。</span><span class="sxs-lookup"><span data-stu-id="5a379-1245">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="5a379-1246">以滑鼠右鍵按一下網站的應用程式集區，然後從操作功能表選取 [基本設定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-1246">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="5a379-1247">在 [編輯應用程式集區] 視窗中，將 [.NET CLR 版本] 設定為 [沒有受控碼]：</span><span class="sxs-lookup"><span data-stu-id="5a379-1247">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![將 .NET CLR 版本設為 [沒有受控碼]。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="5a379-1249">ASP.NET Core 會在不同的處理序中執行，並管理執行階段。</span><span class="sxs-lookup"><span data-stu-id="5a379-1249">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="5a379-1250">ASP.NET Core 不仰賴載入桌面 CLR (.NET CLR)&mdash;會使用 .NET Core 的核心通用語言執行平台 (CoreCLR) 來開機以在背景工作處理序中裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-1250">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="5a379-1251">將 [.NET CLR 版本] 設定為 [沒有受控碼] 是選擇性的，但建議這樣做。</span><span class="sxs-lookup"><span data-stu-id="5a379-1251">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="5a379-1252">*ASP.NET Core 2.2 或更新版本*：對於使用 [同處理序主控模型](#in-process-hosting-model)的 64 位元 (x64) [獨立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，會停用 32 位元 (x86) 處理序的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-1252">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="5a379-1253">在 IIS 管理員的 [動作] 資訊看板 > [應用程式集區] 中，選取 [設定應用程式集區預設值] 或 [進階設定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-1253">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="5a379-1254">找到 [啟用 32 位元應用程式]，然後將其值設定為 `False`。</span><span class="sxs-lookup"><span data-stu-id="5a379-1254">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="5a379-1255">此設定不會影響為[處理程序外裝載](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)部署的應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-1255">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="5a379-1256">確認處理序模型身分識別具有適當的權限。</span><span class="sxs-lookup"><span data-stu-id="5a379-1256">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="5a379-1257">如果應用程式集區的預設身分識別 (**進程模型**  >  **Identity**) 從 **ApplicationPool Identity** 變更為另一個身分識別，請確認新的身分識別具有存取應用程式資料夾、資料庫和其他必要資源所需的許可權。</span><span class="sxs-lookup"><span data-stu-id="5a379-1257">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="5a379-1258">例如，應用程式集區需要針對應用程式讀取和寫入檔案的資料夾取得讀取和寫入權限。</span><span class="sxs-lookup"><span data-stu-id="5a379-1258">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="5a379-1259">**Windows 驗證設定 (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5a379-1259">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="5a379-1260">如需詳細資訊，請參閱[設定 Windows 驗證](xref:security/authentication/windowsauth)主題。</span><span class="sxs-lookup"><span data-stu-id="5a379-1260">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="5a379-1261">部署應用程式</span><span class="sxs-lookup"><span data-stu-id="5a379-1261">Deploy the app</span></span>

<span data-ttu-id="5a379-1262">將應用程式部署至在 [建立 IIS 網站](#create-the-iis-site)一節中所建立的 IIS **實體路徑** 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5a379-1262">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="5a379-1263">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) 是建議的部署機制，但有數個選項可將應用程式從專案的 [publish] 資料夾移動至主機系統的部署資料夾。</span><span class="sxs-lookup"><span data-stu-id="5a379-1263">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="5a379-1264">使用 Visual Studio 的 Web Deploy</span><span class="sxs-lookup"><span data-stu-id="5a379-1264">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="5a379-1265">請參閱[適用於 ASP.NET Core 應用程式部署的 Visual Studio 發行設定檔](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)主題，了解如何建立搭配 Web Deploy 使用的發行設定檔。</span><span class="sxs-lookup"><span data-stu-id="5a379-1265">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="5a379-1266">如果主機服務提供者提供或支援建立發行設定檔，請下載其設定檔，並使用 Visual Studio 的 [發行] 對話方塊匯入其設定檔：</span><span class="sxs-lookup"><span data-stu-id="5a379-1266">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![[發佈] 對話方塊頁](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="5a379-1268">Visual Studio 外部的 Web Deploy</span><span class="sxs-lookup"><span data-stu-id="5a379-1268">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="5a379-1269">透過命令列也可以在 Visual Studio 外部使用 [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1269">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="5a379-1270">如需詳細資訊，請參閱 [Web 部署工具](/iis/publish/using-web-deploy/use-the-web-deployment-tool)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1270">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="5a379-1271">Web Deploy 的替代項目</span><span class="sxs-lookup"><span data-stu-id="5a379-1271">Alternatives to Web Deploy</span></span>

<span data-ttu-id="5a379-1272">使用數種方法的其中一種將應用程式移到主機系統，例如手動複製、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1272">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="5a379-1273">如需 IIS 的 ASP.NET Core 部署詳細資訊，請參閱 [IIS 系統管理員的部署資源](#deployment-resources-for-iis-administrators)一節。</span><span class="sxs-lookup"><span data-stu-id="5a379-1273">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="5a379-1274">瀏覽網站</span><span class="sxs-lookup"><span data-stu-id="5a379-1274">Browse the website</span></span>

<span data-ttu-id="5a379-1275">應用程式部署到主機系統之後，請向其中一個應用程式的公用端點發出要求。</span><span class="sxs-lookup"><span data-stu-id="5a379-1275">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="5a379-1276">在下列範例中，網站繫結至 IIS 上的 **連接埠** `80`，其 **主機名稱** 為 `www.mysite.com`。</span><span class="sxs-lookup"><span data-stu-id="5a379-1276">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="5a379-1277">對 `http://www.mysite.com` 發出要求：</span><span class="sxs-lookup"><span data-stu-id="5a379-1277">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 瀏覽器已載入 IIS 啟動頁面。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="5a379-1279">已鎖定的部署檔案</span><span class="sxs-lookup"><span data-stu-id="5a379-1279">Locked deployment files</span></span>

<span data-ttu-id="5a379-1280">當應用程式執行時，會鎖定部署資料夾中的檔案。</span><span class="sxs-lookup"><span data-stu-id="5a379-1280">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="5a379-1281">無法於部署期間覆寫已鎖定的檔案。</span><span class="sxs-lookup"><span data-stu-id="5a379-1281">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="5a379-1282">若要釋放部署中的已鎖定檔案，請使用下列其中 **一種** 方法停止應用程式集區：</span><span class="sxs-lookup"><span data-stu-id="5a379-1282">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="5a379-1283">使用 Web Deploy 並參考專案檔中的 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="5a379-1283">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="5a379-1284">*app_offline.htm* 檔案是放在 Web 應用程式目錄的根目錄中。</span><span class="sxs-lookup"><span data-stu-id="5a379-1284">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="5a379-1285">當檔案存在時，ASP.NET Core 模組會正常關閉應用程式，並在部署期間提供 *app_offline.htm* 檔案。</span><span class="sxs-lookup"><span data-stu-id="5a379-1285">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="5a379-1286">如需詳細資訊，請參閱 [ASP.NET Core 模組組態參考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1286">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="5a379-1287">在伺服器上的 IIS 管理員中手動停止應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-1287">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="5a379-1288">使用 PowerShell 卸載 *app_offline.htm* (需要 PowerShell 5 或更新版本) ：</span><span class="sxs-lookup"><span data-stu-id="5a379-1288">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="5a379-1289">資料保護</span><span class="sxs-lookup"><span data-stu-id="5a379-1289">Data protection</span></span>

<span data-ttu-id="5a379-1290">[ASP.NET Core 資料保護堆疊](xref:security/data-protection/introduction)是由數個 ASP.NET Core [中介軟體](xref:fundamentals/middleware/index)所使用，包括用於驗證的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="5a379-1290">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="5a379-1291">即使資料保護 API 不是由使用者程式碼呼叫，仍應使用部署指令碼或是在使用者程式碼中設定資料保護，以建立持續性的密碼編譯[金鑰存放區](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1291">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="5a379-1292">如不設定資料保護，金鑰會保留在記憶體中，並於應用程式重新啟動時捨棄。</span><span class="sxs-lookup"><span data-stu-id="5a379-1292">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="5a379-1293">如果 Keyring 儲存在記憶體中，則當應用程式重新啟動時：</span><span class="sxs-lookup"><span data-stu-id="5a379-1293">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="5a379-1294">所有 cookie 的驗證權杖都會失效。</span><span class="sxs-lookup"><span data-stu-id="5a379-1294">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="5a379-1295">當使用者提出下一個要求時，需要再次登入。</span><span class="sxs-lookup"><span data-stu-id="5a379-1295">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="5a379-1296">所有以 Keyring 保護的資料都無法再解密。</span><span class="sxs-lookup"><span data-stu-id="5a379-1296">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="5a379-1297">這可能包括 [CSRF 權杖](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 和 [ASP.NET Core MVC TempData cookie ](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1297">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="5a379-1298">若要在 IIS 下設定資料保護以保存 Keyring，請使用下列其中 **一種** 方法：</span><span class="sxs-lookup"><span data-stu-id="5a379-1298">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="5a379-1299">**建立資料保護登錄機碼**</span><span class="sxs-lookup"><span data-stu-id="5a379-1299">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="5a379-1300">ASP.NET Core 應用程式所使用的資料保護金鑰會儲存在應用程式外部的登錄中。</span><span class="sxs-lookup"><span data-stu-id="5a379-1300">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="5a379-1301">若要保存指定應用程式的金鑰，請為應用程式集區建立登錄機碼。</span><span class="sxs-lookup"><span data-stu-id="5a379-1301">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="5a379-1302">若為獨立的非Web 伺服陣列 IIS 安裝，請針對搭配使用 ASP.NET Core 應用程式的每個應用程式集區，使用[資料保護 Provision-AutoGenKeys.ps1 PowerShell 指令碼](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1302">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="5a379-1303">此指令碼會在 HKLM 登錄中建立登錄機碼，只有應用程式之應用程式集區的背景工作處理序帳戶可以存取它。</span><span class="sxs-lookup"><span data-stu-id="5a379-1303">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="5a379-1304">在待用期間使用 DPAPI 和全電腦金鑰加密金鑰。</span><span class="sxs-lookup"><span data-stu-id="5a379-1304">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="5a379-1305">在 Web 伺服陣列案例中，應用程式可以設定成使用 UNC 路徑來儲存其資料保護 Keyring。</span><span class="sxs-lookup"><span data-stu-id="5a379-1305">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="5a379-1306">根據預設，資料保護金鑰不予加密。</span><span class="sxs-lookup"><span data-stu-id="5a379-1306">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="5a379-1307">請確保網路共用的檔案權限僅限於執行應用程式的 Windows 帳戶。</span><span class="sxs-lookup"><span data-stu-id="5a379-1307">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="5a379-1308">可以使用 X509 憑證來保護待用的金鑰。</span><span class="sxs-lookup"><span data-stu-id="5a379-1308">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="5a379-1309">請考慮下列讓使用者上傳憑證的機制：將憑證放入使用者的受信任憑證存放區，並確保在執行使用者應用程式的所有電腦上都能使用這些憑證。</span><span class="sxs-lookup"><span data-stu-id="5a379-1309">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="5a379-1310">如需詳細資訊，請參閱[設定 ASP.NET Core 資料保護](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1310">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="5a379-1311">**設定 IIS 應用程式集區載入使用者設定檔**</span><span class="sxs-lookup"><span data-stu-id="5a379-1311">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="5a379-1312">此設定位在應用程式集區 [進階設定] 下的 [處理序模型] 區段中。</span><span class="sxs-lookup"><span data-stu-id="5a379-1312">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="5a379-1313">將 [載入使用者設定檔] 設為 `True`。</span><span class="sxs-lookup"><span data-stu-id="5a379-1313">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="5a379-1314">當設定為 `True` 時，金鑰會儲存在使用者設定檔目錄中，且使用具有使用者帳戶專屬金鑰的 DPAPI 保護。</span><span class="sxs-lookup"><span data-stu-id="5a379-1314">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="5a379-1315">金鑰會保存到 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5a379-1315">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="5a379-1316">應用程式集區的 [setProfileEnvironment 屬性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)也必須啟用。</span><span class="sxs-lookup"><span data-stu-id="5a379-1316">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="5a379-1317">`setProfileEnvironment` 的預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="5a379-1317">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="5a379-1318">在某些情況下 (例如 Windows OS)，`setProfileEnvironment` 會設為 `false`。</span><span class="sxs-lookup"><span data-stu-id="5a379-1318">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="5a379-1319">如果金鑰並未如預期地儲存在使用者設定檔目錄中：</span><span class="sxs-lookup"><span data-stu-id="5a379-1319">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="5a379-1320">瀏覽至 *%windir%/system32/inetsrv/config* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5a379-1320">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="5a379-1321">開啟 *applicationHost.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="5a379-1321">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="5a379-1322">找出 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="5a379-1322">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="5a379-1323">確認 `setProfileEnvironment` 屬性不存在 (其預設值為 `true`)，或明確地將屬性值設為 `true`。</span><span class="sxs-lookup"><span data-stu-id="5a379-1323">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="5a379-1324">**將檔案系統當作 Keyring 存放區使用**</span><span class="sxs-lookup"><span data-stu-id="5a379-1324">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="5a379-1325">調整應用程式程式碼，以[將檔案系統當作 Keyring 存放區使用](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1325">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="5a379-1326">使用 X509 憑證來保護 Keyring，並確保憑證是受信任的憑證。</span><span class="sxs-lookup"><span data-stu-id="5a379-1326">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="5a379-1327">如果憑證為自我簽署，請將憑證放在受信任的根存放區。</span><span class="sxs-lookup"><span data-stu-id="5a379-1327">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="5a379-1328">在 Web 伺服陣列中使用 IIS 時：</span><span class="sxs-lookup"><span data-stu-id="5a379-1328">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="5a379-1329">請使用所有電腦都可以存取的檔案共用。</span><span class="sxs-lookup"><span data-stu-id="5a379-1329">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="5a379-1330">將 X509 憑證部署到每一部電腦。</span><span class="sxs-lookup"><span data-stu-id="5a379-1330">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="5a379-1331">設定[程式碼中的資料保護](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1331">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="5a379-1332">**設定資料保護的全電腦原則**</span><span class="sxs-lookup"><span data-stu-id="5a379-1332">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="5a379-1333">針對取用資料保護 API 的所有應用程式，資料保護系統僅支援有限的預設[全電腦原則](xref:security/data-protection/configuration/machine-wide-policy)設定。</span><span class="sxs-lookup"><span data-stu-id="5a379-1333">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="5a379-1334">如需詳細資訊，請參閱<xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="5a379-1334">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="5a379-1335">虛擬目錄</span><span class="sxs-lookup"><span data-stu-id="5a379-1335">Virtual Directories</span></span>

<span data-ttu-id="5a379-1336">ASP.NET Core 應用程式不支援 [IIS 虛擬目錄](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1336">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="5a379-1337">應用程式能以[子應用程式](#sub-applications)的形式裝載。</span><span class="sxs-lookup"><span data-stu-id="5a379-1337">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="5a379-1338">子應用程式</span><span class="sxs-lookup"><span data-stu-id="5a379-1338">Sub-applications</span></span>

<span data-ttu-id="5a379-1339">ASP.NET Core 應用程式能以 [IIS 子應用程式](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)的形式裝載。</span><span class="sxs-lookup"><span data-stu-id="5a379-1339">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="5a379-1340">子應用程式的路徑會成為根應用程式 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="5a379-1340">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="5a379-1341">子應用程式不應該包括 ASP.NET Core 模組做為處理常式。</span><span class="sxs-lookup"><span data-stu-id="5a379-1341">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="5a379-1342">如果您在子應用程式的 *web.config* 檔案中將模組新增為處理常式，則嘗試瀏覽子應用程式時，會收到參考錯誤設定檔的 *500.19 內部伺服器錯誤*。</span><span class="sxs-lookup"><span data-stu-id="5a379-1342">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="5a379-1343">下列範例顯示 ASP.NET Core 子應用程式的已發佈 *web.config* 檔案：</span><span class="sxs-lookup"><span data-stu-id="5a379-1343">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="5a379-1344">在 ASP.NET Core 應用程式下裝載非 ASP.NET Core 子應用程式時，請明確移除子應用程式 *web.config* 檔案中已繼承的處理常式：</span><span class="sxs-lookup"><span data-stu-id="5a379-1344">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="5a379-1345">子應用程式內的靜態資產連結應該使用波狀符號與斜線 (`~/`) 標記法。</span><span class="sxs-lookup"><span data-stu-id="5a379-1345">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="5a379-1346">波狀符號與斜線標記法會觸發[標記協助程式](xref:mvc/views/tag-helpers/intro)以將子應用程式的路徑基底附加到轉譯的相對連結前面。</span><span class="sxs-lookup"><span data-stu-id="5a379-1346">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="5a379-1347">針對位於 `/subapp_path` 的子應用程式，使用 `src="~/image.png"` 連結的影像會轉譯為 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="5a379-1347">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="5a379-1348">根應用程式的靜態檔案中介軟體不會處理靜態檔案要求。</span><span class="sxs-lookup"><span data-stu-id="5a379-1348">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="5a379-1349">要求會由子應用程式的靜態檔案中介軟體處理。</span><span class="sxs-lookup"><span data-stu-id="5a379-1349">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="5a379-1350">若靜態資產的 `src` 屬性是設定為絕對路徑 (例如，`src="/image.png"`)，會以不使用子應用程式路徑基底的方式轉譯連結。</span><span class="sxs-lookup"><span data-stu-id="5a379-1350">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="5a379-1351">根應用程式的靜態檔案中介軟體會嘗試從根應用程式的 [webroot](xref:fundamentals/index#web-root) 提供資產，這會導致「404 - 找不到」回應 (除非根應用程式可存取靜態資產)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1351">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="5a379-1352">裝載 ASP.NET Core 應用程式做為另一個 ASP.NET Core 應用程式下的子應用程式：</span><span class="sxs-lookup"><span data-stu-id="5a379-1352">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="5a379-1353">為子應用程式建立應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-1353">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="5a379-1354">將 [.NET CLR 版本] 設定為 [沒有受控碼]，因為會將核心通用語言執行平台 (CoreCLR) 開機以在背景工作處理序中裝載應用程式，而非在桌面 CLR (.NET CLR) 中裝載。</span><span class="sxs-lookup"><span data-stu-id="5a379-1354">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="5a379-1355">使用根網站下資料夾中的子應用程式在 IIS 管理員中新增根網站。</span><span class="sxs-lookup"><span data-stu-id="5a379-1355">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="5a379-1356">以滑鼠右鍵按一下 IIS 管理員中的子應用程式資料夾，然後選取 [轉換成應用程式]。</span><span class="sxs-lookup"><span data-stu-id="5a379-1356">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="5a379-1357">在 [新增應用程式] 對話方塊中，使用 [應用程式集區] 的[選取] 按鈕來指派您為子應用程式建立的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-1357">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="5a379-1358">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-1358">Select **OK**.</span></span>

<span data-ttu-id="5a379-1359">將不同的應用程式集區指派給子應用程式是使用同處理序裝載模型。</span><span class="sxs-lookup"><span data-stu-id="5a379-1359">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="5a379-1360">如需有關同進程裝載模型和設定 ASP.NET 核心模組的詳細資訊，請參閱 <xref:host-and-deploy/aspnet-core-module> 。</span><span class="sxs-lookup"><span data-stu-id="5a379-1360">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="5a379-1361">使用 web.config 的 IIS 組態</span><span class="sxs-lookup"><span data-stu-id="5a379-1361">Configuration of IIS with web.config</span></span>

<span data-ttu-id="5a379-1362">在對使用了 ASP.NET Core 模組的 ASP.NET Core 有作用的 IIS 情境下，設定會受 *web.config* 的 `<system.webServer>` 區段影響。</span><span class="sxs-lookup"><span data-stu-id="5a379-1362">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="5a379-1363">舉例來說，IIS 設定對動態壓縮有作用。</span><span class="sxs-lookup"><span data-stu-id="5a379-1363">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="5a379-1364">如果在伺服器層級將 IIS 設為使用動態壓縮，應用程式 *web.config* 檔案中的 `<urlCompression>` 元素則可為 ASP.NET Core 應用程式予以停用。</span><span class="sxs-lookup"><span data-stu-id="5a379-1364">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="5a379-1365">如需詳細資訊，請參閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="5a379-1365">For more information, see the following topics:</span></span>

* [<span data-ttu-id="5a379-1366">的設定參考 \<system.webServer></span><span class="sxs-lookup"><span data-stu-id="5a379-1366">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="5a379-1367">若要為在隔離的應用程式集區中執行的個別應用程式設定環境變數 (IIS 10.0 或更新版本的) 支援，請參閱 IIS 參考檔中 [環境變數 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe)主題的 *AppCmd.exe 命令* 部分。</span><span class="sxs-lookup"><span data-stu-id="5a379-1367">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="5a379-1368">web.config 的組態區段</span><span class="sxs-lookup"><span data-stu-id="5a379-1368">Configuration sections of web.config</span></span>

<span data-ttu-id="5a379-1369">ASP.NET Core 應用程式的設定不使用 *web.config* 中 ASP.NET 4.x 應用程式的設定區段：</span><span class="sxs-lookup"><span data-stu-id="5a379-1369">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="5a379-1370">使用其他組態提供者設定的 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-1370">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="5a379-1371">如需詳細資訊，請參閱 [Configuration](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1371">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="5a379-1372">應用程式集區</span><span class="sxs-lookup"><span data-stu-id="5a379-1372">Application Pools</span></span>

<span data-ttu-id="5a379-1373">在伺服器上裝載多個網站時，建議您在其各自的應用程式集區中執行各個應用程式，讓應用程式彼此隔離。</span><span class="sxs-lookup"><span data-stu-id="5a379-1373">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="5a379-1374">IIS [新增網站] 對話方塊預設成此組態。</span><span class="sxs-lookup"><span data-stu-id="5a379-1374">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="5a379-1375">當提供 **網站名稱** 時，文字會自動轉移至 [應用程式集區] 文字方塊。</span><span class="sxs-lookup"><span data-stu-id="5a379-1375">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="5a379-1376">新增網站時，會使用該網站名稱建立新的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5a379-1376">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="5a379-1377">應用程式集區 Identity</span><span class="sxs-lookup"><span data-stu-id="5a379-1377">Application Pool Identity</span></span>

<span data-ttu-id="5a379-1378">應用程式集區身分識別帳戶可讓應用程式在唯一的帳戶下執行，不必建立及管理網域或本機帳戶。</span><span class="sxs-lookup"><span data-stu-id="5a379-1378">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="5a379-1379">在 IIS 8.0 或更新版本中，IIS 管理背景工作處理序 (WAS) 會使用新的應用程式集區名稱建立虛擬帳戶，並預設在此帳戶下執行應用程式集區的背景工作處理序。</span><span class="sxs-lookup"><span data-stu-id="5a379-1379">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="5a379-1380">在 [IIS 管理主控台] 中，于應用程式集區的 [ **Advanced Settings** ] 底下，確定 **Identity** 已將設定為 [使用 **Identity ApplicationPool**：</span><span class="sxs-lookup"><span data-stu-id="5a379-1380">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![應用程式集區進階設定對話方塊](index/_static/apppool-identity.png)

<span data-ttu-id="5a379-1382">IIS 管理程序會在 Windows 安全系統中，以應用程式集區的名稱建立安全識別碼。</span><span class="sxs-lookup"><span data-stu-id="5a379-1382">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="5a379-1383">可使用此身分識別來保護資源。</span><span class="sxs-lookup"><span data-stu-id="5a379-1383">Resources can be secured using this identity.</span></span> <span data-ttu-id="5a379-1384">不過，此身分識別不是真正的使用者帳戶，也不會顯示在 Windows 使用者管理主控台中。</span><span class="sxs-lookup"><span data-stu-id="5a379-1384">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="5a379-1385">如果 IIS 背景工作處理序需要提升應用程式的存取權限，請修改包含應用程式的目錄存取控制清單 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="5a379-1385">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="5a379-1386">開啟 Windows 檔案總管，巡覽至目錄。</span><span class="sxs-lookup"><span data-stu-id="5a379-1386">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="5a379-1387">以滑鼠右鍵按一下目錄並選取 [屬性]。</span><span class="sxs-lookup"><span data-stu-id="5a379-1387">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="5a379-1388">依序選取 [安全性] 索引標籤下的 [編輯] 按鈕和 [新增] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="5a379-1388">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="5a379-1389">選取 [位置] 按鈕，並確定選取系統。</span><span class="sxs-lookup"><span data-stu-id="5a379-1389">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="5a379-1390">在 [輸入要選取的物件名稱] 區域中，輸入 **IIS AppPool\\<app_pool_name>**。</span><span class="sxs-lookup"><span data-stu-id="5a379-1390">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="5a379-1391">選取 [檢查名稱] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="5a379-1391">Select the **Check Names** button.</span></span> <span data-ttu-id="5a379-1392">針對 [DefaultAppPool]，請使用 **IIS AppPool\DefaultAppPool** 檢查名稱。</span><span class="sxs-lookup"><span data-stu-id="5a379-1392">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="5a379-1393">選取 [檢查名稱] 按鈕時，[DefaultAppPool] 的值便會顯示於物件名稱區域中。</span><span class="sxs-lookup"><span data-stu-id="5a379-1393">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="5a379-1394">您無法直接將應用程式集區名稱輸入至物件名稱區域。</span><span class="sxs-lookup"><span data-stu-id="5a379-1394">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="5a379-1395">檢查物件名稱時，請使用 **IIS AppPool\\<app_pool_name>** 的格式。</span><span class="sxs-lookup"><span data-stu-id="5a379-1395">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![針對應用程式資料夾選取使用者或群組對話方塊：在選取 [檢查名稱] 之前，"DefaultAppPool" 這個應用程式集區名稱在物件名稱區域中會附加至 "IIS AppPool\"。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="5a379-1397">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="5a379-1397">Select **OK**.</span></span>

   ![針對應用程式資料夾選取使用者或群組對話方塊：選取 [檢查名稱] 之後，物件名稱 "DefaultAppPool" 會顯示在物件名稱區域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="5a379-1399">預設應該會授與讀取 &amp; 執行權限。</span><span class="sxs-lookup"><span data-stu-id="5a379-1399">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="5a379-1400">請視需要提供其他權限。</span><span class="sxs-lookup"><span data-stu-id="5a379-1400">Provide additional permissions as needed.</span></span>

<span data-ttu-id="5a379-1401">也可使用 **ICACLS** 工具透過命令提示字元授與存取權限。</span><span class="sxs-lookup"><span data-stu-id="5a379-1401">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="5a379-1402">使用 *DefaultAppPool* 作為範例，將會使用下列命令：</span><span class="sxs-lookup"><span data-stu-id="5a379-1402">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="5a379-1403">如需詳細資訊，請參閱 [icacls](/windows-server/administration/windows-commands/icacls) 主題。</span><span class="sxs-lookup"><span data-stu-id="5a379-1403">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="5a379-1404">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="5a379-1404">HTTP/2 support</span></span>

<span data-ttu-id="5a379-1405">[HTTP/2](https://httpwg.org/specs/rfc7540.html) 支援符合下列基本需求的跨處理序部署：</span><span class="sxs-lookup"><span data-stu-id="5a379-1405">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="5a379-1406">Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5a379-1406">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="5a379-1407">公開 Edge Server 連線使用 HTTP/2，但是對 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)的反向 Proxy 連線使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="5a379-1407">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="5a379-1408">目標 Framework：不適用於跨處理序部署，因為 HTTP/2 連線完全由 IIS 處理。</span><span class="sxs-lookup"><span data-stu-id="5a379-1408">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="5a379-1409">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="5a379-1409">TLS 1.2 or later connection</span></span>

<span data-ttu-id="5a379-1410">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="5a379-1410">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="5a379-1411">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="5a379-1411">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="5a379-1412">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="5a379-1412">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="5a379-1413">如需使用 IIS 部署之 HTTP/2 設定的詳細資訊，請參閱 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1413">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="5a379-1414">CORS 預檢要求</span><span class="sxs-lookup"><span data-stu-id="5a379-1414">CORS preflight requests</span></span>

<span data-ttu-id="5a379-1415">*此節只適用於以 .NET Framework 為目標的 ASP.NET Core 應用程式。*</span><span class="sxs-lookup"><span data-stu-id="5a379-1415">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="5a379-1416">針對以 .NET Framework 為目標的 ASP.NET Core 應用程式，在 IIS 中OPTIONS 要求預設不會傳遞到應用程式。</span><span class="sxs-lookup"><span data-stu-id="5a379-1416">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="5a379-1417">若要瞭解如何在 *web.config* 中設定應用程式的 IIS 處理常式來傳遞選項要求，請參閱 [在 ASP.NET Web API 2 中啟用跨原始來源要求： CORS 的運作方式](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="5a379-1417">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="5a379-1418">IIS 系統管理員的部署資源</span><span class="sxs-lookup"><span data-stu-id="5a379-1418">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="5a379-1419">IIS 文件</span><span class="sxs-lookup"><span data-stu-id="5a379-1419">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="5a379-1420">IIS 中的 IIS 管理員使用者入門</span><span class="sxs-lookup"><span data-stu-id="5a379-1420">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="5a379-1421">.NET Core 應用程式部署</span><span class="sxs-lookup"><span data-stu-id="5a379-1421">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="5a379-1422">其他資源</span><span class="sxs-lookup"><span data-stu-id="5a379-1422">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="5a379-1423">Microsoft IIS 官方網站</span><span class="sxs-lookup"><span data-stu-id="5a379-1423">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="5a379-1424">Windows Server 技術內容庫</span><span class="sxs-lookup"><span data-stu-id="5a379-1424">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="5a379-1425">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="5a379-1425">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
