---
title: ASP.NET Core 中的網頁伺服器實作
author: rick-anderson
description: 探索 ASP.NET Core 的網頁伺服器 Kestrel 與 HTTP.sys。 了解如何選擇伺服器，以及何時使用反向 Proxy 伺服器。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
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
uid: fundamentals/servers/index
ms.openlocfilehash: a49388c73f2ec0ea03b35dbba1575ce70a4e1512
ms.sourcegitcommit: 75db2f684a9302b0be7925eab586aa091c6bd19f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2021
ms.locfileid: "99238332"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="c77e9-104">ASP.NET Core 中的網頁伺服器實作</span><span class="sxs-lookup"><span data-stu-id="c77e9-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="c77e9-105">由 [Tom Dykstra](https://github.com/tdykstra)、[Steve Smith](https://ardalis.com/)、[Stephen Halter](https://twitter.com/halter73) 和 [Chris Ross](https://github.com/Tratcher) 提供</span><span class="sxs-lookup"><span data-stu-id="c77e9-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="c77e9-106">ASP.NET Core 應用程式執行時，需使用內含式 HTTP 伺服器實作。</span><span class="sxs-lookup"><span data-stu-id="c77e9-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="c77e9-107">伺服器實作會接聽 HTTP 要求，並以組成 <xref:Microsoft.AspNetCore.Http.HttpContext> 的一組[要求功能](xref:fundamentals/request-features)形式向應用程式呈現。</span><span class="sxs-lookup"><span data-stu-id="c77e9-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="c77e9-108">Windows</span><span class="sxs-lookup"><span data-stu-id="c77e9-108">Windows</span></span>](#tab/windows)

<span data-ttu-id="c77e9-109">ASP.NET Core 隨附下列項目：</span><span class="sxs-lookup"><span data-stu-id="c77e9-109">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="c77e9-110">[Kestrel 伺服器](xref:fundamentals/servers/kestrel)是預設、跨平台的 HTTP 伺服器實作。</span><span class="sxs-lookup"><span data-stu-id="c77e9-110">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span> <span data-ttu-id="c77e9-111">Kestrel 可提供最佳的效能和記憶體使用率，但 HTTP.sys 中並沒有一些先進的功能。</span><span class="sxs-lookup"><span data-stu-id="c77e9-111">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys.</span></span> <span data-ttu-id="c77e9-112">如需詳細資訊，請參閱下一節中的 [Kestrel 與 HTTP.sys](#korh) 。</span><span class="sxs-lookup"><span data-stu-id="c77e9-112">For more information, see [Kestrel vs. HTTP.sys](#korh) in the next section.</span></span>
* <span data-ttu-id="c77e9-113">IIS HTTP 伺服器是 IIS 的[同處理序伺服器](#hosting-models)。</span><span class="sxs-lookup"><span data-stu-id="c77e9-113">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="c77e9-114">[HTTP.sys 伺服器](xref:fundamentals/servers/httpsys)是以 [HTTP.sys 核心驅動程式與 HTTP 伺服器 API](/windows/desktop/Http/http-api-start-page) 為基礎的僅限 Windows HTTP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="c77e9-114">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="c77e9-115">使用 [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 時，應用程式可能會執行於：</span><span class="sxs-lookup"><span data-stu-id="c77e9-115">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="c77e9-116">在與 IIS 背景工作進程相同的程式中 (同 [進程裝載模型](#hosting-models)) 與 Iis HTTP 伺服器一起使用。</span><span class="sxs-lookup"><span data-stu-id="c77e9-116">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="c77e9-117">「同處理序」是建議的設定。</span><span class="sxs-lookup"><span data-stu-id="c77e9-117">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="c77e9-118">從 IIS 背景工作處理序中分離出的處理序 ([跨處理序裝載模型](#hosting-models))，並搭配 [Kestrel 伺服器](#kestrel)。</span><span class="sxs-lookup"><span data-stu-id="c77e9-118">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="c77e9-119">[ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)是一種原生 IIS 模組，可處理 IIS 與同處理序 IIS HTTP 伺服器或 Kestrel 之間的原生 IIS 要求。</span><span class="sxs-lookup"><span data-stu-id="c77e9-119">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="c77e9-120">如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="c77e9-120">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<a name="korh"></a>

## <a name="kestrel-vs-httpsys"></a><span data-ttu-id="c77e9-121">Kestrel 與 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="c77e9-121">Kestrel vs. HTTP.sys</span></span>

<span data-ttu-id="c77e9-122">Kestrel 具有下列優於 HTTP.sys 的優點：</span><span class="sxs-lookup"><span data-stu-id="c77e9-122">Kestrel has the following advantages over HTTP.sys:</span></span>

  * <span data-ttu-id="c77e9-123">更佳的效能和記憶體使用率。</span><span class="sxs-lookup"><span data-stu-id="c77e9-123">Better performance and memory utilization.</span></span>
  * <span data-ttu-id="c77e9-124">跨平台</span><span class="sxs-lookup"><span data-stu-id="c77e9-124">Cross platform</span></span>
  * <span data-ttu-id="c77e9-125">彈性，它是與作業系統無關的開發和修補。</span><span class="sxs-lookup"><span data-stu-id="c77e9-125">Agility, it's developed and patched independent of the OS.</span></span>
  * <span data-ttu-id="c77e9-126">程式設計埠和 TLS 設定</span><span class="sxs-lookup"><span data-stu-id="c77e9-126">Programmatic port and TLS configuration</span></span>
  * <span data-ttu-id="c77e9-127">允許 [PPv2](https://github.com/aspnet/AspLabs/blob/master/src/ProxyProtocol/ProxyProtocol.Sample/ProxyProtocol.cs) 和替代傳輸等通訊協定的擴充性。</span><span class="sxs-lookup"><span data-stu-id="c77e9-127">Extensibility that allows for protocols like [PPv2](https://github.com/aspnet/AspLabs/blob/master/src/ProxyProtocol/ProxyProtocol.Sample/ProxyProtocol.cs) and alternate transports.</span></span>

<span data-ttu-id="c77e9-128">Http.Sys 會以共用核心模式元件的形式運作，kestrel 沒有下列功能：</span><span class="sxs-lookup"><span data-stu-id="c77e9-128">Http.Sys operates as a shared kernel mode component with the following features that kestrel does not have:</span></span>

  * <span data-ttu-id="c77e9-129">連接埠共用</span><span class="sxs-lookup"><span data-stu-id="c77e9-129">Port sharing</span></span>
  * <span data-ttu-id="c77e9-130">核心模式 windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="c77e9-130">Kernel mode windows authentication.</span></span> <span data-ttu-id="c77e9-131">[Kestrel 只支援使用者模式驗證](xref:security/authentication/windowsauth#kestrel)。</span><span class="sxs-lookup"><span data-stu-id="c77e9-131">[Kestrel supports only user-mode authentication](xref:security/authentication/windowsauth#kestrel).</span></span>
  * <span data-ttu-id="c77e9-132">透過佇列傳送快速 proxy</span><span class="sxs-lookup"><span data-stu-id="c77e9-132">Fast proxying via queue transfers</span></span>
  * <span data-ttu-id="c77e9-133">直接檔案傳輸</span><span class="sxs-lookup"><span data-stu-id="c77e9-133">Direct file transmission</span></span>
  * <span data-ttu-id="c77e9-134">回應快取</span><span class="sxs-lookup"><span data-stu-id="c77e9-134">Response caching</span></span>

## <a name="hosting-models"></a><span data-ttu-id="c77e9-135">裝載模型</span><span class="sxs-lookup"><span data-stu-id="c77e9-135">Hosting models</span></span>

<span data-ttu-id="c77e9-136">使用同處理序裝載，ASP.NET Core 應用程式會在與其 IIS 工作者處理序相同的處理序中執行。</span><span class="sxs-lookup"><span data-stu-id="c77e9-136">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="c77e9-137">因為要求未透過回送介面卡 (將連出網路流量傳回同一部電腦的網路介面) 進行 proxy 處理，所以同處理序裝載會提供優於跨處理序裝載的效能。</span><span class="sxs-lookup"><span data-stu-id="c77e9-137">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="c77e9-138">IIS 透過 [Windows 處理序啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 來執行處理程序管理。</span><span class="sxs-lookup"><span data-stu-id="c77e9-138">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="c77e9-139">使用非同處理序代管，ASP.NET Core 應用程式可執行於與 IIS 背景工作處理序不同的處理序中，且此模組會進行處理序的管理。</span><span class="sxs-lookup"><span data-stu-id="c77e9-139">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="c77e9-140">此模組會在第一個要求到達時啟動 ASP.NET Core 應用程式的處理序，並在應用程式關閉或損毀時將它重新啟動。</span><span class="sxs-lookup"><span data-stu-id="c77e9-140">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="c77e9-141">此行為基本上與執行同處理序，並由 [Windows 處理序啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 所管理的應用程式相同。</span><span class="sxs-lookup"><span data-stu-id="c77e9-141">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="c77e9-142">如需詳細資訊與組態指南，請參閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="c77e9-142">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="c77e9-143">macOS</span><span class="sxs-lookup"><span data-stu-id="c77e9-143">macOS</span></span>](#tab/macos)

<span data-ttu-id="c77e9-144">ASP.NET Core 隨附 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)，這是預設、跨平台的 HTTP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="c77e9-144">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="c77e9-145">Linux</span><span class="sxs-lookup"><span data-stu-id="c77e9-145">Linux</span></span>](#tab/linux)

<span data-ttu-id="c77e9-146">ASP.NET Core 隨附 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)，這是預設、跨平台的 HTTP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="c77e9-146">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

## <a name="kestrel"></a><span data-ttu-id="c77e9-147">Kestrel</span><span class="sxs-lookup"><span data-stu-id="c77e9-147">Kestrel</span></span>

 <span data-ttu-id="c77e9-148">[Kestrel 伺服器](xref:fundamentals/servers/kestrel)是預設、跨平台的 HTTP 伺服器實作。</span><span class="sxs-lookup"><span data-stu-id="c77e9-148">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span> <span data-ttu-id="c77e9-149">Kestrel 可提供最佳的效能和記憶體使用率，但 HTTP.sys 中並沒有一些先進的功能。</span><span class="sxs-lookup"><span data-stu-id="c77e9-149">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys.</span></span> <span data-ttu-id="c77e9-150">如需詳細資訊，請參閱本檔中的 [Kestrel 與 HTTP.sys](#korh) 。</span><span class="sxs-lookup"><span data-stu-id="c77e9-150">For more information, see [Kestrel vs. HTTP.sys](#korh) in this document.</span></span>

<span data-ttu-id="c77e9-151">使用 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="c77e9-151">Use Kestrel:</span></span>

* <span data-ttu-id="c77e9-152">供本身當作直接從網路 (包括網際網路) 處理要求的邊緣伺服器。</span><span class="sxs-lookup"><span data-stu-id="c77e9-152">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel 不使用反向 Proxy 伺服器直接與網際網路通訊](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="c77e9-154">搭配「反向 Proxy 伺服器」使用，例如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)。</span><span class="sxs-lookup"><span data-stu-id="c77e9-154">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="c77e9-155">反向 Proxy 伺服器會從網際網路接收 HTTP 要求，然後轉送到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c77e9-155">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel 透過 IIS、Nginx 或 Apache 等反向 Proxy 伺服器間接與網際網路通訊](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="c77e9-157">支援裝載設定 &mdash; ，不論是否使用反向 proxy 伺服器 &mdash; 。</span><span class="sxs-lookup"><span data-stu-id="c77e9-157">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="c77e9-158">如需 Kestrel 設定指南及資訊，以了解在反向 Proxy 設定中使用 Kestrel 的時機，請參閱 <xref:fundamentals/servers/kestrel>。</span><span class="sxs-lookup"><span data-stu-id="c77e9-158">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="c77e9-159">Windows</span><span class="sxs-lookup"><span data-stu-id="c77e9-159">Windows</span></span>](#tab/windows)

<span data-ttu-id="c77e9-160">ASP.NET Core 隨附下列項目：</span><span class="sxs-lookup"><span data-stu-id="c77e9-160">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="c77e9-161">[Kestrel 伺服器](xref:fundamentals/servers/kestrel)是預設、跨平台的 HTTP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="c77e9-161">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="c77e9-162">[HTTP.sys 伺服器](xref:fundamentals/servers/httpsys)是以 [HTTP.sys 核心驅動程式與 HTTP 伺服器 API](/windows/desktop/Http/http-api-start-page) 為基礎的僅限 Windows HTTP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="c77e9-162">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="c77e9-163">在使用 [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 時，應用程式會執行於從 IIS 背景工作處理序中分離出的處理序 (跨處理序)，並搭配 [Kestrel 伺服器](#kestrel)。</span><span class="sxs-lookup"><span data-stu-id="c77e9-163">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="c77e9-164">因為 ASP.NET Core 應用程式執行所在的處理序會與 IIS 工作者處理序分開，所以此模組會執行處理程序管理。</span><span class="sxs-lookup"><span data-stu-id="c77e9-164">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="c77e9-165">此模組會在第一個要求到達時啟動 ASP.NET Core 應用程式的處理序，並在應用程式關閉或損毀時將它重新啟動。</span><span class="sxs-lookup"><span data-stu-id="c77e9-165">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="c77e9-166">此行為基本上與執行同處理序，並由 [Windows 處理序啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 所管理的應用程式相同。</span><span class="sxs-lookup"><span data-stu-id="c77e9-166">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="c77e9-167">下圖說明 IIS、ASP.NET Core 模組和跨處理序裝載應用程式之間的關聯性：</span><span class="sxs-lookup"><span data-stu-id="c77e9-167">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core 模組](_static/ancm-outofprocess.png)

<span data-ttu-id="c77e9-169">要求會從 Web 到達核心模式的 HTTP.sys 驅動程式。</span><span class="sxs-lookup"><span data-stu-id="c77e9-169">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="c77e9-170">驅動程式會在網站設定的通訊埠上將要求路由至 IIS，此通訊埠通常是 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="c77e9-170">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="c77e9-171">此模組會在應用程式的隨機通訊埠上將要求轉送至 Kestrel，而且不會是通訊埠 80 或 443。</span><span class="sxs-lookup"><span data-stu-id="c77e9-171">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="c77e9-172">模組會在啟動時透過環境變數指定埠，而 [IIS 整合中介軟體](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 會設定要接聽的伺服器 `http://localhost:{port}` 。</span><span class="sxs-lookup"><span data-stu-id="c77e9-172">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="c77e9-173">將會執行額外檢查，不是源自模組的要求都會遭到拒絕。</span><span class="sxs-lookup"><span data-stu-id="c77e9-173">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="c77e9-174">此模組不支援 HTTPS 轉送，因此即使由 IIS 透過 HTTPS 接收，要求還是會透過 HTTP 轉送。</span><span class="sxs-lookup"><span data-stu-id="c77e9-174">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="c77e9-175">Kestrel 收取來自模組的要求之後，要求會被推送至 ASP.NET Core 中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="c77e9-175">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="c77e9-176">中介軟體管線會處理要求，並將其作為 `HttpContext` 執行個體傳遞至應用程式的邏輯。</span><span class="sxs-lookup"><span data-stu-id="c77e9-176">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="c77e9-177">IIS Integration 新增的中介軟體會更新配置、遠端 IP 和帳戶路徑基底，以將要求轉送至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c77e9-177">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="c77e9-178">應用程式的回應會傳回 IIS，而 IIS 會將其推送回起始要求的 HTTP 用戶端。</span><span class="sxs-lookup"><span data-stu-id="c77e9-178">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="c77e9-179">如需 IIS 和 ASP.NET Core 模組的設定指南，請參閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="c77e9-179">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="c77e9-180">macOS</span><span class="sxs-lookup"><span data-stu-id="c77e9-180">macOS</span></span>](#tab/macos)

<span data-ttu-id="c77e9-181">ASP.NET Core 隨附 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)，這是預設、跨平台的 HTTP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="c77e9-181">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="c77e9-182">Linux</span><span class="sxs-lookup"><span data-stu-id="c77e9-182">Linux</span></span>](#tab/linux)

<span data-ttu-id="c77e9-183">ASP.NET Core 隨附 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)，這是預設、跨平台的 HTTP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="c77e9-183">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="c77e9-184">Nginx 與 Kestrel</span><span class="sxs-lookup"><span data-stu-id="c77e9-184">Nginx with Kestrel</span></span>

<span data-ttu-id="c77e9-185">如需如何在 Linux 上使用 Nginx 作為 Kestrel 反向 Proxy 伺服器的資訊，請參閱 <xref:host-and-deploy/linux-nginx>。</span><span class="sxs-lookup"><span data-stu-id="c77e9-185">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="c77e9-186">Apache 與 Kestrel</span><span class="sxs-lookup"><span data-stu-id="c77e9-186">Apache with Kestrel</span></span>

<span data-ttu-id="c77e9-187">如需如何在 Linux 上使用 Apache 作為 Kestrel 反向 Proxy 伺服器的資訊，請參閱 <xref:host-and-deploy/linux-apache>。</span><span class="sxs-lookup"><span data-stu-id="c77e9-187">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="c77e9-188">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="c77e9-188">HTTP.sys</span></span>

<span data-ttu-id="c77e9-189">如果您在 Windows 上執行 ASP.NET Core 應用程式，則 HTTP.sys 是 Kestrel 的替代方案。</span><span class="sxs-lookup"><span data-stu-id="c77e9-189">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="c77e9-190">除非應用程式需要 Kestrel 中未提供的功能，否則建議使用 Kestrel 來進行 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="c77e9-190">Kestrel is recommended over HTTP.sys unless the app requires features not available in Kestrel.</span></span> <span data-ttu-id="c77e9-191">如需詳細資訊，請參閱<xref:fundamentals/servers/httpsys>。</span><span class="sxs-lookup"><span data-stu-id="c77e9-191">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys 直接與網際網路通訊](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="c77e9-193">HTTP.sys 也可用於只公開到內部網路的應用程式。</span><span class="sxs-lookup"><span data-stu-id="c77e9-193">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys 直接與內部網路通訊](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="c77e9-195">如需 HTTP.sys 設定指南，請參閱 <xref:fundamentals/servers/httpsys>。</span><span class="sxs-lookup"><span data-stu-id="c77e9-195">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="c77e9-196">ASP.NET Core 伺服器基礎結構</span><span class="sxs-lookup"><span data-stu-id="c77e9-196">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="c77e9-197">`Startup.Configure` 方法提供的 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 會公開類型<xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection> 的 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> 屬性。</span><span class="sxs-lookup"><span data-stu-id="c77e9-197">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="c77e9-198">Kestrel 與 HTTP.sys 只會公開 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> 功能，但不同的伺服器實作則可能會公開更多的功能。</span><span class="sxs-lookup"><span data-stu-id="c77e9-198">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="c77e9-199">`IServerAddressesFeature` 可用來找出伺服器實作在執行階段已繫結的連接埠。</span><span class="sxs-lookup"><span data-stu-id="c77e9-199">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="c77e9-200">自訂伺服器</span><span class="sxs-lookup"><span data-stu-id="c77e9-200">Custom servers</span></span>

<span data-ttu-id="c77e9-201">如果內建伺服器不符合應用程式的需求，則可以建立自訂伺服器實作。</span><span class="sxs-lookup"><span data-stu-id="c77e9-201">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="c77e9-202">[Open Web Interface for .NET (OWIN) 指南](xref:fundamentals/owin)示範如何撰寫採用 [Nowin](https://github.com/Bobris/Nowin) 的 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 實作。</span><span class="sxs-lookup"><span data-stu-id="c77e9-202">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="c77e9-203">只有該應用程式使用的功能介面才需要實作，但至少須支援 <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> 及 <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>。</span><span class="sxs-lookup"><span data-stu-id="c77e9-203">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="c77e9-204">伺服器啟動</span><span class="sxs-lookup"><span data-stu-id="c77e9-204">Server startup</span></span>

<span data-ttu-id="c77e9-205">伺服器會在整合式開發環境 (IDE) 或編輯器啟動應用程式時啟動：</span><span class="sxs-lookup"><span data-stu-id="c77e9-205">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="c77e9-206">[Visual Studio](https://visualstudio.microsoft.com)：啟動設定檔可用來透過[IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) / [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)或主控台來啟動應用程式和伺服器。</span><span class="sxs-lookup"><span data-stu-id="c77e9-206">[Visual Studio](https://visualstudio.microsoft.com): Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="c77e9-207">[Visual Studio Code](https://code.visualstudio.com/)：應用程式和伺服器是由 [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode)啟動，這會啟用 CoreCLR 偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="c77e9-207">[Visual Studio Code](https://code.visualstudio.com/): The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="c77e9-208">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)： [Mono Soft-Mode 調試](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/)程式會啟動應用程式和伺服器。</span><span class="sxs-lookup"><span data-stu-id="c77e9-208">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/): The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="c77e9-209">當您在專案資料夾中使用命令提示字元啟動應用程式時，[dotnet run](/dotnet/core/tools/dotnet-run) 會啟動應用程式和伺服器 (僅限 Kestrel 和 HTTP.sys)。</span><span class="sxs-lookup"><span data-stu-id="c77e9-209">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="c77e9-210">組態是由 `-c|--configuration` 選項指定，會設為 `Debug` (預設值) 或 `Release`。</span><span class="sxs-lookup"><span data-stu-id="c77e9-210">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="c77e9-211">當使用或工具內建的偵錯工具（例如 Visual Studio）來啟動應用程式時，檔案 *上的launchSettings.js* 會提供設定 `dotnet run` 。</span><span class="sxs-lookup"><span data-stu-id="c77e9-211">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="c77e9-212">如果啟動設定檔存在於檔案的 *launchSettings.js* 中，請使用 `--launch-profile {PROFILE NAME}` 選項搭配 `dotnet run` 命令或選取 Visual Studio 中的設定檔。</span><span class="sxs-lookup"><span data-stu-id="c77e9-212">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="c77e9-213">如需詳細資訊，請參閱 [dotnet run](/dotnet/core/tools/dotnet-run) 和 [.NET Core 發佈封裝](/dotnet/core/build/distribution-packaging)。</span><span class="sxs-lookup"><span data-stu-id="c77e9-213">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="c77e9-214">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="c77e9-214">HTTP/2 support</span></span>

<span data-ttu-id="c77e9-215">在下列部署案例中，ASP.NET Core 支援 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="c77e9-215">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="c77e9-216">Kestrel</span><span class="sxs-lookup"><span data-stu-id="c77e9-216">Kestrel</span></span>](xref:fundamentals/servers/kestrel/http2)
  * <span data-ttu-id="c77e9-217">作業系統</span><span class="sxs-lookup"><span data-stu-id="c77e9-217">Operating system</span></span>
    * <span data-ttu-id="c77e9-218">Windows Server 2016/Windows 10 或更新版本&dagger;</span><span class="sxs-lookup"><span data-stu-id="c77e9-218">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="c77e9-219">Linux 含 OpenSSL 1.0.2 或更新版本 (例如 Ubuntu 16.04 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="c77e9-219">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="c77e9-220">未來版本的 macOS 將會支援 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="c77e9-220">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="c77e9-221">目標 Framework：.NET Core 2.2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="c77e9-221">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="c77e9-222">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="c77e9-222">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="c77e9-223">Windows Server 2016/Windows 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="c77e9-223">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="c77e9-224">目標 Framework：不適用於 HTTP.sys 部署。</span><span class="sxs-lookup"><span data-stu-id="c77e9-224">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="c77e9-225">IIS (同處理序)</span><span class="sxs-lookup"><span data-stu-id="c77e9-225">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="c77e9-226">Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="c77e9-226">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="c77e9-227">目標 Framework：.NET Core 2.2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="c77e9-227">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="c77e9-228">IIS (跨處理序)</span><span class="sxs-lookup"><span data-stu-id="c77e9-228">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="c77e9-229">Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="c77e9-229">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="c77e9-230">公眾對應 Edge Server 連線使用 HTTP/2，但是對 Kestrel 的反向 Proxy 連線使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="c77e9-230">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="c77e9-231">目標 Framework：不適用於 IIS 跨處理序部署。</span><span class="sxs-lookup"><span data-stu-id="c77e9-231">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="c77e9-232">&dagger;Kestrel 在 Windows Server 2012 R2 與 Windows 8.1 對 HTTP/2 的支援有限。</span><span class="sxs-lookup"><span data-stu-id="c77e9-232">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="c77e9-233">支援有限的原因是這些作業系統上的支援 TLS 密碼編譯套件清單有限。</span><span class="sxs-lookup"><span data-stu-id="c77e9-233">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="c77e9-234">可能需要使用橢圓曲線數位簽章演算法 (ECDSA) 產生的憑證來保護 TLS 連線。</span><span class="sxs-lookup"><span data-stu-id="c77e9-234">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"

* [<span data-ttu-id="c77e9-235">Kestrel</span><span class="sxs-lookup"><span data-stu-id="c77e9-235">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="c77e9-236">作業系統</span><span class="sxs-lookup"><span data-stu-id="c77e9-236">Operating system</span></span>
    * <span data-ttu-id="c77e9-237">Windows Server 2016/Windows 10 或更新版本&dagger;</span><span class="sxs-lookup"><span data-stu-id="c77e9-237">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="c77e9-238">Linux 含 OpenSSL 1.0.2 或更新版本 (例如 Ubuntu 16.04 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="c77e9-238">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="c77e9-239">未來版本的 macOS 將會支援 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="c77e9-239">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="c77e9-240">目標 Framework：.NET Core 2.2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="c77e9-240">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="c77e9-241">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="c77e9-241">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="c77e9-242">Windows Server 2016/Windows 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="c77e9-242">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="c77e9-243">目標 Framework：不適用於 HTTP.sys 部署。</span><span class="sxs-lookup"><span data-stu-id="c77e9-243">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="c77e9-244">IIS (同處理序)</span><span class="sxs-lookup"><span data-stu-id="c77e9-244">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="c77e9-245">Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="c77e9-245">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="c77e9-246">目標 Framework：.NET Core 2.2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="c77e9-246">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="c77e9-247">IIS (跨處理序)</span><span class="sxs-lookup"><span data-stu-id="c77e9-247">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="c77e9-248">Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="c77e9-248">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="c77e9-249">公眾對應 Edge Server 連線使用 HTTP/2，但是對 Kestrel 的反向 Proxy 連線使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="c77e9-249">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="c77e9-250">目標 Framework：不適用於 IIS 跨處理序部署。</span><span class="sxs-lookup"><span data-stu-id="c77e9-250">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="c77e9-251">&dagger;Kestrel 在 Windows Server 2012 R2 與 Windows 8.1 對 HTTP/2 的支援有限。</span><span class="sxs-lookup"><span data-stu-id="c77e9-251">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="c77e9-252">支援有限的原因是這些作業系統上的支援 TLS 密碼編譯套件清單有限。</span><span class="sxs-lookup"><span data-stu-id="c77e9-252">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="c77e9-253">可能需要使用橢圓曲線數位簽章演算法 (ECDSA) 產生的憑證來保護 TLS 連線。</span><span class="sxs-lookup"><span data-stu-id="c77e9-253">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="c77e9-254">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="c77e9-254">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="c77e9-255">Windows Server 2016/Windows 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="c77e9-255">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="c77e9-256">目標 Framework：不適用於 HTTP.sys 部署。</span><span class="sxs-lookup"><span data-stu-id="c77e9-256">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="c77e9-257">IIS (跨處理序)</span><span class="sxs-lookup"><span data-stu-id="c77e9-257">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="c77e9-258">Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="c77e9-258">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="c77e9-259">公眾對應 Edge Server 連線使用 HTTP/2，但是對 Kestrel 的反向 Proxy 連線使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="c77e9-259">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="c77e9-260">目標 Framework：不適用於 IIS 跨處理序部署。</span><span class="sxs-lookup"><span data-stu-id="c77e9-260">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="c77e9-261">HTTP/2 連線必須使用 [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 和 TLS 1.2 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="c77e9-261">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="c77e9-262">如需詳細資訊，請參閱與伺服器部署案例相關的主題。</span><span class="sxs-lookup"><span data-stu-id="c77e9-262">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c77e9-263">其他資源</span><span class="sxs-lookup"><span data-stu-id="c77e9-263">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
