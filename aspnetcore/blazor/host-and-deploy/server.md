---
title: 裝載和部署 ASP.NET Core Blazor Server
author: guardrex
description: 瞭解如何使用 ASP.NET Core 來裝載和部署 Blazor Server 應用程式。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/host-and-deploy/server
ms.openlocfilehash: fd5ea02be9396b2c1418ac82b6a4be79ec2b4849
ms.sourcegitcommit: 7923a9ec594690f01e0c9c6df3416c239e6745fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081347"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="d665e-103">裝載和部署 Blazor Server</span><span class="sxs-lookup"><span data-stu-id="d665e-103">Host and deploy Blazor Server</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="d665e-104">主機組態值</span><span class="sxs-lookup"><span data-stu-id="d665e-104">Host configuration values</span></span>

<span data-ttu-id="d665e-105">[ Blazor Server 應用程式](xref:blazor/hosting-models#blazor-server)可以接受[一般主機配置值](xref:fundamentals/host/generic-host#host-configuration)。</span><span class="sxs-lookup"><span data-stu-id="d665e-105">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="d665e-106">部署</span><span class="sxs-lookup"><span data-stu-id="d665e-106">Deployment</span></span>

<span data-ttu-id="d665e-107">使用[ Blazor Server 裝載模型](xref:blazor/hosting-models#blazor-server)， Blazor 會在伺服器上從 ASP.NET Core 應用程式內執行。</span><span class="sxs-lookup"><span data-stu-id="d665e-107">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="d665e-108">UI 更新、事件處理及 JavaScript 呼叫會透過連接來處理 [SignalR](xref:signalr/introduction) 。</span><span class="sxs-lookup"><span data-stu-id="d665e-108">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="d665e-109">需要能夠裝載 ASP.NET Core 應用程式的網路伺服器。</span><span class="sxs-lookup"><span data-stu-id="d665e-109">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="d665e-110">使用命令) 時，Visual Studio 包含 **Blazor Server 應用程式** 專案範本 (`blazorserverside` 範本 [`dotnet new`](/dotnet/core/tools/dotnet-new) 。</span><span class="sxs-lookup"><span data-stu-id="d665e-110">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command).</span></span> <span data-ttu-id="d665e-111">如需專案範本的詳細資訊 Blazor ，請參閱 <xref:blazor/project-structure> 。</span><span class="sxs-lookup"><span data-stu-id="d665e-111">For more information on Blazor project templates, see <xref:blazor/project-structure>.</span></span>

## <a name="scalability"></a><span data-ttu-id="d665e-112">延展性</span><span class="sxs-lookup"><span data-stu-id="d665e-112">Scalability</span></span>

<span data-ttu-id="d665e-113">規劃部署，以充分利用應用程式的可用基礎結構 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="d665e-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="d665e-114">請參閱下列資源來處理 Blazor Server 應用程式的擴充性：</span><span class="sxs-lookup"><span data-stu-id="d665e-114">See the following resources to address Blazor Server app scalability:</span></span>

* [<span data-ttu-id="d665e-115">應用程式的基本概念 Blazor Server</span><span class="sxs-lookup"><span data-stu-id="d665e-115">Fundamentals of Blazor Server apps</span></span>](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="d665e-116">部署伺服器</span><span class="sxs-lookup"><span data-stu-id="d665e-116">Deployment server</span></span>

<span data-ttu-id="d665e-117">當考慮單一伺服器的擴充性 (擴大) 時，應用程式可用的記憶體，可能是應用程式在使用者需求增加時將耗盡的第一項資源。</span><span class="sxs-lookup"><span data-stu-id="d665e-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="d665e-118">伺服器上的可用記憶體會影響：</span><span class="sxs-lookup"><span data-stu-id="d665e-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="d665e-119">伺服器可支援的作用中線路數目。</span><span class="sxs-lookup"><span data-stu-id="d665e-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="d665e-120">用戶端上的 UI 延遲。</span><span class="sxs-lookup"><span data-stu-id="d665e-120">UI latency on the client.</span></span>

<span data-ttu-id="d665e-121">如需建立安全且可擴充的 Blazor 伺服器應用程式的指引，請參閱 <xref:blazor/security/server/threat-mitigation> 。</span><span class="sxs-lookup"><span data-stu-id="d665e-121">For guidance on building secure and scalable Blazor server apps, see <xref:blazor/security/server/threat-mitigation>.</span></span>

<span data-ttu-id="d665e-122">針對基本的 *Hello World* 樣式應用程式，每個線路會使用大約 250 KB 的記憶體。</span><span class="sxs-lookup"><span data-stu-id="d665e-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="d665e-123">電路的大小取決於應用程式的程式碼，以及與每個元件相關聯的狀態維護需求。</span><span class="sxs-lookup"><span data-stu-id="d665e-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="d665e-124">建議您在開發期間測量應用程式和基礎結構的資源需求，但下列基準可作為規劃部署目標的起點：如果您希望應用程式支援5000的並行使用者，請考慮將至少 1.3 GB 的伺服器記憶體預算為應用程式 (或 ~ 每位使用者) ~ 273 KB。</span><span class="sxs-lookup"><span data-stu-id="d665e-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a><span data-ttu-id="d665e-125">SignalR 配置</span><span class="sxs-lookup"><span data-stu-id="d665e-125">SignalR configuration</span></span>

<span data-ttu-id="d665e-126">Blazor Server 應用程式會使用 ASP.NET Core SignalR 與瀏覽器進行通訊。</span><span class="sxs-lookup"><span data-stu-id="d665e-126">Blazor Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="d665e-127">[ SignalR 的裝載和調整條件](xref:signalr/publish-to-azure-web-app)適用于 Blazor Server 應用程式。</span><span class="sxs-lookup"><span data-stu-id="d665e-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

<span data-ttu-id="d665e-128">Blazor 使用 Websocket 作為傳輸的最佳方式 SignalR ，是因為延遲、可靠性和 [安全性](xref:signalr/security)較低。</span><span class="sxs-lookup"><span data-stu-id="d665e-128">Blazor works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="d665e-129">SignalR當 websocket 無法使用時，或當應用程式明確設定為使用長時間輪詢時，就會使用長時間輪詢。</span><span class="sxs-lookup"><span data-stu-id="d665e-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="d665e-130">部署至 Azure App Service 時，請將應用程式設定為在服務的 Azure 入口網站設定中使用 Websocket。</span><span class="sxs-lookup"><span data-stu-id="d665e-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="d665e-131">如需設定應用程式以進行 Azure App Service 的詳細資訊，請參閱[ SignalR 發佈指導方針](xref:signalr/publish-to-azure-web-app)。</span><span class="sxs-lookup"><span data-stu-id="d665e-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-signalr-service"></a><span data-ttu-id="d665e-132">Azure SignalR 服務</span><span class="sxs-lookup"><span data-stu-id="d665e-132">Azure SignalR Service</span></span>

<span data-ttu-id="d665e-133">我們建議使用 [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) for Blazor Server apps。</span><span class="sxs-lookup"><span data-stu-id="d665e-133">We recommend using the [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) for Blazor Server apps.</span></span> <span data-ttu-id="d665e-134">服務可讓您將 Blazor Server 應用程式相應增加為大量的並行 SignalR 連接。</span><span class="sxs-lookup"><span data-stu-id="d665e-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="d665e-135">此外， SignalR 服務的全球接觸和高效能資料中心大幅有助於降低因地理位置而造成的延遲。</span><span class="sxs-lookup"><span data-stu-id="d665e-135">In addition, the SignalR Service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d665e-136">當 [websocket](https://wikipedia.org/wiki/WebSocket) 停用時，Azure App Service 會使用 HTTP 長時間輪詢來模擬即時連接。</span><span class="sxs-lookup"><span data-stu-id="d665e-136">When [WebSockets](https://wikipedia.org/wiki/WebSocket) are disabled, Azure App Service simulates a real-time connection using HTTP Long Polling.</span></span> <span data-ttu-id="d665e-137">HTTP 長時間輪詢的速度明顯低於啟用 Websocket 的執行，而不會使用輪詢來模擬用戶端-伺服器連接。</span><span class="sxs-lookup"><span data-stu-id="d665e-137">HTTP Long Polling is noticeably slower than running with WebSockets enabled, which doesn't use polling to simulate a client-server connection.</span></span>
>
> <span data-ttu-id="d665e-138">建議您針對 Blazor Server 部署至 Azure App Service 的應用程式使用 websocket。</span><span class="sxs-lookup"><span data-stu-id="d665e-138">We recommend using WebSockets for Blazor Server apps deployed to Azure App Service.</span></span> <span data-ttu-id="d665e-139">[Azure SignalR 服務](xref:signalr/scale#azure-signalr-service)預設會使用 websocket。</span><span class="sxs-lookup"><span data-stu-id="d665e-139">The [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) uses WebSockets by default.</span></span> <span data-ttu-id="d665e-140">如果應用程式不使用 Azure SignalR 服務，請參閱 <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service> 。</span><span class="sxs-lookup"><span data-stu-id="d665e-140">If the app doesn't use the Azure SignalR Service, see <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service>.</span></span>
>
> <span data-ttu-id="d665e-141">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="d665e-141">For more information, see:</span></span>
>
> * [<span data-ttu-id="d665e-142">什麼是 Azure SignalR 服務？</span><span class="sxs-lookup"><span data-stu-id="d665e-142">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
> * [<span data-ttu-id="d665e-143">Azure 服務的效能指南 SignalR</span><span class="sxs-lookup"><span data-stu-id="d665e-143">Performance guide for Azure SignalR Service</span></span>](/azure-signalr/signalr-concept-performance#performance-factors)

### <a name="configuration"></a><span data-ttu-id="d665e-144">組態</span><span class="sxs-lookup"><span data-stu-id="d665e-144">Configuration</span></span>

<span data-ttu-id="d665e-145">若要設定 Azure 服務的應用程式 SignalR ，應用程式必須支援「 *粘滯話*」，其中用戶端會在進行預導 [時重新導向回相同的伺服器](xref:blazor/hosting-models#connection-to-the-server)。</span><span class="sxs-lookup"><span data-stu-id="d665e-145">To configure an app for the Azure SignalR Service, the app must support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="d665e-146">`ServerStickyMode`選項或設定值設定為 `Required` 。</span><span class="sxs-lookup"><span data-stu-id="d665e-146">The `ServerStickyMode` option or configuration value is set to `Required`.</span></span> <span data-ttu-id="d665e-147">一般而言，應用程式會使用下列 **_其中一_** 種方法來建立設定：</span><span class="sxs-lookup"><span data-stu-id="d665e-147">Typically, an app creates the configuration using **_ONE_** of the following approaches:</span></span>

   * <span data-ttu-id="d665e-148">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d665e-148">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="d665e-149">Configuration (使用下列 **_其中一_** 種方法) ：</span><span class="sxs-lookup"><span data-stu-id="d665e-149">Configuration (use **_ONE_** of the following approaches):</span></span>
  
     * <span data-ttu-id="d665e-150">在 `appsettings.json` 中：</span><span class="sxs-lookup"><span data-stu-id="d665e-150">In `appsettings.json`:</span></span>

       ```json
       "Azure:SignalR:StickyServerMode": "Required"
       ```

     * <span data-ttu-id="d665e-151">Azure 入口網站中 **的 app** service  >  **設定應用程式設定** (**名稱**： `Azure__SignalR__StickyServerMode` ，**值**： `Required`) 。</span><span class="sxs-lookup"><span data-stu-id="d665e-151">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure__SignalR__StickyServerMode`, **Value**: `Required`).</span></span> <span data-ttu-id="d665e-152">如果您布建 [Azure SignalR 服務](#provision-the-azure-signalr-service)，則會自動為應用程式採用此方法。</span><span class="sxs-lookup"><span data-stu-id="d665e-152">This approach is adopted for the app automatically if you [provision the Azure SignalR Service](#provision-the-azure-signalr-service).</span></span>

### <a name="provision-the-azure-signalr-service"></a><span data-ttu-id="d665e-153">布建 Azure SignalR 服務</span><span class="sxs-lookup"><span data-stu-id="d665e-153">Provision the Azure SignalR Service</span></span>

<span data-ttu-id="d665e-154">若要 SignalR 在 Visual Studio 中布建應用程式的 Azure 服務：</span><span class="sxs-lookup"><span data-stu-id="d665e-154">To provision the Azure SignalR Service for an app in Visual Studio:</span></span>

1. <span data-ttu-id="d665e-155">在應用程式的 Visual Studio 中建立 Azure 應用程式發佈設定檔 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="d665e-155">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="d665e-156">將 **Azure SignalR 服務** 相依性新增至設定檔。</span><span class="sxs-lookup"><span data-stu-id="d665e-156">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="d665e-157">如果 Azure 訂用帳戶沒有預先存在的 Azure SignalR 服務實例可指派給該應用程式，請選取 [ **建立新的 azure SignalR 服務實例** ] 以布建新的服務實例。</span><span class="sxs-lookup"><span data-stu-id="d665e-157">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="d665e-158">將應用程式發佈至 Azure。</span><span class="sxs-lookup"><span data-stu-id="d665e-158">Publish the app to Azure.</span></span>

<span data-ttu-id="d665e-159">SignalR在 Visual Studio 中布建 Azure 服務會自動 [啟用 *粘滯會話*](#configuration) ，並將 SignalR 連接字串新增至 app Service 的設定。</span><span class="sxs-lookup"><span data-stu-id="d665e-159">Provisioning the Azure SignalR Service in Visual Studio automatically [enables *sticky sessions*](#configuration) and adds the SignalR connection string to the app service's configuration.</span></span>

#### <a name="iis"></a><span data-ttu-id="d665e-160">IIS</span><span class="sxs-lookup"><span data-stu-id="d665e-160">IIS</span></span>

<span data-ttu-id="d665e-161">使用 IIS 時，請啟用：</span><span class="sxs-lookup"><span data-stu-id="d665e-161">When using IIS, enable:</span></span>

* <span data-ttu-id="d665e-162">[IIS 上的 websocket](xref:fundamentals/websockets#enabling-websockets-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="d665e-162">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="d665e-163">[使用應用程式要求路由的「粘滯話](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)」。</span><span class="sxs-lookup"><span data-stu-id="d665e-163">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="d665e-164">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="d665e-164">Kubernetes</span></span>

<span data-ttu-id="d665e-165">使用下列 [Kubernetes 注釋來建立輸入](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)定義：</span><span class="sxs-lookup"><span data-stu-id="d665e-165">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a><span data-ttu-id="d665e-166">使用 Nginx 的 Linux</span><span class="sxs-lookup"><span data-stu-id="d665e-166">Linux with Nginx</span></span>

<span data-ttu-id="d665e-167">SignalR若要讓 websocket 正常運作，請確認 proxy 的 `Upgrade` 和 `Connection` 標頭已設定為下列值，而且 `$connection_upgrade` 對應至其中一個：</span><span class="sxs-lookup"><span data-stu-id="d665e-167">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="d665e-168">依預設，升級標頭值。</span><span class="sxs-lookup"><span data-stu-id="d665e-168">The Upgrade header value by default.</span></span>
* <span data-ttu-id="d665e-169">`close` 當升級標頭遺失或空白時。</span><span class="sxs-lookup"><span data-stu-id="d665e-169">`close` when the Upgrade header is missing or empty.</span></span>

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

<span data-ttu-id="d665e-170">如需詳細資訊，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="d665e-170">For more information, see the following articles:</span></span>

* [<span data-ttu-id="d665e-171">NGINX 做為 WebSocket Proxy</span><span class="sxs-lookup"><span data-stu-id="d665e-171">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="d665e-172">WebSocket proxy</span><span class="sxs-lookup"><span data-stu-id="d665e-172">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="d665e-173">使用 Apache 的 Linux</span><span class="sxs-lookup"><span data-stu-id="d665e-173">Linux with Apache</span></span>

<span data-ttu-id="d665e-174">若要將 Blazor 應用程式裝載在 Linux 上的 Apache，請設定 `ProxyPass` HTTP 和 websocket 流量。</span><span class="sxs-lookup"><span data-stu-id="d665e-174">To host a Blazor app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="d665e-175">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="d665e-175">In the following example:</span></span>

* <span data-ttu-id="d665e-176">Kestrel 伺服器正在主機電腦上執行。</span><span class="sxs-lookup"><span data-stu-id="d665e-176">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="d665e-177">應用程式會接聽埠5000上的流量。</span><span class="sxs-lookup"><span data-stu-id="d665e-177">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="d665e-178">啟用下列模組：</span><span class="sxs-lookup"><span data-stu-id="d665e-178">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="d665e-179">檢查瀏覽器主控台中的 Websocket 錯誤。</span><span class="sxs-lookup"><span data-stu-id="d665e-179">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="d665e-180">範例錯誤：</span><span class="sxs-lookup"><span data-stu-id="d665e-180">Example errors:</span></span>

* <span data-ttu-id="d665e-181">Firefox 無法在 ws://the-domain-name.tld/_blazor?id=XXX 建立與伺服器的連接。</span><span class="sxs-lookup"><span data-stu-id="d665e-181">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="d665e-182">錯誤：無法啟動傳輸 ' Websocket '：錯誤：傳輸時發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="d665e-182">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="d665e-183">錯誤：無法啟動傳輸 ' LongPolling '： TypeError：此傳輸未定義</span><span class="sxs-lookup"><span data-stu-id="d665e-183">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="d665e-184">錯誤：無法使用任何可用的傳輸來連接到伺服器。</span><span class="sxs-lookup"><span data-stu-id="d665e-184">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="d665e-185">Websocket 失敗</span><span class="sxs-lookup"><span data-stu-id="d665e-185">WebSockets failed</span></span>
* <span data-ttu-id="d665e-186">錯誤：如果連接不是處於「已連線」狀態，則無法傳送資料。</span><span class="sxs-lookup"><span data-stu-id="d665e-186">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="d665e-187">如需詳細資訊，請參閱 [Apache 檔](https://httpd.apache.org/docs/current/mod/mod_proxy.html)。</span><span class="sxs-lookup"><span data-stu-id="d665e-187">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="d665e-188">測量網路延遲</span><span class="sxs-lookup"><span data-stu-id="d665e-188">Measure network latency</span></span>

<span data-ttu-id="d665e-189">您可以使用[JS interop](xref:blazor/call-javascript-from-dotnet)來測量網路延遲，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="d665e-189">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code {
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            startTime = DateTime.UtcNow;
            var _ = await JS.InvokeAsync<string>("toString");
            latency = DateTime.UtcNow - startTime;
            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="d665e-190">針對合理的 UI 體驗，建議250毫秒或更少的持續性 UI 延遲。</span><span class="sxs-lookup"><span data-stu-id="d665e-190">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
