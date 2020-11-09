---
title: 在 IIS 上搭配 HTTP/2 使用 ASP.NET Core
author: rick-anderson
description: 瞭解如何搭配 IIS 使用 HTTP/2 功能。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 4d0dc1239467e92c4127f631709c2ffd6098bfc5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057410"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a><span data-ttu-id="c18e2-103">在 IIS 上搭配 HTTP/2 使用 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c18e2-103">Use ASP.NET Core with HTTP/2 on IIS</span></span>

<span data-ttu-id="c18e2-104">作者：[Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="c18e2-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="c18e2-105">在下列 IIS 部署案例中，ASP.NET Core 支援 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="c18e2-105">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="c18e2-106">Windows Server 2016 或更新版本/Windows 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="c18e2-106">Windows Server 2016 or later / Windows 10 or later</span></span>
* <span data-ttu-id="c18e2-107">IIS 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="c18e2-107">IIS 10 or later</span></span>
* <span data-ttu-id="c18e2-108">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="c18e2-108">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="c18e2-109">[裝載跨進程](xref:host-and-deploy/iis/index#out-of-process-hosting-model)時：公開的 edge server 連線使用 HTTP/2，但[Kestrel 伺服器](xref:fundamentals/servers/kestrel)的反向 proxy 連線使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="c18e2-109">When [hosting out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model): Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>

<span data-ttu-id="c18e2-110">若為建立 HTTP/2 連接時的同進程部署，則為 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 報告 `HTTP/2` 。</span><span class="sxs-lookup"><span data-stu-id="c18e2-110">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="c18e2-111">若為建立 HTTP/2 連接時的跨進程部署，則為 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 報告 `HTTP/1.1` 。</span><span class="sxs-lookup"><span data-stu-id="c18e2-111">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="c18e2-112">如需有關同處理序和跨處理序主控模型的詳細資訊，請參閱 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="c18e2-112">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="c18e2-113">HTTPS/TLS 連接預設會啟用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="c18e2-113">HTTP/2 is enabled by default for HTTPS/TLS connections.</span></span> <span data-ttu-id="c18e2-114">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="c18e2-114">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="c18e2-115">如需使用 IIS 部署之 HTTP/2 設定的詳細資訊，請參閱 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="c18e2-115">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="c18e2-116">支援 gRPC 的 Advanced HTTP/2 功能</span><span class="sxs-lookup"><span data-stu-id="c18e2-116">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="c18e2-117">IIS 中的額外 HTTP/2 功能支援 gRPC，包括對回應結尾和傳送重設框架的支援。</span><span class="sxs-lookup"><span data-stu-id="c18e2-117">Additional HTTP/2 features in IIS support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="c18e2-118">在 IIS 上執行 gRPC 的需求：</span><span class="sxs-lookup"><span data-stu-id="c18e2-118">Requirements to run gRPC on IIS:</span></span>

* <span data-ttu-id="c18e2-119">同進程裝載。</span><span class="sxs-lookup"><span data-stu-id="c18e2-119">In-process hosting.</span></span>
* <span data-ttu-id="c18e2-120">Windows 10、OS 組建20300.1000 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="c18e2-120">Windows 10, OS Build 20300.1000 or later.</span></span> <span data-ttu-id="c18e2-121">可能需要使用 Windows 測試人員組建。</span><span class="sxs-lookup"><span data-stu-id="c18e2-121">May require use of Windows Insider Builds.</span></span>
* <span data-ttu-id="c18e2-122">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="c18e2-122">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="c18e2-123">預告片</span><span class="sxs-lookup"><span data-stu-id="c18e2-123">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="c18e2-124">重設</span><span class="sxs-lookup"><span data-stu-id="c18e2-124">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]
