---
title: 在使用 IIS 的 Windows 上裝載 ASP.NET Core
author: rick-anderson
description: 了解如何在 Windows Server Internet Information Services (IIS) 上裝載 ASP.NET Core 應用程式。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/index
ms.openlocfilehash: f648837ce42bef4a828d7eda1a6abdfdd8ac07a2
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654032"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="5c365-103">在使用 IIS 的 Windows 上裝載 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5c365-103">Host ASP.NET Core on Windows with IIS</span></span>

<!-- 

    NOTE FOR 5.0
    
    When making the 5.0 version of this topic, remove the Hosting Bundle
    direct download section from the (new) <5.0 & >2.2 version and modify 
    the text and heading for the *Earlier versions of the installer* 
    section. See the 2.2 version for an example.
    
-->

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5c365-104">如需將 ASP.NET Core 應用程式發佈至 IIS 伺服器的教學課程體驗，請參閱<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="5c365-104">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="5c365-105">安裝 .NET Core 裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="5c365-105">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="5c365-106">支援的作業系統</span><span class="sxs-lookup"><span data-stu-id="5c365-106">Supported operating systems</span></span>

<span data-ttu-id="5c365-107">以下為支援的作業系統：</span><span class="sxs-lookup"><span data-stu-id="5c365-107">The following operating systems are supported:</span></span>

* <span data-ttu-id="5c365-108">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5c365-108">Windows 7 or later</span></span>
* <span data-ttu-id="5c365-109">Windows Server 2012 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5c365-109">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="5c365-110">[HTTP.sys 伺服器](xref:fundamentals/servers/httpsys) (先前稱為 WebListener) 不適用搭配 IIS 的反向 Proxy 設定。</span><span class="sxs-lookup"><span data-stu-id="5c365-110">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="5c365-111">請使用 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="5c365-111">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="5c365-112">如需在 Azure 中裝載的資訊，請參閱 <xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="5c365-112">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="5c365-113">如需疑難排解指引，請參閱 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="5c365-113">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="5c365-114">支援的平台</span><span class="sxs-lookup"><span data-stu-id="5c365-114">Supported platforms</span></span>

<span data-ttu-id="5c365-115">支援針對 32 位元 (x86) 或 64 位元 (x64) 部署發行的應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-115">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="5c365-116">使用 32 位元 (x86) .NET Core SDK 部署 32 位元應用程式，除非應用程式：</span><span class="sxs-lookup"><span data-stu-id="5c365-116">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="5c365-117">需要提供給 64 位元應用程式使用的較大虛擬記憶體位址空間。</span><span class="sxs-lookup"><span data-stu-id="5c365-117">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="5c365-118">需要較大的 IIS 堆疊大小。</span><span class="sxs-lookup"><span data-stu-id="5c365-118">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="5c365-119">有 64 位元原生相依性。</span><span class="sxs-lookup"><span data-stu-id="5c365-119">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="5c365-120">針對32位 (x86) 發佈的應用程式必須為其 IIS 應用程式集區啟用32位。</span><span class="sxs-lookup"><span data-stu-id="5c365-120">Apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="5c365-121">如需詳細資訊，請參閱 [建立 IIS 網站](#create-the-iis-site) 一節。</span><span class="sxs-lookup"><span data-stu-id="5c365-121">For more information, see the [Create the IIS site](#create-the-iis-site) section.</span></span>

<span data-ttu-id="5c365-122">使用 64 位元 (x64) .NET Core SDK 來發行 64 位元應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-122">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="5c365-123">主機系統上必須有 64 位元執行階段存在。</span><span class="sxs-lookup"><span data-stu-id="5c365-123">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="5c365-124">裝載模型</span><span class="sxs-lookup"><span data-stu-id="5c365-124">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="5c365-125">同處理序裝載模型</span><span class="sxs-lookup"><span data-stu-id="5c365-125">In-process hosting model</span></span>

<span data-ttu-id="5c365-126">使用同處理序裝載，ASP.NET Core 應用程式會在與其 IIS 工作者處理序相同的處理序中執行。</span><span class="sxs-lookup"><span data-stu-id="5c365-126">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="5c365-127">因為要求未透過回送介面卡 (將連出網路流量傳回同一部電腦的網路介面) 進行 proxy 處理，所以同處理序裝載會提供優於跨處理序裝載的效能。</span><span class="sxs-lookup"><span data-stu-id="5c365-127">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="5c365-128">IIS 透過 [Windows 處理序啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 來執行處理程序管理。</span><span class="sxs-lookup"><span data-stu-id="5c365-128">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="5c365-129">[ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)：</span><span class="sxs-lookup"><span data-stu-id="5c365-129">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="5c365-130">執行應用程式初始化。</span><span class="sxs-lookup"><span data-stu-id="5c365-130">Performs app initialization.</span></span>
  * <span data-ttu-id="5c365-131">載入 [CoreCLR](/dotnet/standard/glossary#coreclr)。</span><span class="sxs-lookup"><span data-stu-id="5c365-131">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="5c365-132">呼叫 `Program.Main`。</span><span class="sxs-lookup"><span data-stu-id="5c365-132">Calls `Program.Main`.</span></span>
* <span data-ttu-id="5c365-133">處理 IIS 原生要求的存留期。</span><span class="sxs-lookup"><span data-stu-id="5c365-133">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="5c365-134">下圖說明 IIS、ASP.NET Core 模組和同處理序裝載應用程式之間的關聯性：</span><span class="sxs-lookup"><span data-stu-id="5c365-134">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![同處理序代管內的 ASP.NET Core 模組案例](index/_static/ancm-inprocess.png)

1. <span data-ttu-id="5c365-136">要求會從 Web 到達核心模式的 HTTP.sys 驅動程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-136">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="5c365-137">驅動程式會在網站設定的連接埠上將原生要求路由至 IIS，此連接埠通常是 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="5c365-137">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="5c365-138">ASP.NET Core 模組會接收原生要求，並將它傳遞給 IIS HTTP 伺服器 (`IISHttpServer`) 。</span><span class="sxs-lookup"><span data-stu-id="5c365-138">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="5c365-139">IIS HTTP 伺服器是 IIS 的同處理序伺服程式實作，可將要求從原生轉換為受控。</span><span class="sxs-lookup"><span data-stu-id="5c365-139">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="5c365-140">IIS HTTP 伺服器處理要求之後：</span><span class="sxs-lookup"><span data-stu-id="5c365-140">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="5c365-141">要求會傳送至 ASP.NET Core 中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="5c365-141">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="5c365-142">中介軟體管線會處理要求，並將其作為 `HttpContext` 執行個體傳遞至應用程式的邏輯。</span><span class="sxs-lookup"><span data-stu-id="5c365-142">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="5c365-143">應用程式的回應會透過 IIS HTTP 伺服器傳回 IIS。</span><span class="sxs-lookup"><span data-stu-id="5c365-143">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="5c365-144">IIS 會將回應傳送到起始該要求的用戶端。</span><span class="sxs-lookup"><span data-stu-id="5c365-144">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="5c365-145">內含式裝載可加入現有的應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-145">In-process hosting is opt-in for existing apps.</span></span> <span data-ttu-id="5c365-146">ASP.NET Core 的 web 範本會使用同進程裝載模型。</span><span class="sxs-lookup"><span data-stu-id="5c365-146">The ASP.NET Core web templates use the in-process hosting model.</span></span>

<span data-ttu-id="5c365-147">`CreateDefaultBuilder` 會透過呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 方法來啟動 [CoreCLR](/dotnet/standard/glossary#coreclr) 以新增 <xref:Microsoft.AspNetCore.Hosting.Server.IServer>，並在 IIS 工作者處理序 (*w3wp.exe* 或 *iisexpress.exe*) 內裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-147">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="5c365-148">效能測試指出，相較於將 .NET Core 應用程式裝載於處理序外，並將要求 Proxy 處理至 [Kestrel](xref:fundamentals/servers/kestrel)，裝載於處理序內可提供明顯更高的要求輸送量。</span><span class="sxs-lookup"><span data-stu-id="5c365-148">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="5c365-149">發佈為單一檔案可執行檔的應用程式無法由同處理序裝載模型載入。</span><span class="sxs-lookup"><span data-stu-id="5c365-149">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="5c365-150">跨處理序裝載模型</span><span class="sxs-lookup"><span data-stu-id="5c365-150">Out-of-process hosting model</span></span>

<span data-ttu-id="5c365-151">由於 ASP.NET Core apps 會在與 IIS 背景工作進程不同的進程中執行，因此 ASP.NET Core 模組會處理進程管理。</span><span class="sxs-lookup"><span data-stu-id="5c365-151">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="5c365-152">此模組會在第一個要求到達時啟動 ASP.NET Core 應用程式的處理序，並在應用程式關閉或損毀時將它重新啟動。</span><span class="sxs-lookup"><span data-stu-id="5c365-152">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="5c365-153">此行為基本上與執行同處理序，並由 [Windows 處理序啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 所管理的應用程式相同。</span><span class="sxs-lookup"><span data-stu-id="5c365-153">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="5c365-154">下圖說明 IIS、ASP.NET Core 模組和跨處理序裝載應用程式之間的關聯性：</span><span class="sxs-lookup"><span data-stu-id="5c365-154">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![非同處理序代管內的 ASP.NET Core 模組案例](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="5c365-156">要求會從 Web 到達核心模式的 HTTP.sys 驅動程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-156">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="5c365-157">驅動程式會在網站設定的埠上將要求路由傳送至 IIS。</span><span class="sxs-lookup"><span data-stu-id="5c365-157">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="5c365-158">設定的埠通常是 80 (HTTP) 或 443 (HTTPS) 。</span><span class="sxs-lookup"><span data-stu-id="5c365-158">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="5c365-159">模組會將要求轉送到應用程式的隨機埠上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="5c365-159">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="5c365-160">隨機埠不是80或443。</span><span class="sxs-lookup"><span data-stu-id="5c365-160">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="5c365-161">ASP.NET Core 模組會在啟動時透過環境變數指定埠。</span><span class="sxs-lookup"><span data-stu-id="5c365-161">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="5c365-162">延伸模組會設定 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 要接聽的伺服器 `http://localhost:{PORT}` 。</span><span class="sxs-lookup"><span data-stu-id="5c365-162">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="5c365-163">將會執行額外檢查，不是源自模組的要求都會遭到拒絕。</span><span class="sxs-lookup"><span data-stu-id="5c365-163">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="5c365-164">模組不支援 HTTPS 轉送。</span><span class="sxs-lookup"><span data-stu-id="5c365-164">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="5c365-165">即使 IIS 透過 HTTPS 接收，要求還是會透過 HTTP 轉送。</span><span class="sxs-lookup"><span data-stu-id="5c365-165">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="5c365-166">在 Kestrel 收取來自模組的要求之後，會將要求轉送到 ASP.NET Core 中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="5c365-166">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="5c365-167">中介軟體管線會處理要求，並將其作為 `HttpContext` 執行個體傳遞至應用程式的邏輯。</span><span class="sxs-lookup"><span data-stu-id="5c365-167">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="5c365-168">IIS Integration 新增的中介軟體會更新配置、遠端 IP 和帳戶路徑基底，以將要求轉送至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="5c365-168">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="5c365-169">應用程式的回應會傳回 IIS，然後將其轉送回起始要求的 HTTP 用戶端。</span><span class="sxs-lookup"><span data-stu-id="5c365-169">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="5c365-170">如需 ASP.NET Core 模組組態指南，請參閱 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="5c365-170">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="5c365-171">如需代管的詳細資訊，請參閱[在 ASP.NET Core 中代管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="5c365-171">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="5c365-172">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="5c365-172">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="5c365-173">啟用 IISIntegration 元件</span><span class="sxs-lookup"><span data-stu-id="5c365-173">Enable the IISIntegration components</span></span>

<span data-ttu-id="5c365-174">在 `CreateHostBuilder` (*Program.cs*) 中建立主機時，請呼叫 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 來啟用 IIS 整合：</span><span class="sxs-lookup"><span data-stu-id="5c365-174">When building a host in `CreateHostBuilder` (*Program.cs*), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="5c365-175">如需 `CreateDefaultBuilder` 的詳細資訊，請參閱 <xref:fundamentals/host/generic-host#default-builder-settings>。</span><span class="sxs-lookup"><span data-stu-id="5c365-175">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="5c365-176">IIS 選項</span><span class="sxs-lookup"><span data-stu-id="5c365-176">IIS options</span></span>

<span data-ttu-id="5c365-177">**同處理序主控模型**</span><span class="sxs-lookup"><span data-stu-id="5c365-177">**In-process hosting model**</span></span>

<span data-ttu-id="5c365-178">若要設定 IIS 伺服器選項，請在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中加入 <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 的服務設定。</span><span class="sxs-lookup"><span data-stu-id="5c365-178">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="5c365-179">下列範例會停用 AutomaticAuthentication：</span><span class="sxs-lookup"><span data-stu-id="5c365-179">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="5c365-180">選項</span><span class="sxs-lookup"><span data-stu-id="5c365-180">Option</span></span>                         | <span data-ttu-id="5c365-181">預設</span><span class="sxs-lookup"><span data-stu-id="5c365-181">Default</span></span> | <span data-ttu-id="5c365-182">設定</span><span class="sxs-lookup"><span data-stu-id="5c365-182">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="5c365-183">若為 `true`，IIS 伺服器會設定由 [Windows 驗證](xref:security/authentication/windowsauth)所驗證的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="5c365-183">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="5c365-184">若為 `false`，則伺服器僅會對 `HttpContext.User` 提供身分識別，並在 `AuthenticationScheme` 明確要求時回應挑戰。</span><span class="sxs-lookup"><span data-stu-id="5c365-184">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="5c365-185">必須在 IIS 中啟用 Windows 驗證以讓 `AutomaticAuthentication` 作用。</span><span class="sxs-lookup"><span data-stu-id="5c365-185">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="5c365-186">如需詳細資訊，請參閱 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="5c365-186">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="5c365-187">設定使用者在登入頁面上看到的顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="5c365-187">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO`           | `false` | <span data-ttu-id="5c365-188">和是否允許同步 i/o `HttpContext.Request` `HttpContext.Response` 。</span><span class="sxs-lookup"><span data-stu-id="5c365-188">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize`           | `30000000`  | <span data-ttu-id="5c365-189">取得或設定 `HttpRequest` 的要求本文大小上限。</span><span class="sxs-lookup"><span data-stu-id="5c365-189">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="5c365-190">請注意，IIS 本身具有限制 `maxAllowedContentLength`，此限制將在 `IISServerOptions` 中設定 `MaxRequestBodySize` 時處理。</span><span class="sxs-lookup"><span data-stu-id="5c365-190">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="5c365-191">變更 `MaxRequestBodySize` 將不會影響 `maxAllowedContentLength`。</span><span class="sxs-lookup"><span data-stu-id="5c365-191">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="5c365-192">若要增加 `maxAllowedContentLength`，請在 *web.config* 中新增項目，以將 `maxAllowedContentLength` 設定為較高的值。</span><span class="sxs-lookup"><span data-stu-id="5c365-192">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="5c365-193">如需更多詳細資料，請參閱[組態](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)。</span><span class="sxs-lookup"><span data-stu-id="5c365-193">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="5c365-194">**跨處理序裝載模型**</span><span class="sxs-lookup"><span data-stu-id="5c365-194">**Out-of-process hosting model**</span></span>

<span data-ttu-id="5c365-195">若要設定 IIS 選項，請在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中加入 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服務設定。</span><span class="sxs-lookup"><span data-stu-id="5c365-195">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="5c365-196">下列範例會防止應用程式填入 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="5c365-196">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="5c365-197">選項</span><span class="sxs-lookup"><span data-stu-id="5c365-197">Option</span></span>                         | <span data-ttu-id="5c365-198">預設</span><span class="sxs-lookup"><span data-stu-id="5c365-198">Default</span></span> | <span data-ttu-id="5c365-199">設定</span><span class="sxs-lookup"><span data-stu-id="5c365-199">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="5c365-200">若為 `true`，[IIS 整合中介軟體](#enable-the-iisintegration-components)會設定由 [Windows 驗證](xref:security/authentication/windowsauth)所驗證的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="5c365-200">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="5c365-201">如果為 `false`，則驗證中介軟體僅針對 `HttpContext.User` 提供身分識別，並在游 `AuthenticationScheme` 提出明確要求時回應挑戰。</span><span class="sxs-lookup"><span data-stu-id="5c365-201">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="5c365-202">必須在 IIS 中啟用 Windows 驗證以讓 `AutomaticAuthentication` 作用。</span><span class="sxs-lookup"><span data-stu-id="5c365-202">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="5c365-203">如需詳細資訊，請參閱 [Windows 驗證](xref:security/authentication/windowsauth)主題。</span><span class="sxs-lookup"><span data-stu-id="5c365-203">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="5c365-204">設定使用者在登入頁面上看到的顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="5c365-204">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="5c365-205">如果為 `true` 且 `MS-ASPNETCORE-CLIENTCERT` 要求標頭已存在，則會填入 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="5c365-205">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="5c365-206">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="5c365-206">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="5c365-207">[IIS 整合中介軟體](#enable-the-iisintegration-components)和 ASP.NET Core 模組會設定為轉寄：</span><span class="sxs-lookup"><span data-stu-id="5c365-207">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="5c365-208"> (HTTP/HTTPS) 的配置。</span><span class="sxs-lookup"><span data-stu-id="5c365-208">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="5c365-209">發出要求的遠端 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="5c365-209">Remote IP address where the request originated.</span></span>

<span data-ttu-id="5c365-210">[IIS 整合中介軟體](#enable-the-iisintegration-components)會設定轉送的標頭中介軟體。</span><span class="sxs-lookup"><span data-stu-id="5c365-210">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="5c365-211">其他 Proxy 伺服器和負載平衡器後方託管的應用程式可能需要其他設定。</span><span class="sxs-lookup"><span data-stu-id="5c365-211">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="5c365-212">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="5c365-212">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="5c365-213">web.config 檔案</span><span class="sxs-lookup"><span data-stu-id="5c365-213">web.config file</span></span>

<span data-ttu-id="5c365-214">*web.config* 檔案是用來設定 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="5c365-214">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="5c365-215">發佈專案時，由 MSBuild 目標 (`_TransformWebConfig`) 處理 *web.config* 檔案的建立、轉換及發佈。</span><span class="sxs-lookup"><span data-stu-id="5c365-215">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="5c365-216">此目標存在於 Web SDK 目標 (`Microsoft.NET.Sdk.Web`)。</span><span class="sxs-lookup"><span data-stu-id="5c365-216">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="5c365-217">SDK 設定在專案檔的頂端：</span><span class="sxs-lookup"><span data-stu-id="5c365-217">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="5c365-218">如果專案中沒有 *web.config* 檔案，則系統會使用正確的 *processPath* 和 *arguments* 建立該檔案以設定 ASP.NET Core 模組，並將該檔案移至[已發行的輸出](xref:host-and-deploy/directory-structure)。</span><span class="sxs-lookup"><span data-stu-id="5c365-218">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="5c365-219">如果 *web.config* 檔案存在於專案中，則系統會使用正確的 *processPath* 和 *arguments* 來轉換該檔案以設定 ASP.NET Core 模組，然後將它移至已發行的輸出。</span><span class="sxs-lookup"><span data-stu-id="5c365-219">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="5c365-220">轉換不會修改檔案中的 IIS 組態設定。</span><span class="sxs-lookup"><span data-stu-id="5c365-220">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="5c365-221">*web.config* 檔案可提供能控制作用中 IIS 模組的額外 IIS 組態設定。</span><span class="sxs-lookup"><span data-stu-id="5c365-221">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="5c365-222">如需能處理 ASP.NET Core 應用程式要求之 IIS 模組的相關資訊，請參閱 [IIS 模組](xref:host-and-deploy/iis/modules)主題。</span><span class="sxs-lookup"><span data-stu-id="5c365-222">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="5c365-223">若要防止 Web SDK 轉換 *web.config* 檔案，請使用專案檔 **\<IsTransformWebConfigDisabled>** 中的屬性：</span><span class="sxs-lookup"><span data-stu-id="5c365-223">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="5c365-224">使 Web SDK 無法轉換檔案時，應該由開發人員手動設定 *processPath* 和 *arguments*。</span><span class="sxs-lookup"><span data-stu-id="5c365-224">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="5c365-225">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="5c365-225">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="5c365-226">web.config 檔案位置</span><span class="sxs-lookup"><span data-stu-id="5c365-226">web.config file location</span></span>

<span data-ttu-id="5c365-227">為了正確設定 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module) ， *web.config* 檔案必須存在於 [內容根](xref:fundamentals/index#content-root) 路徑 (通常是已部署應用程式) 的應用程式基底路徑。</span><span class="sxs-lookup"><span data-stu-id="5c365-227">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="5c365-228">這是與提供給 IIS 的網站實體路徑相同的位置。</span><span class="sxs-lookup"><span data-stu-id="5c365-228">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="5c365-229">應用程式的根目錄需有 *web.config* 檔案，才能使用 Web Deploy 發行多個應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-229">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="5c365-230">機密檔案存在於應用程式的實體路徑上，例如\* \<assembly>.runtimeconfig.json*、 \* \<assembly> .xml* (xml 檔批註) 以及\* \<assembly>.deps.js\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-230">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="5c365-231">當 *web.config* 檔案存在且網站正常啟動時，如果有人要求機密檔案，IIS 不會予以提供。</span><span class="sxs-lookup"><span data-stu-id="5c365-231">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="5c365-232">若 *web.config* 檔案遺失或沒有正確命名，或是無法設定網站以正常啟動，IIS 可能會公開提供機密檔案。</span><span class="sxs-lookup"><span data-stu-id="5c365-232">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="5c365-233">\***web.config*檔案必須在部署中隨時都存在、正確命名，而且能夠設定網站以正常啟動。請勿從生產環境部署中移除*web.config\*的檔案。**</span><span class="sxs-lookup"><span data-stu-id="5c365-233">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="5c365-234">轉換 web.config</span><span class="sxs-lookup"><span data-stu-id="5c365-234">Transform web.config</span></span>

<span data-ttu-id="5c365-235">如果您需要在發佈時轉換 *web.config* ，請參閱 <xref:host-and-deploy/iis/transform-webconfig> 。</span><span class="sxs-lookup"><span data-stu-id="5c365-235">If you need to transform *web.config* on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="5c365-236">您可能需要根據設定、設定檔或環境，在 [發行] 上轉換 *web.config* 來設定環境變數。</span><span class="sxs-lookup"><span data-stu-id="5c365-236">You might need to transform *web.config* on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="5c365-237">IIS 組態</span><span class="sxs-lookup"><span data-stu-id="5c365-237">IIS configuration</span></span>

<span data-ttu-id="5c365-238">**Windows Server 作業系統**</span><span class="sxs-lookup"><span data-stu-id="5c365-238">**Windows Server operating systems**</span></span>

<span data-ttu-id="5c365-239">啟用**網頁伺服器 (IIS)** 伺服器角色，並建立角色服務。</span><span class="sxs-lookup"><span data-stu-id="5c365-239">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="5c365-240">使用來自 [管理]\*\*\*\* 功能表的 [新增角色及功能]\*\*\*\* 精靈，或是 [伺服器管理員]\*\*\*\* 中的連結。</span><span class="sxs-lookup"><span data-stu-id="5c365-240">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="5c365-241">在**伺服器角色**步驟中，核取 [網頁伺服器 (IIS)]\*\*\*\* 方塊。</span><span class="sxs-lookup"><span data-stu-id="5c365-241">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在選取伺服器角色步驟中選取網頁伺服器 IIS 角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="5c365-243">在 [功能]\*\*\*\* 步驟之後，[角色服務]\*\*\*\* 步驟會針對網頁伺服器 (IIS) 進行載入。</span><span class="sxs-lookup"><span data-stu-id="5c365-243">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="5c365-244">選取所需的 IIS 角色服務或接受所提供的預設角色服務。</span><span class="sxs-lookup"><span data-stu-id="5c365-244">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在選取角色服務步驟中，選取預設的角色服務。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="5c365-246">**Windows 驗證 (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5c365-246">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="5c365-247">若要啟用 Windows 驗證，請展開下列節點：**網頁伺服器**  >  **安全性**。</span><span class="sxs-lookup"><span data-stu-id="5c365-247">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="5c365-248">選取 [Windows 驗證]\*\*\*\* 功能。</span><span class="sxs-lookup"><span data-stu-id="5c365-248">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="5c365-249">如需詳細資訊，請參閱[Windows 驗證 \<windowsAuthentication> ](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)和[設定 Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="5c365-249">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="5c365-250">**WebSocket (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5c365-250">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="5c365-251">WebSocket 由 ASP.NET Core 1.1 或更新版本所支援。</span><span class="sxs-lookup"><span data-stu-id="5c365-251">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="5c365-252">若要啟用 websocket，請展開下列節點：**網頁伺服器**  >  **應用程式開發**。</span><span class="sxs-lookup"><span data-stu-id="5c365-252">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="5c365-253">選取 [WebSocket 通訊協定]\*\*\*\* 功能。</span><span class="sxs-lookup"><span data-stu-id="5c365-253">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="5c365-254">如需詳細資訊，請參閱 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="5c365-254">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="5c365-255">透過**確認**步驟繼續作業，安裝網頁伺服器角色和服務。</span><span class="sxs-lookup"><span data-stu-id="5c365-255">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="5c365-256">在安裝 \*\*Web 服務器 (iis) \*\* 角色之後，不需要重新開機伺服器。</span><span class="sxs-lookup"><span data-stu-id="5c365-256">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="5c365-257">**Windows 桌面作業系統**</span><span class="sxs-lookup"><span data-stu-id="5c365-257">**Windows desktop operating systems**</span></span>

<span data-ttu-id="5c365-258">啟用 [IIS 管理主控台]\*\*\*\* 和 [World Wide Web 服務]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-258">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="5c365-259">瀏覽到 [控制台]\*\* [程式]\*\* > \*\* [程式和功能]\*\* > \*\*\*\* > **[開啟或關閉 Windows 功能]** \(畫面左側\)。</span><span class="sxs-lookup"><span data-stu-id="5c365-259">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="5c365-260">開啟 [Internet Information Services]\*\*\*\* 節點。</span><span class="sxs-lookup"><span data-stu-id="5c365-260">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="5c365-261">開啟 [Web 管理工具]\*\*\*\* 節點。</span><span class="sxs-lookup"><span data-stu-id="5c365-261">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="5c365-262">核取 [IIS 管理主控台]\*\*\*\* 方塊。</span><span class="sxs-lookup"><span data-stu-id="5c365-262">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="5c365-263">[World Wide Web Services] (全球資訊網服務)\*\*\*\* 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="5c365-263">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="5c365-264">接受**全球資訊網服務**的預設功能，或自訂 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="5c365-264">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="5c365-265">**Windows 驗證 (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5c365-265">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="5c365-266">若要啟用 Windows 驗證，請展開下列節點： **World Wide Web 服務**  >  **安全性**。</span><span class="sxs-lookup"><span data-stu-id="5c365-266">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="5c365-267">選取 [Windows 驗證]\*\*\*\* 功能。</span><span class="sxs-lookup"><span data-stu-id="5c365-267">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="5c365-268">如需詳細資訊，請參閱[Windows 驗證 \<windowsAuthentication> ](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)和[設定 Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="5c365-268">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="5c365-269">**WebSocket (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5c365-269">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="5c365-270">WebSocket 由 ASP.NET Core 1.1 或更新版本所支援。</span><span class="sxs-lookup"><span data-stu-id="5c365-270">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="5c365-271">若要啟用 websocket，請展開下列節點： **World Wide Web 服務**  >  **應用程式開發功能**。</span><span class="sxs-lookup"><span data-stu-id="5c365-271">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="5c365-272">選取 [WebSocket 通訊協定]\*\*\*\* 功能。</span><span class="sxs-lookup"><span data-stu-id="5c365-272">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="5c365-273">如需詳細資訊，請參閱 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="5c365-273">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="5c365-274">若 IIS 安裝需要重新啟動，請重新啟動系統。</span><span class="sxs-lookup"><span data-stu-id="5c365-274">If the IIS installation requires a restart, restart the system.</span></span>

![選取 [Windows 功能] 中的 [IIS 管理主控台] 和 [World Wide Web Services] (全球資訊網服務)。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="5c365-276">安裝 .NET Core 裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="5c365-276">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="5c365-277">在主控系統上安裝 .NET Core 裝載套件組合\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-277">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="5c365-278">套件組合會安裝 .NET Core 執行時間、.NET Core 程式庫和 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="5c365-278">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="5c365-279">此模組可讓 ASP.NET Core 應用程式在 IIS 背後執行。</span><span class="sxs-lookup"><span data-stu-id="5c365-279">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5c365-280">若裝載套件組合在 IIS 之前安裝，則必須對該套件組合安裝進行修復。</span><span class="sxs-lookup"><span data-stu-id="5c365-280">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="5c365-281">請在安裝 IIS 之後，再次執行裝載套件組合安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-281">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="5c365-282">如果在安裝 64 位元 (x64) 版本的 .NET Core 後才安裝裝載套件組合，那麼可能會遺漏 SDK ([未偵測到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="5c365-282">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="5c365-283">若要解決此問題，請參閱 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="5c365-283">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="5c365-284">直接下載 (目前版本)</span><span class="sxs-lookup"><span data-stu-id="5c365-284">Direct download (current version)</span></span>

<span data-ttu-id="5c365-285">使用下列連結下載安裝程式：</span><span class="sxs-lookup"><span data-stu-id="5c365-285">Download the installer using the following link:</span></span>

[<span data-ttu-id="5c365-286">目前的 .NET Core 裝載套件組合安裝程式 (直接下載)</span><span class="sxs-lookup"><span data-stu-id="5c365-286">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="5c365-287">安裝程式的先前版本</span><span class="sxs-lookup"><span data-stu-id="5c365-287">Earlier versions of the installer</span></span>

<span data-ttu-id="5c365-288">若要取得安裝程式的先前版本：</span><span class="sxs-lookup"><span data-stu-id="5c365-288">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="5c365-289">流覽至 [ [下載 .Net Core](https://dotnet.microsoft.com/download/dotnet-core) ] 頁面。</span><span class="sxs-lookup"><span data-stu-id="5c365-289">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="5c365-290">選取所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="5c365-290">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="5c365-291">在 [執行應用程式 - 執行階段]\*\*\*\* 欄中，尋找想要的 .NET Core 執行階段版本列。</span><span class="sxs-lookup"><span data-stu-id="5c365-291">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="5c365-292">使用 **裝載** 套件組合連結來下載安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-292">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="5c365-293">某些安裝程式包含已達到期生命週期結束 (EOL) 的發行版本，這些發行版本已不受 Microsoft 支援。</span><span class="sxs-lookup"><span data-stu-id="5c365-293">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="5c365-294">如需詳細資訊，請參閱[支援原則](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) \(英文 \)。</span><span class="sxs-lookup"><span data-stu-id="5c365-294">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="5c365-295">安裝裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="5c365-295">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="5c365-296">在伺服器上執行安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-296">Run the installer on the server.</span></span> <span data-ttu-id="5c365-297">從系統管理員命令殼層執行安裝程式時，有 下列參數可用：</span><span class="sxs-lookup"><span data-stu-id="5c365-297">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="5c365-298">`OPT_NO_ANCM=1`：略過安裝 ASP.NET Core 模組。</span><span class="sxs-lookup"><span data-stu-id="5c365-298">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="5c365-299">`OPT_NO_RUNTIME=1`：略過安裝 .NET Core 執行時間。</span><span class="sxs-lookup"><span data-stu-id="5c365-299">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="5c365-300">當伺服器僅裝載 [獨立部署 (SCD) ](/dotnet/core/deploying/#self-contained-deployments-scd)時使用。</span><span class="sxs-lookup"><span data-stu-id="5c365-300">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="5c365-301">`OPT_NO_SHAREDFX=1`：略過安裝 ASP.NET 共用架構 (ASP.NET 執行時間) 。</span><span class="sxs-lookup"><span data-stu-id="5c365-301">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="5c365-302">當伺服器僅裝載 [獨立部署 (SCD) ](/dotnet/core/deploying/#self-contained-deployments-scd)時使用。</span><span class="sxs-lookup"><span data-stu-id="5c365-302">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="5c365-303">`OPT_NO_X86=1`：略過安裝 x86 執行時間。</span><span class="sxs-lookup"><span data-stu-id="5c365-303">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="5c365-304">當您確定不會裝載 32 位元應用程式時，請使用此參數。</span><span class="sxs-lookup"><span data-stu-id="5c365-304">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="5c365-305">如果將來有可能同時裝載 32 位元和 64 位元應用程式，請不要使用此參數並安裝這兩個執行階段。</span><span class="sxs-lookup"><span data-stu-id="5c365-305">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="5c365-306">`OPT_NO_SHARED_CONFIG_CHECK=1`：當共用設定 (*applicationHost.config*) 位於與 IIS 安裝相同的電腦上時，請停用 iis 共用設定的檢查。</span><span class="sxs-lookup"><span data-stu-id="5c365-306">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="5c365-307">*只在 ASP.NET Core 2.2 或更新版本的裝載套件組合安裝程式上可用。*</span><span class="sxs-lookup"><span data-stu-id="5c365-307">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="5c365-308">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="5c365-308">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="5c365-309">重新開機系統，或在命令 shell 中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="5c365-309">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="5c365-310">重新啟動 IIS 將能偵測到由安裝程式對系統路徑 (此為環境變數) 所做出的變更。</span><span class="sxs-lookup"><span data-stu-id="5c365-310">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="5c365-311">ASP.NET Core 不會針對共用架構套件的修補程式版本採用向前復原行為。</span><span class="sxs-lookup"><span data-stu-id="5c365-311">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="5c365-312">藉由安裝新的裝載套件組合升級共用架構之後，請重新開機系統，或在命令 shell 中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="5c365-312">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="5c365-313">如需 IIS 共用組態的資訊，請參閱[使用 IIS 共用組態的 ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="5c365-313">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="5c365-314">使用 Visual Studio 發佈時安裝 Web Deploy</span><span class="sxs-lookup"><span data-stu-id="5c365-314">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="5c365-315">將應用程式部署到具有 [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later) 的伺服器時，請在伺服器上安裝最新版的 Web Deploy。</span><span class="sxs-lookup"><span data-stu-id="5c365-315">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="5c365-316">若要安裝 Web Deploy，請使用 [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=43717)直接取得安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-316">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="5c365-317">慣用的方法是使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="5c365-317">The preferred method is to use WebPI.</span></span> <span data-ttu-id="5c365-318">WebPI 提供獨立的安裝程式和組態以裝載提供者。</span><span class="sxs-lookup"><span data-stu-id="5c365-318">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="5c365-319">建立 IIS 網站</span><span class="sxs-lookup"><span data-stu-id="5c365-319">Create the IIS site</span></span>

1. <span data-ttu-id="5c365-320">在主控系統上，請建立資料夾以容納應用程式的已發行資料夾和檔案。</span><span class="sxs-lookup"><span data-stu-id="5c365-320">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="5c365-321">在下列步驟中，您提供資料夾路徑給 IIS，作為應用程式的實體路徑。</span><span class="sxs-lookup"><span data-stu-id="5c365-321">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="5c365-322">如需應用程式之部署資料夾和檔案配置的詳細資訊，請參閱 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="5c365-322">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="5c365-323">在 [IIS 管理員] 中 **，在 [連線] 面板中** 開啟伺服器的節點。</span><span class="sxs-lookup"><span data-stu-id="5c365-323">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="5c365-324">以滑鼠右鍵按一下 [網站]\*\*\*\* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5c365-324">Right-click the **Sites** folder.</span></span> <span data-ttu-id="5c365-325">從操作功能表選取 [新增網站]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-325">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="5c365-326">提供**網站名稱**，並將**實體路徑**設定為應用程式的部署資料夾。</span><span class="sxs-lookup"><span data-stu-id="5c365-326">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="5c365-327">提供系結 **設定，並** 選取 **[確定]** 以建立網站：</span><span class="sxs-lookup"><span data-stu-id="5c365-327">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在新增網站步驟中提供站台名稱、實體路徑和主機名稱。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="5c365-329">請**勿**使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="5c365-329">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="5c365-330">最上層萬用字元繫結可能暴露您的應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="5c365-330">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="5c365-331">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="5c365-331">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="5c365-332">請使用明確主機名稱，而非萬用字元。</span><span class="sxs-lookup"><span data-stu-id="5c365-332">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="5c365-333">若您擁有整個父網域 (與具弱點的 `*.com` 相對) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 就沒有此安全性風險。</span><span class="sxs-lookup"><span data-stu-id="5c365-333">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="5c365-334">如需詳細資訊，請參閱 [rfc7230 5.4 節](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="5c365-334">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="5c365-335">在伺服器的節點之下，選取 [應用程式集區]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-335">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="5c365-336">以滑鼠右鍵按一下網站的應用程式集區，然後從操作功能表選取 [基本設定]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-336">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="5c365-337">在 [編輯應用程式集區]\*\*\*\* 視窗中，將 [.NET CLR 版本]\*\*\*\* 設定為 [沒有受控碼]\*\*\*\*：</span><span class="sxs-lookup"><span data-stu-id="5c365-337">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![將 .NET CLR 版本設為 [沒有受控碼]。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="5c365-339">ASP.NET Core 會在不同的處理序中執行，並管理執行階段。</span><span class="sxs-lookup"><span data-stu-id="5c365-339">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="5c365-340">ASP.NET Core 不依賴載入桌面 CLR ( .NET CLR) 。</span><span class="sxs-lookup"><span data-stu-id="5c365-340">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR).</span></span> <span data-ttu-id="5c365-341">.NET Core 的核心 Common Language Runtime (CoreCLR) 會開機，以在背景工作進程中裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-341">The Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="5c365-342">將 [.NET CLR 版本]\*\*\*\* 設定為 [沒有受控碼]\*\*\*\* 是選擇性的，但建議這樣做。</span><span class="sxs-lookup"><span data-stu-id="5c365-342">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="5c365-343">*ASP.NET Core 2.2 或更新版本*：</span><span class="sxs-lookup"><span data-stu-id="5c365-343">*ASP.NET Core 2.2 or later*:</span></span>

   * <span data-ttu-id="5c365-344">若為32位 (x86) [獨立部署](/dotnet/core/deploying/#self-contained-deployments-scd) ，以使用同 [進程裝載模型](#in-process-hosting-model)的32位 SDK 發佈，請啟用應用程式集區以取得32位。</span><span class="sxs-lookup"><span data-stu-id="5c365-344">For a 32-bit (x86) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) published with a 32-bit SDK that uses the [in-process hosting model](#in-process-hosting-model), enable the Application Pool for 32-bit.</span></span> <span data-ttu-id="5c365-345">在 **[IIS**管理員] 中，流覽至 [連線] 提要欄位中的**應用程式**集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-345">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="5c365-346">選取應用程式的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-346">Select the app's Application Pool.</span></span> <span data-ttu-id="5c365-347">在 [ **動作** ] 提要欄位中選取 [ **Advanced Settings**]。</span><span class="sxs-lookup"><span data-stu-id="5c365-347">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="5c365-348">將 [ **啟用32位應用程式** ] 設定為 `True` 。</span><span class="sxs-lookup"><span data-stu-id="5c365-348">Set **Enable 32-Bit Applications** to `True`.</span></span> 

   * <span data-ttu-id="5c365-349">對於使用[同處理序主控模型](#in-process-hosting-model)的 64 位元 (x64) [自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，會停用 32 位元 (x86) 處理序的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-349">For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span> <span data-ttu-id="5c365-350">在 **[IIS**管理員] 中，流覽至 [連線] 提要欄位中的**應用程式**集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-350">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="5c365-351">選取應用程式的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-351">Select the app's Application Pool.</span></span> <span data-ttu-id="5c365-352">在 [ **動作** ] 提要欄位中選取 [ **Advanced Settings**]。</span><span class="sxs-lookup"><span data-stu-id="5c365-352">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="5c365-353">將 [ **啟用32位應用程式** ] 設定為 `False` 。</span><span class="sxs-lookup"><span data-stu-id="5c365-353">Set **Enable 32-Bit Applications** to `False`.</span></span> 

1. <span data-ttu-id="5c365-354">確認處理序模型身分識別具有適當的權限。</span><span class="sxs-lookup"><span data-stu-id="5c365-354">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="5c365-355">如果應用程式集區的預設身分識別 (**進程模型**  >  **Identity**) 從\*\*ApplicationPool Identity \*\*變更為另一個身分識別，請確認新的身分識別具有存取應用程式資料夾、資料庫和其他必要資源所需的許可權。</span><span class="sxs-lookup"><span data-stu-id="5c365-355">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="5c365-356">例如，應用程式集區需要針對應用程式讀取和寫入檔案的資料夾取得讀取和寫入權限。</span><span class="sxs-lookup"><span data-stu-id="5c365-356">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="5c365-357">**Windows 驗證設定 (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5c365-357">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="5c365-358">如需詳細資訊，請參閱[設定 Windows 驗證](xref:security/authentication/windowsauth)主題。</span><span class="sxs-lookup"><span data-stu-id="5c365-358">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="5c365-359">部署應用程式</span><span class="sxs-lookup"><span data-stu-id="5c365-359">Deploy the app</span></span>

<span data-ttu-id="5c365-360">將應用程式部署至在[建立 IIS 網站](#create-the-iis-site)一節中所建立的 IIS **實體路徑**資料夾。</span><span class="sxs-lookup"><span data-stu-id="5c365-360">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="5c365-361">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) 是建議的部署機制，但有數個選項可將應用程式從專案的 [publish]\*\* 資料夾移動至主機系統的部署資料夾。</span><span class="sxs-lookup"><span data-stu-id="5c365-361">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="5c365-362">使用 Visual Studio 的 Web Deploy</span><span class="sxs-lookup"><span data-stu-id="5c365-362">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="5c365-363">請參閱[適用於 ASP.NET Core 應用程式部署的 Visual Studio 發行設定檔](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)主題，了解如何建立搭配 Web Deploy 使用的發行設定檔。</span><span class="sxs-lookup"><span data-stu-id="5c365-363">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="5c365-364">如果主機服務提供者提供或支援建立發行設定檔，請下載其設定檔，並使用 Visual Studio 的 [發行]\*\*\*\* 對話方塊匯入其設定檔：</span><span class="sxs-lookup"><span data-stu-id="5c365-364">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![[發佈] 對話方塊頁](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="5c365-366">Visual Studio 外部的 Web Deploy</span><span class="sxs-lookup"><span data-stu-id="5c365-366">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="5c365-367">透過命令列也可以在 Visual Studio 外部使用 [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="5c365-367">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="5c365-368">如需詳細資訊，請參閱 [Web 部署工具](/iis/publish/using-web-deploy/use-the-web-deployment-tool)。</span><span class="sxs-lookup"><span data-stu-id="5c365-368">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="5c365-369">Web Deploy 的替代項目</span><span class="sxs-lookup"><span data-stu-id="5c365-369">Alternatives to Web Deploy</span></span>

<span data-ttu-id="5c365-370">使用數種方法的其中一種將應用程式移到主機系統，例如手動複製、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)。</span><span class="sxs-lookup"><span data-stu-id="5c365-370">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="5c365-371">如需 IIS 的 ASP.NET Core 部署詳細資訊，請參閱 [IIS 系統管理員的部署資源](#deployment-resources-for-iis-administrators)一節。</span><span class="sxs-lookup"><span data-stu-id="5c365-371">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="5c365-372">瀏覽網站</span><span class="sxs-lookup"><span data-stu-id="5c365-372">Browse the website</span></span>

<span data-ttu-id="5c365-373">應用程式部署到主機系統之後，請向其中一個應用程式的公用端點發出要求。</span><span class="sxs-lookup"><span data-stu-id="5c365-373">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="5c365-374">在下列範例中，網站繫結至 IIS 上的**連接埠** `80`，其**主機名稱**為 `www.mysite.com`。</span><span class="sxs-lookup"><span data-stu-id="5c365-374">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="5c365-375">對 `http://www.mysite.com` 發出要求：</span><span class="sxs-lookup"><span data-stu-id="5c365-375">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 瀏覽器已載入 IIS 啟動頁面。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="5c365-377">已鎖定的部署檔案</span><span class="sxs-lookup"><span data-stu-id="5c365-377">Locked deployment files</span></span>

<span data-ttu-id="5c365-378">當應用程式執行時，會鎖定部署資料夾中的檔案。</span><span class="sxs-lookup"><span data-stu-id="5c365-378">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="5c365-379">無法於部署期間覆寫已鎖定的檔案。</span><span class="sxs-lookup"><span data-stu-id="5c365-379">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="5c365-380">若要釋放部署中的已鎖定檔案，請使用下列其中**一種**方法停止應用程式集區：</span><span class="sxs-lookup"><span data-stu-id="5c365-380">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="5c365-381">使用 Web Deploy 並參考專案檔中的 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="5c365-381">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="5c365-382">*app_offline.htm* 檔案是放在 Web 應用程式目錄的根目錄中。</span><span class="sxs-lookup"><span data-stu-id="5c365-382">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="5c365-383">當檔案存在時，ASP.NET Core 模組會正常關閉應用程式，並在部署期間提供 *app_offline.htm* 檔案。</span><span class="sxs-lookup"><span data-stu-id="5c365-383">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="5c365-384">如需詳細資訊，請參閱 [ASP.NET Core 模組組態參考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="5c365-384">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="5c365-385">在伺服器上的 IIS 管理員中手動停止應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-385">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="5c365-386">使用 PowerShell 卸載 *app_offline.htm* (需要 PowerShell 5 或更新版本) ：</span><span class="sxs-lookup"><span data-stu-id="5c365-386">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="5c365-387">資料保護</span><span class="sxs-lookup"><span data-stu-id="5c365-387">Data protection</span></span>

<span data-ttu-id="5c365-388">[ASP.NET Core 資料保護堆疊](xref:security/data-protection/introduction)是由數個 ASP.NET Core [中介軟體](xref:fundamentals/middleware/index)所使用，包括用於驗證的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="5c365-388">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="5c365-389">即使資料保護 API 不是由使用者程式碼呼叫，仍應使用部署指令碼或是在使用者程式碼中設定資料保護，以建立持續性的密碼編譯[金鑰存放區](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="5c365-389">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="5c365-390">如不設定資料保護，金鑰會保留在記憶體中，並於應用程式重新啟動時捨棄。</span><span class="sxs-lookup"><span data-stu-id="5c365-390">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="5c365-391">如果 Keyring 儲存在記憶體中，則當應用程式重新啟動時：</span><span class="sxs-lookup"><span data-stu-id="5c365-391">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="5c365-392">所有 cookie 的驗證權杖都會失效。</span><span class="sxs-lookup"><span data-stu-id="5c365-392">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="5c365-393">當使用者提出下一個要求時，需要再次登入。</span><span class="sxs-lookup"><span data-stu-id="5c365-393">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="5c365-394">所有以 Keyring 保護的資料都無法再解密。</span><span class="sxs-lookup"><span data-stu-id="5c365-394">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="5c365-395">這可能包括 [CSRF 權杖](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 和 [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="5c365-395">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="5c365-396">若要在 IIS 下設定資料保護以保存 Keyring，請使用下列其中**一種**方法：</span><span class="sxs-lookup"><span data-stu-id="5c365-396">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="5c365-397">**建立資料保護登錄機碼**</span><span class="sxs-lookup"><span data-stu-id="5c365-397">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="5c365-398">ASP.NET Core 應用程式所使用的資料保護金鑰會儲存在應用程式外部的登錄中。</span><span class="sxs-lookup"><span data-stu-id="5c365-398">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="5c365-399">若要保存指定應用程式的金鑰，請為應用程式集區建立登錄機碼。</span><span class="sxs-lookup"><span data-stu-id="5c365-399">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="5c365-400">若為獨立的非Web 伺服陣列 IIS 安裝，請針對搭配使用 ASP.NET Core 應用程式的每個應用程式集區，使用[資料保護 Provision-AutoGenKeys.ps1 PowerShell 指令碼](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="5c365-400">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="5c365-401">此指令碼會在 HKLM 登錄中建立登錄機碼，只有應用程式之應用程式集區的背景工作處理序帳戶可以存取它。</span><span class="sxs-lookup"><span data-stu-id="5c365-401">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="5c365-402">在待用期間使用 DPAPI 和全電腦金鑰加密金鑰。</span><span class="sxs-lookup"><span data-stu-id="5c365-402">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="5c365-403">在 Web 伺服陣列案例中，應用程式可以設定成使用 UNC 路徑來儲存其資料保護 Keyring。</span><span class="sxs-lookup"><span data-stu-id="5c365-403">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="5c365-404">根據預設，資料保護金鑰不予加密。</span><span class="sxs-lookup"><span data-stu-id="5c365-404">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="5c365-405">請確保網路共用的檔案權限僅限於執行應用程式的 Windows 帳戶。</span><span class="sxs-lookup"><span data-stu-id="5c365-405">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="5c365-406">可以使用 X509 憑證來保護待用的金鑰。</span><span class="sxs-lookup"><span data-stu-id="5c365-406">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="5c365-407">請考慮下列讓使用者上傳憑證的機制：將憑證放入使用者的受信任憑證存放區，並確保在執行使用者應用程式的所有電腦上都能使用這些憑證。</span><span class="sxs-lookup"><span data-stu-id="5c365-407">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="5c365-408">如需詳細資訊，請參閱[設定 ASP.NET Core 資料保護](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="5c365-408">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="5c365-409">**設定 IIS 應用程式集區載入使用者設定檔**</span><span class="sxs-lookup"><span data-stu-id="5c365-409">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="5c365-410">此設定位在應用程式集區 [進階設定]\*\*\*\* 下的 [處理序模型]\*\*\*\* 區段中。</span><span class="sxs-lookup"><span data-stu-id="5c365-410">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="5c365-411">將 [載入使用者設定檔]\*\*\*\* 設為 `True`。</span><span class="sxs-lookup"><span data-stu-id="5c365-411">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="5c365-412">當設定為 `True` 時，金鑰會儲存在使用者設定檔目錄中，且使用具有使用者帳戶專屬金鑰的 DPAPI 保護。</span><span class="sxs-lookup"><span data-stu-id="5c365-412">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="5c365-413">金鑰會保存到 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5c365-413">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="5c365-414">應用程式集區的 [setProfileEnvironment 屬性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)也必須啟用。</span><span class="sxs-lookup"><span data-stu-id="5c365-414">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="5c365-415">`setProfileEnvironment` 的預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="5c365-415">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="5c365-416">在某些情況下 (例如 Windows OS)，`setProfileEnvironment` 會設為 `false`。</span><span class="sxs-lookup"><span data-stu-id="5c365-416">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="5c365-417">如果金鑰並未如預期地儲存在使用者設定檔目錄中：</span><span class="sxs-lookup"><span data-stu-id="5c365-417">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="5c365-418">瀏覽至 *%windir%/system32/inetsrv/config* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5c365-418">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="5c365-419">開啟 *applicationHost.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="5c365-419">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="5c365-420">找出 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="5c365-420">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="5c365-421">確認 `setProfileEnvironment` 屬性不存在 (其預設值為 `true`)，或明確地將屬性值設為 `true`。</span><span class="sxs-lookup"><span data-stu-id="5c365-421">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="5c365-422">**將檔案系統當作 Keyring 存放區使用**</span><span class="sxs-lookup"><span data-stu-id="5c365-422">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="5c365-423">調整應用程式程式碼，以[將檔案系統當作 Keyring 存放區使用](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="5c365-423">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="5c365-424">使用 X509 憑證來保護 Keyring，並確保憑證是受信任的憑證。</span><span class="sxs-lookup"><span data-stu-id="5c365-424">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="5c365-425">如果憑證為自我簽署，請將憑證放在受信任的根存放區。</span><span class="sxs-lookup"><span data-stu-id="5c365-425">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="5c365-426">在 Web 伺服陣列中使用 IIS 時：</span><span class="sxs-lookup"><span data-stu-id="5c365-426">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="5c365-427">請使用所有電腦都可以存取的檔案共用。</span><span class="sxs-lookup"><span data-stu-id="5c365-427">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="5c365-428">將 X509 憑證部署到每一部電腦。</span><span class="sxs-lookup"><span data-stu-id="5c365-428">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="5c365-429">設定[程式碼中的資料保護](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="5c365-429">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="5c365-430">**設定資料保護的全電腦原則**</span><span class="sxs-lookup"><span data-stu-id="5c365-430">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="5c365-431">針對取用資料保護 API 的所有應用程式，資料保護系統僅支援有限的預設[全電腦原則](xref:security/data-protection/configuration/machine-wide-policy)設定。</span><span class="sxs-lookup"><span data-stu-id="5c365-431">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="5c365-432">如需詳細資訊，請參閱<xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="5c365-432">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="5c365-433">虛擬目錄</span><span class="sxs-lookup"><span data-stu-id="5c365-433">Virtual Directories</span></span>

<span data-ttu-id="5c365-434">ASP.NET Core 應用程式不支援 [IIS 虛擬目錄](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="5c365-434">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="5c365-435">應用程式能以[子應用程式](#sub-applications)的形式裝載。</span><span class="sxs-lookup"><span data-stu-id="5c365-435">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="5c365-436">子應用程式</span><span class="sxs-lookup"><span data-stu-id="5c365-436">Sub-applications</span></span>

<span data-ttu-id="5c365-437">ASP.NET Core 應用程式能以 [IIS 子應用程式](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)的形式裝載。</span><span class="sxs-lookup"><span data-stu-id="5c365-437">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="5c365-438">子應用程式的路徑會成為根應用程式 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="5c365-438">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="5c365-439">子應用程式內的靜態資產連結應該使用波狀符號與斜線 (`~/`) 標記法。</span><span class="sxs-lookup"><span data-stu-id="5c365-439">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="5c365-440">波狀符號與斜線標記法會觸發[標記協助程式](xref:mvc/views/tag-helpers/intro)以將子應用程式的路徑基底附加到轉譯的相對連結前面。</span><span class="sxs-lookup"><span data-stu-id="5c365-440">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="5c365-441">針對位於 `/subapp_path` 的子應用程式，使用 `src="~/image.png"` 連結的影像會轉譯為 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="5c365-441">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="5c365-442">根應用程式的靜態檔案中介軟體不會處理靜態檔案要求。</span><span class="sxs-lookup"><span data-stu-id="5c365-442">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="5c365-443">要求會由子應用程式的靜態檔案中介軟體處理。</span><span class="sxs-lookup"><span data-stu-id="5c365-443">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="5c365-444">若靜態資產的 `src` 屬性是設定為絕對路徑 (例如，`src="/image.png"`)，會以不使用子應用程式路徑基底的方式轉譯連結。</span><span class="sxs-lookup"><span data-stu-id="5c365-444">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="5c365-445">根應用程式的靜態檔案中介軟體會嘗試從根應用程式的 [webroot](xref:fundamentals/index#web-root) 提供資產，這會導致「404 - 找不到」\*\* 回應 (除非根應用程式可存取靜態資產)。</span><span class="sxs-lookup"><span data-stu-id="5c365-445">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="5c365-446">裝載 ASP.NET Core 應用程式做為另一個 ASP.NET Core 應用程式下的子應用程式：</span><span class="sxs-lookup"><span data-stu-id="5c365-446">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="5c365-447">為子應用程式建立應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-447">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="5c365-448">將 [.NET CLR 版本]\*\*\*\* 設定為 [沒有受控碼]\*\*\*\*，因為會將核心通用語言執行平台 (CoreCLR) 開機以在背景工作處理序中裝載應用程式，而非在桌面 CLR (.NET CLR) 中裝載。</span><span class="sxs-lookup"><span data-stu-id="5c365-448">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="5c365-449">使用根網站下資料夾中的子應用程式在 IIS 管理員中新增根網站。</span><span class="sxs-lookup"><span data-stu-id="5c365-449">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="5c365-450">以滑鼠右鍵按一下 IIS 管理員中的子應用程式資料夾，然後選取 [轉換成應用程式]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-450">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="5c365-451">在 [新增應用程式]\*\*\*\* 對話方塊中，使用 [應用程式集區]\*\*\*\* 的[選取]\*\*\*\* 按鈕來指派您為子應用程式建立的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-451">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="5c365-452">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="5c365-452">Select **OK**.</span></span>

<span data-ttu-id="5c365-453">將不同的應用程式集區指派給子應用程式是使用同處理序裝載模型。</span><span class="sxs-lookup"><span data-stu-id="5c365-453">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="5c365-454">如需有關同進程裝載模型和設定 ASP.NET Core 模組的詳細資訊，請參閱 <xref:host-and-deploy/aspnet-core-module> 。</span><span class="sxs-lookup"><span data-stu-id="5c365-454">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="5c365-455">使用 web.config 的 IIS 組態</span><span class="sxs-lookup"><span data-stu-id="5c365-455">Configuration of IIS with web.config</span></span>

<span data-ttu-id="5c365-456">在對使用了 ASP.NET Core 模組的 ASP.NET Core 有作用的 IIS 情境下，設定會受 *web.config* 的 `<system.webServer>` 區段影響。</span><span class="sxs-lookup"><span data-stu-id="5c365-456">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="5c365-457">舉例來說，IIS 設定對動態壓縮有作用。</span><span class="sxs-lookup"><span data-stu-id="5c365-457">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="5c365-458">如果在伺服器層級將 IIS 設為使用動態壓縮，應用程式 *web.config* 檔案中的 `<urlCompression>` 元素則可為 ASP.NET Core 應用程式予以停用。</span><span class="sxs-lookup"><span data-stu-id="5c365-458">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="5c365-459">如需詳細資訊，請參閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="5c365-459">For more information, see the following topics:</span></span>

* [<span data-ttu-id="5c365-460">的設定參考 \<system.webServer></span><span class="sxs-lookup"><span data-stu-id="5c365-460">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="5c365-461">若要為在隔離的應用程式集區中執行的個別應用程式設定環境變數 (IIS 10.0 或更新版本的) 支援，請參閱 IIS 參考檔中[環境變數 \<environmentVariables> ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe)主題的*AppCmd.exe 命令*部分。</span><span class="sxs-lookup"><span data-stu-id="5c365-461">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="5c365-462">web.config 的組態區段</span><span class="sxs-lookup"><span data-stu-id="5c365-462">Configuration sections of web.config</span></span>

<span data-ttu-id="5c365-463">ASP.NET Core 應用程式的設定不使用 *web.config* 中 ASP.NET 4.x 應用程式的設定區段：</span><span class="sxs-lookup"><span data-stu-id="5c365-463">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="5c365-464">使用其他組態提供者設定的 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-464">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="5c365-465">如需詳細資訊，請參閱 [Configuration](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="5c365-465">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="5c365-466">應用程式集區</span><span class="sxs-lookup"><span data-stu-id="5c365-466">Application Pools</span></span>

<span data-ttu-id="5c365-467">應用程式集區隔離取決於裝載模型：</span><span class="sxs-lookup"><span data-stu-id="5c365-467">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="5c365-468">同進程裝載：應用程式必須在不同的應用程式集區中執行。</span><span class="sxs-lookup"><span data-stu-id="5c365-468">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="5c365-469">跨進程裝載：建議您在應用程式本身的應用程式集區中執行每個應用程式，以隔離彼此的應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-469">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="5c365-470">IIS [新增網站]\*\*\*\* 對話方塊預設每個應用程式皆為單一應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-470">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="5c365-471">當提供**網站名稱**時，文字會自動轉移至 [應用程式集區]\*\*\*\* 文字方塊。</span><span class="sxs-lookup"><span data-stu-id="5c365-471">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="5c365-472">新增網站時，會使用該網站名稱建立新的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-472">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="5c365-473">應用程式集區 Identity</span><span class="sxs-lookup"><span data-stu-id="5c365-473">Application Pool Identity</span></span>

<span data-ttu-id="5c365-474">應用程式集區身分識別帳戶可讓應用程式在唯一的帳戶下執行，不必建立及管理網域或本機帳戶。</span><span class="sxs-lookup"><span data-stu-id="5c365-474">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="5c365-475">在 IIS 8.0 或更新版本中，IIS 管理背景工作處理序 (WAS) 會使用新的應用程式集區名稱建立虛擬帳戶，並預設在此帳戶下執行應用程式集區的背景工作處理序。</span><span class="sxs-lookup"><span data-stu-id="5c365-475">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="5c365-476">在 [IIS 管理主控台] 中，于應用程式集區的 [ **Advanced Settings** ] 底下，確定 **Identity** 已將設定為 [使用\*\* Identity ApplicationPool\*\*：</span><span class="sxs-lookup"><span data-stu-id="5c365-476">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![應用程式集區進階設定對話方塊](index/_static/apppool-identity.png)

<span data-ttu-id="5c365-478">IIS 管理程序會在 Windows 安全系統中，以應用程式集區的名稱建立安全識別碼。</span><span class="sxs-lookup"><span data-stu-id="5c365-478">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="5c365-479">可使用此身分識別來保護資源。</span><span class="sxs-lookup"><span data-stu-id="5c365-479">Resources can be secured using this identity.</span></span> <span data-ttu-id="5c365-480">不過，此身分識別不是真正的使用者帳戶，也不會顯示在 Windows 使用者管理主控台中。</span><span class="sxs-lookup"><span data-stu-id="5c365-480">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="5c365-481">如果 IIS 背景工作處理序需要提升應用程式的存取權限，請修改包含應用程式的目錄存取控制清單 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="5c365-481">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="5c365-482">開啟 Windows 檔案總管，巡覽至目錄。</span><span class="sxs-lookup"><span data-stu-id="5c365-482">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="5c365-483">以滑鼠右鍵按一下目錄並選取 [屬性]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-483">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="5c365-484">依序選取 [安全性]\*\*\*\* 索引標籤下的 [編輯]\*\*\*\* 按鈕和 [新增]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="5c365-484">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="5c365-485">選取 [位置]\*\*\*\* 按鈕，並確定選取系統。</span><span class="sxs-lookup"><span data-stu-id="5c365-485">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="5c365-486">在 [輸入要選取的物件名稱]\*\*\*\* 區域中，輸入 **IIS AppPool\\<app_pool_name>**。</span><span class="sxs-lookup"><span data-stu-id="5c365-486">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="5c365-487">選取 [檢查名稱]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="5c365-487">Select the **Check Names** button.</span></span> <span data-ttu-id="5c365-488">針對 [DefaultAppPool]\*\*，請使用 **IIS AppPool\DefaultAppPool** 檢查名稱。</span><span class="sxs-lookup"><span data-stu-id="5c365-488">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="5c365-489">選取 [檢查名稱]\*\*\*\* 按鈕時，[DefaultAppPool]\*\*\*\* 的值便會顯示於物件名稱區域中。</span><span class="sxs-lookup"><span data-stu-id="5c365-489">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="5c365-490">您無法直接將應用程式集區名稱輸入至物件名稱區域。</span><span class="sxs-lookup"><span data-stu-id="5c365-490">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="5c365-491">檢查物件名稱時，請使用 **IIS AppPool\\<app_pool_name>** 的格式。</span><span class="sxs-lookup"><span data-stu-id="5c365-491">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![針對應用程式資料夾選取使用者或群組對話方塊：在選取 [檢查名稱] 之前，"DefaultAppPool" 這個應用程式集區名稱在物件名稱區域中會附加至 "IIS AppPool\"。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="5c365-493">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="5c365-493">Select **OK**.</span></span>

   ![針對應用程式資料夾選取使用者或群組對話方塊：選取 [檢查名稱] 之後，物件名稱 "DefaultAppPool" 會顯示在物件名稱區域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="5c365-495">預設應該會授與讀取 &amp; 執行權限。</span><span class="sxs-lookup"><span data-stu-id="5c365-495">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="5c365-496">請視需要提供其他權限。</span><span class="sxs-lookup"><span data-stu-id="5c365-496">Provide additional permissions as needed.</span></span>

<span data-ttu-id="5c365-497">也可使用 **ICACLS** 工具透過命令提示字元授與存取權限。</span><span class="sxs-lookup"><span data-stu-id="5c365-497">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="5c365-498">使用 *DefaultAppPool*作為範例，將會使用下列命令：</span><span class="sxs-lookup"><span data-stu-id="5c365-498">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="5c365-499">如需詳細資訊，請參閱 [icacls](/windows-server/administration/windows-commands/icacls) 主題。</span><span class="sxs-lookup"><span data-stu-id="5c365-499">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="5c365-500">CORS 預檢要求</span><span class="sxs-lookup"><span data-stu-id="5c365-500">CORS preflight requests</span></span>

<span data-ttu-id="5c365-501">*此節只適用於以 .NET Framework 為目標的 ASP.NET Core 應用程式。*</span><span class="sxs-lookup"><span data-stu-id="5c365-501">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="5c365-502">針對以 .NET Framework 為目標的 ASP.NET Core 應用程式，在 IIS 中OPTIONS 要求預設不會傳遞到應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-502">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="5c365-503">若要瞭解如何在 *web.config* 中設定應用程式的 IIS 處理常式來傳遞選項要求，請參閱 [ASP.NET Web API 2 中啟用跨原始來源的要求： CORS 的運作方式](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="5c365-503">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="5c365-504">應用程式初始化模組與閒置逾時</span><span class="sxs-lookup"><span data-stu-id="5c365-504">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="5c365-505">在 IIS 中由 ASP.NET Core 模組版本 2 裝載時：</span><span class="sxs-lookup"><span data-stu-id="5c365-505">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="5c365-506">[應用程式初始化模組](#application-initialization-module)：應用程式裝載的同 [進程](#in-process-hosting-model) 或 [跨進程](#out-of-process-hosting-model) 可設定為在背景工作進程重新開機或伺服器重新開機時自動啟動。</span><span class="sxs-lookup"><span data-stu-id="5c365-506">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="5c365-507">[閒置 Timeout](#idle-timeout)：應用程式裝載的同 [進程](#in-process-hosting-model) 可設定為在無活動期間停止超時。</span><span class="sxs-lookup"><span data-stu-id="5c365-507">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="5c365-508">應用程式初始化模組</span><span class="sxs-lookup"><span data-stu-id="5c365-508">Application Initialization Module</span></span>

<span data-ttu-id="5c365-509">*套用到應用程式裝載同處理序與非同處理序。*</span><span class="sxs-lookup"><span data-stu-id="5c365-509">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="5c365-510">[IIS 應用程式初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一項 IIS 功能，可在應用程式集區啟動或回收時，將 HTTP 要求傳送給應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-510">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="5c365-511">要求會觸發應用程式啟動。</span><span class="sxs-lookup"><span data-stu-id="5c365-511">The request triggers the app to start.</span></span> <span data-ttu-id="5c365-512">根據預設，IIS 會發出應用程式根 URL (`/`) 的要求以初始化應用程式 (如需有關設定的詳細資訊，請參閱[額外資源](#application-initialization-module-and-idle-timeout-additional-resources))。</span><span class="sxs-lookup"><span data-stu-id="5c365-512">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="5c365-513">確認已啟用「IIS 應用程式初始化」角色功能：</span><span class="sxs-lookup"><span data-stu-id="5c365-513">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="5c365-514">在 Windows 7 或更新的電腦系統上，當在本機使用 IIS 時：</span><span class="sxs-lookup"><span data-stu-id="5c365-514">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="5c365-515">瀏覽到 [控制台]\*\* [程式]\*\* > \*\* [程式和功能]\*\* > \*\*\*\* > **[開啟或關閉 Windows 功能]** \(畫面左側\)。</span><span class="sxs-lookup"><span data-stu-id="5c365-515">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="5c365-516">開啟 [網際網路資訊服務]\*\* [World Wide Web 服務]\*\* > \*\* [應用程式開發功能]\*\* > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-516">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="5c365-517">選取 [應用程式初始化]\*\*\*\* 的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="5c365-517">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="5c365-518">在 Windows Server 2008 R2 或更新版本上：</span><span class="sxs-lookup"><span data-stu-id="5c365-518">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="5c365-519">開啟「新增角色與功能精靈」\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-519">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="5c365-520">在 [選取角色服務]\*\*\*\* 面板中，開啟 [應用程式開發]\*\*\*\* 節點。</span><span class="sxs-lookup"><span data-stu-id="5c365-520">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="5c365-521">選取 [應用程式初始化]\*\*\*\* 的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="5c365-521">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="5c365-522">使用下列任一方式為網站啟用應用程式初始化模組：</span><span class="sxs-lookup"><span data-stu-id="5c365-522">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="5c365-523">使用 IIS 管理員：</span><span class="sxs-lookup"><span data-stu-id="5c365-523">Using IIS Manager:</span></span>

  1. <span data-ttu-id="5c365-524">選取 [連線]\*\*\*\* 面板中的 [應用程式集區]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-524">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="5c365-525">以滑鼠右鍵按一下清單中應用程式的應用程式集區，然後選取 [進階設定]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-525">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="5c365-526">預設的 [啟動模式]\*\*\*\* 是 [OnDemand]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-526">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="5c365-527">將 [啟動模式]\*\*\*\* 設定為 [AlwaysRunning]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-527">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="5c365-528">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="5c365-528">Select **OK**.</span></span>
  1. <span data-ttu-id="5c365-529">開啟 [連線]\*\*\*\* 面板中的 [站台]\*\*\*\* 節點。</span><span class="sxs-lookup"><span data-stu-id="5c365-529">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="5c365-530">以滑鼠右鍵按一下應用程式，然後選取 [管理網站]\*\* [進階設定]\*\* > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-530">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="5c365-531">預設 [預先載入已啟用]\*\*\*\* 設定是 [False]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-531">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="5c365-532">將 [預先載入已啟用]\*\*\*\* 設定為 [True]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-532">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="5c365-533">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="5c365-533">Select **OK**.</span></span>

* <span data-ttu-id="5c365-534">使用 *web.config*，新增 `<applicationInitialization>` 元素並將 `doAppInitAfterRestart` 設定為 `true` 至應用程式 *web.config* 檔案中的 `<system.webServer>` 元素：</span><span class="sxs-lookup"><span data-stu-id="5c365-534">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

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

### <a name="idle-timeout"></a><span data-ttu-id="5c365-535">閒置逾時</span><span class="sxs-lookup"><span data-stu-id="5c365-535">Idle Timeout</span></span>

<span data-ttu-id="5c365-536">*僅適用於應用程式裝載同處理序。*</span><span class="sxs-lookup"><span data-stu-id="5c365-536">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="5c365-537">若要防止應用程式閒置，請使用 IIS 管理員設定應用程式集區的閒置逾時：</span><span class="sxs-lookup"><span data-stu-id="5c365-537">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="5c365-538">選取 [連線]\*\*\*\* 面板中的 [應用程式集區]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-538">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="5c365-539">以滑鼠右鍵按一下清單中應用程式的應用程式集區，然後選取 [進階設定]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-539">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="5c365-540">預設 [閒置逾時 (分鐘)]\*\*\*\* 是 **20** 分鐘。</span><span class="sxs-lookup"><span data-stu-id="5c365-540">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="5c365-541">將 [閒置逾時 (分鐘)]\*\*\*\* 設定為 **0** (零)。</span><span class="sxs-lookup"><span data-stu-id="5c365-541">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="5c365-542">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="5c365-542">Select **OK**.</span></span>
1. <span data-ttu-id="5c365-543">回收背景工作處理序。</span><span class="sxs-lookup"><span data-stu-id="5c365-543">Recycle the worker process.</span></span>

<span data-ttu-id="5c365-544">若要防止應用程式裝載[非同處理序](#out-of-process-hosting-model)逾時，請使用下列任一方式：</span><span class="sxs-lookup"><span data-stu-id="5c365-544">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="5c365-545">從外部服務對應用程式執行 Ping 以讓它繼續執行。</span><span class="sxs-lookup"><span data-stu-id="5c365-545">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="5c365-546">若應用程式只裝載背景服務，避免 IIS 裝載並使用 [Windows 服務來裝載 ASP.NET Core 應用程式](xref:host-and-deploy/windows-service)。</span><span class="sxs-lookup"><span data-stu-id="5c365-546">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="5c365-547">應用程式初始化模組與閒置逾時額外資源</span><span class="sxs-lookup"><span data-stu-id="5c365-547">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="5c365-548">IIS 8.0 應用程式初始化</span><span class="sxs-lookup"><span data-stu-id="5c365-548">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="5c365-549">[應用程式 \<applicationInitialization> 初始化](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="5c365-549">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="5c365-550">[應用程式集 \<processModel> 區的進程模型設定](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="5c365-550">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="5c365-551">IIS 系統管理員的部署資源</span><span class="sxs-lookup"><span data-stu-id="5c365-551">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="5c365-552">IIS 文件</span><span class="sxs-lookup"><span data-stu-id="5c365-552">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="5c365-553">IIS 中的 IIS 管理員使用者入門</span><span class="sxs-lookup"><span data-stu-id="5c365-553">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="5c365-554">.NET Core 應用程式部署</span><span class="sxs-lookup"><span data-stu-id="5c365-554">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="5c365-555">其他資源</span><span class="sxs-lookup"><span data-stu-id="5c365-555">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="5c365-556">Microsoft IIS 官方網站</span><span class="sxs-lookup"><span data-stu-id="5c365-556">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="5c365-557">Windows Server 技術內容庫</span><span class="sxs-lookup"><span data-stu-id="5c365-557">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="5c365-558">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="5c365-558">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="5c365-559">如需將 ASP.NET Core 應用程式發佈至 IIS 伺服器的教學課程體驗，請參閱<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="5c365-559">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="5c365-560">安裝 .NET Core 裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="5c365-560">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="5c365-561">支援的作業系統</span><span class="sxs-lookup"><span data-stu-id="5c365-561">Supported operating systems</span></span>

<span data-ttu-id="5c365-562">以下為支援的作業系統：</span><span class="sxs-lookup"><span data-stu-id="5c365-562">The following operating systems are supported:</span></span>

* <span data-ttu-id="5c365-563">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5c365-563">Windows 7 or later</span></span>
* <span data-ttu-id="5c365-564">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5c365-564">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="5c365-565">[HTTP.sys 伺服器](xref:fundamentals/servers/httpsys) (先前稱為 WebListener) 不適用搭配 IIS 的反向 Proxy 設定。</span><span class="sxs-lookup"><span data-stu-id="5c365-565">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="5c365-566">請使用 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="5c365-566">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="5c365-567">如需在 Azure 中裝載的資訊，請參閱 <xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="5c365-567">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="5c365-568">如需疑難排解指引，請參閱 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="5c365-568">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="5c365-569">支援的平台</span><span class="sxs-lookup"><span data-stu-id="5c365-569">Supported platforms</span></span>

<span data-ttu-id="5c365-570">支援針對 32 位元 (x86) 或 64 位元 (x64) 部署發行的應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-570">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="5c365-571">使用 32 位元 (x86) .NET Core SDK 部署 32 位元應用程式，除非應用程式：</span><span class="sxs-lookup"><span data-stu-id="5c365-571">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="5c365-572">需要提供給 64 位元應用程式使用的較大虛擬記憶體位址空間。</span><span class="sxs-lookup"><span data-stu-id="5c365-572">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="5c365-573">需要較大的 IIS 堆疊大小。</span><span class="sxs-lookup"><span data-stu-id="5c365-573">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="5c365-574">有 64 位元原生相依性。</span><span class="sxs-lookup"><span data-stu-id="5c365-574">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="5c365-575">使用 64 位元 (x64) .NET Core SDK 來發行 64 位元應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-575">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="5c365-576">主機系統上必須有 64 位元執行階段存在。</span><span class="sxs-lookup"><span data-stu-id="5c365-576">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="5c365-577">裝載模型</span><span class="sxs-lookup"><span data-stu-id="5c365-577">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="5c365-578">同處理序裝載模型</span><span class="sxs-lookup"><span data-stu-id="5c365-578">In-process hosting model</span></span>

<span data-ttu-id="5c365-579">使用同處理序裝載，ASP.NET Core 應用程式會在與其 IIS 工作者處理序相同的處理序中執行。</span><span class="sxs-lookup"><span data-stu-id="5c365-579">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="5c365-580">內含式裝載可提升跨進程裝載的效能，因為：</span><span class="sxs-lookup"><span data-stu-id="5c365-580">In-process hosting provides improved performance over out-of-process hosting because:</span></span>

* <span data-ttu-id="5c365-581">要求不會透過回送介面卡進行 proxy。</span><span class="sxs-lookup"><span data-stu-id="5c365-581">Requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="5c365-582">回送介面卡是一種網路介面，可將連出的網路流量傳回相同的電腦。</span><span class="sxs-lookup"><span data-stu-id="5c365-582">A loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="5c365-583">IIS 透過 [Windows 處理序啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 來執行處理程序管理。</span><span class="sxs-lookup"><span data-stu-id="5c365-583">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="5c365-584">[ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)：</span><span class="sxs-lookup"><span data-stu-id="5c365-584">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="5c365-585">執行應用程式初始化。</span><span class="sxs-lookup"><span data-stu-id="5c365-585">Performs app initialization.</span></span>
  * <span data-ttu-id="5c365-586">載入 [CoreCLR](/dotnet/standard/glossary#coreclr)。</span><span class="sxs-lookup"><span data-stu-id="5c365-586">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="5c365-587">呼叫 `Program.Main`。</span><span class="sxs-lookup"><span data-stu-id="5c365-587">Calls `Program.Main`.</span></span>
* <span data-ttu-id="5c365-588">處理 IIS 原生要求的存留期。</span><span class="sxs-lookup"><span data-stu-id="5c365-588">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="5c365-589">以 .NET Framework 為目標的 ASP.NET Core 應用程式不支援處理序內裝載模型。</span><span class="sxs-lookup"><span data-stu-id="5c365-589">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="5c365-590">下圖說明 IIS、ASP.NET Core 模組和同處理序裝載應用程式之間的關聯性：</span><span class="sxs-lookup"><span data-stu-id="5c365-590">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![同處理序代管內的 ASP.NET Core 模組案例](index/_static/ancm-inprocess.png)

<span data-ttu-id="5c365-592">要求會從 Web 到達核心模式的 HTTP.sys 驅動程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-592">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="5c365-593">驅動程式會在網站設定的連接埠上將原生要求路由至 IIS，此連接埠通常是 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="5c365-593">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="5c365-594">ASP.NET Core 模組會接收原生要求，並將它傳遞給 IIS HTTP 伺服器 (`IISHttpServer`) 。</span><span class="sxs-lookup"><span data-stu-id="5c365-594">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="5c365-595">IIS HTTP 伺服器是 IIS 的同處理序伺服程式實作，可將要求從原生轉換為受控。</span><span class="sxs-lookup"><span data-stu-id="5c365-595">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="5c365-596">IIS HTTP 伺服器處理要求之後，要求會被推送至 ASP.NET Core 中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="5c365-596">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="5c365-597">中介軟體管線會處理要求，並將其作為 `HttpContext` 執行個體傳遞至應用程式的邏輯。</span><span class="sxs-lookup"><span data-stu-id="5c365-597">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="5c365-598">應用程式的回應會透過 IIS HTTP 伺服器傳回 IIS。</span><span class="sxs-lookup"><span data-stu-id="5c365-598">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="5c365-599">IIS 會將回應傳送到起始該要求的用戶端。</span><span class="sxs-lookup"><span data-stu-id="5c365-599">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="5c365-600">現有的應用程式可以選擇同處理序裝載，但 [dotnet new](/dotnet/core/tools/dotnet-new) 範本預設會對所有 IIS 和 IIS Express 案例使用同處理序裝載模型。</span><span class="sxs-lookup"><span data-stu-id="5c365-600">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="5c365-601">`CreateDefaultBuilder` 會透過呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 方法來啟動 [CoreCLR](/dotnet/standard/glossary#coreclr) 以新增 <xref:Microsoft.AspNetCore.Hosting.Server.IServer>，並在 IIS 工作者處理序 (*w3wp.exe* 或 *iisexpress.exe*) 內裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-601">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="5c365-602">效能測試指出，相較於跨處理序裝載 .NET Core 應用程式，並將要求 Proxy 處理至 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器，裝載於同處理序可提供明顯更高的要求輸送量。</span><span class="sxs-lookup"><span data-stu-id="5c365-602">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="5c365-603">跨處理序裝載模型</span><span class="sxs-lookup"><span data-stu-id="5c365-603">Out-of-process hosting model</span></span>

<span data-ttu-id="5c365-604">由於 ASP.NET Core apps 會在與 IIS 背景工作進程不同的進程中執行，因此 ASP.NET Core 模組會處理進程管理。</span><span class="sxs-lookup"><span data-stu-id="5c365-604">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="5c365-605">此模組會在第一個要求到達時啟動 ASP.NET Core 應用程式的處理序，並在應用程式關閉或損毀時將它重新啟動。</span><span class="sxs-lookup"><span data-stu-id="5c365-605">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="5c365-606">此行為基本上與執行同處理序，並由 [Windows 處理序啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 所管理的應用程式相同。</span><span class="sxs-lookup"><span data-stu-id="5c365-606">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="5c365-607">下圖說明 IIS、ASP.NET Core 模組和跨處理序裝載應用程式之間的關聯性：</span><span class="sxs-lookup"><span data-stu-id="5c365-607">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![非同處理序代管內的 ASP.NET Core 模組案例](index/_static/ancm-outofprocess.png)

<span data-ttu-id="5c365-609">要求會從 Web 到達核心模式的 HTTP.sys 驅動程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-609">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="5c365-610">驅動程式會在網站設定的通訊埠上將要求路由至 IIS，此通訊埠通常是 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="5c365-610">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="5c365-611">此模組會在應用程式的隨機通訊埠上將要求轉送至 Kestrel，而且不會是通訊埠 80 或 443。</span><span class="sxs-lookup"><span data-stu-id="5c365-611">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="5c365-612">此模組在啟動時透過環境變數指定連接埠，而 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 延伸模組則會設定伺服器來接聽 `http://localhost:{PORT}`。</span><span class="sxs-lookup"><span data-stu-id="5c365-612">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="5c365-613">將會執行額外檢查，不是源自模組的要求都會遭到拒絕。</span><span class="sxs-lookup"><span data-stu-id="5c365-613">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="5c365-614">此模組不支援 HTTPS 轉送，因此即使由 IIS 透過 HTTPS 接收，要求還是會透過 HTTP 轉送。</span><span class="sxs-lookup"><span data-stu-id="5c365-614">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="5c365-615">Kestrel 收取來自模組的要求之後，要求會被推送至 ASP.NET Core 中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="5c365-615">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="5c365-616">中介軟體管線會處理要求，並將其作為 `HttpContext` 執行個體傳遞至應用程式的邏輯。</span><span class="sxs-lookup"><span data-stu-id="5c365-616">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="5c365-617">IIS Integration 新增的中介軟體會更新配置、遠端 IP 和帳戶路徑基底，以將要求轉送至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="5c365-617">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="5c365-618">應用程式的回應會傳回 IIS，而 IIS 會將其推送回起始要求的 HTTP 用戶端。</span><span class="sxs-lookup"><span data-stu-id="5c365-618">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="5c365-619">如需 ASP.NET Core 模組組態指南，請參閱 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="5c365-619">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="5c365-620">如需代管的詳細資訊，請參閱[在 ASP.NET Core 中代管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="5c365-620">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="5c365-621">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="5c365-621">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="5c365-622">啟用 IISIntegration 元件</span><span class="sxs-lookup"><span data-stu-id="5c365-622">Enable the IISIntegration components</span></span>

<span data-ttu-id="5c365-623">在 `CreateWebHostBuilder` (*Program.cs*) 中建立主機時，請呼叫 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 來啟用 IIS 整合：</span><span class="sxs-lookup"><span data-stu-id="5c365-623">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="5c365-624">如需 `CreateDefaultBuilder` 的詳細資訊，請參閱 <xref:fundamentals/host/web-host#set-up-a-host>。</span><span class="sxs-lookup"><span data-stu-id="5c365-624">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="5c365-625">IIS 選項</span><span class="sxs-lookup"><span data-stu-id="5c365-625">IIS options</span></span>

<span data-ttu-id="5c365-626">**同處理序主控模型**</span><span class="sxs-lookup"><span data-stu-id="5c365-626">**In-process hosting model**</span></span>

<span data-ttu-id="5c365-627">若要設定 IIS 伺服器選項，請在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中加入 <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 的服務設定。</span><span class="sxs-lookup"><span data-stu-id="5c365-627">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="5c365-628">下列範例會停用 AutomaticAuthentication：</span><span class="sxs-lookup"><span data-stu-id="5c365-628">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="5c365-629">選項</span><span class="sxs-lookup"><span data-stu-id="5c365-629">Option</span></span>                         | <span data-ttu-id="5c365-630">預設</span><span class="sxs-lookup"><span data-stu-id="5c365-630">Default</span></span> | <span data-ttu-id="5c365-631">設定</span><span class="sxs-lookup"><span data-stu-id="5c365-631">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="5c365-632">若為 `true`，IIS 伺服器會設定由 [Windows 驗證](xref:security/authentication/windowsauth)所驗證的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="5c365-632">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="5c365-633">若為 `false`，則伺服器僅會對 `HttpContext.User` 提供身分識別，並在 `AuthenticationScheme` 明確要求時回應挑戰。</span><span class="sxs-lookup"><span data-stu-id="5c365-633">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="5c365-634">必須在 IIS 中啟用 Windows 驗證以讓 `AutomaticAuthentication` 作用。</span><span class="sxs-lookup"><span data-stu-id="5c365-634">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="5c365-635">如需詳細資訊，請參閱 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="5c365-635">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="5c365-636">設定使用者在登入頁面上看到的顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="5c365-636">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="5c365-637">**跨處理序裝載模型**</span><span class="sxs-lookup"><span data-stu-id="5c365-637">**Out-of-process hosting model**</span></span>

<span data-ttu-id="5c365-638">若要設定 IIS 選項，請在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中加入 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服務設定。</span><span class="sxs-lookup"><span data-stu-id="5c365-638">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="5c365-639">下列範例會防止應用程式填入 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="5c365-639">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="5c365-640">選項</span><span class="sxs-lookup"><span data-stu-id="5c365-640">Option</span></span>                         | <span data-ttu-id="5c365-641">預設</span><span class="sxs-lookup"><span data-stu-id="5c365-641">Default</span></span> | <span data-ttu-id="5c365-642">設定</span><span class="sxs-lookup"><span data-stu-id="5c365-642">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="5c365-643">若為 `true`，[IIS 整合中介軟體](#enable-the-iisintegration-components)會設定由 [Windows 驗證](xref:security/authentication/windowsauth)所驗證的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="5c365-643">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="5c365-644">如果為 `false`，則驗證中介軟體僅針對 `HttpContext.User` 提供身分識別，並在游 `AuthenticationScheme` 提出明確要求時回應挑戰。</span><span class="sxs-lookup"><span data-stu-id="5c365-644">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="5c365-645">必須在 IIS 中啟用 Windows 驗證以讓 `AutomaticAuthentication` 作用。</span><span class="sxs-lookup"><span data-stu-id="5c365-645">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="5c365-646">如需詳細資訊，請參閱 [Windows 驗證](xref:security/authentication/windowsauth)主題。</span><span class="sxs-lookup"><span data-stu-id="5c365-646">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="5c365-647">設定使用者在登入頁面上看到的顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="5c365-647">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="5c365-648">如果為 `true` 且 `MS-ASPNETCORE-CLIENTCERT` 要求標頭已存在，則會填入 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="5c365-648">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="5c365-649">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="5c365-649">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="5c365-650">用來設定轉送標頭中介軟體及 ASP.NET Core 模組的 [IIS 整合中介軟體](#enable-the-iisintegration-components)會設定為轉送配置 (HTTP/HTTPS) 與發出要求的遠端 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="5c365-650">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="5c365-651">其他 Proxy 伺服器和負載平衡器後方託管的應用程式可能需要其他設定。</span><span class="sxs-lookup"><span data-stu-id="5c365-651">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="5c365-652">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="5c365-652">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="5c365-653">web.config 檔案</span><span class="sxs-lookup"><span data-stu-id="5c365-653">web.config file</span></span>

<span data-ttu-id="5c365-654">*web.config* 檔案是用來設定 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="5c365-654">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="5c365-655">發佈專案時，由 MSBuild 目標 (`_TransformWebConfig`) 處理 *web.config* 檔案的建立、轉換及發佈。</span><span class="sxs-lookup"><span data-stu-id="5c365-655">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="5c365-656">此目標存在於 Web SDK 目標 (`Microsoft.NET.Sdk.Web`)。</span><span class="sxs-lookup"><span data-stu-id="5c365-656">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="5c365-657">SDK 設定在專案檔的頂端：</span><span class="sxs-lookup"><span data-stu-id="5c365-657">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="5c365-658">如果專案中沒有 *web.config* 檔案，則系統會使用正確的 *processPath* 和 *arguments* 建立該檔案以設定 ASP.NET Core 模組，並將該檔案移至[已發行的輸出](xref:host-and-deploy/directory-structure)。</span><span class="sxs-lookup"><span data-stu-id="5c365-658">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="5c365-659">如果 *web.config* 檔案存在於專案中，則系統會使用正確的 *processPath* 和 *arguments* 來轉換該檔案以設定 ASP.NET Core 模組，然後將它移至已發行的輸出。</span><span class="sxs-lookup"><span data-stu-id="5c365-659">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="5c365-660">轉換不會修改檔案中的 IIS 組態設定。</span><span class="sxs-lookup"><span data-stu-id="5c365-660">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="5c365-661">*web.config* 檔案可提供能控制作用中 IIS 模組的額外 IIS 組態設定。</span><span class="sxs-lookup"><span data-stu-id="5c365-661">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="5c365-662">如需能處理 ASP.NET Core 應用程式要求之 IIS 模組的相關資訊，請參閱 [IIS 模組](xref:host-and-deploy/iis/modules)主題。</span><span class="sxs-lookup"><span data-stu-id="5c365-662">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="5c365-663">若要防止 Web SDK 轉換 *web.config* 檔案，請使用專案檔 **\<IsTransformWebConfigDisabled>** 中的屬性：</span><span class="sxs-lookup"><span data-stu-id="5c365-663">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="5c365-664">使 Web SDK 無法轉換檔案時，應該由開發人員手動設定 *processPath* 和 *arguments*。</span><span class="sxs-lookup"><span data-stu-id="5c365-664">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="5c365-665">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="5c365-665">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="5c365-666">web.config 檔案位置</span><span class="sxs-lookup"><span data-stu-id="5c365-666">web.config file location</span></span>

<span data-ttu-id="5c365-667">為了正確設定 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module) ， *web.config* 檔案必須存在於 [內容根](xref:fundamentals/index#content-root) 路徑 (通常是已部署應用程式) 的應用程式基底路徑。</span><span class="sxs-lookup"><span data-stu-id="5c365-667">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="5c365-668">這是與提供給 IIS 的網站實體路徑相同的位置。</span><span class="sxs-lookup"><span data-stu-id="5c365-668">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="5c365-669">應用程式的根目錄需有 *web.config* 檔案，才能使用 Web Deploy 發行多個應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-669">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="5c365-670">機密檔案存在於應用程式的實體路徑上，例如\* \<assembly>.runtimeconfig.json*、 \* \<assembly> .xml* (xml 檔批註) 以及\* \<assembly>.deps.js\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-670">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="5c365-671">當 *web.config* 檔案存在且網站正常啟動時，如果有人要求機密檔案，IIS 不會予以提供。</span><span class="sxs-lookup"><span data-stu-id="5c365-671">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="5c365-672">若 *web.config* 檔案遺失或沒有正確命名，或是無法設定網站以正常啟動，IIS 可能會公開提供機密檔案。</span><span class="sxs-lookup"><span data-stu-id="5c365-672">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="5c365-673">\***web.config*檔案必須在部署中隨時都存在、正確命名，而且能夠設定網站以正常啟動。請勿從生產環境部署中移除*web.config\*的檔案。**</span><span class="sxs-lookup"><span data-stu-id="5c365-673">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="5c365-674">轉換 web.config</span><span class="sxs-lookup"><span data-stu-id="5c365-674">Transform web.config</span></span>

<span data-ttu-id="5c365-675">如需在發佈時轉換 *web.config* (例如依據設定、設定檔或環境設定環境變數)，請參閱<xref:host-and-deploy/iis/transform-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="5c365-675">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="5c365-676">IIS 組態</span><span class="sxs-lookup"><span data-stu-id="5c365-676">IIS configuration</span></span>

<span data-ttu-id="5c365-677">**Windows Server 作業系統**</span><span class="sxs-lookup"><span data-stu-id="5c365-677">**Windows Server operating systems**</span></span>

<span data-ttu-id="5c365-678">啟用**網頁伺服器 (IIS)** 伺服器角色，並建立角色服務。</span><span class="sxs-lookup"><span data-stu-id="5c365-678">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="5c365-679">使用來自 [管理]\*\*\*\* 功能表的 [新增角色及功能]\*\*\*\* 精靈，或是 [伺服器管理員]\*\*\*\* 中的連結。</span><span class="sxs-lookup"><span data-stu-id="5c365-679">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="5c365-680">在**伺服器角色**步驟中，核取 [網頁伺服器 (IIS)]\*\*\*\* 方塊。</span><span class="sxs-lookup"><span data-stu-id="5c365-680">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在選取伺服器角色步驟中選取網頁伺服器 IIS 角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="5c365-682">在 [功能]\*\*\*\* 步驟之後，[角色服務]\*\*\*\* 步驟會針對網頁伺服器 (IIS) 進行載入。</span><span class="sxs-lookup"><span data-stu-id="5c365-682">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="5c365-683">選取所需的 IIS 角色服務或接受所提供的預設角色服務。</span><span class="sxs-lookup"><span data-stu-id="5c365-683">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在選取角色服務步驟中，選取預設的角色服務。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="5c365-685">**Windows 驗證 (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5c365-685">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="5c365-686">若要啟用 Windows 驗證，請展開下列節點：**網頁伺服器**  >  **安全性**。</span><span class="sxs-lookup"><span data-stu-id="5c365-686">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="5c365-687">選取 [Windows 驗證]\*\*\*\* 功能。</span><span class="sxs-lookup"><span data-stu-id="5c365-687">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="5c365-688">如需詳細資訊，請參閱[Windows 驗證 \<windowsAuthentication> ](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)和[設定 Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="5c365-688">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="5c365-689">**WebSocket (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5c365-689">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="5c365-690">WebSocket 由 ASP.NET Core 1.1 或更新版本所支援。</span><span class="sxs-lookup"><span data-stu-id="5c365-690">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="5c365-691">若要啟用 websocket，請展開下列節點：**網頁伺服器**  >  **應用程式開發**。</span><span class="sxs-lookup"><span data-stu-id="5c365-691">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="5c365-692">選取 [WebSocket 通訊協定]\*\*\*\* 功能。</span><span class="sxs-lookup"><span data-stu-id="5c365-692">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="5c365-693">如需詳細資訊，請參閱 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="5c365-693">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="5c365-694">透過**確認**步驟繼續作業，安裝網頁伺服器角色和服務。</span><span class="sxs-lookup"><span data-stu-id="5c365-694">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="5c365-695">在安裝 \*\*Web 服務器 (iis) \*\* 角色之後，不需要重新開機伺服器。</span><span class="sxs-lookup"><span data-stu-id="5c365-695">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="5c365-696">**Windows 桌面作業系統**</span><span class="sxs-lookup"><span data-stu-id="5c365-696">**Windows desktop operating systems**</span></span>

<span data-ttu-id="5c365-697">啟用 [IIS 管理主控台]\*\*\*\* 和 [World Wide Web 服務]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-697">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="5c365-698">瀏覽到 [控制台]\*\* [程式]\*\* > \*\* [程式和功能]\*\* > \*\*\*\* > **[開啟或關閉 Windows 功能]** \(畫面左側\)。</span><span class="sxs-lookup"><span data-stu-id="5c365-698">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="5c365-699">開啟 [Internet Information Services]\*\*\*\* 節點。</span><span class="sxs-lookup"><span data-stu-id="5c365-699">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="5c365-700">開啟 [Web 管理工具]\*\*\*\* 節點。</span><span class="sxs-lookup"><span data-stu-id="5c365-700">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="5c365-701">核取 [IIS 管理主控台]\*\*\*\* 方塊。</span><span class="sxs-lookup"><span data-stu-id="5c365-701">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="5c365-702">[World Wide Web Services] (全球資訊網服務)\*\*\*\* 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="5c365-702">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="5c365-703">接受**全球資訊網服務**的預設功能，或自訂 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="5c365-703">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="5c365-704">**Windows 驗證 (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5c365-704">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="5c365-705">若要啟用 Windows 驗證，請展開下列節點： **World Wide Web 服務**  >  **安全性**。</span><span class="sxs-lookup"><span data-stu-id="5c365-705">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="5c365-706">選取 [Windows 驗證]\*\*\*\* 功能。</span><span class="sxs-lookup"><span data-stu-id="5c365-706">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="5c365-707">如需詳細資訊，請參閱[Windows 驗證 \<windowsAuthentication> ](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)和[設定 Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="5c365-707">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="5c365-708">**WebSocket (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5c365-708">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="5c365-709">WebSocket 由 ASP.NET Core 1.1 或更新版本所支援。</span><span class="sxs-lookup"><span data-stu-id="5c365-709">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="5c365-710">若要啟用 websocket，請展開下列節點： **World Wide Web 服務**  >  **應用程式開發功能**。</span><span class="sxs-lookup"><span data-stu-id="5c365-710">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="5c365-711">選取 [WebSocket 通訊協定]\*\*\*\* 功能。</span><span class="sxs-lookup"><span data-stu-id="5c365-711">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="5c365-712">如需詳細資訊，請參閱 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="5c365-712">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="5c365-713">若 IIS 安裝需要重新啟動，請重新啟動系統。</span><span class="sxs-lookup"><span data-stu-id="5c365-713">If the IIS installation requires a restart, restart the system.</span></span>

![選取 [Windows 功能] 中的 [IIS 管理主控台] 和 [World Wide Web Services] (全球資訊網服務)。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="5c365-715">安裝 .NET Core 裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="5c365-715">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="5c365-716">在主控系統上安裝 .NET Core 裝載套件組合\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-716">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="5c365-717">套件組合會安裝 .NET Core 執行時間、.NET Core 程式庫和 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="5c365-717">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="5c365-718">此模組可讓 ASP.NET Core 應用程式在 IIS 背後執行。</span><span class="sxs-lookup"><span data-stu-id="5c365-718">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5c365-719">若裝載套件組合在 IIS 之前安裝，則必須對該套件組合安裝進行修復。</span><span class="sxs-lookup"><span data-stu-id="5c365-719">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="5c365-720">請在安裝 IIS 之後，再次執行裝載套件組合安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-720">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="5c365-721">如果在安裝 64 位元 (x64) 版本的 .NET Core 後才安裝裝載套件組合，那麼可能會遺漏 SDK ([未偵測到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="5c365-721">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="5c365-722">若要解決此問題，請參閱 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="5c365-722">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="5c365-723">下載</span><span class="sxs-lookup"><span data-stu-id="5c365-723">Download</span></span>

1. <span data-ttu-id="5c365-724">流覽至 [ [下載 .Net Core](https://dotnet.microsoft.com/download/dotnet-core) ] 頁面。</span><span class="sxs-lookup"><span data-stu-id="5c365-724">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="5c365-725">選取所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="5c365-725">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="5c365-726">在 [執行應用程式 - 執行階段]\*\*\*\* 欄中，尋找想要的 .NET Core 執行階段版本列。</span><span class="sxs-lookup"><span data-stu-id="5c365-726">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="5c365-727">使用 **裝載** 套件組合連結來下載安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-727">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="5c365-728">某些安裝程式包含已達到期生命週期結束 (EOL) 的發行版本，這些發行版本已不受 Microsoft 支援。</span><span class="sxs-lookup"><span data-stu-id="5c365-728">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="5c365-729">如需詳細資訊，請參閱[支援原則](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) \(英文 \)。</span><span class="sxs-lookup"><span data-stu-id="5c365-729">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="5c365-730">安裝裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="5c365-730">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="5c365-731">在伺服器上執行安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-731">Run the installer on the server.</span></span> <span data-ttu-id="5c365-732">從系統管理員命令殼層執行安裝程式時，有 下列參數可用：</span><span class="sxs-lookup"><span data-stu-id="5c365-732">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="5c365-733">`OPT_NO_ANCM=1`：略過安裝 ASP.NET Core 模組。</span><span class="sxs-lookup"><span data-stu-id="5c365-733">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="5c365-734">`OPT_NO_RUNTIME=1`：略過安裝 .NET Core 執行時間。</span><span class="sxs-lookup"><span data-stu-id="5c365-734">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="5c365-735">當伺服器僅裝載 [獨立部署 (SCD) ](/dotnet/core/deploying/#self-contained-deployments-scd)時使用。</span><span class="sxs-lookup"><span data-stu-id="5c365-735">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="5c365-736">`OPT_NO_SHAREDFX=1`：略過安裝 ASP.NET 共用架構 (ASP.NET 執行時間) 。</span><span class="sxs-lookup"><span data-stu-id="5c365-736">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="5c365-737">當伺服器僅裝載 [獨立部署 (SCD) ](/dotnet/core/deploying/#self-contained-deployments-scd)時使用。</span><span class="sxs-lookup"><span data-stu-id="5c365-737">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="5c365-738">`OPT_NO_X86=1`：略過安裝 x86 執行時間。</span><span class="sxs-lookup"><span data-stu-id="5c365-738">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="5c365-739">當您確定不會裝載 32 位元應用程式時，請使用此參數。</span><span class="sxs-lookup"><span data-stu-id="5c365-739">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="5c365-740">如果將來有可能同時裝載 32 位元和 64 位元應用程式，請不要使用此參數並安裝這兩個執行階段。</span><span class="sxs-lookup"><span data-stu-id="5c365-740">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="5c365-741">`OPT_NO_SHARED_CONFIG_CHECK=1`：當共用設定 (*applicationHost.config*) 位於與 IIS 安裝相同的電腦上時，請停用 iis 共用設定的檢查。</span><span class="sxs-lookup"><span data-stu-id="5c365-741">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="5c365-742">*只在 ASP.NET Core 2.2 或更新版本的裝載套件組合安裝程式上可用。*</span><span class="sxs-lookup"><span data-stu-id="5c365-742">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="5c365-743">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="5c365-743">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="5c365-744">重新開機系統，或在命令 shell 中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="5c365-744">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="5c365-745">重新啟動 IIS 將能偵測到由安裝程式對系統路徑 (此為環境變數) 所做出的變更。</span><span class="sxs-lookup"><span data-stu-id="5c365-745">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="5c365-746">安裝裝載套件組合時，不需要在 IIS 中手動停止個別的網站。</span><span class="sxs-lookup"><span data-stu-id="5c365-746">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="5c365-747">IIS 網站 (的託管應用程式) 在 IIS 重新開機時重新開機。</span><span class="sxs-lookup"><span data-stu-id="5c365-747">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="5c365-748">當應用程式收到第一個要求（包括從 [應用程式初始化模組](#application-initialization-module-and-idle-timeout)）時，就會再次啟動。</span><span class="sxs-lookup"><span data-stu-id="5c365-748">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="5c365-749">針對共用架構套件的修補程式版本，ASP.NET Core 採用向前復原行為。</span><span class="sxs-lookup"><span data-stu-id="5c365-749">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="5c365-750">當 IIS 以 IIS 重新開機應用程式時，應用程式會在收到第一個要求時，以其參考封裝的最新修補程式版本進行載入。</span><span class="sxs-lookup"><span data-stu-id="5c365-750">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="5c365-751">如果未重新開機 IIS，應用程式就會重新開機，並在其背景工作進程回收並收到其第一個要求時，呈現向前復原行為。</span><span class="sxs-lookup"><span data-stu-id="5c365-751">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="5c365-752">如需 IIS 共用組態的資訊，請參閱[使用 IIS 共用組態的 ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="5c365-752">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="5c365-753">使用 Visual Studio 發佈時安裝 Web Deploy</span><span class="sxs-lookup"><span data-stu-id="5c365-753">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="5c365-754">將應用程式部署到具有 [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later) 的伺服器時，請在伺服器上安裝最新版的 Web Deploy。</span><span class="sxs-lookup"><span data-stu-id="5c365-754">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="5c365-755">若要安裝 Web Deploy，請使用 [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=43717)直接取得安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-755">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="5c365-756">慣用的方法是使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="5c365-756">The preferred method is to use WebPI.</span></span> <span data-ttu-id="5c365-757">WebPI 提供獨立的安裝程式和組態以裝載提供者。</span><span class="sxs-lookup"><span data-stu-id="5c365-757">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="5c365-758">建立 IIS 網站</span><span class="sxs-lookup"><span data-stu-id="5c365-758">Create the IIS site</span></span>

1. <span data-ttu-id="5c365-759">在主控系統上，請建立資料夾以容納應用程式的已發行資料夾和檔案。</span><span class="sxs-lookup"><span data-stu-id="5c365-759">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="5c365-760">在下列步驟中，您提供資料夾路徑給 IIS，作為應用程式的實體路徑。</span><span class="sxs-lookup"><span data-stu-id="5c365-760">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="5c365-761">如需應用程式之部署資料夾和檔案配置的詳細資訊，請參閱 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="5c365-761">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="5c365-762">在 [IIS 管理員] 中 **，在 [連線] 面板中** 開啟伺服器的節點。</span><span class="sxs-lookup"><span data-stu-id="5c365-762">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="5c365-763">以滑鼠右鍵按一下 [網站]\*\*\*\* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5c365-763">Right-click the **Sites** folder.</span></span> <span data-ttu-id="5c365-764">從操作功能表選取 [新增網站]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-764">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="5c365-765">提供**網站名稱**，並將**實體路徑**設定為應用程式的部署資料夾。</span><span class="sxs-lookup"><span data-stu-id="5c365-765">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="5c365-766">提供系結 **設定，並** 選取 **[確定]** 以建立網站：</span><span class="sxs-lookup"><span data-stu-id="5c365-766">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在新增網站步驟中提供站台名稱、實體路徑和主機名稱。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="5c365-768">請**勿**使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="5c365-768">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="5c365-769">最上層萬用字元繫結可能暴露您的應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="5c365-769">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="5c365-770">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="5c365-770">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="5c365-771">請使用明確主機名稱，而非萬用字元。</span><span class="sxs-lookup"><span data-stu-id="5c365-771">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="5c365-772">若您擁有整個父網域 (與具弱點的 `*.com` 相對) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 就沒有此安全性風險。</span><span class="sxs-lookup"><span data-stu-id="5c365-772">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="5c365-773">如需詳細資訊，請參閱 [rfc7230 5.4 節](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="5c365-773">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="5c365-774">在伺服器的節點之下，選取 [應用程式集區]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-774">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="5c365-775">以滑鼠右鍵按一下網站的應用程式集區，然後從操作功能表選取 [基本設定]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-775">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="5c365-776">在 [編輯應用程式集區]\*\*\*\* 視窗中，將 [.NET CLR 版本]\*\*\*\* 設定為 [沒有受控碼]\*\*\*\*：</span><span class="sxs-lookup"><span data-stu-id="5c365-776">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![將 .NET CLR 版本設為 [沒有受控碼]。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="5c365-778">ASP.NET Core 會在不同的處理序中執行，並管理執行階段。</span><span class="sxs-lookup"><span data-stu-id="5c365-778">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="5c365-779">ASP.NET Core 不仰賴載入桌面 CLR (.NET CLR)&mdash;會使用 .NET Core 的核心通用語言執行平台 (CoreCLR) 來開機以在背景工作處理序中裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-779">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="5c365-780">將 [.NET CLR 版本]\*\*\*\* 設定為 [沒有受控碼]\*\*\*\* 是選擇性的，但建議這樣做。</span><span class="sxs-lookup"><span data-stu-id="5c365-780">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="5c365-781">*ASP.NET Core 2.2 或更新版本*：對於使用[同處理序主控模型](#in-process-hosting-model)的 64 位元 (x64) [獨立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，會停用 32 位元 (x86) 處理序的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-781">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="5c365-782">在 IIS 管理員的 [動作]\*\*\*\* 資訊看板 > [應用程式集區]\*\*\*\* 中，選取 [設定應用程式集區預設值]\*\*\*\* 或 [進階設定]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-782">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="5c365-783">找到 [啟用 32 位元應用程式]\*\*\*\*，然後將其值設定為 `False`。</span><span class="sxs-lookup"><span data-stu-id="5c365-783">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="5c365-784">此設定不會影響為[處理程序外裝載](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)部署的應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-784">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="5c365-785">確認處理序模型身分識別具有適當的權限。</span><span class="sxs-lookup"><span data-stu-id="5c365-785">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="5c365-786">如果應用程式集區的預設身分識別 (**進程模型**  >  **Identity**) 從\*\*ApplicationPool Identity \*\*變更為另一個身分識別，請確認新的身分識別具有存取應用程式資料夾、資料庫和其他必要資源所需的許可權。</span><span class="sxs-lookup"><span data-stu-id="5c365-786">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="5c365-787">例如，應用程式集區需要針對應用程式讀取和寫入檔案的資料夾取得讀取和寫入權限。</span><span class="sxs-lookup"><span data-stu-id="5c365-787">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="5c365-788">**Windows 驗證設定 (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5c365-788">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="5c365-789">如需詳細資訊，請參閱[設定 Windows 驗證](xref:security/authentication/windowsauth)主題。</span><span class="sxs-lookup"><span data-stu-id="5c365-789">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="5c365-790">部署應用程式</span><span class="sxs-lookup"><span data-stu-id="5c365-790">Deploy the app</span></span>

<span data-ttu-id="5c365-791">將應用程式部署至在[建立 IIS 網站](#create-the-iis-site)一節中所建立的 IIS **實體路徑**資料夾。</span><span class="sxs-lookup"><span data-stu-id="5c365-791">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="5c365-792">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) 是建議的部署機制，但有數個選項可將應用程式從專案的 [publish]\*\* 資料夾移動至主機系統的部署資料夾。</span><span class="sxs-lookup"><span data-stu-id="5c365-792">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="5c365-793">使用 Visual Studio 的 Web Deploy</span><span class="sxs-lookup"><span data-stu-id="5c365-793">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="5c365-794">請參閱[適用於 ASP.NET Core 應用程式部署的 Visual Studio 發行設定檔](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)主題，了解如何建立搭配 Web Deploy 使用的發行設定檔。</span><span class="sxs-lookup"><span data-stu-id="5c365-794">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="5c365-795">如果主機服務提供者提供或支援建立發行設定檔，請下載其設定檔，並使用 Visual Studio 的 [發行]\*\*\*\* 對話方塊匯入其設定檔：</span><span class="sxs-lookup"><span data-stu-id="5c365-795">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![[發佈] 對話方塊頁](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="5c365-797">Visual Studio 外部的 Web Deploy</span><span class="sxs-lookup"><span data-stu-id="5c365-797">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="5c365-798">透過命令列也可以在 Visual Studio 外部使用 [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="5c365-798">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="5c365-799">如需詳細資訊，請參閱 [Web 部署工具](/iis/publish/using-web-deploy/use-the-web-deployment-tool)。</span><span class="sxs-lookup"><span data-stu-id="5c365-799">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="5c365-800">Web Deploy 的替代項目</span><span class="sxs-lookup"><span data-stu-id="5c365-800">Alternatives to Web Deploy</span></span>

<span data-ttu-id="5c365-801">使用數種方法的其中一種將應用程式移到主機系統，例如手動複製、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)。</span><span class="sxs-lookup"><span data-stu-id="5c365-801">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="5c365-802">如需 IIS 的 ASP.NET Core 部署詳細資訊，請參閱 [IIS 系統管理員的部署資源](#deployment-resources-for-iis-administrators)一節。</span><span class="sxs-lookup"><span data-stu-id="5c365-802">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="5c365-803">瀏覽網站</span><span class="sxs-lookup"><span data-stu-id="5c365-803">Browse the website</span></span>

<span data-ttu-id="5c365-804">應用程式部署到主機系統之後，請向其中一個應用程式的公用端點發出要求。</span><span class="sxs-lookup"><span data-stu-id="5c365-804">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="5c365-805">在下列範例中，網站繫結至 IIS 上的**連接埠** `80`，其**主機名稱**為 `www.mysite.com`。</span><span class="sxs-lookup"><span data-stu-id="5c365-805">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="5c365-806">對 `http://www.mysite.com` 發出要求：</span><span class="sxs-lookup"><span data-stu-id="5c365-806">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 瀏覽器已載入 IIS 啟動頁面。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="5c365-808">已鎖定的部署檔案</span><span class="sxs-lookup"><span data-stu-id="5c365-808">Locked deployment files</span></span>

<span data-ttu-id="5c365-809">當應用程式執行時，會鎖定部署資料夾中的檔案。</span><span class="sxs-lookup"><span data-stu-id="5c365-809">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="5c365-810">無法於部署期間覆寫已鎖定的檔案。</span><span class="sxs-lookup"><span data-stu-id="5c365-810">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="5c365-811">若要釋放部署中的已鎖定檔案，請使用下列其中**一種**方法停止應用程式集區：</span><span class="sxs-lookup"><span data-stu-id="5c365-811">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="5c365-812">使用 Web Deploy 並參考專案檔中的 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="5c365-812">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="5c365-813">*app_offline.htm* 檔案是放在 Web 應用程式目錄的根目錄中。</span><span class="sxs-lookup"><span data-stu-id="5c365-813">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="5c365-814">當檔案存在時，ASP.NET Core 模組會正常關閉應用程式，並在部署期間提供 *app_offline.htm* 檔案。</span><span class="sxs-lookup"><span data-stu-id="5c365-814">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="5c365-815">如需詳細資訊，請參閱 [ASP.NET Core 模組組態參考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="5c365-815">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="5c365-816">在伺服器上的 IIS 管理員中手動停止應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-816">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="5c365-817">使用 PowerShell 卸載 *app_offline.htm* (需要 PowerShell 5 或更新版本) ：</span><span class="sxs-lookup"><span data-stu-id="5c365-817">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="5c365-818">資料保護</span><span class="sxs-lookup"><span data-stu-id="5c365-818">Data protection</span></span>

<span data-ttu-id="5c365-819">[ASP.NET Core 資料保護堆疊](xref:security/data-protection/introduction)是由數個 ASP.NET Core [中介軟體](xref:fundamentals/middleware/index)所使用，包括用於驗證的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="5c365-819">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="5c365-820">即使資料保護 API 不是由使用者程式碼呼叫，仍應使用部署指令碼或是在使用者程式碼中設定資料保護，以建立持續性的密碼編譯[金鑰存放區](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="5c365-820">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="5c365-821">如不設定資料保護，金鑰會保留在記憶體中，並於應用程式重新啟動時捨棄。</span><span class="sxs-lookup"><span data-stu-id="5c365-821">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="5c365-822">如果 Keyring 儲存在記憶體中，則當應用程式重新啟動時：</span><span class="sxs-lookup"><span data-stu-id="5c365-822">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="5c365-823">所有 cookie 的驗證權杖都會失效。</span><span class="sxs-lookup"><span data-stu-id="5c365-823">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="5c365-824">當使用者提出下一個要求時，需要再次登入。</span><span class="sxs-lookup"><span data-stu-id="5c365-824">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="5c365-825">所有以 Keyring 保護的資料都無法再解密。</span><span class="sxs-lookup"><span data-stu-id="5c365-825">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="5c365-826">這可能包括 [CSRF 權杖](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 和 [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="5c365-826">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="5c365-827">若要在 IIS 下設定資料保護以保存 Keyring，請使用下列其中**一種**方法：</span><span class="sxs-lookup"><span data-stu-id="5c365-827">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="5c365-828">**建立資料保護登錄機碼**</span><span class="sxs-lookup"><span data-stu-id="5c365-828">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="5c365-829">ASP.NET Core 應用程式所使用的資料保護金鑰會儲存在應用程式外部的登錄中。</span><span class="sxs-lookup"><span data-stu-id="5c365-829">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="5c365-830">若要保存指定應用程式的金鑰，請為應用程式集區建立登錄機碼。</span><span class="sxs-lookup"><span data-stu-id="5c365-830">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="5c365-831">若為獨立的非Web 伺服陣列 IIS 安裝，請針對搭配使用 ASP.NET Core 應用程式的每個應用程式集區，使用[資料保護 Provision-AutoGenKeys.ps1 PowerShell 指令碼](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="5c365-831">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="5c365-832">此指令碼會在 HKLM 登錄中建立登錄機碼，只有應用程式之應用程式集區的背景工作處理序帳戶可以存取它。</span><span class="sxs-lookup"><span data-stu-id="5c365-832">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="5c365-833">在待用期間使用 DPAPI 和全電腦金鑰加密金鑰。</span><span class="sxs-lookup"><span data-stu-id="5c365-833">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="5c365-834">在 Web 伺服陣列案例中，應用程式可以設定成使用 UNC 路徑來儲存其資料保護 Keyring。</span><span class="sxs-lookup"><span data-stu-id="5c365-834">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="5c365-835">根據預設，資料保護金鑰不予加密。</span><span class="sxs-lookup"><span data-stu-id="5c365-835">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="5c365-836">請確保網路共用的檔案權限僅限於執行應用程式的 Windows 帳戶。</span><span class="sxs-lookup"><span data-stu-id="5c365-836">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="5c365-837">可以使用 X509 憑證來保護待用的金鑰。</span><span class="sxs-lookup"><span data-stu-id="5c365-837">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="5c365-838">請考慮下列讓使用者上傳憑證的機制：將憑證放入使用者的受信任憑證存放區，並確保在執行使用者應用程式的所有電腦上都能使用這些憑證。</span><span class="sxs-lookup"><span data-stu-id="5c365-838">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="5c365-839">如需詳細資訊，請參閱[設定 ASP.NET Core 資料保護](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="5c365-839">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="5c365-840">**設定 IIS 應用程式集區載入使用者設定檔**</span><span class="sxs-lookup"><span data-stu-id="5c365-840">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="5c365-841">此設定位在應用程式集區 [進階設定]\*\*\*\* 下的 [處理序模型]\*\*\*\* 區段中。</span><span class="sxs-lookup"><span data-stu-id="5c365-841">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="5c365-842">將 [載入使用者設定檔]\*\*\*\* 設為 `True`。</span><span class="sxs-lookup"><span data-stu-id="5c365-842">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="5c365-843">當設定為 `True` 時，金鑰會儲存在使用者設定檔目錄中，且使用具有使用者帳戶專屬金鑰的 DPAPI 保護。</span><span class="sxs-lookup"><span data-stu-id="5c365-843">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="5c365-844">金鑰會保存到 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5c365-844">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="5c365-845">應用程式集區的 [setProfileEnvironment 屬性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)也必須啟用。</span><span class="sxs-lookup"><span data-stu-id="5c365-845">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="5c365-846">`setProfileEnvironment` 的預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="5c365-846">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="5c365-847">在某些情況下 (例如 Windows OS)，`setProfileEnvironment` 會設為 `false`。</span><span class="sxs-lookup"><span data-stu-id="5c365-847">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="5c365-848">如果金鑰並未如預期地儲存在使用者設定檔目錄中：</span><span class="sxs-lookup"><span data-stu-id="5c365-848">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="5c365-849">瀏覽至 *%windir%/system32/inetsrv/config* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5c365-849">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="5c365-850">開啟 *applicationHost.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="5c365-850">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="5c365-851">找出 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="5c365-851">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="5c365-852">確認 `setProfileEnvironment` 屬性不存在 (其預設值為 `true`)，或明確地將屬性值設為 `true`。</span><span class="sxs-lookup"><span data-stu-id="5c365-852">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="5c365-853">**將檔案系統當作 Keyring 存放區使用**</span><span class="sxs-lookup"><span data-stu-id="5c365-853">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="5c365-854">調整應用程式程式碼，以[將檔案系統當作 Keyring 存放區使用](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="5c365-854">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="5c365-855">使用 X509 憑證來保護 Keyring，並確保憑證是受信任的憑證。</span><span class="sxs-lookup"><span data-stu-id="5c365-855">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="5c365-856">如果憑證為自我簽署，請將憑證放在受信任的根存放區。</span><span class="sxs-lookup"><span data-stu-id="5c365-856">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="5c365-857">在 Web 伺服陣列中使用 IIS 時：</span><span class="sxs-lookup"><span data-stu-id="5c365-857">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="5c365-858">請使用所有電腦都可以存取的檔案共用。</span><span class="sxs-lookup"><span data-stu-id="5c365-858">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="5c365-859">將 X509 憑證部署到每一部電腦。</span><span class="sxs-lookup"><span data-stu-id="5c365-859">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="5c365-860">設定[程式碼中的資料保護](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="5c365-860">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="5c365-861">**設定資料保護的全電腦原則**</span><span class="sxs-lookup"><span data-stu-id="5c365-861">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="5c365-862">針對取用資料保護 API 的所有應用程式，資料保護系統僅支援有限的預設[全電腦原則](xref:security/data-protection/configuration/machine-wide-policy)設定。</span><span class="sxs-lookup"><span data-stu-id="5c365-862">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="5c365-863">如需詳細資訊，請參閱<xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="5c365-863">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="5c365-864">虛擬目錄</span><span class="sxs-lookup"><span data-stu-id="5c365-864">Virtual Directories</span></span>

<span data-ttu-id="5c365-865">ASP.NET Core 應用程式不支援 [IIS 虛擬目錄](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="5c365-865">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="5c365-866">應用程式能以[子應用程式](#sub-applications)的形式裝載。</span><span class="sxs-lookup"><span data-stu-id="5c365-866">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="5c365-867">子應用程式</span><span class="sxs-lookup"><span data-stu-id="5c365-867">Sub-applications</span></span>

<span data-ttu-id="5c365-868">ASP.NET Core 應用程式能以 [IIS 子應用程式](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)的形式裝載。</span><span class="sxs-lookup"><span data-stu-id="5c365-868">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="5c365-869">子應用程式的路徑會成為根應用程式 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="5c365-869">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="5c365-870">子應用程式內的靜態資產連結應該使用波狀符號與斜線 (`~/`) 標記法。</span><span class="sxs-lookup"><span data-stu-id="5c365-870">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="5c365-871">波狀符號與斜線標記法會觸發[標記協助程式](xref:mvc/views/tag-helpers/intro)以將子應用程式的路徑基底附加到轉譯的相對連結前面。</span><span class="sxs-lookup"><span data-stu-id="5c365-871">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="5c365-872">針對位於 `/subapp_path` 的子應用程式，使用 `src="~/image.png"` 連結的影像會轉譯為 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="5c365-872">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="5c365-873">根應用程式的靜態檔案中介軟體不會處理靜態檔案要求。</span><span class="sxs-lookup"><span data-stu-id="5c365-873">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="5c365-874">要求會由子應用程式的靜態檔案中介軟體處理。</span><span class="sxs-lookup"><span data-stu-id="5c365-874">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="5c365-875">若靜態資產的 `src` 屬性是設定為絕對路徑 (例如，`src="/image.png"`)，會以不使用子應用程式路徑基底的方式轉譯連結。</span><span class="sxs-lookup"><span data-stu-id="5c365-875">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="5c365-876">根應用程式的靜態檔案中介軟體會嘗試從根應用程式的 [webroot](xref:fundamentals/index#web-root) 提供資產，這會導致「404 - 找不到」\*\* 回應 (除非根應用程式可存取靜態資產)。</span><span class="sxs-lookup"><span data-stu-id="5c365-876">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="5c365-877">裝載 ASP.NET Core 應用程式做為另一個 ASP.NET Core 應用程式下的子應用程式：</span><span class="sxs-lookup"><span data-stu-id="5c365-877">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="5c365-878">為子應用程式建立應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-878">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="5c365-879">將 [.NET CLR 版本]\*\*\*\* 設定為 [沒有受控碼]\*\*\*\*，因為會將核心通用語言執行平台 (CoreCLR) 開機以在背景工作處理序中裝載應用程式，而非在桌面 CLR (.NET CLR) 中裝載。</span><span class="sxs-lookup"><span data-stu-id="5c365-879">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="5c365-880">使用根網站下資料夾中的子應用程式在 IIS 管理員中新增根網站。</span><span class="sxs-lookup"><span data-stu-id="5c365-880">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="5c365-881">以滑鼠右鍵按一下 IIS 管理員中的子應用程式資料夾，然後選取 [轉換成應用程式]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-881">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="5c365-882">在 [新增應用程式]\*\*\*\* 對話方塊中，使用 [應用程式集區]\*\*\*\* 的[選取]\*\*\*\* 按鈕來指派您為子應用程式建立的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-882">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="5c365-883">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="5c365-883">Select **OK**.</span></span>

<span data-ttu-id="5c365-884">將不同的應用程式集區指派給子應用程式是使用同處理序裝載模型。</span><span class="sxs-lookup"><span data-stu-id="5c365-884">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="5c365-885">如需有關同進程裝載模型和設定 ASP.NET Core 模組的詳細資訊，請參閱 <xref:host-and-deploy/aspnet-core-module> 。</span><span class="sxs-lookup"><span data-stu-id="5c365-885">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="5c365-886">使用 web.config 的 IIS 組態</span><span class="sxs-lookup"><span data-stu-id="5c365-886">Configuration of IIS with web.config</span></span>

<span data-ttu-id="5c365-887">在對使用了 ASP.NET Core 模組的 ASP.NET Core 有作用的 IIS 情境下，設定會受 *web.config* 的 `<system.webServer>` 區段影響。</span><span class="sxs-lookup"><span data-stu-id="5c365-887">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="5c365-888">舉例來說，IIS 設定對動態壓縮有作用。</span><span class="sxs-lookup"><span data-stu-id="5c365-888">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="5c365-889">如果在伺服器層級將 IIS 設為使用動態壓縮，應用程式 *web.config* 檔案中的 `<urlCompression>` 元素則可為 ASP.NET Core 應用程式予以停用。</span><span class="sxs-lookup"><span data-stu-id="5c365-889">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="5c365-890">如需詳細資訊，請參閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="5c365-890">For more information, see the following topics:</span></span>

* [<span data-ttu-id="5c365-891">的設定參考 \<system.webServer></span><span class="sxs-lookup"><span data-stu-id="5c365-891">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="5c365-892">若要為在隔離的應用程式集區中執行的個別應用程式設定環境變數 (IIS 10.0 或更新版本的) 支援，請參閱 IIS 參考檔中[環境變數 \<environmentVariables> ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe)主題的*AppCmd.exe 命令*部分。</span><span class="sxs-lookup"><span data-stu-id="5c365-892">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="5c365-893">web.config 的組態區段</span><span class="sxs-lookup"><span data-stu-id="5c365-893">Configuration sections of web.config</span></span>

<span data-ttu-id="5c365-894">ASP.NET Core 應用程式的設定不使用 *web.config* 中 ASP.NET 4.x 應用程式的設定區段：</span><span class="sxs-lookup"><span data-stu-id="5c365-894">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="5c365-895">使用其他組態提供者設定的 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-895">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="5c365-896">如需詳細資訊，請參閱 [Configuration](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="5c365-896">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="5c365-897">應用程式集區</span><span class="sxs-lookup"><span data-stu-id="5c365-897">Application Pools</span></span>

<span data-ttu-id="5c365-898">應用程式集區隔離取決於裝載模型：</span><span class="sxs-lookup"><span data-stu-id="5c365-898">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="5c365-899">同進程裝載：應用程式必須在不同的應用程式集區中執行。</span><span class="sxs-lookup"><span data-stu-id="5c365-899">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="5c365-900">跨進程裝載：建議您在應用程式本身的應用程式集區中執行每個應用程式，以隔離彼此的應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-900">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="5c365-901">IIS [新增網站]\*\*\*\* 對話方塊預設每個應用程式皆為單一應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-901">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="5c365-902">當提供**網站名稱**時，文字會自動轉移至 [應用程式集區]\*\*\*\* 文字方塊。</span><span class="sxs-lookup"><span data-stu-id="5c365-902">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="5c365-903">新增網站時，會使用該網站名稱建立新的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-903">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="5c365-904">應用程式集區 Identity</span><span class="sxs-lookup"><span data-stu-id="5c365-904">Application Pool Identity</span></span>

<span data-ttu-id="5c365-905">應用程式集區身分識別帳戶可讓應用程式在唯一的帳戶下執行，不必建立及管理網域或本機帳戶。</span><span class="sxs-lookup"><span data-stu-id="5c365-905">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="5c365-906">在 IIS 8.0 或更新版本中，IIS 管理背景工作處理序 (WAS) 會使用新的應用程式集區名稱建立虛擬帳戶，並預設在此帳戶下執行應用程式集區的背景工作處理序。</span><span class="sxs-lookup"><span data-stu-id="5c365-906">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="5c365-907">在 [IIS 管理主控台] 中，于應用程式集區的 [ **Advanced Settings** ] 底下，確定 **Identity** 已將設定為 [使用\*\* Identity ApplicationPool\*\*：</span><span class="sxs-lookup"><span data-stu-id="5c365-907">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![應用程式集區進階設定對話方塊](index/_static/apppool-identity.png)

<span data-ttu-id="5c365-909">IIS 管理程序會在 Windows 安全系統中，以應用程式集區的名稱建立安全識別碼。</span><span class="sxs-lookup"><span data-stu-id="5c365-909">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="5c365-910">可使用此身分識別來保護資源。</span><span class="sxs-lookup"><span data-stu-id="5c365-910">Resources can be secured using this identity.</span></span> <span data-ttu-id="5c365-911">不過，此身分識別不是真正的使用者帳戶，也不會顯示在 Windows 使用者管理主控台中。</span><span class="sxs-lookup"><span data-stu-id="5c365-911">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="5c365-912">如果 IIS 背景工作處理序需要提升應用程式的存取權限，請修改包含應用程式的目錄存取控制清單 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="5c365-912">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="5c365-913">開啟 Windows 檔案總管，巡覽至目錄。</span><span class="sxs-lookup"><span data-stu-id="5c365-913">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="5c365-914">以滑鼠右鍵按一下目錄並選取 [屬性]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-914">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="5c365-915">依序選取 [安全性]\*\*\*\* 索引標籤下的 [編輯]\*\*\*\* 按鈕和 [新增]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="5c365-915">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="5c365-916">選取 [位置]\*\*\*\* 按鈕，並確定選取系統。</span><span class="sxs-lookup"><span data-stu-id="5c365-916">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="5c365-917">在 [輸入要選取的物件名稱]\*\*\*\* 區域中，輸入 **IIS AppPool\\<app_pool_name>**。</span><span class="sxs-lookup"><span data-stu-id="5c365-917">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="5c365-918">選取 [檢查名稱]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="5c365-918">Select the **Check Names** button.</span></span> <span data-ttu-id="5c365-919">針對 [DefaultAppPool]\*\*，請使用 **IIS AppPool\DefaultAppPool** 檢查名稱。</span><span class="sxs-lookup"><span data-stu-id="5c365-919">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="5c365-920">選取 [檢查名稱]\*\*\*\* 按鈕時，[DefaultAppPool]\*\*\*\* 的值便會顯示於物件名稱區域中。</span><span class="sxs-lookup"><span data-stu-id="5c365-920">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="5c365-921">您無法直接將應用程式集區名稱輸入至物件名稱區域。</span><span class="sxs-lookup"><span data-stu-id="5c365-921">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="5c365-922">檢查物件名稱時，請使用 **IIS AppPool\\<app_pool_name>** 的格式。</span><span class="sxs-lookup"><span data-stu-id="5c365-922">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![針對應用程式資料夾選取使用者或群組對話方塊：在選取 [檢查名稱] 之前，"DefaultAppPool" 這個應用程式集區名稱在物件名稱區域中會附加至 "IIS AppPool\"。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="5c365-924">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="5c365-924">Select **OK**.</span></span>

   ![針對應用程式資料夾選取使用者或群組對話方塊：選取 [檢查名稱] 之後，物件名稱 "DefaultAppPool" 會顯示在物件名稱區域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="5c365-926">預設應該會授與讀取 &amp; 執行權限。</span><span class="sxs-lookup"><span data-stu-id="5c365-926">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="5c365-927">請視需要提供其他權限。</span><span class="sxs-lookup"><span data-stu-id="5c365-927">Provide additional permissions as needed.</span></span>

<span data-ttu-id="5c365-928">也可使用 **ICACLS** 工具透過命令提示字元授與存取權限。</span><span class="sxs-lookup"><span data-stu-id="5c365-928">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="5c365-929">使用 *DefaultAppPool*作為範例，將會使用下列命令：</span><span class="sxs-lookup"><span data-stu-id="5c365-929">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="5c365-930">如需詳細資訊，請參閱 [icacls](/windows-server/administration/windows-commands/icacls) 主題。</span><span class="sxs-lookup"><span data-stu-id="5c365-930">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="5c365-931">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="5c365-931">HTTP/2 support</span></span>

<span data-ttu-id="5c365-932">在下列 IIS 部署案例中，ASP.NET Core 支援 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="5c365-932">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="5c365-933">內含式</span><span class="sxs-lookup"><span data-stu-id="5c365-933">In-process</span></span>
  * <span data-ttu-id="5c365-934">Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5c365-934">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="5c365-935">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="5c365-935">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="5c365-936">跨處理序</span><span class="sxs-lookup"><span data-stu-id="5c365-936">Out-of-process</span></span>
  * <span data-ttu-id="5c365-937">Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5c365-937">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="5c365-938">公開 Edge Server 連線使用 HTTP/2，但是對 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)的反向 Proxy 連線使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="5c365-938">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="5c365-939">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="5c365-939">TLS 1.2 or later connection</span></span>

<span data-ttu-id="5c365-940">針對建立 HTTP/2 連線時的同處理序部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會回報 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="5c365-940">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="5c365-941">針對建立 HTTP/2 連線時的跨處理序部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會回報 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="5c365-941">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="5c365-942">如需有關同處理序和跨處理序主控模型的詳細資訊，請參閱 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="5c365-942">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="5c365-943">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="5c365-943">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="5c365-944">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="5c365-944">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="5c365-945">如需使用 IIS 部署之 HTTP/2 設定的詳細資訊，請參閱 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="5c365-945">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="5c365-946">CORS 預檢要求</span><span class="sxs-lookup"><span data-stu-id="5c365-946">CORS preflight requests</span></span>

<span data-ttu-id="5c365-947">*此節只適用於以 .NET Framework 為目標的 ASP.NET Core 應用程式。*</span><span class="sxs-lookup"><span data-stu-id="5c365-947">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="5c365-948">針對以 .NET Framework 為目標的 ASP.NET Core 應用程式，在 IIS 中OPTIONS 要求預設不會傳遞到應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-948">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="5c365-949">若要瞭解如何在 *web.config* 中設定應用程式的 IIS 處理常式來傳遞選項要求，請參閱 [ASP.NET Web API 2 中啟用跨原始來源的要求： CORS 的運作方式](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="5c365-949">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="5c365-950">應用程式初始化模組與閒置逾時</span><span class="sxs-lookup"><span data-stu-id="5c365-950">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="5c365-951">在 IIS 中由 ASP.NET Core 模組版本 2 裝載時：</span><span class="sxs-lookup"><span data-stu-id="5c365-951">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="5c365-952">[應用程式初始化模組](#application-initialization-module)：應用程式裝載的同 [進程](#in-process-hosting-model) 或 [跨進程](#out-of-process-hosting-model) 可設定為在背景工作進程重新開機或伺服器重新開機時自動啟動。</span><span class="sxs-lookup"><span data-stu-id="5c365-952">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="5c365-953">[閒置 Timeout](#idle-timeout)：應用程式裝載的同 [進程](#in-process-hosting-model) 可設定為在無活動期間停止超時。</span><span class="sxs-lookup"><span data-stu-id="5c365-953">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="5c365-954">應用程式初始化模組</span><span class="sxs-lookup"><span data-stu-id="5c365-954">Application Initialization Module</span></span>

<span data-ttu-id="5c365-955">*套用到應用程式裝載同處理序與非同處理序。*</span><span class="sxs-lookup"><span data-stu-id="5c365-955">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="5c365-956">[IIS 應用程式初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一項 IIS 功能，可在應用程式集區啟動或回收時，將 HTTP 要求傳送給應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-956">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="5c365-957">要求會觸發應用程式啟動。</span><span class="sxs-lookup"><span data-stu-id="5c365-957">The request triggers the app to start.</span></span> <span data-ttu-id="5c365-958">根據預設，IIS 會發出應用程式根 URL (`/`) 的要求以初始化應用程式 (如需有關設定的詳細資訊，請參閱[額外資源](#application-initialization-module-and-idle-timeout-additional-resources))。</span><span class="sxs-lookup"><span data-stu-id="5c365-958">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="5c365-959">確認已啟用「IIS 應用程式初始化」角色功能：</span><span class="sxs-lookup"><span data-stu-id="5c365-959">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="5c365-960">在 Windows 7 或更新的電腦系統上，當在本機使用 IIS 時：</span><span class="sxs-lookup"><span data-stu-id="5c365-960">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="5c365-961">瀏覽到 [控制台]\*\* [程式]\*\* > \*\* [程式和功能]\*\* > \*\*\*\* > **[開啟或關閉 Windows 功能]** \(畫面左側\)。</span><span class="sxs-lookup"><span data-stu-id="5c365-961">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="5c365-962">開啟 [網際網路資訊服務]\*\* [World Wide Web 服務]\*\* > \*\* [應用程式開發功能]\*\* > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-962">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="5c365-963">選取 [應用程式初始化]\*\*\*\* 的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="5c365-963">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="5c365-964">在 Windows Server 2008 R2 或更新版本上：</span><span class="sxs-lookup"><span data-stu-id="5c365-964">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="5c365-965">開啟「新增角色與功能精靈」\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-965">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="5c365-966">在 [選取角色服務]\*\*\*\* 面板中，開啟 [應用程式開發]\*\*\*\* 節點。</span><span class="sxs-lookup"><span data-stu-id="5c365-966">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="5c365-967">選取 [應用程式初始化]\*\*\*\* 的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="5c365-967">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="5c365-968">使用下列任一方式為網站啟用應用程式初始化模組：</span><span class="sxs-lookup"><span data-stu-id="5c365-968">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="5c365-969">使用 IIS 管理員：</span><span class="sxs-lookup"><span data-stu-id="5c365-969">Using IIS Manager:</span></span>

  1. <span data-ttu-id="5c365-970">選取 [連線]\*\*\*\* 面板中的 [應用程式集區]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-970">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="5c365-971">以滑鼠右鍵按一下清單中應用程式的應用程式集區，然後選取 [進階設定]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-971">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="5c365-972">預設的 [啟動模式]\*\*\*\* 是 [OnDemand]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-972">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="5c365-973">將 [啟動模式]\*\*\*\* 設定為 [AlwaysRunning]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-973">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="5c365-974">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="5c365-974">Select **OK**.</span></span>
  1. <span data-ttu-id="5c365-975">開啟 [連線]\*\*\*\* 面板中的 [站台]\*\*\*\* 節點。</span><span class="sxs-lookup"><span data-stu-id="5c365-975">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="5c365-976">以滑鼠右鍵按一下應用程式，然後選取 [管理網站]\*\* [進階設定]\*\* > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-976">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="5c365-977">預設 [預先載入已啟用]\*\*\*\* 設定是 [False]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-977">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="5c365-978">將 [預先載入已啟用]\*\*\*\* 設定為 [True]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-978">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="5c365-979">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="5c365-979">Select **OK**.</span></span>

* <span data-ttu-id="5c365-980">使用 *web.config*，新增 `<applicationInitialization>` 元素並將 `doAppInitAfterRestart` 設定為 `true` 至應用程式 *web.config* 檔案中的 `<system.webServer>` 元素：</span><span class="sxs-lookup"><span data-stu-id="5c365-980">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

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

### <a name="idle-timeout"></a><span data-ttu-id="5c365-981">閒置逾時</span><span class="sxs-lookup"><span data-stu-id="5c365-981">Idle Timeout</span></span>

<span data-ttu-id="5c365-982">*僅適用於應用程式裝載同處理序。*</span><span class="sxs-lookup"><span data-stu-id="5c365-982">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="5c365-983">若要防止應用程式閒置，請使用 IIS 管理員設定應用程式集區的閒置逾時：</span><span class="sxs-lookup"><span data-stu-id="5c365-983">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="5c365-984">選取 [連線]\*\*\*\* 面板中的 [應用程式集區]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-984">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="5c365-985">以滑鼠右鍵按一下清單中應用程式的應用程式集區，然後選取 [進階設定]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-985">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="5c365-986">預設 [閒置逾時 (分鐘)]\*\*\*\* 是 **20** 分鐘。</span><span class="sxs-lookup"><span data-stu-id="5c365-986">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="5c365-987">將 [閒置逾時 (分鐘)]\*\*\*\* 設定為 **0** (零)。</span><span class="sxs-lookup"><span data-stu-id="5c365-987">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="5c365-988">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="5c365-988">Select **OK**.</span></span>
1. <span data-ttu-id="5c365-989">回收背景工作處理序。</span><span class="sxs-lookup"><span data-stu-id="5c365-989">Recycle the worker process.</span></span>

<span data-ttu-id="5c365-990">若要防止應用程式裝載[非同處理序](#out-of-process-hosting-model)逾時，請使用下列任一方式：</span><span class="sxs-lookup"><span data-stu-id="5c365-990">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="5c365-991">從外部服務對應用程式執行 Ping 以讓它繼續執行。</span><span class="sxs-lookup"><span data-stu-id="5c365-991">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="5c365-992">若應用程式只裝載背景服務，避免 IIS 裝載並使用 [Windows 服務來裝載 ASP.NET Core 應用程式](xref:host-and-deploy/windows-service)。</span><span class="sxs-lookup"><span data-stu-id="5c365-992">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="5c365-993">應用程式初始化模組與閒置逾時額外資源</span><span class="sxs-lookup"><span data-stu-id="5c365-993">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="5c365-994">IIS 8.0 應用程式初始化</span><span class="sxs-lookup"><span data-stu-id="5c365-994">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="5c365-995">[應用程式 \<applicationInitialization> 初始化](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="5c365-995">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="5c365-996">[應用程式集 \<processModel> 區的進程模型設定](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="5c365-996">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="5c365-997">IIS 系統管理員的部署資源</span><span class="sxs-lookup"><span data-stu-id="5c365-997">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="5c365-998">IIS 文件</span><span class="sxs-lookup"><span data-stu-id="5c365-998">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="5c365-999">IIS 中的 IIS 管理員使用者入門</span><span class="sxs-lookup"><span data-stu-id="5c365-999">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="5c365-1000">.NET Core 應用程式部署</span><span class="sxs-lookup"><span data-stu-id="5c365-1000">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="5c365-1001">其他資源</span><span class="sxs-lookup"><span data-stu-id="5c365-1001">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="5c365-1002">Microsoft IIS 官方網站</span><span class="sxs-lookup"><span data-stu-id="5c365-1002">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="5c365-1003">Windows Server 技術內容庫</span><span class="sxs-lookup"><span data-stu-id="5c365-1003">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="5c365-1004">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="5c365-1004">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5c365-1005">如需將 ASP.NET Core 應用程式發佈至 IIS 伺服器的教學課程體驗，請參閱<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="5c365-1005">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="5c365-1006">安裝 .NET Core 裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="5c365-1006">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="5c365-1007">支援的作業系統</span><span class="sxs-lookup"><span data-stu-id="5c365-1007">Supported operating systems</span></span>

<span data-ttu-id="5c365-1008">以下為支援的作業系統：</span><span class="sxs-lookup"><span data-stu-id="5c365-1008">The following operating systems are supported:</span></span>

* <span data-ttu-id="5c365-1009">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5c365-1009">Windows 7 or later</span></span>
* <span data-ttu-id="5c365-1010">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5c365-1010">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="5c365-1011">[HTTP.sys 伺服器](xref:fundamentals/servers/httpsys) (先前稱為 WebListener) 不適用搭配 IIS 的反向 Proxy 設定。</span><span class="sxs-lookup"><span data-stu-id="5c365-1011">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="5c365-1012">請使用 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1012">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="5c365-1013">如需在 Azure 中裝載的資訊，請參閱 <xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="5c365-1013">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="5c365-1014">如需疑難排解指引，請參閱 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="5c365-1014">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="5c365-1015">支援的平台</span><span class="sxs-lookup"><span data-stu-id="5c365-1015">Supported platforms</span></span>

<span data-ttu-id="5c365-1016">支援針對 32 位元 (x86) 或 64 位元 (x64) 部署發行的應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-1016">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="5c365-1017">使用 32 位元 (x86) .NET Core SDK 部署 32 位元應用程式，除非應用程式：</span><span class="sxs-lookup"><span data-stu-id="5c365-1017">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="5c365-1018">需要提供給 64 位元應用程式使用的較大虛擬記憶體位址空間。</span><span class="sxs-lookup"><span data-stu-id="5c365-1018">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="5c365-1019">需要較大的 IIS 堆疊大小。</span><span class="sxs-lookup"><span data-stu-id="5c365-1019">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="5c365-1020">有 64 位元原生相依性。</span><span class="sxs-lookup"><span data-stu-id="5c365-1020">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="5c365-1021">使用 64 位元 (x64) .NET Core SDK 來發行 64 位元應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-1021">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="5c365-1022">主機系統上必須有 64 位元執行階段存在。</span><span class="sxs-lookup"><span data-stu-id="5c365-1022">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="5c365-1023">ASP.NET Core 隨附 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)，其為預設的跨平台 HTTP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="5c365-1023">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="5c365-1024">在使用 [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 時，應用程式會執行於從 IIS 背景工作處理序中分離出的處理序 (跨處理序\*\*)，並搭配 [Kestrel 伺服器](xref:fundamentals/servers/index#kestrel)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1024">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="5c365-1025">因為 ASP.NET Core 應用程式執行所在的處理序會與 IIS 工作者處理序分開，所以此模組會執行處理程序管理。</span><span class="sxs-lookup"><span data-stu-id="5c365-1025">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="5c365-1026">此模組會在第一個要求到達時啟動 ASP.NET Core 應用程式的處理序，並在應用程式關閉或損毀時將它重新啟動。</span><span class="sxs-lookup"><span data-stu-id="5c365-1026">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="5c365-1027">此行為基本上與執行同處理序，並由 [Windows 處理序啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 所管理的應用程式相同。</span><span class="sxs-lookup"><span data-stu-id="5c365-1027">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="5c365-1028">下圖說明 IIS、ASP.NET Core 模組和跨處理序裝載應用程式之間的關聯性：</span><span class="sxs-lookup"><span data-stu-id="5c365-1028">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core 模組](index/_static/ancm-outofprocess.png)

<span data-ttu-id="5c365-1030">要求會從 Web 到達核心模式的 HTTP.sys 驅動程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-1030">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="5c365-1031">驅動程式會在網站設定的通訊埠上將要求路由至 IIS，此通訊埠通常是 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1031">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="5c365-1032">此模組會在應用程式的隨機通訊埠上將要求轉送至 Kestrel，而且不會是通訊埠 80 或 443。</span><span class="sxs-lookup"><span data-stu-id="5c365-1032">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="5c365-1033">模組會在啟動時透過環境變數指定埠，而 [IIS 整合中介軟體](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 會設定要接聽的伺服器 `http://localhost:{port}` 。</span><span class="sxs-lookup"><span data-stu-id="5c365-1033">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="5c365-1034">將會執行額外檢查，不是源自模組的要求都會遭到拒絕。</span><span class="sxs-lookup"><span data-stu-id="5c365-1034">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="5c365-1035">此模組不支援 HTTPS 轉送，因此即使由 IIS 透過 HTTPS 接收，要求還是會透過 HTTP 轉送。</span><span class="sxs-lookup"><span data-stu-id="5c365-1035">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="5c365-1036">Kestrel 收取來自模組的要求之後，要求會被推送至 ASP.NET Core 中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="5c365-1036">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="5c365-1037">中介軟體管線會處理要求，並將其作為 `HttpContext` 執行個體傳遞至應用程式的邏輯。</span><span class="sxs-lookup"><span data-stu-id="5c365-1037">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="5c365-1038">IIS Integration 新增的中介軟體會更新配置、遠端 IP 和帳戶路徑基底，以將要求轉送至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="5c365-1038">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="5c365-1039">應用程式的回應會傳回 IIS，而 IIS 會將其推送回起始要求的 HTTP 用戶端。</span><span class="sxs-lookup"><span data-stu-id="5c365-1039">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="5c365-1040">`CreateDefaultBuilder` 會將 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器設為網頁伺服器，並設定 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)的基底路徑與連接埠來啟用 IIS 整合。</span><span class="sxs-lookup"><span data-stu-id="5c365-1040">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="5c365-1041">ASP.NET Core 模組會產生要指派給後端處理序的動態連接埠。</span><span class="sxs-lookup"><span data-stu-id="5c365-1041">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="5c365-1042">`CreateDefaultBuilder` 會呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 方法。</span><span class="sxs-lookup"><span data-stu-id="5c365-1042">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="5c365-1043">`UseIISIntegration` 會將 Kestrel 設定為在位於 localhost IP 位址 (`127.0.0.1`) 的動態連接埠上接聽。</span><span class="sxs-lookup"><span data-stu-id="5c365-1043">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="5c365-1044">若動態連接埠是 1234，Kestrel 會在 `127.0.0.1:1234` 接聽。</span><span class="sxs-lookup"><span data-stu-id="5c365-1044">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="5c365-1045">此設定會取代由下列項目提供的其他 URL 設定：</span><span class="sxs-lookup"><span data-stu-id="5c365-1045">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="5c365-1046">Kestrel 的接聽 API</span><span class="sxs-lookup"><span data-stu-id="5c365-1046">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="5c365-1047">[設定](xref:fundamentals/configuration/index) (或[命令列 --urls 選項](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="5c365-1047">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="5c365-1048">在使用模組時不需要呼叫 `UseUrls` 或 Kestrel 的 `Listen` API。</span><span class="sxs-lookup"><span data-stu-id="5c365-1048">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="5c365-1049">若呼叫 `UseUrls` 或 `Listen`，Kestrel 只會接聽未使用 IIS 執行應用程式時指定的連接埠。</span><span class="sxs-lookup"><span data-stu-id="5c365-1049">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="5c365-1050">如需 ASP.NET Core 模組組態指南，請參閱 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="5c365-1050">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="5c365-1051">如需代管的詳細資訊，請參閱[在 ASP.NET Core 中代管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1051">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="5c365-1052">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="5c365-1052">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="5c365-1053">啟用 IISIntegration 元件</span><span class="sxs-lookup"><span data-stu-id="5c365-1053">Enable the IISIntegration components</span></span>

<span data-ttu-id="5c365-1054">在 `CreateWebHostBuilder` (*Program.cs*) 中建立主機時，請呼叫 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 來啟用 IIS 整合：</span><span class="sxs-lookup"><span data-stu-id="5c365-1054">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="5c365-1055">如需 `CreateDefaultBuilder` 的詳細資訊，請參閱 <xref:fundamentals/host/web-host#set-up-a-host>。</span><span class="sxs-lookup"><span data-stu-id="5c365-1055">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="5c365-1056">IIS 選項</span><span class="sxs-lookup"><span data-stu-id="5c365-1056">IIS options</span></span>

| <span data-ttu-id="5c365-1057">選項</span><span class="sxs-lookup"><span data-stu-id="5c365-1057">Option</span></span>                         | <span data-ttu-id="5c365-1058">預設</span><span class="sxs-lookup"><span data-stu-id="5c365-1058">Default</span></span> | <span data-ttu-id="5c365-1059">設定</span><span class="sxs-lookup"><span data-stu-id="5c365-1059">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="5c365-1060">若為 `true`，IIS 伺服器會設定由 [Windows 驗證](xref:security/authentication/windowsauth)所驗證的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="5c365-1060">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="5c365-1061">若為 `false`，則伺服器僅會對 `HttpContext.User` 提供身分識別，並在 `AuthenticationScheme` 明確要求時回應挑戰。</span><span class="sxs-lookup"><span data-stu-id="5c365-1061">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="5c365-1062">必須在 IIS 中啟用 Windows 驗證以讓 `AutomaticAuthentication` 作用。</span><span class="sxs-lookup"><span data-stu-id="5c365-1062">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="5c365-1063">如需詳細資訊，請參閱 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1063">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="5c365-1064">設定使用者在登入頁面上看到的顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="5c365-1064">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="5c365-1065">若要設定 IIS 選項，請在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中加入 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服務設定。</span><span class="sxs-lookup"><span data-stu-id="5c365-1065">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="5c365-1066">下列範例會防止應用程式填入 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="5c365-1066">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="5c365-1067">選項</span><span class="sxs-lookup"><span data-stu-id="5c365-1067">Option</span></span>                         | <span data-ttu-id="5c365-1068">預設</span><span class="sxs-lookup"><span data-stu-id="5c365-1068">Default</span></span> | <span data-ttu-id="5c365-1069">設定</span><span class="sxs-lookup"><span data-stu-id="5c365-1069">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="5c365-1070">若為 `true`，[IIS 整合中介軟體](#enable-the-iisintegration-components)會設定由 [Windows 驗證](xref:security/authentication/windowsauth)所驗證的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="5c365-1070">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="5c365-1071">如果為 `false`，則驗證中介軟體僅針對 `HttpContext.User` 提供身分識別，並在游 `AuthenticationScheme` 提出明確要求時回應挑戰。</span><span class="sxs-lookup"><span data-stu-id="5c365-1071">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="5c365-1072">必須在 IIS 中啟用 Windows 驗證以讓 `AutomaticAuthentication` 作用。</span><span class="sxs-lookup"><span data-stu-id="5c365-1072">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="5c365-1073">如需詳細資訊，請參閱 [Windows 驗證](xref:security/authentication/windowsauth)主題。</span><span class="sxs-lookup"><span data-stu-id="5c365-1073">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="5c365-1074">設定使用者在登入頁面上看到的顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="5c365-1074">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="5c365-1075">如果為 `true` 且 `MS-ASPNETCORE-CLIENTCERT` 要求標頭已存在，則會填入 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="5c365-1075">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="5c365-1076">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="5c365-1076">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="5c365-1077">用來設定轉送標頭中介軟體及 ASP.NET Core 模組的 [IIS 整合中介軟體](#enable-the-iisintegration-components)會設定為轉送配置 (HTTP/HTTPS) 與發出要求的遠端 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="5c365-1077">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="5c365-1078">其他 Proxy 伺服器和負載平衡器後方託管的應用程式可能需要其他設定。</span><span class="sxs-lookup"><span data-stu-id="5c365-1078">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="5c365-1079">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1079">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="5c365-1080">web.config 檔案</span><span class="sxs-lookup"><span data-stu-id="5c365-1080">web.config file</span></span>

<span data-ttu-id="5c365-1081">*web.config* 檔案是用來設定 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1081">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="5c365-1082">發佈專案時，由 MSBuild 目標 (`_TransformWebConfig`) 處理 *web.config* 檔案的建立、轉換及發佈。</span><span class="sxs-lookup"><span data-stu-id="5c365-1082">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="5c365-1083">此目標存在於 Web SDK 目標 (`Microsoft.NET.Sdk.Web`)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1083">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="5c365-1084">SDK 設定在專案檔的頂端：</span><span class="sxs-lookup"><span data-stu-id="5c365-1084">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="5c365-1085">如果專案中沒有 *web.config* 檔案，則系統會使用正確的 *processPath* 和 *arguments* 建立該檔案以設定 ASP.NET Core 模組，並將該檔案移至[已發行的輸出](xref:host-and-deploy/directory-structure)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1085">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="5c365-1086">如果 *web.config* 檔案存在於專案中，則系統會使用正確的 *processPath* 和 *arguments* 來轉換該檔案以設定 ASP.NET Core 模組，然後將它移至已發行的輸出。</span><span class="sxs-lookup"><span data-stu-id="5c365-1086">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="5c365-1087">轉換不會修改檔案中的 IIS 組態設定。</span><span class="sxs-lookup"><span data-stu-id="5c365-1087">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="5c365-1088">*web.config* 檔案可提供能控制作用中 IIS 模組的額外 IIS 組態設定。</span><span class="sxs-lookup"><span data-stu-id="5c365-1088">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="5c365-1089">如需能處理 ASP.NET Core 應用程式要求之 IIS 模組的相關資訊，請參閱 [IIS 模組](xref:host-and-deploy/iis/modules)主題。</span><span class="sxs-lookup"><span data-stu-id="5c365-1089">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="5c365-1090">若要防止 Web SDK 轉換 *web.config* 檔案，請使用專案檔 **\<IsTransformWebConfigDisabled>** 中的屬性：</span><span class="sxs-lookup"><span data-stu-id="5c365-1090">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="5c365-1091">使 Web SDK 無法轉換檔案時，應該由開發人員手動設定 *processPath* 和 *arguments*。</span><span class="sxs-lookup"><span data-stu-id="5c365-1091">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="5c365-1092">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="5c365-1092">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="5c365-1093">web.config 檔案位置</span><span class="sxs-lookup"><span data-stu-id="5c365-1093">web.config file location</span></span>

<span data-ttu-id="5c365-1094">為了正確設定 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module) ， *web.config* 檔案必須存在於 [內容根](xref:fundamentals/index#content-root) 路徑 (通常是已部署應用程式) 的應用程式基底路徑。</span><span class="sxs-lookup"><span data-stu-id="5c365-1094">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="5c365-1095">這是與提供給 IIS 的網站實體路徑相同的位置。</span><span class="sxs-lookup"><span data-stu-id="5c365-1095">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="5c365-1096">應用程式的根目錄需有 *web.config* 檔案，才能使用 Web Deploy 發行多個應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-1096">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="5c365-1097">機密檔案存在於應用程式的實體路徑上，例如\* \<assembly>.runtimeconfig.json*、 \* \<assembly> .xml* (xml 檔批註) 以及\* \<assembly>.deps.js\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-1097">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="5c365-1098">當 *web.config* 檔案存在且網站正常啟動時，如果有人要求機密檔案，IIS 不會予以提供。</span><span class="sxs-lookup"><span data-stu-id="5c365-1098">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="5c365-1099">若 *web.config* 檔案遺失或沒有正確命名，或是無法設定網站以正常啟動，IIS 可能會公開提供機密檔案。</span><span class="sxs-lookup"><span data-stu-id="5c365-1099">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="5c365-1100">\***web.config*檔案必須在部署中隨時都存在、正確命名，而且能夠設定網站以正常啟動。請勿從生產環境部署中移除*web.config\*的檔案。**</span><span class="sxs-lookup"><span data-stu-id="5c365-1100">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="5c365-1101">轉換 web.config</span><span class="sxs-lookup"><span data-stu-id="5c365-1101">Transform web.config</span></span>

<span data-ttu-id="5c365-1102">如需在發佈時轉換 *web.config* (例如依據設定、設定檔或環境設定環境變數)，請參閱<xref:host-and-deploy/iis/transform-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="5c365-1102">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="5c365-1103">IIS 組態</span><span class="sxs-lookup"><span data-stu-id="5c365-1103">IIS configuration</span></span>

<span data-ttu-id="5c365-1104">**Windows Server 作業系統**</span><span class="sxs-lookup"><span data-stu-id="5c365-1104">**Windows Server operating systems**</span></span>

<span data-ttu-id="5c365-1105">啟用**網頁伺服器 (IIS)** 伺服器角色，並建立角色服務。</span><span class="sxs-lookup"><span data-stu-id="5c365-1105">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="5c365-1106">使用來自 [管理]\*\*\*\* 功能表的 [新增角色及功能]\*\*\*\* 精靈，或是 [伺服器管理員]\*\*\*\* 中的連結。</span><span class="sxs-lookup"><span data-stu-id="5c365-1106">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="5c365-1107">在**伺服器角色**步驟中，核取 [網頁伺服器 (IIS)]\*\*\*\* 方塊。</span><span class="sxs-lookup"><span data-stu-id="5c365-1107">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在選取伺服器角色步驟中選取網頁伺服器 IIS 角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="5c365-1109">在 [功能]\*\*\*\* 步驟之後，[角色服務]\*\*\*\* 步驟會針對網頁伺服器 (IIS) 進行載入。</span><span class="sxs-lookup"><span data-stu-id="5c365-1109">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="5c365-1110">選取所需的 IIS 角色服務或接受所提供的預設角色服務。</span><span class="sxs-lookup"><span data-stu-id="5c365-1110">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在選取角色服務步驟中，選取預設的角色服務。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="5c365-1112">**Windows 驗證 (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5c365-1112">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="5c365-1113">若要啟用 Windows 驗證，請展開下列節點：**網頁伺服器**  >  **安全性**。</span><span class="sxs-lookup"><span data-stu-id="5c365-1113">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="5c365-1114">選取 [Windows 驗證]\*\*\*\* 功能。</span><span class="sxs-lookup"><span data-stu-id="5c365-1114">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="5c365-1115">如需詳細資訊，請參閱[Windows 驗證 \<windowsAuthentication> ](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)和[設定 Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1115">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="5c365-1116">**WebSocket (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5c365-1116">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="5c365-1117">WebSocket 由 ASP.NET Core 1.1 或更新版本所支援。</span><span class="sxs-lookup"><span data-stu-id="5c365-1117">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="5c365-1118">若要啟用 websocket，請展開下列節點：**網頁伺服器**  >  **應用程式開發**。</span><span class="sxs-lookup"><span data-stu-id="5c365-1118">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="5c365-1119">選取 [WebSocket 通訊協定]\*\*\*\* 功能。</span><span class="sxs-lookup"><span data-stu-id="5c365-1119">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="5c365-1120">如需詳細資訊，請參閱 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1120">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="5c365-1121">透過**確認**步驟繼續作業，安裝網頁伺服器角色和服務。</span><span class="sxs-lookup"><span data-stu-id="5c365-1121">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="5c365-1122">在安裝 \*\*Web 服務器 (iis) \*\* 角色之後，不需要重新開機伺服器。</span><span class="sxs-lookup"><span data-stu-id="5c365-1122">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="5c365-1123">**Windows 桌面作業系統**</span><span class="sxs-lookup"><span data-stu-id="5c365-1123">**Windows desktop operating systems**</span></span>

<span data-ttu-id="5c365-1124">啟用 [IIS 管理主控台]\*\*\*\* 和 [World Wide Web 服務]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-1124">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="5c365-1125">瀏覽到 [控制台]\*\* [程式]\*\* > \*\* [程式和功能]\*\* > \*\*\*\* > **[開啟或關閉 Windows 功能]** \(畫面左側\)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1125">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="5c365-1126">開啟 [Internet Information Services]\*\*\*\* 節點。</span><span class="sxs-lookup"><span data-stu-id="5c365-1126">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="5c365-1127">開啟 [Web 管理工具]\*\*\*\* 節點。</span><span class="sxs-lookup"><span data-stu-id="5c365-1127">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="5c365-1128">核取 [IIS 管理主控台]\*\*\*\* 方塊。</span><span class="sxs-lookup"><span data-stu-id="5c365-1128">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="5c365-1129">[World Wide Web Services] (全球資訊網服務)\*\*\*\* 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="5c365-1129">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="5c365-1130">接受**全球資訊網服務**的預設功能，或自訂 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="5c365-1130">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="5c365-1131">**Windows 驗證 (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5c365-1131">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="5c365-1132">若要啟用 Windows 驗證，請展開下列節點： **World Wide Web 服務**  >  **安全性**。</span><span class="sxs-lookup"><span data-stu-id="5c365-1132">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="5c365-1133">選取 [Windows 驗證]\*\*\*\* 功能。</span><span class="sxs-lookup"><span data-stu-id="5c365-1133">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="5c365-1134">如需詳細資訊，請參閱[Windows 驗證 \<windowsAuthentication> ](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)和[設定 Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1134">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="5c365-1135">**WebSocket (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5c365-1135">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="5c365-1136">WebSocket 由 ASP.NET Core 1.1 或更新版本所支援。</span><span class="sxs-lookup"><span data-stu-id="5c365-1136">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="5c365-1137">若要啟用 websocket，請展開下列節點： **World Wide Web 服務**  >  **應用程式開發功能**。</span><span class="sxs-lookup"><span data-stu-id="5c365-1137">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="5c365-1138">選取 [WebSocket 通訊協定]\*\*\*\* 功能。</span><span class="sxs-lookup"><span data-stu-id="5c365-1138">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="5c365-1139">如需詳細資訊，請參閱 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1139">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="5c365-1140">若 IIS 安裝需要重新啟動，請重新啟動系統。</span><span class="sxs-lookup"><span data-stu-id="5c365-1140">If the IIS installation requires a restart, restart the system.</span></span>

![選取 [Windows 功能] 中的 [IIS 管理主控台] 和 [World Wide Web Services] (全球資訊網服務)。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="5c365-1142">安裝 .NET Core 裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="5c365-1142">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="5c365-1143">在主控系統上安裝 .NET Core 裝載套件組合\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-1143">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="5c365-1144">套件組合會安裝 .NET Core 執行時間、.NET Core 程式庫和 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1144">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="5c365-1145">此模組可讓 ASP.NET Core 應用程式在 IIS 背後執行。</span><span class="sxs-lookup"><span data-stu-id="5c365-1145">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5c365-1146">若裝載套件組合在 IIS 之前安裝，則必須對該套件組合安裝進行修復。</span><span class="sxs-lookup"><span data-stu-id="5c365-1146">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="5c365-1147">請在安裝 IIS 之後，再次執行裝載套件組合安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-1147">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="5c365-1148">如果在安裝 64 位元 (x64) 版本的 .NET Core 後才安裝裝載套件組合，那麼可能會遺漏 SDK ([未偵測到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="5c365-1148">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="5c365-1149">若要解決此問題，請參閱 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="5c365-1149">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="5c365-1150">下載</span><span class="sxs-lookup"><span data-stu-id="5c365-1150">Download</span></span>

1. <span data-ttu-id="5c365-1151">流覽至 [ [下載 .Net Core](https://dotnet.microsoft.com/download/dotnet-core) ] 頁面。</span><span class="sxs-lookup"><span data-stu-id="5c365-1151">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="5c365-1152">選取所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="5c365-1152">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="5c365-1153">在 [執行應用程式 - 執行階段]\*\*\*\* 欄中，尋找想要的 .NET Core 執行階段版本列。</span><span class="sxs-lookup"><span data-stu-id="5c365-1153">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="5c365-1154">使用 **裝載** 套件組合連結來下載安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-1154">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="5c365-1155">某些安裝程式包含已達到期生命週期結束 (EOL) 的發行版本，這些發行版本已不受 Microsoft 支援。</span><span class="sxs-lookup"><span data-stu-id="5c365-1155">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="5c365-1156">如需詳細資訊，請參閱[支援原則](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) \(英文 \)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1156">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="5c365-1157">安裝裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="5c365-1157">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="5c365-1158">在伺服器上執行安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-1158">Run the installer on the server.</span></span> <span data-ttu-id="5c365-1159">從系統管理員命令殼層執行安裝程式時，有 下列參數可用：</span><span class="sxs-lookup"><span data-stu-id="5c365-1159">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="5c365-1160">`OPT_NO_ANCM=1`：略過安裝 ASP.NET Core 模組。</span><span class="sxs-lookup"><span data-stu-id="5c365-1160">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="5c365-1161">`OPT_NO_RUNTIME=1`：略過安裝 .NET Core 執行時間。</span><span class="sxs-lookup"><span data-stu-id="5c365-1161">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="5c365-1162">當伺服器僅裝載 [獨立部署 (SCD) ](/dotnet/core/deploying/#self-contained-deployments-scd)時使用。</span><span class="sxs-lookup"><span data-stu-id="5c365-1162">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="5c365-1163">`OPT_NO_SHAREDFX=1`：略過安裝 ASP.NET 共用架構 (ASP.NET 執行時間) 。</span><span class="sxs-lookup"><span data-stu-id="5c365-1163">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="5c365-1164">當伺服器僅裝載 [獨立部署 (SCD) ](/dotnet/core/deploying/#self-contained-deployments-scd)時使用。</span><span class="sxs-lookup"><span data-stu-id="5c365-1164">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="5c365-1165">`OPT_NO_X86=1`：略過安裝 x86 執行時間。</span><span class="sxs-lookup"><span data-stu-id="5c365-1165">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="5c365-1166">當您確定不會裝載 32 位元應用程式時，請使用此參數。</span><span class="sxs-lookup"><span data-stu-id="5c365-1166">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="5c365-1167">如果將來有可能同時裝載 32 位元和 64 位元應用程式，請不要使用此參數並安裝這兩個執行階段。</span><span class="sxs-lookup"><span data-stu-id="5c365-1167">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="5c365-1168">`OPT_NO_SHARED_CONFIG_CHECK=1`：當共用設定 (*applicationHost.config*) 位於與 IIS 安裝相同的電腦上時，請停用 iis 共用設定的檢查。</span><span class="sxs-lookup"><span data-stu-id="5c365-1168">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="5c365-1169">*只在 ASP.NET Core 2.2 或更新版本的裝載套件組合安裝程式上可用。*</span><span class="sxs-lookup"><span data-stu-id="5c365-1169">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="5c365-1170">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="5c365-1170">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="5c365-1171">重新開機系統，或在命令 shell 中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="5c365-1171">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="5c365-1172">重新啟動 IIS 將能偵測到由安裝程式對系統路徑 (此為環境變數) 所做出的變更。</span><span class="sxs-lookup"><span data-stu-id="5c365-1172">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="5c365-1173">安裝裝載套件組合時，不需要在 IIS 中手動停止個別的網站。</span><span class="sxs-lookup"><span data-stu-id="5c365-1173">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="5c365-1174">IIS 網站 (的託管應用程式) 在 IIS 重新開機時重新開機。</span><span class="sxs-lookup"><span data-stu-id="5c365-1174">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="5c365-1175">當應用程式收到第一個要求（包括從 [應用程式初始化模組](#application-initialization-module-and-idle-timeout)）時，就會再次啟動。</span><span class="sxs-lookup"><span data-stu-id="5c365-1175">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="5c365-1176">針對共用架構套件的修補程式版本，ASP.NET Core 採用向前復原行為。</span><span class="sxs-lookup"><span data-stu-id="5c365-1176">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="5c365-1177">當 IIS 以 IIS 重新開機應用程式時，應用程式會在收到第一個要求時，以其參考封裝的最新修補程式版本進行載入。</span><span class="sxs-lookup"><span data-stu-id="5c365-1177">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="5c365-1178">如果未重新開機 IIS，應用程式就會重新開機，並在其背景工作進程回收並收到其第一個要求時，呈現向前復原行為。</span><span class="sxs-lookup"><span data-stu-id="5c365-1178">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="5c365-1179">如需 IIS 共用組態的資訊，請參閱[使用 IIS 共用組態的 ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1179">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="5c365-1180">使用 Visual Studio 發佈時安裝 Web Deploy</span><span class="sxs-lookup"><span data-stu-id="5c365-1180">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="5c365-1181">將應用程式部署到具有 [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later) 的伺服器時，請在伺服器上安裝最新版的 Web Deploy。</span><span class="sxs-lookup"><span data-stu-id="5c365-1181">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="5c365-1182">若要安裝 Web Deploy，請使用 [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=43717)直接取得安裝程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-1182">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="5c365-1183">慣用的方法是使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="5c365-1183">The preferred method is to use WebPI.</span></span> <span data-ttu-id="5c365-1184">WebPI 提供獨立的安裝程式和組態以裝載提供者。</span><span class="sxs-lookup"><span data-stu-id="5c365-1184">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="5c365-1185">建立 IIS 網站</span><span class="sxs-lookup"><span data-stu-id="5c365-1185">Create the IIS site</span></span>

1. <span data-ttu-id="5c365-1186">在主控系統上，請建立資料夾以容納應用程式的已發行資料夾和檔案。</span><span class="sxs-lookup"><span data-stu-id="5c365-1186">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="5c365-1187">在下列步驟中，您提供資料夾路徑給 IIS，作為應用程式的實體路徑。</span><span class="sxs-lookup"><span data-stu-id="5c365-1187">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="5c365-1188">如需應用程式之部署資料夾和檔案配置的詳細資訊，請參閱 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="5c365-1188">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="5c365-1189">在 [IIS 管理員] 中 **，在 [連線] 面板中** 開啟伺服器的節點。</span><span class="sxs-lookup"><span data-stu-id="5c365-1189">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="5c365-1190">以滑鼠右鍵按一下 [網站]\*\*\*\* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5c365-1190">Right-click the **Sites** folder.</span></span> <span data-ttu-id="5c365-1191">從操作功能表選取 [新增網站]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-1191">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="5c365-1192">提供**網站名稱**，並將**實體路徑**設定為應用程式的部署資料夾。</span><span class="sxs-lookup"><span data-stu-id="5c365-1192">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="5c365-1193">提供系結 **設定，並** 選取 **[確定]** 以建立網站：</span><span class="sxs-lookup"><span data-stu-id="5c365-1193">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在新增網站步驟中提供站台名稱、實體路徑和主機名稱。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="5c365-1195">請**勿**使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1195">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="5c365-1196">最上層萬用字元繫結可能暴露您的應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="5c365-1196">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="5c365-1197">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="5c365-1197">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="5c365-1198">請使用明確主機名稱，而非萬用字元。</span><span class="sxs-lookup"><span data-stu-id="5c365-1198">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="5c365-1199">若您擁有整個父網域 (與具弱點的 `*.com` 相對) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 就沒有此安全性風險。</span><span class="sxs-lookup"><span data-stu-id="5c365-1199">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="5c365-1200">如需詳細資訊，請參閱 [rfc7230 5.4 節](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1200">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="5c365-1201">在伺服器的節點之下，選取 [應用程式集區]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-1201">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="5c365-1202">以滑鼠右鍵按一下網站的應用程式集區，然後從操作功能表選取 [基本設定]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-1202">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="5c365-1203">在 [編輯應用程式集區]\*\*\*\* 視窗中，將 [.NET CLR 版本]\*\*\*\* 設定為 [沒有受控碼]\*\*\*\*：</span><span class="sxs-lookup"><span data-stu-id="5c365-1203">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![將 .NET CLR 版本設為 [沒有受控碼]。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="5c365-1205">ASP.NET Core 會在不同的處理序中執行，並管理執行階段。</span><span class="sxs-lookup"><span data-stu-id="5c365-1205">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="5c365-1206">ASP.NET Core 不仰賴載入桌面 CLR (.NET CLR)&mdash;會使用 .NET Core 的核心通用語言執行平台 (CoreCLR) 來開機以在背景工作處理序中裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-1206">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="5c365-1207">將 [.NET CLR 版本]\*\*\*\* 設定為 [沒有受控碼]\*\*\*\* 是選擇性的，但建議這樣做。</span><span class="sxs-lookup"><span data-stu-id="5c365-1207">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="5c365-1208">*ASP.NET Core 2.2 或更新版本*：對於使用[同處理序主控模型](#in-process-hosting-model)的 64 位元 (x64) [獨立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，會停用 32 位元 (x86) 處理序的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-1208">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="5c365-1209">在 IIS 管理員的 [動作]\*\*\*\* 資訊看板 > [應用程式集區]\*\*\*\* 中，選取 [設定應用程式集區預設值]\*\*\*\* 或 [進階設定]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-1209">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="5c365-1210">找到 [啟用 32 位元應用程式]\*\*\*\*，然後將其值設定為 `False`。</span><span class="sxs-lookup"><span data-stu-id="5c365-1210">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="5c365-1211">此設定不會影響為[處理程序外裝載](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)部署的應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-1211">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="5c365-1212">確認處理序模型身分識別具有適當的權限。</span><span class="sxs-lookup"><span data-stu-id="5c365-1212">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="5c365-1213">如果應用程式集區的預設身分識別 (**進程模型**  >  **Identity**) 從\*\*ApplicationPool Identity \*\*變更為另一個身分識別，請確認新的身分識別具有存取應用程式資料夾、資料庫和其他必要資源所需的許可權。</span><span class="sxs-lookup"><span data-stu-id="5c365-1213">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="5c365-1214">例如，應用程式集區需要針對應用程式讀取和寫入檔案的資料夾取得讀取和寫入權限。</span><span class="sxs-lookup"><span data-stu-id="5c365-1214">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="5c365-1215">**Windows 驗證設定 (選擇性)**</span><span class="sxs-lookup"><span data-stu-id="5c365-1215">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="5c365-1216">如需詳細資訊，請參閱[設定 Windows 驗證](xref:security/authentication/windowsauth)主題。</span><span class="sxs-lookup"><span data-stu-id="5c365-1216">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="5c365-1217">部署應用程式</span><span class="sxs-lookup"><span data-stu-id="5c365-1217">Deploy the app</span></span>

<span data-ttu-id="5c365-1218">將應用程式部署至在[建立 IIS 網站](#create-the-iis-site)一節中所建立的 IIS **實體路徑**資料夾。</span><span class="sxs-lookup"><span data-stu-id="5c365-1218">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="5c365-1219">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) 是建議的部署機制，但有數個選項可將應用程式從專案的 [publish]\*\* 資料夾移動至主機系統的部署資料夾。</span><span class="sxs-lookup"><span data-stu-id="5c365-1219">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="5c365-1220">使用 Visual Studio 的 Web Deploy</span><span class="sxs-lookup"><span data-stu-id="5c365-1220">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="5c365-1221">請參閱[適用於 ASP.NET Core 應用程式部署的 Visual Studio 發行設定檔](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)主題，了解如何建立搭配 Web Deploy 使用的發行設定檔。</span><span class="sxs-lookup"><span data-stu-id="5c365-1221">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="5c365-1222">如果主機服務提供者提供或支援建立發行設定檔，請下載其設定檔，並使用 Visual Studio 的 [發行]\*\*\*\* 對話方塊匯入其設定檔：</span><span class="sxs-lookup"><span data-stu-id="5c365-1222">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![[發佈] 對話方塊頁](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="5c365-1224">Visual Studio 外部的 Web Deploy</span><span class="sxs-lookup"><span data-stu-id="5c365-1224">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="5c365-1225">透過命令列也可以在 Visual Studio 外部使用 [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1225">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="5c365-1226">如需詳細資訊，請參閱 [Web 部署工具](/iis/publish/using-web-deploy/use-the-web-deployment-tool)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1226">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="5c365-1227">Web Deploy 的替代項目</span><span class="sxs-lookup"><span data-stu-id="5c365-1227">Alternatives to Web Deploy</span></span>

<span data-ttu-id="5c365-1228">使用數種方法的其中一種將應用程式移到主機系統，例如手動複製、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1228">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="5c365-1229">如需 IIS 的 ASP.NET Core 部署詳細資訊，請參閱 [IIS 系統管理員的部署資源](#deployment-resources-for-iis-administrators)一節。</span><span class="sxs-lookup"><span data-stu-id="5c365-1229">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="5c365-1230">瀏覽網站</span><span class="sxs-lookup"><span data-stu-id="5c365-1230">Browse the website</span></span>

<span data-ttu-id="5c365-1231">應用程式部署到主機系統之後，請向其中一個應用程式的公用端點發出要求。</span><span class="sxs-lookup"><span data-stu-id="5c365-1231">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="5c365-1232">在下列範例中，網站繫結至 IIS 上的**連接埠** `80`，其**主機名稱**為 `www.mysite.com`。</span><span class="sxs-lookup"><span data-stu-id="5c365-1232">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="5c365-1233">對 `http://www.mysite.com` 發出要求：</span><span class="sxs-lookup"><span data-stu-id="5c365-1233">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 瀏覽器已載入 IIS 啟動頁面。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="5c365-1235">已鎖定的部署檔案</span><span class="sxs-lookup"><span data-stu-id="5c365-1235">Locked deployment files</span></span>

<span data-ttu-id="5c365-1236">當應用程式執行時，會鎖定部署資料夾中的檔案。</span><span class="sxs-lookup"><span data-stu-id="5c365-1236">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="5c365-1237">無法於部署期間覆寫已鎖定的檔案。</span><span class="sxs-lookup"><span data-stu-id="5c365-1237">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="5c365-1238">若要釋放部署中的已鎖定檔案，請使用下列其中**一種**方法停止應用程式集區：</span><span class="sxs-lookup"><span data-stu-id="5c365-1238">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="5c365-1239">使用 Web Deploy 並參考專案檔中的 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="5c365-1239">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="5c365-1240">*app_offline.htm* 檔案是放在 Web 應用程式目錄的根目錄中。</span><span class="sxs-lookup"><span data-stu-id="5c365-1240">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="5c365-1241">當檔案存在時，ASP.NET Core 模組會正常關閉應用程式，並在部署期間提供 *app_offline.htm* 檔案。</span><span class="sxs-lookup"><span data-stu-id="5c365-1241">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="5c365-1242">如需詳細資訊，請參閱 [ASP.NET Core 模組組態參考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1242">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="5c365-1243">在伺服器上的 IIS 管理員中手動停止應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-1243">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="5c365-1244">使用 PowerShell 卸載 *app_offline.htm* (需要 PowerShell 5 或更新版本) ：</span><span class="sxs-lookup"><span data-stu-id="5c365-1244">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="5c365-1245">資料保護</span><span class="sxs-lookup"><span data-stu-id="5c365-1245">Data protection</span></span>

<span data-ttu-id="5c365-1246">[ASP.NET Core 資料保護堆疊](xref:security/data-protection/introduction)是由數個 ASP.NET Core [中介軟體](xref:fundamentals/middleware/index)所使用，包括用於驗證的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="5c365-1246">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="5c365-1247">即使資料保護 API 不是由使用者程式碼呼叫，仍應使用部署指令碼或是在使用者程式碼中設定資料保護，以建立持續性的密碼編譯[金鑰存放區](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1247">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="5c365-1248">如不設定資料保護，金鑰會保留在記憶體中，並於應用程式重新啟動時捨棄。</span><span class="sxs-lookup"><span data-stu-id="5c365-1248">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="5c365-1249">如果 Keyring 儲存在記憶體中，則當應用程式重新啟動時：</span><span class="sxs-lookup"><span data-stu-id="5c365-1249">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="5c365-1250">所有 cookie 的驗證權杖都會失效。</span><span class="sxs-lookup"><span data-stu-id="5c365-1250">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="5c365-1251">當使用者提出下一個要求時，需要再次登入。</span><span class="sxs-lookup"><span data-stu-id="5c365-1251">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="5c365-1252">所有以 Keyring 保護的資料都無法再解密。</span><span class="sxs-lookup"><span data-stu-id="5c365-1252">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="5c365-1253">這可能包括 [CSRF 權杖](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 和 [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1253">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="5c365-1254">若要在 IIS 下設定資料保護以保存 Keyring，請使用下列其中**一種**方法：</span><span class="sxs-lookup"><span data-stu-id="5c365-1254">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="5c365-1255">**建立資料保護登錄機碼**</span><span class="sxs-lookup"><span data-stu-id="5c365-1255">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="5c365-1256">ASP.NET Core 應用程式所使用的資料保護金鑰會儲存在應用程式外部的登錄中。</span><span class="sxs-lookup"><span data-stu-id="5c365-1256">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="5c365-1257">若要保存指定應用程式的金鑰，請為應用程式集區建立登錄機碼。</span><span class="sxs-lookup"><span data-stu-id="5c365-1257">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="5c365-1258">若為獨立的非Web 伺服陣列 IIS 安裝，請針對搭配使用 ASP.NET Core 應用程式的每個應用程式集區，使用[資料保護 Provision-AutoGenKeys.ps1 PowerShell 指令碼](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1258">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="5c365-1259">此指令碼會在 HKLM 登錄中建立登錄機碼，只有應用程式之應用程式集區的背景工作處理序帳戶可以存取它。</span><span class="sxs-lookup"><span data-stu-id="5c365-1259">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="5c365-1260">在待用期間使用 DPAPI 和全電腦金鑰加密金鑰。</span><span class="sxs-lookup"><span data-stu-id="5c365-1260">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="5c365-1261">在 Web 伺服陣列案例中，應用程式可以設定成使用 UNC 路徑來儲存其資料保護 Keyring。</span><span class="sxs-lookup"><span data-stu-id="5c365-1261">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="5c365-1262">根據預設，資料保護金鑰不予加密。</span><span class="sxs-lookup"><span data-stu-id="5c365-1262">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="5c365-1263">請確保網路共用的檔案權限僅限於執行應用程式的 Windows 帳戶。</span><span class="sxs-lookup"><span data-stu-id="5c365-1263">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="5c365-1264">可以使用 X509 憑證來保護待用的金鑰。</span><span class="sxs-lookup"><span data-stu-id="5c365-1264">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="5c365-1265">請考慮下列讓使用者上傳憑證的機制：將憑證放入使用者的受信任憑證存放區，並確保在執行使用者應用程式的所有電腦上都能使用這些憑證。</span><span class="sxs-lookup"><span data-stu-id="5c365-1265">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="5c365-1266">如需詳細資訊，請參閱[設定 ASP.NET Core 資料保護](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1266">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="5c365-1267">**設定 IIS 應用程式集區載入使用者設定檔**</span><span class="sxs-lookup"><span data-stu-id="5c365-1267">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="5c365-1268">此設定位在應用程式集區 [進階設定]\*\*\*\* 下的 [處理序模型]\*\*\*\* 區段中。</span><span class="sxs-lookup"><span data-stu-id="5c365-1268">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="5c365-1269">將 [載入使用者設定檔]\*\*\*\* 設為 `True`。</span><span class="sxs-lookup"><span data-stu-id="5c365-1269">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="5c365-1270">當設定為 `True` 時，金鑰會儲存在使用者設定檔目錄中，且使用具有使用者帳戶專屬金鑰的 DPAPI 保護。</span><span class="sxs-lookup"><span data-stu-id="5c365-1270">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="5c365-1271">金鑰會保存到 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5c365-1271">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="5c365-1272">應用程式集區的 [setProfileEnvironment 屬性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)也必須啟用。</span><span class="sxs-lookup"><span data-stu-id="5c365-1272">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="5c365-1273">`setProfileEnvironment` 的預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="5c365-1273">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="5c365-1274">在某些情況下 (例如 Windows OS)，`setProfileEnvironment` 會設為 `false`。</span><span class="sxs-lookup"><span data-stu-id="5c365-1274">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="5c365-1275">如果金鑰並未如預期地儲存在使用者設定檔目錄中：</span><span class="sxs-lookup"><span data-stu-id="5c365-1275">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="5c365-1276">瀏覽至 *%windir%/system32/inetsrv/config* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="5c365-1276">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="5c365-1277">開啟 *applicationHost.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="5c365-1277">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="5c365-1278">找出 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="5c365-1278">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="5c365-1279">確認 `setProfileEnvironment` 屬性不存在 (其預設值為 `true`)，或明確地將屬性值設為 `true`。</span><span class="sxs-lookup"><span data-stu-id="5c365-1279">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="5c365-1280">**將檔案系統當作 Keyring 存放區使用**</span><span class="sxs-lookup"><span data-stu-id="5c365-1280">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="5c365-1281">調整應用程式程式碼，以[將檔案系統當作 Keyring 存放區使用](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1281">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="5c365-1282">使用 X509 憑證來保護 Keyring，並確保憑證是受信任的憑證。</span><span class="sxs-lookup"><span data-stu-id="5c365-1282">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="5c365-1283">如果憑證為自我簽署，請將憑證放在受信任的根存放區。</span><span class="sxs-lookup"><span data-stu-id="5c365-1283">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="5c365-1284">在 Web 伺服陣列中使用 IIS 時：</span><span class="sxs-lookup"><span data-stu-id="5c365-1284">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="5c365-1285">請使用所有電腦都可以存取的檔案共用。</span><span class="sxs-lookup"><span data-stu-id="5c365-1285">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="5c365-1286">將 X509 憑證部署到每一部電腦。</span><span class="sxs-lookup"><span data-stu-id="5c365-1286">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="5c365-1287">設定[程式碼中的資料保護](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1287">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="5c365-1288">**設定資料保護的全電腦原則**</span><span class="sxs-lookup"><span data-stu-id="5c365-1288">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="5c365-1289">針對取用資料保護 API 的所有應用程式，資料保護系統僅支援有限的預設[全電腦原則](xref:security/data-protection/configuration/machine-wide-policy)設定。</span><span class="sxs-lookup"><span data-stu-id="5c365-1289">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="5c365-1290">如需詳細資訊，請參閱<xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="5c365-1290">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="5c365-1291">虛擬目錄</span><span class="sxs-lookup"><span data-stu-id="5c365-1291">Virtual Directories</span></span>

<span data-ttu-id="5c365-1292">ASP.NET Core 應用程式不支援 [IIS 虛擬目錄](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1292">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="5c365-1293">應用程式能以[子應用程式](#sub-applications)的形式裝載。</span><span class="sxs-lookup"><span data-stu-id="5c365-1293">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="5c365-1294">子應用程式</span><span class="sxs-lookup"><span data-stu-id="5c365-1294">Sub-applications</span></span>

<span data-ttu-id="5c365-1295">ASP.NET Core 應用程式能以 [IIS 子應用程式](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)的形式裝載。</span><span class="sxs-lookup"><span data-stu-id="5c365-1295">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="5c365-1296">子應用程式的路徑會成為根應用程式 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="5c365-1296">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="5c365-1297">子應用程式不應該包括 ASP.NET Core 模組做為處理常式。</span><span class="sxs-lookup"><span data-stu-id="5c365-1297">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="5c365-1298">如果您在子應用程式的 *web.config* 檔案中將模組新增為處理常式，則嘗試瀏覽子應用程式時，會收到參考錯誤設定檔的 *500.19 內部伺服器錯誤*。</span><span class="sxs-lookup"><span data-stu-id="5c365-1298">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="5c365-1299">下列範例顯示 ASP.NET Core 子應用程式的已發佈 *web.config* 檔案：</span><span class="sxs-lookup"><span data-stu-id="5c365-1299">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

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

<span data-ttu-id="5c365-1300">在 ASP.NET Core 應用程式下裝載非 ASP.NET Core 子應用程式時，請明確移除子應用程式 *web.config* 檔案中已繼承的處理常式：</span><span class="sxs-lookup"><span data-stu-id="5c365-1300">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

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

<span data-ttu-id="5c365-1301">子應用程式內的靜態資產連結應該使用波狀符號與斜線 (`~/`) 標記法。</span><span class="sxs-lookup"><span data-stu-id="5c365-1301">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="5c365-1302">波狀符號與斜線標記法會觸發[標記協助程式](xref:mvc/views/tag-helpers/intro)以將子應用程式的路徑基底附加到轉譯的相對連結前面。</span><span class="sxs-lookup"><span data-stu-id="5c365-1302">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="5c365-1303">針對位於 `/subapp_path` 的子應用程式，使用 `src="~/image.png"` 連結的影像會轉譯為 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="5c365-1303">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="5c365-1304">根應用程式的靜態檔案中介軟體不會處理靜態檔案要求。</span><span class="sxs-lookup"><span data-stu-id="5c365-1304">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="5c365-1305">要求會由子應用程式的靜態檔案中介軟體處理。</span><span class="sxs-lookup"><span data-stu-id="5c365-1305">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="5c365-1306">若靜態資產的 `src` 屬性是設定為絕對路徑 (例如，`src="/image.png"`)，會以不使用子應用程式路徑基底的方式轉譯連結。</span><span class="sxs-lookup"><span data-stu-id="5c365-1306">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="5c365-1307">根應用程式的靜態檔案中介軟體會嘗試從根應用程式的 [webroot](xref:fundamentals/index#web-root) 提供資產，這會導致「404 - 找不到」\*\* 回應 (除非根應用程式可存取靜態資產)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1307">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="5c365-1308">裝載 ASP.NET Core 應用程式做為另一個 ASP.NET Core 應用程式下的子應用程式：</span><span class="sxs-lookup"><span data-stu-id="5c365-1308">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="5c365-1309">為子應用程式建立應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-1309">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="5c365-1310">將 [.NET CLR 版本]\*\*\*\* 設定為 [沒有受控碼]\*\*\*\*，因為會將核心通用語言執行平台 (CoreCLR) 開機以在背景工作處理序中裝載應用程式，而非在桌面 CLR (.NET CLR) 中裝載。</span><span class="sxs-lookup"><span data-stu-id="5c365-1310">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="5c365-1311">使用根網站下資料夾中的子應用程式在 IIS 管理員中新增根網站。</span><span class="sxs-lookup"><span data-stu-id="5c365-1311">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="5c365-1312">以滑鼠右鍵按一下 IIS 管理員中的子應用程式資料夾，然後選取 [轉換成應用程式]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-1312">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="5c365-1313">在 [新增應用程式]\*\*\*\* 對話方塊中，使用 [應用程式集區]\*\*\*\* 的[選取]\*\*\*\* 按鈕來指派您為子應用程式建立的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-1313">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="5c365-1314">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="5c365-1314">Select **OK**.</span></span>

<span data-ttu-id="5c365-1315">將不同的應用程式集區指派給子應用程式是使用同處理序裝載模型。</span><span class="sxs-lookup"><span data-stu-id="5c365-1315">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="5c365-1316">如需有關同進程裝載模型和設定 ASP.NET Core 模組的詳細資訊，請參閱 <xref:host-and-deploy/aspnet-core-module> 。</span><span class="sxs-lookup"><span data-stu-id="5c365-1316">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="5c365-1317">使用 web.config 的 IIS 組態</span><span class="sxs-lookup"><span data-stu-id="5c365-1317">Configuration of IIS with web.config</span></span>

<span data-ttu-id="5c365-1318">在對使用了 ASP.NET Core 模組的 ASP.NET Core 有作用的 IIS 情境下，設定會受 *web.config* 的 `<system.webServer>` 區段影響。</span><span class="sxs-lookup"><span data-stu-id="5c365-1318">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="5c365-1319">舉例來說，IIS 設定對動態壓縮有作用。</span><span class="sxs-lookup"><span data-stu-id="5c365-1319">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="5c365-1320">如果在伺服器層級將 IIS 設為使用動態壓縮，應用程式 *web.config* 檔案中的 `<urlCompression>` 元素則可為 ASP.NET Core 應用程式予以停用。</span><span class="sxs-lookup"><span data-stu-id="5c365-1320">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="5c365-1321">如需詳細資訊，請參閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="5c365-1321">For more information, see the following topics:</span></span>

* [<span data-ttu-id="5c365-1322">的設定參考 \<system.webServer></span><span class="sxs-lookup"><span data-stu-id="5c365-1322">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="5c365-1323">若要為在隔離的應用程式集區中執行的個別應用程式設定環境變數 (IIS 10.0 或更新版本的) 支援，請參閱 IIS 參考檔中[環境變數 \<environmentVariables> ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe)主題的*AppCmd.exe 命令*部分。</span><span class="sxs-lookup"><span data-stu-id="5c365-1323">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="5c365-1324">web.config 的組態區段</span><span class="sxs-lookup"><span data-stu-id="5c365-1324">Configuration sections of web.config</span></span>

<span data-ttu-id="5c365-1325">ASP.NET Core 應用程式的設定不使用 *web.config* 中 ASP.NET 4.x 應用程式的設定區段：</span><span class="sxs-lookup"><span data-stu-id="5c365-1325">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="5c365-1326">使用其他組態提供者設定的 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-1326">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="5c365-1327">如需詳細資訊，請參閱 [Configuration](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1327">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="5c365-1328">應用程式集區</span><span class="sxs-lookup"><span data-stu-id="5c365-1328">Application Pools</span></span>

<span data-ttu-id="5c365-1329">在伺服器上裝載多個網站時，建議您在其各自的應用程式集區中執行各個應用程式，讓應用程式彼此隔離。</span><span class="sxs-lookup"><span data-stu-id="5c365-1329">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="5c365-1330">IIS [新增網站]\*\*\*\* 對話方塊預設成此組態。</span><span class="sxs-lookup"><span data-stu-id="5c365-1330">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="5c365-1331">當提供**網站名稱**時，文字會自動轉移至 [應用程式集區]\*\*\*\* 文字方塊。</span><span class="sxs-lookup"><span data-stu-id="5c365-1331">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="5c365-1332">新增網站時，會使用該網站名稱建立新的應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="5c365-1332">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="5c365-1333">應用程式集區 Identity</span><span class="sxs-lookup"><span data-stu-id="5c365-1333">Application Pool Identity</span></span>

<span data-ttu-id="5c365-1334">應用程式集區身分識別帳戶可讓應用程式在唯一的帳戶下執行，不必建立及管理網域或本機帳戶。</span><span class="sxs-lookup"><span data-stu-id="5c365-1334">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="5c365-1335">在 IIS 8.0 或更新版本中，IIS 管理背景工作處理序 (WAS) 會使用新的應用程式集區名稱建立虛擬帳戶，並預設在此帳戶下執行應用程式集區的背景工作處理序。</span><span class="sxs-lookup"><span data-stu-id="5c365-1335">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="5c365-1336">在 [IIS 管理主控台] 中，于應用程式集區的 [ **Advanced Settings** ] 底下，確定 **Identity** 已將設定為 [使用\*\* Identity ApplicationPool\*\*：</span><span class="sxs-lookup"><span data-stu-id="5c365-1336">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![應用程式集區進階設定對話方塊](index/_static/apppool-identity.png)

<span data-ttu-id="5c365-1338">IIS 管理程序會在 Windows 安全系統中，以應用程式集區的名稱建立安全識別碼。</span><span class="sxs-lookup"><span data-stu-id="5c365-1338">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="5c365-1339">可使用此身分識別來保護資源。</span><span class="sxs-lookup"><span data-stu-id="5c365-1339">Resources can be secured using this identity.</span></span> <span data-ttu-id="5c365-1340">不過，此身分識別不是真正的使用者帳戶，也不會顯示在 Windows 使用者管理主控台中。</span><span class="sxs-lookup"><span data-stu-id="5c365-1340">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="5c365-1341">如果 IIS 背景工作處理序需要提升應用程式的存取權限，請修改包含應用程式的目錄存取控制清單 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="5c365-1341">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="5c365-1342">開啟 Windows 檔案總管，巡覽至目錄。</span><span class="sxs-lookup"><span data-stu-id="5c365-1342">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="5c365-1343">以滑鼠右鍵按一下目錄並選取 [屬性]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5c365-1343">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="5c365-1344">依序選取 [安全性]\*\*\*\* 索引標籤下的 [編輯]\*\*\*\* 按鈕和 [新增]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="5c365-1344">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="5c365-1345">選取 [位置]\*\*\*\* 按鈕，並確定選取系統。</span><span class="sxs-lookup"><span data-stu-id="5c365-1345">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="5c365-1346">在 [輸入要選取的物件名稱]\*\*\*\* 區域中，輸入 **IIS AppPool\\<app_pool_name>**。</span><span class="sxs-lookup"><span data-stu-id="5c365-1346">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="5c365-1347">選取 [檢查名稱]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="5c365-1347">Select the **Check Names** button.</span></span> <span data-ttu-id="5c365-1348">針對 [DefaultAppPool]\*\*，請使用 **IIS AppPool\DefaultAppPool** 檢查名稱。</span><span class="sxs-lookup"><span data-stu-id="5c365-1348">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="5c365-1349">選取 [檢查名稱]\*\*\*\* 按鈕時，[DefaultAppPool]\*\*\*\* 的值便會顯示於物件名稱區域中。</span><span class="sxs-lookup"><span data-stu-id="5c365-1349">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="5c365-1350">您無法直接將應用程式集區名稱輸入至物件名稱區域。</span><span class="sxs-lookup"><span data-stu-id="5c365-1350">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="5c365-1351">檢查物件名稱時，請使用 **IIS AppPool\\<app_pool_name>** 的格式。</span><span class="sxs-lookup"><span data-stu-id="5c365-1351">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![針對應用程式資料夾選取使用者或群組對話方塊：在選取 [檢查名稱] 之前，"DefaultAppPool" 這個應用程式集區名稱在物件名稱區域中會附加至 "IIS AppPool\"。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="5c365-1353">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="5c365-1353">Select **OK**.</span></span>

   ![針對應用程式資料夾選取使用者或群組對話方塊：選取 [檢查名稱] 之後，物件名稱 "DefaultAppPool" 會顯示在物件名稱區域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="5c365-1355">預設應該會授與讀取 &amp; 執行權限。</span><span class="sxs-lookup"><span data-stu-id="5c365-1355">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="5c365-1356">請視需要提供其他權限。</span><span class="sxs-lookup"><span data-stu-id="5c365-1356">Provide additional permissions as needed.</span></span>

<span data-ttu-id="5c365-1357">也可使用 **ICACLS** 工具透過命令提示字元授與存取權限。</span><span class="sxs-lookup"><span data-stu-id="5c365-1357">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="5c365-1358">使用 *DefaultAppPool*作為範例，將會使用下列命令：</span><span class="sxs-lookup"><span data-stu-id="5c365-1358">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="5c365-1359">如需詳細資訊，請參閱 [icacls](/windows-server/administration/windows-commands/icacls) 主題。</span><span class="sxs-lookup"><span data-stu-id="5c365-1359">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="5c365-1360">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="5c365-1360">HTTP/2 support</span></span>

<span data-ttu-id="5c365-1361">[HTTP/2](https://httpwg.org/specs/rfc7540.html) 支援符合下列基本需求的跨處理序部署：</span><span class="sxs-lookup"><span data-stu-id="5c365-1361">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="5c365-1362">Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="5c365-1362">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="5c365-1363">公開 Edge Server 連線使用 HTTP/2，但是對 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)的反向 Proxy 連線使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="5c365-1363">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="5c365-1364">目標 Framework：不適用於跨處理序部署，因為 HTTP/2 連線完全由 IIS 處理。</span><span class="sxs-lookup"><span data-stu-id="5c365-1364">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="5c365-1365">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="5c365-1365">TLS 1.2 or later connection</span></span>

<span data-ttu-id="5c365-1366">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="5c365-1366">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="5c365-1367">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="5c365-1367">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="5c365-1368">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="5c365-1368">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="5c365-1369">如需使用 IIS 部署之 HTTP/2 設定的詳細資訊，請參閱 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1369">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="5c365-1370">CORS 預檢要求</span><span class="sxs-lookup"><span data-stu-id="5c365-1370">CORS preflight requests</span></span>

<span data-ttu-id="5c365-1371">*此節只適用於以 .NET Framework 為目標的 ASP.NET Core 應用程式。*</span><span class="sxs-lookup"><span data-stu-id="5c365-1371">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="5c365-1372">針對以 .NET Framework 為目標的 ASP.NET Core 應用程式，在 IIS 中OPTIONS 要求預設不會傳遞到應用程式。</span><span class="sxs-lookup"><span data-stu-id="5c365-1372">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="5c365-1373">若要瞭解如何在 *web.config* 中設定應用程式的 IIS 處理常式來傳遞選項要求，請參閱 [ASP.NET Web API 2 中啟用跨原始來源的要求： CORS 的運作方式](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="5c365-1373">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="5c365-1374">IIS 系統管理員的部署資源</span><span class="sxs-lookup"><span data-stu-id="5c365-1374">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="5c365-1375">IIS 文件</span><span class="sxs-lookup"><span data-stu-id="5c365-1375">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="5c365-1376">IIS 中的 IIS 管理員使用者入門</span><span class="sxs-lookup"><span data-stu-id="5c365-1376">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="5c365-1377">.NET Core 應用程式部署</span><span class="sxs-lookup"><span data-stu-id="5c365-1377">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="5c365-1378">其他資源</span><span class="sxs-lookup"><span data-stu-id="5c365-1378">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="5c365-1379">Microsoft IIS 官方網站</span><span class="sxs-lookup"><span data-stu-id="5c365-1379">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="5c365-1380">Windows Server 技術內容庫</span><span class="sxs-lookup"><span data-stu-id="5c365-1380">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="5c365-1381">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="5c365-1381">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
