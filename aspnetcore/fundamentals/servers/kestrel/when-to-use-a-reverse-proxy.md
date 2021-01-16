---
title: 搭配 ASP.NET Core Kestrel web 伺服器使用反向 proxy 的時機
author: rick-anderson
description: 瞭解如何在 Kestrel 前面使用反向 proxy，ASP.NET Core 的跨平臺網頁伺服器。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2021
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
uid: fundamentals/servers/kestrel/when-to-use-a-reverse-proxy
ms.openlocfilehash: fc89a9f841403bbccedff0a9c0720a08c11abdd6
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253923"
---
# <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="7bda3-103">何時搭配使用 Kestrel 與反向 Proxy</span><span class="sxs-lookup"><span data-stu-id="7bda3-103">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="7bda3-104">您可以單獨使用 Kestrel，或與 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 等「反向 Proxy 伺服器」搭配使用。</span><span class="sxs-lookup"><span data-stu-id="7bda3-104">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="7bda3-105">反向 Proxy 伺服器會從網路接收 HTTP 要求，然後轉送到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="7bda3-105">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="7bda3-106">Kestrel 用作邊緣 (網際網路對應) 網頁伺服器：</span><span class="sxs-lookup"><span data-stu-id="7bda3-106">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 不使用反向 Proxy 伺服器直接與網際網路通訊](_static/kestrel-to-internet2.png)

<span data-ttu-id="7bda3-108">Kestrel 用於反向 Proxy 組態中：</span><span class="sxs-lookup"><span data-stu-id="7bda3-108">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 透過 IIS、Nginx 或 Apache 等反向 Proxy 伺服器間接與網際網路通訊](_static/kestrel-to-internet.png)

<span data-ttu-id="7bda3-110">不論是否有反向 proxy 伺服器，設定都是支援的裝載設定。</span><span class="sxs-lookup"><span data-stu-id="7bda3-110">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="7bda3-111">使用 Kestrel 做為沒有反向 proxy 伺服器的 edge server 時，不支援在多個進程之間共用相同的 IP 位址和埠。</span><span class="sxs-lookup"><span data-stu-id="7bda3-111">When Kestrel is used as an edge server without a reverse proxy server, sharing of the same IP address and port among multiple processes is unsupported.</span></span> <span data-ttu-id="7bda3-112">當 Kestrel 設定為接聽埠時，Kestrel 會處理該埠的所有流量，而不論要求的 `Host` 標頭為何。</span><span class="sxs-lookup"><span data-stu-id="7bda3-112">When Kestrel is configured to listen on a port, Kestrel handles all traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="7bda3-113">可以共用埠的反向 proxy 可以將要求轉送至唯一 IP 和埠上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="7bda3-113">A reverse proxy that can share ports can forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="7bda3-114">即使不需要反向 Proxy 伺服器，使用反向 Proxy 伺服器也是不錯的選擇。</span><span class="sxs-lookup"><span data-stu-id="7bda3-114">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="7bda3-115">反向 Proxy：</span><span class="sxs-lookup"><span data-stu-id="7bda3-115">A reverse proxy:</span></span>

* <span data-ttu-id="7bda3-116">可以限制它所主控之應用程式的公開介面區。</span><span class="sxs-lookup"><span data-stu-id="7bda3-116">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="7bda3-117">提供額外的組態和防禦層。</span><span class="sxs-lookup"><span data-stu-id="7bda3-117">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="7bda3-118">能夠與現有基礎結構更好地整合。</span><span class="sxs-lookup"><span data-stu-id="7bda3-118">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="7bda3-119">簡化負載平衡和安全通訊 (HTTPS) 組態。</span><span class="sxs-lookup"><span data-stu-id="7bda3-119">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="7bda3-120">只有反向 proxy 伺服器需要 x.509 憑證，而且該伺服器可以使用一般 HTTP 與內部網路上的應用程式伺服器進行通訊。</span><span class="sxs-lookup"><span data-stu-id="7bda3-120">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="7bda3-121">裝載於反向 Proxy 組態需要[主機篩選](xref:fundamentals/servers/kestrel/host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="7bda3-121">Hosting in a reverse proxy configuration requires [host filtering](xref:fundamentals/servers/kestrel/host-filtering).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7bda3-122">其他資源</span><span class="sxs-lookup"><span data-stu-id="7bda3-122">Additional resources</span></span>

<xref:host-and-deploy/proxy-load-balancer>

