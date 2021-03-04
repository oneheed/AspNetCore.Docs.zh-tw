---
title: 設定 ASP.NET Core 以與 Proxy 伺服器和負載平衡器搭配運作
author: rick-anderson
description: 了解裝載在 Proxy 伺服器和負載平衡器 (通常會遮蔽重要要求資訊) 後方之應用程式的設定。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 28a802414fd59f684a56e2b735140438d33be740
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109945"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="65369-103">設定 ASP.NET Core 以與 Proxy 伺服器和負載平衡器搭配運作</span><span class="sxs-lookup"><span data-stu-id="65369-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="65369-104">由 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="65369-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="65369-105">在建議的 ASP.NET Core 設定中，是使用 IIS/ASP.NET Core 模組、Nginx 或 Apache 來裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="65369-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="65369-106">Proxy 伺服器、負載平衡器及其他網路設備通常會在要求觸達應用程式之前，遮蔽要求的相關資訊：</span><span class="sxs-lookup"><span data-stu-id="65369-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="65369-107">透過 HTTP 作為 Proxy 來處理 HTTPS 要求時，原始配置 (HTTPS) 會遺失而必須在標頭中轉送。</span><span class="sxs-lookup"><span data-stu-id="65369-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="65369-108">由於應用程式會從 Proxy 而不是從網際網路或公司網路上的真實來源收到要求，因此必須也在標頭中轉送原始用戶端 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="65369-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="65369-109">此資訊在要求處理方面可能相當重要，例如在重新導向、驗證、連結產生、原則評估及用戶端地理位置方面。</span><span class="sxs-lookup"><span data-stu-id="65369-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="65369-110">轉送的標頭</span><span class="sxs-lookup"><span data-stu-id="65369-110">Forwarded headers</span></span>

<span data-ttu-id="65369-111">依照慣例，Proxy 會以 HTTP 標頭轉送資訊。</span><span class="sxs-lookup"><span data-stu-id="65369-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="65369-112">標頭</span><span class="sxs-lookup"><span data-stu-id="65369-112">Header</span></span> | <span data-ttu-id="65369-113">描述</span><span class="sxs-lookup"><span data-stu-id="65369-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="65369-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="65369-114">X-Forwarded-For</span></span> | <span data-ttu-id="65369-115">針對在 Proxy 鏈結中起始要求及後續 Proxy 的用戶端，保存用戶端的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="65369-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="65369-116">此參數可能包含 IP 位址 (以及視需要可能會有連接埠號碼)。</span><span class="sxs-lookup"><span data-stu-id="65369-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="65369-117">在 Proxy 伺服器鏈結中，第一個參數會指出起始要求的用戶端。</span><span class="sxs-lookup"><span data-stu-id="65369-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="65369-118">後面接著後續的 Proxy 識別碼。</span><span class="sxs-lookup"><span data-stu-id="65369-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="65369-119">鏈結中的最後一個 Proxy 並不在參數清單中。</span><span class="sxs-lookup"><span data-stu-id="65369-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="65369-120">最後一個 Proxy 的 IP 位址 (以及視需要會有連接埠號碼) 會在傳輸層以遠端 IP 位址的形式提供。</span><span class="sxs-lookup"><span data-stu-id="65369-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="65369-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="65369-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="65369-122">原始配置的值 (HTTP/HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="65369-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="65369-123">如果要求周遊了多個 Proxy，則此值也可能是一個配置清單。</span><span class="sxs-lookup"><span data-stu-id="65369-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="65369-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="65369-124">X-Forwarded-Host</span></span> | <span data-ttu-id="65369-125">主機標頭欄位的原始值。</span><span class="sxs-lookup"><span data-stu-id="65369-125">The original value of the Host header field.</span></span> <span data-ttu-id="65369-126">通常，Proxy 不會修改主機標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="65369-127">如需有關權限提高弱點的資訊，請參閱 [Microsoft 資訊安全諮詢 CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) \(英文\)，此弱點會影響 Proxy 不會驗證或限制主機標頭為已知有效值的系統。</span><span class="sxs-lookup"><span data-stu-id="65369-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="65369-128">轉送的標頭中介軟體 (<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>) ，會讀取這些標頭，並在上填入相關聯的欄位 <xref:Microsoft.AspNetCore.Http.HttpContext> 。</span><span class="sxs-lookup"><span data-stu-id="65369-128">The Forwarded Headers Middleware (<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>), reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="65369-129">中介軟體會更新：</span><span class="sxs-lookup"><span data-stu-id="65369-129">The middleware updates:</span></span>

* <span data-ttu-id="65369-130">[Server.remoteipaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress)：使用 `X-Forwarded-For` 標頭值設定。</span><span class="sxs-lookup"><span data-stu-id="65369-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="65369-131">額外的設定會影響中介軟體設定 `RemoteIpAddress`的方式。</span><span class="sxs-lookup"><span data-stu-id="65369-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="65369-132">如需詳細資料，請參閱[轉送的標頭中介軟體選項](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="65369-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="65369-133">[HttpCoNtext. 配置](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme)：使用 `X-Forwarded-Proto` 標頭值設定。</span><span class="sxs-lookup"><span data-stu-id="65369-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="65369-134">[HttpcoNtext.current](xref:Microsoft.AspNetCore.Http.HttpRequest.Host)：使用 `X-Forwarded-Host` 標頭值設定。</span><span class="sxs-lookup"><span data-stu-id="65369-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="65369-135">如需上述的詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/21615)。</span><span class="sxs-lookup"><span data-stu-id="65369-135">For more information on the preceding, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/21615).</span></span>

<span data-ttu-id="65369-136">您可以設定「轉送的標頭中介軟體」的[預設設定](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="65369-136">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="65369-137">預設設定值為：</span><span class="sxs-lookup"><span data-stu-id="65369-137">The default settings are:</span></span>

* <span data-ttu-id="65369-138">在應用程式與要求的來源之間只有「一個 Proxy」。</span><span class="sxs-lookup"><span data-stu-id="65369-138">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="65369-139">針對已知的 Proxy 和已知的網路，只會設定回送位址。</span><span class="sxs-lookup"><span data-stu-id="65369-139">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="65369-140">轉送標頭名稱為 `X-Forwarded-For` 和 `X-Forwarded-Proto`。</span><span class="sxs-lookup"><span data-stu-id="65369-140">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="65369-141">並非所有網路設備在新增 `X-Forwarded-For` 和 `X-Forwarded-Proto` 標頭時都不含額外組態。</span><span class="sxs-lookup"><span data-stu-id="65369-141">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="65369-142">如果透過 Proxy 傳送的要求在觸達應用程式時未包含這些標頭，請向您的設備製造商尋求指引。</span><span class="sxs-lookup"><span data-stu-id="65369-142">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="65369-143">如果設備使用的名稱不是 `X-Forwarded-For` 和 `X-Forwarded-Proto`，請設定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 與 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 選項，以符合設備使用的標頭名稱。</span><span class="sxs-lookup"><span data-stu-id="65369-143">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="65369-144">如需詳細資訊，請參閱[轉送標頭中介軟體選項](#forwarded-headers-middleware-options)和[使用不同標頭名稱之 Proxy 的組態](#configuration-for-a-proxy-that-uses-different-header-names)。</span><span class="sxs-lookup"><span data-stu-id="65369-144">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="65369-145">IIS/IIS Express 和 ASP.NET Core 模組</span><span class="sxs-lookup"><span data-stu-id="65369-145">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="65369-146">當應用程式是在 IIS 和 ASP.NET Core 模組的後方進行[處理序外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)裝載時，[IIS 整合中介軟體](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)預設會啟用「轉送的標頭中介軟體」。</span><span class="sxs-lookup"><span data-stu-id="65369-146">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="65369-147">由於有與轉送標頭相關的信任考量 (例如 [IP 詐騙](https://www.iplocation.net/ip-spoofing))，因此「轉送的標頭中介軟體」在啟用後會先在中介軟體管線中搭配 ASP.NET Core 模組限定的設定來執行。</span><span class="sxs-lookup"><span data-stu-id="65369-147">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="65369-148">中介軟體會經設定來轉送 `X-Forwarded-For` 和 `X-Forwarded-Proto` 標頭，並限制成單一 localhost Proxy。</span><span class="sxs-lookup"><span data-stu-id="65369-148">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="65369-149">如果需要額外的設定，請參閱[轉送的標頭中介軟體選項](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="65369-149">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="65369-150">其他 Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="65369-150">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="65369-151">除了在[處理序外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)裝載時使用 [IIS 整合](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)之外，都未預設啟用「轉送的標頭中介軟體」。</span><span class="sxs-lookup"><span data-stu-id="65369-151">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="65369-152">必須啟用「轉送的標頭中介軟體」，應用程式才能使用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 來處理轉送的標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-152">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="65369-153">啟用此中介軟體之後，如果未將任何 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 指定給中介軟體，則預設的 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 會是 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="65369-153">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="65369-154">搭配 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 設定中介軟體，以在 `Startup.ConfigureServices` 中轉送 `X-Forwarded-For` 與 `X-Forwarded-Proto` 標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-154">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span>

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="65369-155">轉送標頭中介軟體順序</span><span class="sxs-lookup"><span data-stu-id="65369-155">Forwarded Headers Middleware order</span></span>

<span data-ttu-id="65369-156">轉送的標頭中介軟體應該在其他中介軟體之前執行。</span><span class="sxs-lookup"><span data-stu-id="65369-156">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="65369-157">這種排序可確保依賴轉送標頭資訊的中介軟體可以耗用用於處理的標頭值。</span><span class="sxs-lookup"><span data-stu-id="65369-157">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="65369-158">轉送的標頭中介軟體可在診斷和錯誤處理之後執行，但必須在呼叫之前執行 `UseHsts` ：</span><span class="sxs-lookup"><span data-stu-id="65369-158">Forwarded Headers Middleware can run after diagnostics and error handling, but it must be run before calling `UseHsts`:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

<span data-ttu-id="65369-159">或者， `UseForwardedHeaders` 在診斷之前呼叫：</span><span class="sxs-lookup"><span data-stu-id="65369-159">Alternatively, call `UseForwardedHeaders` before diagnostics:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="65369-160">若未在 `Startup.ConfigureServices` 中指定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>，或未使用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 直接指定到擴充方法，則要轉送的預設標頭是 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="65369-160">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="65369-161">必須使用要轉送的標頭設定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> 屬性。</span><span class="sxs-lookup"><span data-stu-id="65369-161">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="65369-162">Nginx 組態</span><span class="sxs-lookup"><span data-stu-id="65369-162">Nginx configuration</span></span>

<span data-ttu-id="65369-163">若要轉送 `X-Forwarded-For` 和 `X-Forwarded-Proto` 標頭，請參閱 <xref:host-and-deploy/linux-nginx#configure-nginx>。</span><span class="sxs-lookup"><span data-stu-id="65369-163">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="65369-164">如需詳細資訊，請參閱 [NGINX：使用轉送的標頭](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)。</span><span class="sxs-lookup"><span data-stu-id="65369-164">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="65369-165">Apache 組態</span><span class="sxs-lookup"><span data-stu-id="65369-165">Apache configuration</span></span>

<span data-ttu-id="65369-166">`X-Forwarded-For` 會自動新增 (請參閱 [Apache 模組 mod_proxy：反向 Proxy 要求標頭](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers))。</span><span class="sxs-lookup"><span data-stu-id="65369-166">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="65369-167">如需如何轉送 `X-Forwarded-Proto` 標頭的資訊，請參閱 <xref:host-and-deploy/linux-apache#configure-apache>。</span><span class="sxs-lookup"><span data-stu-id="65369-167">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="65369-168">轉送的標頭中介軟體選項</span><span class="sxs-lookup"><span data-stu-id="65369-168">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="65369-169"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 會控制轉送標頭中介軟體的行為。</span><span class="sxs-lookup"><span data-stu-id="65369-169"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="65369-170">下列範例會變更預設值：</span><span class="sxs-lookup"><span data-stu-id="65369-170">The following example changes the default values:</span></span>

* <span data-ttu-id="65369-171">將轉送標頭中的項目數限制為 `2`。</span><span class="sxs-lookup"><span data-stu-id="65369-171">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="65369-172">新增已知的 Proxy 位址 `127.0.10.1`。</span><span class="sxs-lookup"><span data-stu-id="65369-172">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="65369-173">將轉送標頭名稱從預設的 `X-Forwarded-For` 變更為 `X-Forwarded-For-My-Custom-Header-Name`。</span><span class="sxs-lookup"><span data-stu-id="65369-173">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="65369-174">選項</span><span class="sxs-lookup"><span data-stu-id="65369-174">Option</span></span> | <span data-ttu-id="65369-175">描述</span><span class="sxs-lookup"><span data-stu-id="65369-175">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="65369-176">依據 `X-Forwarded-Host` 標頭將主機限制成所提供的值。</span><span class="sxs-lookup"><span data-stu-id="65369-176">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="65369-177">比較值時，會使用序數忽略大小寫的方式來比較。</span><span class="sxs-lookup"><span data-stu-id="65369-177">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="65369-178">必須排除連接埠號碼。</span><span class="sxs-lookup"><span data-stu-id="65369-178">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="65369-179">如果清單空白，即表示允許所有主機。</span><span class="sxs-lookup"><span data-stu-id="65369-179">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="65369-180">最上層的萬用字元 `*` 代表會允許所有非空白的主機。</span><span class="sxs-lookup"><span data-stu-id="65369-180">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="65369-181">允許使用子網域萬用字元，但不會比對出根網域。</span><span class="sxs-lookup"><span data-stu-id="65369-181">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="65369-182">例如，`*.contoso.com` 會比對出子網域 `foo.contoso.com`，但不會比對出根網域 `contoso.com`。</span><span class="sxs-lookup"><span data-stu-id="65369-182">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="65369-183">允許使用 Unicode 主機名稱，但會轉換成 [Punycode](https://tools.ietf.org/html/rfc3492) 來進行比對。</span><span class="sxs-lookup"><span data-stu-id="65369-183">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="65369-184">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) 必須包含週框方括號，並採用[慣例格式](https://tools.ietf.org/html/rfc4291#section-2.2) (例如 `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`)。</span><span class="sxs-lookup"><span data-stu-id="65369-184">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="65369-185">IPv6 位址並未特別設計成會檢查不同格式間是否具有邏輯相等性，因此不會執行標準化。</span><span class="sxs-lookup"><span data-stu-id="65369-185">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="65369-186">如果無法限制可允許的主機，可能會讓攻擊者偽造服務所產生的連結。</span><span class="sxs-lookup"><span data-stu-id="65369-186">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="65369-187">預設值是空的 `IList<string>`。</span><span class="sxs-lookup"><span data-stu-id="65369-187">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="65369-188">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-188">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="65369-189">當 Proxy/轉寄站未使用 `X-Forwarded-For` 標頭，而使用其他標頭轉送資訊時，會使用此選項。</span><span class="sxs-lookup"><span data-stu-id="65369-189">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="65369-190">預設為 `X-Forwarded-For`。</span><span class="sxs-lookup"><span data-stu-id="65369-190">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="65369-191">識別應該處理哪個轉送子。</span><span class="sxs-lookup"><span data-stu-id="65369-191">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="65369-192">如需適用的欄位清單，請參閱 [ForwardedHeaders 列舉](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="65369-192">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="65369-193">指派給此屬性的一般值為 `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`。</span><span class="sxs-lookup"><span data-stu-id="65369-193">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="65369-194">預設值為 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="65369-194">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="65369-195">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-195">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="65369-196">當 Proxy/轉寄站未使用 `X-Forwarded-Host` 標頭，而使用其他標頭轉送資訊時，會使用此選項。</span><span class="sxs-lookup"><span data-stu-id="65369-196">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="65369-197">預設為 `X-Forwarded-Host`。</span><span class="sxs-lookup"><span data-stu-id="65369-197">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="65369-198">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-198">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="65369-199">當 Proxy/轉寄站未使用 `X-Forwarded-Proto` 標頭，而使用其他標頭轉送資訊時，會使用此選項。</span><span class="sxs-lookup"><span data-stu-id="65369-199">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="65369-200">預設為 `X-Forwarded-Proto`。</span><span class="sxs-lookup"><span data-stu-id="65369-200">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="65369-201">限制所處理標頭中的項目數。</span><span class="sxs-lookup"><span data-stu-id="65369-201">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="65369-202">設定為 `null` 可停用限制，但應該只有在已設定 `KnownProxies` 或 `KnownNetworks` 的情況下，才這樣做。</span><span class="sxs-lookup"><span data-stu-id="65369-202">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="65369-203">設置非 `null` 值是一種預防措施 (但不是保證)，以防止設定不正確的 Proxy 和來自網路上的旁路惡意要求。</span><span class="sxs-lookup"><span data-stu-id="65369-203">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="65369-204">「轉送的標頭中介軟體」會以相反順序 (從右至左) 處理標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-204">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="65369-205">如果使用預設值 (`1`)，除非 `ForwardLimit` 的值增加，否則只會處理標頭中最右邊的值。</span><span class="sxs-lookup"><span data-stu-id="65369-205">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="65369-206">預設為 `1`。</span><span class="sxs-lookup"><span data-stu-id="65369-206">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="65369-207">可從中接受轉送標頭的已知網路位址範圍。</span><span class="sxs-lookup"><span data-stu-id="65369-207">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="65369-208">請使用無類別網域間路由 (CIDR) 標記法來提供 IP 範圍。</span><span class="sxs-lookup"><span data-stu-id="65369-208">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="65369-209">若伺服器使用雙模式通訊端，會以 IPv6 格式 (例如，IPv4 中的 `10.0.0.1` 在 IPv6 中以 `::ffff:10.0.0.1` 表示) 提供 IPv4 位址。</span><span class="sxs-lookup"><span data-stu-id="65369-209">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="65369-210">請參閱 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="65369-210">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="65369-211">透過查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 以判斷是否需要此格式。</span><span class="sxs-lookup"><span data-stu-id="65369-211">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span><br><br><span data-ttu-id="65369-212">預設值為包含單一  項目的 `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>`IPAddress.Loopback`>。</span><span class="sxs-lookup"><span data-stu-id="65369-212">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="65369-213">可從中接受轉送標頭的已知 Proxy 位址。</span><span class="sxs-lookup"><span data-stu-id="65369-213">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="65369-214">請使用 `KnownProxies` 來指定確切的相符 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="65369-214">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="65369-215">若伺服器使用雙模式通訊端，會以 IPv6 格式 (例如，IPv4 中的 `10.0.0.1` 在 IPv6 中以 `::ffff:10.0.0.1` 表示) 提供 IPv4 位址。</span><span class="sxs-lookup"><span data-stu-id="65369-215">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="65369-216">請參閱 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="65369-216">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="65369-217">透過查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 以判斷是否需要此格式。</span><span class="sxs-lookup"><span data-stu-id="65369-217">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span><br><br><span data-ttu-id="65369-218">預設值為包含單一  項目的 `IList`\<<xref:System.Net.IPAddress>`IPAddress.IPv6Loopback`>。</span><span class="sxs-lookup"><span data-stu-id="65369-218">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="65369-219">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-219">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="65369-220">預設為 `X-Original-For`。</span><span class="sxs-lookup"><span data-stu-id="65369-220">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="65369-221">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-221">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="65369-222">預設為 `X-Original-Host`。</span><span class="sxs-lookup"><span data-stu-id="65369-222">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="65369-223">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-223">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="65369-224">預設為 `X-Original-Proto`。</span><span class="sxs-lookup"><span data-stu-id="65369-224">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="65369-225">要求所處理 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 的標頭值數目必須同步。</span><span class="sxs-lookup"><span data-stu-id="65369-225">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="65369-226">ASP.NET Core 1.x 中的預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="65369-226">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="65369-227">ASP.NET Core 2.0 或更新版本中的預設值為 `false`。</span><span class="sxs-lookup"><span data-stu-id="65369-227">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="65369-228">情節和使用案例</span><span class="sxs-lookup"><span data-stu-id="65369-228">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="65369-229">當可以新增轉送的標頭且所有要求都安全時</span><span class="sxs-lookup"><span data-stu-id="65369-229">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="65369-230">在某些情況下，可能無法將轉送的標頭新增至透過 Proxy 傳送給應用程式的要求。</span><span class="sxs-lookup"><span data-stu-id="65369-230">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="65369-231">如果 Proxy 強制要求所有公用外部要求都必須是 HTTPS，您可以在使用任何類型的中介軟體之前，先手動在 `Startup.Configure` 中設定該配置：</span><span class="sxs-lookup"><span data-stu-id="65369-231">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="65369-232">在開發或預備環境中，可以使用環境變數或其他組態設定來停用此程式碼。</span><span class="sxs-lookup"><span data-stu-id="65369-232">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="65369-233">處理路徑基底和變更要求路徑的 Proxy</span><span class="sxs-lookup"><span data-stu-id="65369-233">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="65369-234">有些 Proxy 會原封不動傳送路徑，但其中含有應移除才能正確路由傳送的應用程式基底路徑。</span><span class="sxs-lookup"><span data-stu-id="65369-234">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="65369-235">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) 中介軟體會將該路徑分割成 [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path)，以及將應用程式基底路徑分割成 [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)。</span><span class="sxs-lookup"><span data-stu-id="65369-235">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="65369-236">如果 `/foo` 是以 `/foo/api/1` 形式傳送之 Proxy 路徑的應用程式基底路徑，中介軟體就會使用下列命令，將 `Request.PathBase` 設定為 `/foo`，以及將 `Request.Path` 設定為 `/api/1`：</span><span class="sxs-lookup"><span data-stu-id="65369-236">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="65369-237">反向再次呼叫應用程式時，則會重新套用原始路徑和路徑基底。</span><span class="sxs-lookup"><span data-stu-id="65369-237">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="65369-238">如需中介軟體順序處理的詳細資訊，請參閱 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="65369-238">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="65369-239">如果 Proxy 會修剪路徑 (例如，將 `/foo/api/1` 轉送給 `/api/1`)，請設定要求的 [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) 屬性來修正重新導向和連結：</span><span class="sxs-lookup"><span data-stu-id="65369-239">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="65369-240">如果 Proxy 會新增路徑資料，請使用 <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> 並指派給 <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> 屬性，以捨棄部分路徑來修正重新導向和連結：</span><span class="sxs-lookup"><span data-stu-id="65369-240">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="65369-241">使用不同標頭名稱之 Proxy 的組態</span><span class="sxs-lookup"><span data-stu-id="65369-241">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="65369-242">如果 Proxy 未使用名為 `X-Forwarded-For` 和 `X-Forwarded-Proto` 的標頭轉送 Proxy 位址/連接埠與原始配置資訊，請設定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 與 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 選項，以符合 Proxy 使用的標頭名稱：</span><span class="sxs-lookup"><span data-stu-id="65369-242">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="65369-243">轉送 Linux 和非 IIS 反向 Proxy 的配置</span><span class="sxs-lookup"><span data-stu-id="65369-243">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="65369-244">如果部署至 Azure Linux App Service、Azure Linux 虛擬機器 (VM)，或 IIS 之外的任何其他反向 Proxy 後端，則呼叫 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> 和 <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> 的這些應用程式會使站台進入無限迴圈。</span><span class="sxs-lookup"><span data-stu-id="65369-244">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="65369-245">反向 Proxy 會終止 TLS，且正確的要求配置不知道有 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="65369-245">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="65369-246">OAuth 和 OIDC 在此組態中也失敗，因為它們會產生不正確的重新導向。</span><span class="sxs-lookup"><span data-stu-id="65369-246">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="65369-247"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 在 IIS 後端執行時，會新增並設定轉送標頭中介軟體，但沒有相符的 Linux (Apache 或 Nginx 整合) 自動組態。</span><span class="sxs-lookup"><span data-stu-id="65369-247"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="65369-248">若要從非 IIS 案例的 Proxy 轉送配置，請新增並設定轉送標頭中介軟體。</span><span class="sxs-lookup"><span data-stu-id="65369-248">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="65369-249">在 `Startup.ConfigureServices` 中，使用下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="65369-249">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="65369-250">憑證轉送</span><span class="sxs-lookup"><span data-stu-id="65369-250">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="65369-251">Azure</span><span class="sxs-lookup"><span data-stu-id="65369-251">Azure</span></span>

<span data-ttu-id="65369-252">若要設定 Azure App Service 以進行憑證轉送，請參閱 [設定 Azure App service 的 TLS 相互驗證](/azure/app-service/app-service-web-configure-tls-mutual-auth)。</span><span class="sxs-lookup"><span data-stu-id="65369-252">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="65369-253">下列指導方針適用于設定 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="65369-253">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="65369-254">在中 `Startup.Configure` ，在呼叫之前加入下列程式碼 `app.UseAuthentication();` ：</span><span class="sxs-lookup"><span data-stu-id="65369-254">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="65369-255">設定憑證轉送中介軟體以指定 Azure 所使用的標頭名稱。</span><span class="sxs-lookup"><span data-stu-id="65369-255">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="65369-256">在中 `Startup.ConfigureServices` ，新增下列程式碼，以設定中介軟體用來建立憑證的標頭：</span><span class="sxs-lookup"><span data-stu-id="65369-256">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="65369-257">其他 web proxy</span><span class="sxs-lookup"><span data-stu-id="65369-257">Other web proxies</span></span>

<span data-ttu-id="65369-258">如果使用的 proxy 不是 IIS 或 Azure App Service 的應用程式要求路由 (ARR) ，請將 proxy 設定為轉寄它在 HTTP 標頭中收到的憑證。</span><span class="sxs-lookup"><span data-stu-id="65369-258">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="65369-259">在中 `Startup.Configure` ，在呼叫之前加入下列程式碼 `app.UseAuthentication();` ：</span><span class="sxs-lookup"><span data-stu-id="65369-259">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="65369-260">設定憑證轉送中介軟體以指定標頭名稱。</span><span class="sxs-lookup"><span data-stu-id="65369-260">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="65369-261">在中 `Startup.ConfigureServices` ，新增下列程式碼，以設定中介軟體用來建立憑證的標頭：</span><span class="sxs-lookup"><span data-stu-id="65369-261">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="65369-262">如果 proxy 不是以 base64 編碼憑證 (與 Nginx) 的情況相同，請設定此 `HeaderConverter` 選項。</span><span class="sxs-lookup"><span data-stu-id="65369-262">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="65369-263">請考慮 `Startup.ConfigureServices` 中的下列範例：</span><span class="sxs-lookup"><span data-stu-id="65369-263">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="65369-264">疑難排解</span><span class="sxs-lookup"><span data-stu-id="65369-264">Troubleshoot</span></span>

<span data-ttu-id="65369-265">當標頭未如預期般傳送時，請啟用[記錄功能](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="65369-265">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="65369-266">如果記錄提供的資訊不足，無法針對問題進行疑難排解，則請列舉伺服器所收到的要求標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-266">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="65369-267">使用內嵌中介軟體將要求標頭寫入應用程式回應或記錄標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-267">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="65369-268">若要將標頭寫入至應用程式的回應，將下列終端機內嵌中介軟體緊接著放置於 `Startup.Configure` 中對 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 的呼叫之後：</span><span class="sxs-lookup"><span data-stu-id="65369-268">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="65369-269">您可以寫入至記錄，而不是回應本文。</span><span class="sxs-lookup"><span data-stu-id="65369-269">You can write to logs instead of the response body.</span></span> <span data-ttu-id="65369-270">寫入至記錄可讓網站在偵錯時正常運作。</span><span class="sxs-lookup"><span data-stu-id="65369-270">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="65369-271">若要寫入記錄而不是回應本文：</span><span class="sxs-lookup"><span data-stu-id="65369-271">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="65369-272">將 `ILogger<Startup>` 插入至 `Startup` 類別，如[在啟動中建立記錄](xref:fundamentals/logging/index#create-logs-in-startup)中所述。</span><span class="sxs-lookup"><span data-stu-id="65369-272">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="65369-273">將下列終端機內嵌中介軟體緊接著放置於 `Startup.Configure` 中對 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 的呼叫之後。</span><span class="sxs-lookup"><span data-stu-id="65369-273">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="65369-274">處理後，`X-Forwarded-{For|Proto|Host}` 值會移至 `X-Original-{For|Proto|Host}`。</span><span class="sxs-lookup"><span data-stu-id="65369-274">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="65369-275">如果指定的標頭中有多個值，「轉送的標頭中介軟體」會以相反順序 (從右至左) 處理標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-275">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="65369-276">預設的 `ForwardLimit` 是 `1` (一)，因此除非 `ForwardLimit` 的值增加，否則只會處理標頭中最右邊的值。</span><span class="sxs-lookup"><span data-stu-id="65369-276">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="65369-277">要求的原始遠端 IP 必須符合 `KnownProxies` 或 `KnownNetworks` 清單中的項目，系統才會處理轉送的標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-277">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="65369-278">這可藉由不接受來自不受信任 Proxy 的轉送子，以限制標頭詐騙行為。</span><span class="sxs-lookup"><span data-stu-id="65369-278">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="65369-279">當偵測到未知的 Proxy 時，記錄會指出 Proxy 的位址：</span><span class="sxs-lookup"><span data-stu-id="65369-279">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="65369-280">在上述範例中，10.0.0.100 是 Proxy 伺服器。</span><span class="sxs-lookup"><span data-stu-id="65369-280">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="65369-281">如果伺服器是信任的 Proxy，請將伺服器的 IP 位址新增至 `Startup.ConfigureServices` 中的 `KnownProxies` (或將信任的網路新增至 `KnownNetworks`)。</span><span class="sxs-lookup"><span data-stu-id="65369-281">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="65369-282">如需詳細資訊，請參閱[轉送的標頭中介軟體選項](#forwarded-headers-middleware-options)一節。</span><span class="sxs-lookup"><span data-stu-id="65369-282">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="65369-283">只允許信任的 Proxy 以及網路轉送標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-283">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="65369-284">否則，[IP 詐騙](https://www.iplocation.net/ip-spoofing)攻擊有可能發生。</span><span class="sxs-lookup"><span data-stu-id="65369-284">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="65369-285">其他資源</span><span class="sxs-lookup"><span data-stu-id="65369-285">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="65369-286">[Microsoft Security Advisory CVE-2018-0787：ASP.NET Core 權限提高弱點](https://github.com/aspnet/Announcements/issues/295) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="65369-286">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="65369-287">在建議的 ASP.NET Core 設定中，是使用 IIS/ASP.NET Core 模組、Nginx 或 Apache 來裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="65369-287">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="65369-288">Proxy 伺服器、負載平衡器及其他網路設備通常會在要求觸達應用程式之前，遮蔽要求的相關資訊：</span><span class="sxs-lookup"><span data-stu-id="65369-288">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="65369-289">透過 HTTP 作為 Proxy 來處理 HTTPS 要求時，原始配置 (HTTPS) 會遺失而必須在標頭中轉送。</span><span class="sxs-lookup"><span data-stu-id="65369-289">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="65369-290">由於應用程式會從 Proxy 而不是從網際網路或公司網路上的真實來源收到要求，因此必須也在標頭中轉送原始用戶端 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="65369-290">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="65369-291">此資訊在要求處理方面可能相當重要，例如在重新導向、驗證、連結產生、原則評估及用戶端地理位置方面。</span><span class="sxs-lookup"><span data-stu-id="65369-291">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="65369-292">轉送的標頭</span><span class="sxs-lookup"><span data-stu-id="65369-292">Forwarded headers</span></span>

<span data-ttu-id="65369-293">依照慣例，Proxy 會以 HTTP 標頭轉送資訊。</span><span class="sxs-lookup"><span data-stu-id="65369-293">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="65369-294">標頭</span><span class="sxs-lookup"><span data-stu-id="65369-294">Header</span></span> | <span data-ttu-id="65369-295">描述</span><span class="sxs-lookup"><span data-stu-id="65369-295">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="65369-296">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="65369-296">X-Forwarded-For</span></span> | <span data-ttu-id="65369-297">針對在 Proxy 鏈結中起始要求及後續 Proxy 的用戶端，保存用戶端的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="65369-297">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="65369-298">此參數可能包含 IP 位址 (以及視需要可能會有連接埠號碼)。</span><span class="sxs-lookup"><span data-stu-id="65369-298">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="65369-299">在 Proxy 伺服器鏈結中，第一個參數會指出起始要求的用戶端。</span><span class="sxs-lookup"><span data-stu-id="65369-299">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="65369-300">後面接著後續的 Proxy 識別碼。</span><span class="sxs-lookup"><span data-stu-id="65369-300">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="65369-301">鏈結中的最後一個 Proxy 並不在參數清單中。</span><span class="sxs-lookup"><span data-stu-id="65369-301">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="65369-302">最後一個 Proxy 的 IP 位址 (以及視需要會有連接埠號碼) 會在傳輸層以遠端 IP 位址的形式提供。</span><span class="sxs-lookup"><span data-stu-id="65369-302">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="65369-303">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="65369-303">X-Forwarded-Proto</span></span> | <span data-ttu-id="65369-304">原始配置的值 (HTTP/HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="65369-304">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="65369-305">如果要求周遊了多個 Proxy，則此值也可能是一個配置清單。</span><span class="sxs-lookup"><span data-stu-id="65369-305">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="65369-306">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="65369-306">X-Forwarded-Host</span></span> | <span data-ttu-id="65369-307">主機標頭欄位的原始值。</span><span class="sxs-lookup"><span data-stu-id="65369-307">The original value of the Host header field.</span></span> <span data-ttu-id="65369-308">通常，Proxy 不會修改主機標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-308">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="65369-309">如需有關權限提高弱點的資訊，請參閱 [Microsoft 資訊安全諮詢 CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) \(英文\)，此弱點會影響 Proxy 不會驗證或限制主機標頭為已知有效值的系統。</span><span class="sxs-lookup"><span data-stu-id="65369-309">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="65369-310">來自 [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) 套件的「轉送的標頭中介軟體」會讀取這些標頭，並填入 <xref:Microsoft.AspNetCore.Http.HttpContext> 上相關聯的欄位。</span><span class="sxs-lookup"><span data-stu-id="65369-310">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="65369-311">中介軟體會更新：</span><span class="sxs-lookup"><span data-stu-id="65369-311">The middleware updates:</span></span>

* <span data-ttu-id="65369-312">[Server.remoteipaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress)：使用 `X-Forwarded-For` 標頭值設定。</span><span class="sxs-lookup"><span data-stu-id="65369-312">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="65369-313">額外的設定會影響中介軟體設定 `RemoteIpAddress`的方式。</span><span class="sxs-lookup"><span data-stu-id="65369-313">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="65369-314">如需詳細資料，請參閱[轉送的標頭中介軟體選項](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="65369-314">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="65369-315">[HttpCoNtext. 配置](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme)：使用 `X-Forwarded-Proto` 標頭值設定。</span><span class="sxs-lookup"><span data-stu-id="65369-315">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="65369-316">[HttpcoNtext.current](xref:Microsoft.AspNetCore.Http.HttpRequest.Host)：使用 `X-Forwarded-Host` 標頭值設定。</span><span class="sxs-lookup"><span data-stu-id="65369-316">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="65369-317">您可以設定「轉送的標頭中介軟體」的[預設設定](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="65369-317">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="65369-318">預設設定值為：</span><span class="sxs-lookup"><span data-stu-id="65369-318">The default settings are:</span></span>

* <span data-ttu-id="65369-319">在應用程式與要求的來源之間只有「一個 Proxy」。</span><span class="sxs-lookup"><span data-stu-id="65369-319">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="65369-320">針對已知的 Proxy 和已知的網路，只會設定回送位址。</span><span class="sxs-lookup"><span data-stu-id="65369-320">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="65369-321">轉送標頭名稱為 `X-Forwarded-For` 和 `X-Forwarded-Proto`。</span><span class="sxs-lookup"><span data-stu-id="65369-321">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="65369-322">並非所有網路設備在新增 `X-Forwarded-For` 和 `X-Forwarded-Proto` 標頭時都不含額外組態。</span><span class="sxs-lookup"><span data-stu-id="65369-322">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="65369-323">如果透過 Proxy 傳送的要求在觸達應用程式時未包含這些標頭，請向您的設備製造商尋求指引。</span><span class="sxs-lookup"><span data-stu-id="65369-323">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="65369-324">如果設備使用的名稱不是 `X-Forwarded-For` 和 `X-Forwarded-Proto`，請設定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 與 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 選項，以符合設備使用的標頭名稱。</span><span class="sxs-lookup"><span data-stu-id="65369-324">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="65369-325">如需詳細資訊，請參閱[轉送標頭中介軟體選項](#forwarded-headers-middleware-options)和[使用不同標頭名稱之 Proxy 的組態](#configuration-for-a-proxy-that-uses-different-header-names)。</span><span class="sxs-lookup"><span data-stu-id="65369-325">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="65369-326">IIS/IIS Express 和 ASP.NET Core 模組</span><span class="sxs-lookup"><span data-stu-id="65369-326">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="65369-327">當應用程式是在 IIS 和 ASP.NET Core 模組的後方進行[處理序外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)裝載時，[IIS 整合中介軟體](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)預設會啟用「轉送的標頭中介軟體」。</span><span class="sxs-lookup"><span data-stu-id="65369-327">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="65369-328">由於有與轉送標頭相關的信任考量 (例如 [IP 詐騙](https://www.iplocation.net/ip-spoofing))，因此「轉送的標頭中介軟體」在啟用後會先在中介軟體管線中搭配 ASP.NET Core 模組限定的設定來執行。</span><span class="sxs-lookup"><span data-stu-id="65369-328">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="65369-329">中介軟體會經設定來轉送 `X-Forwarded-For` 和 `X-Forwarded-Proto` 標頭，並限制成單一 localhost Proxy。</span><span class="sxs-lookup"><span data-stu-id="65369-329">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="65369-330">如果需要額外的設定，請參閱[轉送的標頭中介軟體選項](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="65369-330">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="65369-331">其他 Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="65369-331">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="65369-332">除了在[處理序外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)裝載時使用 [IIS 整合](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)之外，都未預設啟用「轉送的標頭中介軟體」。</span><span class="sxs-lookup"><span data-stu-id="65369-332">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="65369-333">必須啟用「轉送的標頭中介軟體」，應用程式才能使用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 來處理轉送的標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-333">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="65369-334">啟用此中介軟體之後，如果未將任何 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 指定給中介軟體，則預設的 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 會是 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="65369-334">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="65369-335">搭配 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 設定中介軟體，以在 `Startup.ConfigureServices` 中轉送 `X-Forwarded-For` 與 `X-Forwarded-Proto` 標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-335">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="65369-336">在呼叫其他中介軟體之前，請先在 `Startup.Configure` 中叫用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 方法：</span><span class="sxs-lookup"><span data-stu-id="65369-336">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="65369-337">若未在 `Startup.ConfigureServices` 中指定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>，或未使用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 直接指定到擴充方法，則要轉送的預設標頭是 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="65369-337">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="65369-338">必須使用要轉送的標頭設定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> 屬性。</span><span class="sxs-lookup"><span data-stu-id="65369-338">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="65369-339">Nginx 組態</span><span class="sxs-lookup"><span data-stu-id="65369-339">Nginx configuration</span></span>

<span data-ttu-id="65369-340">若要轉送 `X-Forwarded-For` 和 `X-Forwarded-Proto` 標頭，請參閱 <xref:host-and-deploy/linux-nginx#configure-nginx>。</span><span class="sxs-lookup"><span data-stu-id="65369-340">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="65369-341">如需詳細資訊，請參閱 [NGINX：使用轉送的標頭](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)。</span><span class="sxs-lookup"><span data-stu-id="65369-341">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="65369-342">Apache 組態</span><span class="sxs-lookup"><span data-stu-id="65369-342">Apache configuration</span></span>

<span data-ttu-id="65369-343">`X-Forwarded-For` 會自動新增 (請參閱 [Apache 模組 mod_proxy：反向 Proxy 要求標頭](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers))。</span><span class="sxs-lookup"><span data-stu-id="65369-343">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="65369-344">如需如何轉送 `X-Forwarded-Proto` 標頭的資訊，請參閱 <xref:host-and-deploy/linux-apache#configure-apache>。</span><span class="sxs-lookup"><span data-stu-id="65369-344">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="65369-345">轉送的標頭中介軟體選項</span><span class="sxs-lookup"><span data-stu-id="65369-345">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="65369-346"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 會控制轉送標頭中介軟體的行為。</span><span class="sxs-lookup"><span data-stu-id="65369-346"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="65369-347">下列範例會變更預設值：</span><span class="sxs-lookup"><span data-stu-id="65369-347">The following example changes the default values:</span></span>

* <span data-ttu-id="65369-348">將轉送標頭中的項目數限制為 `2`。</span><span class="sxs-lookup"><span data-stu-id="65369-348">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="65369-349">新增已知的 Proxy 位址 `127.0.10.1`。</span><span class="sxs-lookup"><span data-stu-id="65369-349">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="65369-350">將轉送標頭名稱從預設的 `X-Forwarded-For` 變更為 `X-Forwarded-For-My-Custom-Header-Name`。</span><span class="sxs-lookup"><span data-stu-id="65369-350">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="65369-351">選項</span><span class="sxs-lookup"><span data-stu-id="65369-351">Option</span></span> | <span data-ttu-id="65369-352">描述</span><span class="sxs-lookup"><span data-stu-id="65369-352">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="65369-353">依據 `X-Forwarded-Host` 標頭將主機限制成所提供的值。</span><span class="sxs-lookup"><span data-stu-id="65369-353">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="65369-354">比較值時，會使用序數忽略大小寫的方式來比較。</span><span class="sxs-lookup"><span data-stu-id="65369-354">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="65369-355">必須排除連接埠號碼。</span><span class="sxs-lookup"><span data-stu-id="65369-355">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="65369-356">如果清單空白，即表示允許所有主機。</span><span class="sxs-lookup"><span data-stu-id="65369-356">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="65369-357">最上層的萬用字元 `*` 代表會允許所有非空白的主機。</span><span class="sxs-lookup"><span data-stu-id="65369-357">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="65369-358">允許使用子網域萬用字元，但不會比對出根網域。</span><span class="sxs-lookup"><span data-stu-id="65369-358">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="65369-359">例如，`*.contoso.com` 會比對出子網域 `foo.contoso.com`，但不會比對出根網域 `contoso.com`。</span><span class="sxs-lookup"><span data-stu-id="65369-359">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="65369-360">允許使用 Unicode 主機名稱，但會轉換成 [Punycode](https://tools.ietf.org/html/rfc3492) 來進行比對。</span><span class="sxs-lookup"><span data-stu-id="65369-360">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="65369-361">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) 必須包含週框方括號，並採用[慣例格式](https://tools.ietf.org/html/rfc4291#section-2.2) (例如 `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`)。</span><span class="sxs-lookup"><span data-stu-id="65369-361">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="65369-362">IPv6 位址並未特別設計成會檢查不同格式間是否具有邏輯相等性，因此不會執行標準化。</span><span class="sxs-lookup"><span data-stu-id="65369-362">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="65369-363">如果無法限制可允許的主機，可能會讓攻擊者偽造服務所產生的連結。</span><span class="sxs-lookup"><span data-stu-id="65369-363">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="65369-364">預設值是空的 `IList<string>`。</span><span class="sxs-lookup"><span data-stu-id="65369-364">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="65369-365">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-365">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="65369-366">當 Proxy/轉寄站未使用 `X-Forwarded-For` 標頭，而使用其他標頭轉送資訊時，會使用此選項。</span><span class="sxs-lookup"><span data-stu-id="65369-366">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="65369-367">預設為 `X-Forwarded-For`。</span><span class="sxs-lookup"><span data-stu-id="65369-367">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="65369-368">識別應該處理哪個轉送子。</span><span class="sxs-lookup"><span data-stu-id="65369-368">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="65369-369">如需適用的欄位清單，請參閱 [ForwardedHeaders 列舉](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="65369-369">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="65369-370">指派給此屬性的一般值為 `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`。</span><span class="sxs-lookup"><span data-stu-id="65369-370">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="65369-371">預設值為 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="65369-371">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="65369-372">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-372">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="65369-373">當 Proxy/轉寄站未使用 `X-Forwarded-Host` 標頭，而使用其他標頭轉送資訊時，會使用此選項。</span><span class="sxs-lookup"><span data-stu-id="65369-373">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="65369-374">預設為 `X-Forwarded-Host`。</span><span class="sxs-lookup"><span data-stu-id="65369-374">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="65369-375">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-375">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="65369-376">當 Proxy/轉寄站未使用 `X-Forwarded-Proto` 標頭，而使用其他標頭轉送資訊時，會使用此選項。</span><span class="sxs-lookup"><span data-stu-id="65369-376">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="65369-377">預設為 `X-Forwarded-Proto`。</span><span class="sxs-lookup"><span data-stu-id="65369-377">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="65369-378">限制所處理標頭中的項目數。</span><span class="sxs-lookup"><span data-stu-id="65369-378">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="65369-379">設定為 `null` 可停用限制，但應該只有在已設定 `KnownProxies` 或 `KnownNetworks` 的情況下，才這樣做。</span><span class="sxs-lookup"><span data-stu-id="65369-379">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="65369-380">設置非 `null` 值是一種預防措施 (但不是保證)，以防止設定不正確的 Proxy 和來自網路上的旁路惡意要求。</span><span class="sxs-lookup"><span data-stu-id="65369-380">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="65369-381">「轉送的標頭中介軟體」會以相反順序 (從右至左) 處理標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-381">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="65369-382">如果使用預設值 (`1`)，除非 `ForwardLimit` 的值增加，否則只會處理標頭中最右邊的值。</span><span class="sxs-lookup"><span data-stu-id="65369-382">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="65369-383">預設為 `1`。</span><span class="sxs-lookup"><span data-stu-id="65369-383">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="65369-384">可從中接受轉送標頭的已知網路位址範圍。</span><span class="sxs-lookup"><span data-stu-id="65369-384">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="65369-385">請使用無類別網域間路由 (CIDR) 標記法來提供 IP 範圍。</span><span class="sxs-lookup"><span data-stu-id="65369-385">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="65369-386">若伺服器使用雙模式通訊端，會以 IPv6 格式 (例如，IPv4 中的 `10.0.0.1` 在 IPv6 中以 `::ffff:10.0.0.1` 表示) 提供 IPv4 位址。</span><span class="sxs-lookup"><span data-stu-id="65369-386">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="65369-387">請參閱 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="65369-387">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="65369-388">透過查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 以判斷是否需要此格式。</span><span class="sxs-lookup"><span data-stu-id="65369-388">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="65369-389">如需詳細資訊，請參閱[以 IPv6 位址表示的 IPv4 位址設定](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)一節。</span><span class="sxs-lookup"><span data-stu-id="65369-389">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="65369-390">預設值為包含單一  項目的 `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>`IPAddress.Loopback`>。</span><span class="sxs-lookup"><span data-stu-id="65369-390">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="65369-391">可從中接受轉送標頭的已知 Proxy 位址。</span><span class="sxs-lookup"><span data-stu-id="65369-391">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="65369-392">請使用 `KnownProxies` 來指定確切的相符 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="65369-392">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="65369-393">若伺服器使用雙模式通訊端，會以 IPv6 格式 (例如，IPv4 中的 `10.0.0.1` 在 IPv6 中以 `::ffff:10.0.0.1` 表示) 提供 IPv4 位址。</span><span class="sxs-lookup"><span data-stu-id="65369-393">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="65369-394">請參閱 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="65369-394">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="65369-395">透過查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 以判斷是否需要此格式。</span><span class="sxs-lookup"><span data-stu-id="65369-395">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="65369-396">如需詳細資訊，請參閱[以 IPv6 位址表示的 IPv4 位址設定](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)一節。</span><span class="sxs-lookup"><span data-stu-id="65369-396">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="65369-397">預設值為包含單一  項目的 `IList`\<<xref:System.Net.IPAddress>`IPAddress.IPv6Loopback`>。</span><span class="sxs-lookup"><span data-stu-id="65369-397">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="65369-398">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-398">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="65369-399">預設為 `X-Original-For`。</span><span class="sxs-lookup"><span data-stu-id="65369-399">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="65369-400">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-400">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="65369-401">預設為 `X-Original-Host`。</span><span class="sxs-lookup"><span data-stu-id="65369-401">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="65369-402">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-402">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="65369-403">預設為 `X-Original-Proto`。</span><span class="sxs-lookup"><span data-stu-id="65369-403">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="65369-404">要求所處理 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 的標頭值數目必須同步。</span><span class="sxs-lookup"><span data-stu-id="65369-404">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="65369-405">ASP.NET Core 1.x 中的預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="65369-405">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="65369-406">ASP.NET Core 2.0 或更新版本中的預設值為 `false`。</span><span class="sxs-lookup"><span data-stu-id="65369-406">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="65369-407">情節和使用案例</span><span class="sxs-lookup"><span data-stu-id="65369-407">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="65369-408">當可以新增轉送的標頭且所有要求都安全時</span><span class="sxs-lookup"><span data-stu-id="65369-408">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="65369-409">在某些情況下，可能無法將轉送的標頭新增至透過 Proxy 傳送給應用程式的要求。</span><span class="sxs-lookup"><span data-stu-id="65369-409">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="65369-410">如果 Proxy 強制要求所有公用外部要求都必須是 HTTPS，您可以在使用任何類型的中介軟體之前，先手動在 `Startup.Configure` 中設定該配置：</span><span class="sxs-lookup"><span data-stu-id="65369-410">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="65369-411">在開發或預備環境中，可以使用環境變數或其他組態設定來停用此程式碼。</span><span class="sxs-lookup"><span data-stu-id="65369-411">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="65369-412">處理路徑基底和變更要求路徑的 Proxy</span><span class="sxs-lookup"><span data-stu-id="65369-412">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="65369-413">有些 Proxy 會原封不動傳送路徑，但其中含有應移除才能正確路由傳送的應用程式基底路徑。</span><span class="sxs-lookup"><span data-stu-id="65369-413">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="65369-414">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) 中介軟體會將該路徑分割成 [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path)，以及將應用程式基底路徑分割成 [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)。</span><span class="sxs-lookup"><span data-stu-id="65369-414">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="65369-415">如果 `/foo` 是以 `/foo/api/1` 形式傳送之 Proxy 路徑的應用程式基底路徑，中介軟體就會使用下列命令，將 `Request.PathBase` 設定為 `/foo`，以及將 `Request.Path` 設定為 `/api/1`：</span><span class="sxs-lookup"><span data-stu-id="65369-415">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="65369-416">反向再次呼叫應用程式時，則會重新套用原始路徑和路徑基底。</span><span class="sxs-lookup"><span data-stu-id="65369-416">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="65369-417">如需中介軟體順序處理的詳細資訊，請參閱 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="65369-417">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="65369-418">如果 Proxy 會修剪路徑 (例如，將 `/foo/api/1` 轉送給 `/api/1`)，請設定要求的 [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) 屬性來修正重新導向和連結：</span><span class="sxs-lookup"><span data-stu-id="65369-418">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="65369-419">如果 Proxy 會新增路徑資料，請使用 <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> 並指派給 <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> 屬性，以捨棄部分路徑來修正重新導向和連結：</span><span class="sxs-lookup"><span data-stu-id="65369-419">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="65369-420">使用不同標頭名稱之 Proxy 的組態</span><span class="sxs-lookup"><span data-stu-id="65369-420">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="65369-421">如果 Proxy 未使用名為 `X-Forwarded-For` 和 `X-Forwarded-Proto` 的標頭轉送 Proxy 位址/連接埠與原始配置資訊，請設定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 與 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 選項，以符合 Proxy 使用的標頭名稱：</span><span class="sxs-lookup"><span data-stu-id="65369-421">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="65369-422">以 IPv6 位址表示的 IPv4 位址設定</span><span class="sxs-lookup"><span data-stu-id="65369-422">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="65369-423">若伺服器使用雙模式通訊端，會以 IPv6 格式 (例如，IPv4 中的 `10.0.0.1` 在 IPv6 中以 `::ffff:10.0.0.1` 表示) 或 `::ffff:a00:1` 提供 IPv4 位址。</span><span class="sxs-lookup"><span data-stu-id="65369-423">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="65369-424">請參閱 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="65369-424">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="65369-425">透過查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 以判斷是否需要此格式。</span><span class="sxs-lookup"><span data-stu-id="65369-425">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="65369-426">在下列範例中，提供轉寄標頭的網路位址已以 IPv6 格式新增到 `KnownNetworks` 清單中。</span><span class="sxs-lookup"><span data-stu-id="65369-426">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="65369-427">IPv4 位址：`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="65369-427">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="65369-428">轉換的 IPv6 位址：`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="65369-428">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="65369-429">轉換的首碼長度：104</span><span class="sxs-lookup"><span data-stu-id="65369-429">Converted prefix length: 104</span></span>

<span data-ttu-id="65369-430">您也能以十六進位格式提供位址 (`10.11.12.1` 在 IPv6 中以 `::ffff:0a0b:0c01` 表示)。</span><span class="sxs-lookup"><span data-stu-id="65369-430">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="65369-431">當您將 IPv4 位址轉換為 IPv6 時，請加入 96 到 CIDR 首碼長度 (在此範例中為 `8`) 以將額外的 `::ffff:` IPv6 首碼 (8 + 96 = 104) 納入考慮。</span><span class="sxs-lookup"><span data-stu-id="65369-431">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="65369-432">轉送 Linux 和非 IIS 反向 Proxy 的配置</span><span class="sxs-lookup"><span data-stu-id="65369-432">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="65369-433">如果部署至 Azure Linux App Service、Azure Linux 虛擬機器 (VM)，或 IIS 之外的任何其他反向 Proxy 後端，則呼叫 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> 和 <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> 的這些應用程式會使站台進入無限迴圈。</span><span class="sxs-lookup"><span data-stu-id="65369-433">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="65369-434">反向 Proxy 會終止 TLS，且正確的要求配置不知道有 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="65369-434">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="65369-435">OAuth 和 OIDC 在此組態中也失敗，因為它們會產生不正確的重新導向。</span><span class="sxs-lookup"><span data-stu-id="65369-435">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="65369-436"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 在 IIS 後端執行時，會新增並設定轉送標頭中介軟體，但沒有相符的 Linux (Apache 或 Nginx 整合) 自動組態。</span><span class="sxs-lookup"><span data-stu-id="65369-436"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="65369-437">若要從非 IIS 案例的 Proxy 轉送配置，請新增並設定轉送標頭中介軟體。</span><span class="sxs-lookup"><span data-stu-id="65369-437">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="65369-438">在 `Startup.ConfigureServices` 中，使用下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="65369-438">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="65369-439">疑難排解</span><span class="sxs-lookup"><span data-stu-id="65369-439">Troubleshoot</span></span>

<span data-ttu-id="65369-440">當標頭未如預期般傳送時，請啟用[記錄功能](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="65369-440">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="65369-441">如果記錄提供的資訊不足，無法針對問題進行疑難排解，則請列舉伺服器所收到的要求標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-441">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="65369-442">使用內嵌中介軟體將要求標頭寫入應用程式回應或記錄標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-442">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="65369-443">若要將標頭寫入至應用程式的回應，將下列終端機內嵌中介軟體緊接著放置於 `Startup.Configure` 中對 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 的呼叫之後：</span><span class="sxs-lookup"><span data-stu-id="65369-443">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="65369-444">您可以寫入至記錄，而不是回應本文。</span><span class="sxs-lookup"><span data-stu-id="65369-444">You can write to logs instead of the response body.</span></span> <span data-ttu-id="65369-445">寫入至記錄可讓網站在偵錯時正常運作。</span><span class="sxs-lookup"><span data-stu-id="65369-445">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="65369-446">若要寫入記錄而不是回應本文：</span><span class="sxs-lookup"><span data-stu-id="65369-446">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="65369-447">將 `ILogger<Startup>` 插入至 `Startup` 類別，如[在啟動中建立記錄](xref:fundamentals/logging/index#create-logs-in-startup)中所述。</span><span class="sxs-lookup"><span data-stu-id="65369-447">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="65369-448">將下列終端機內嵌中介軟體緊接著放置於 `Startup.Configure` 中對 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 的呼叫之後。</span><span class="sxs-lookup"><span data-stu-id="65369-448">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="65369-449">處理後，`X-Forwarded-{For|Proto|Host}` 值會移至 `X-Original-{For|Proto|Host}`。</span><span class="sxs-lookup"><span data-stu-id="65369-449">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="65369-450">如果指定的標頭中有多個值，「轉送的標頭中介軟體」會以相反順序 (從右至左) 處理標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-450">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="65369-451">預設的 `ForwardLimit` 是 `1` (一)，因此除非 `ForwardLimit` 的值增加，否則只會處理標頭中最右邊的值。</span><span class="sxs-lookup"><span data-stu-id="65369-451">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="65369-452">要求的原始遠端 IP 必須符合 `KnownProxies` 或 `KnownNetworks` 清單中的項目，系統才會處理轉送的標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-452">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="65369-453">這可藉由不接受來自不受信任 Proxy 的轉送子，以限制標頭詐騙行為。</span><span class="sxs-lookup"><span data-stu-id="65369-453">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="65369-454">當偵測到未知的 Proxy 時，記錄會指出 Proxy 的位址：</span><span class="sxs-lookup"><span data-stu-id="65369-454">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="65369-455">在上述範例中，10.0.0.100 是 Proxy 伺服器。</span><span class="sxs-lookup"><span data-stu-id="65369-455">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="65369-456">如果伺服器是信任的 Proxy，請將伺服器的 IP 位址新增至 `Startup.ConfigureServices` 中的 `KnownProxies` (或將信任的網路新增至 `KnownNetworks`)。</span><span class="sxs-lookup"><span data-stu-id="65369-456">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="65369-457">如需詳細資訊，請參閱[轉送的標頭中介軟體選項](#forwarded-headers-middleware-options)一節。</span><span class="sxs-lookup"><span data-stu-id="65369-457">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="65369-458">只允許信任的 Proxy 以及網路轉送標頭。</span><span class="sxs-lookup"><span data-stu-id="65369-458">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="65369-459">否則，[IP 詐騙](https://www.iplocation.net/ip-spoofing)攻擊有可能發生。</span><span class="sxs-lookup"><span data-stu-id="65369-459">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="65369-460">其他資源</span><span class="sxs-lookup"><span data-stu-id="65369-460">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="65369-461">[Microsoft Security Advisory CVE-2018-0787：ASP.NET Core 權限提高弱點](https://github.com/aspnet/Announcements/issues/295) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="65369-461">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end
