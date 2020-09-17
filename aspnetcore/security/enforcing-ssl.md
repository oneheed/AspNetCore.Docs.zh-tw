---
title: 在 ASP.NET Core 中強制使用 HTTPS
author: rick-anderson
description: 瞭解如何要求 ASP.NET Core web 應用程式中的 HTTPS/TLS。
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
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
uid: security/enforcing-ssl
ms.openlocfilehash: b5260084c2fdd296168e918f06d8b54faf1865d5
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722653"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="ad963-103">在 ASP.NET Core 中強制使用 HTTPS</span><span class="sxs-lookup"><span data-stu-id="ad963-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="ad963-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ad963-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ad963-105">本檔說明如何：</span><span class="sxs-lookup"><span data-stu-id="ad963-105">This document shows how to:</span></span>

* <span data-ttu-id="ad963-106">所有要求都需要 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="ad963-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="ad963-107">將所有 HTTP 要求都重新導向至 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="ad963-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="ad963-108">沒有任何 API 可以防止用戶端在第一個要求上傳送機密資料。</span><span class="sxs-lookup"><span data-stu-id="ad963-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="ad963-109">API 專案</span><span class="sxs-lookup"><span data-stu-id="ad963-109">API projects</span></span>
>
> <span data-ttu-id="ad963-110">請勿 **在** 接收敏感資訊的 Web api 上使用 [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) 。</span><span class="sxs-lookup"><span data-stu-id="ad963-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="ad963-111">`RequireHttpsAttribute` 使用 HTTP 狀態碼將瀏覽器從 HTTP 重新導向至 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="ad963-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="ad963-112">API 用戶端可能無法理解或遵守從 HTTP 到 HTTPS 的重新導向。</span><span class="sxs-lookup"><span data-stu-id="ad963-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="ad963-113">這類用戶端可能會透過 HTTP 傳送資訊。</span><span class="sxs-lookup"><span data-stu-id="ad963-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="ad963-114">Web Api 必須：</span><span class="sxs-lookup"><span data-stu-id="ad963-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="ad963-115">未在 HTTP 上接聽。</span><span class="sxs-lookup"><span data-stu-id="ad963-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="ad963-116">關閉狀態碼為400的連線 (錯誤的要求) ，而不提供要求。</span><span class="sxs-lookup"><span data-stu-id="ad963-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="ad963-117">HSTS 和 API 專案</span><span class="sxs-lookup"><span data-stu-id="ad963-117">HSTS and API projects</span></span>
>
> <span data-ttu-id="ad963-118">預設 API 專案不包含 [HSTS](#hsts) ，因為 HSTS 通常是僅限瀏覽器的指令。</span><span class="sxs-lookup"><span data-stu-id="ad963-118">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="ad963-119">其他呼叫端（例如電話或桌面應用程式） **不會** 遵守指令。</span><span class="sxs-lookup"><span data-stu-id="ad963-119">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="ad963-120">即使在瀏覽器中，透過 HTTP 對 API 進行的單一驗證呼叫也會對不安全的網路產生風險。</span><span class="sxs-lookup"><span data-stu-id="ad963-120">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="ad963-121">安全的方法是將 API 專案設定為只透過 HTTPS 接聽和回應。</span><span class="sxs-lookup"><span data-stu-id="ad963-121">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="ad963-122">API 專案</span><span class="sxs-lookup"><span data-stu-id="ad963-122">API projects</span></span>
>
> <span data-ttu-id="ad963-123">請勿 **在** 接收敏感資訊的 Web api 上使用 [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) 。</span><span class="sxs-lookup"><span data-stu-id="ad963-123">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="ad963-124">`RequireHttpsAttribute` 使用 HTTP 狀態碼將瀏覽器從 HTTP 重新導向至 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="ad963-124">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="ad963-125">API 用戶端可能無法理解或遵守從 HTTP 到 HTTPS 的重新導向。</span><span class="sxs-lookup"><span data-stu-id="ad963-125">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="ad963-126">這類用戶端可能會透過 HTTP 傳送資訊。</span><span class="sxs-lookup"><span data-stu-id="ad963-126">Such clients may send information over HTTP.</span></span> <span data-ttu-id="ad963-127">Web Api 必須：</span><span class="sxs-lookup"><span data-stu-id="ad963-127">Web APIs should either:</span></span>
>
> * <span data-ttu-id="ad963-128">未在 HTTP 上接聽。</span><span class="sxs-lookup"><span data-stu-id="ad963-128">Not listen on HTTP.</span></span>
> * <span data-ttu-id="ad963-129">關閉狀態碼為400的連線 (錯誤的要求) ，而不提供要求。</span><span class="sxs-lookup"><span data-stu-id="ad963-129">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="ad963-130">需要 HTTPS</span><span class="sxs-lookup"><span data-stu-id="ad963-130">Require HTTPS</span></span>

<span data-ttu-id="ad963-131">建議 ASP.NET Core web apps 使用生產環境：</span><span class="sxs-lookup"><span data-stu-id="ad963-131">We recommend that production ASP.NET Core web apps use:</span></span>

* <span data-ttu-id="ad963-132">HTTPS 重新導向中介軟體 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) 將 HTTP 要求重新導向至 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="ad963-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="ad963-133">HSTS 中介軟體 ([UseHsts](#http-strict-transport-security-protocol-hsts)) ，以將 HTTP Strict 傳輸安全性通訊協定 (HSTS) 標頭傳送給用戶端。</span><span class="sxs-lookup"><span data-stu-id="ad963-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="ad963-134">部署在反向 proxy 設定中的應用程式，可讓 proxy 處理 (HTTPS) 的連線安全性。</span><span class="sxs-lookup"><span data-stu-id="ad963-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="ad963-135">如果 proxy 也處理 HTTPS 重新導向，則不需要使用 HTTPS 重新導向中介軟體。</span><span class="sxs-lookup"><span data-stu-id="ad963-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="ad963-136">如果 proxy 伺服器也會處理寫入 HSTS 標頭 (例如， [IIS 10.0 (1709) 或更新版本) 中的原生 HSTS 支援](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support) ，則應用程式不需要 HSTS 中介軟體。</span><span class="sxs-lookup"><span data-stu-id="ad963-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="ad963-137">如需詳細資訊，請參閱 [在建立專案時退出宣告 HTTPS/HSTS](#opt-out-of-httpshsts-on-project-creation)。</span><span class="sxs-lookup"><span data-stu-id="ad963-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="ad963-138">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="ad963-138">UseHttpsRedirection</span></span>

<span data-ttu-id="ad963-139">下列程式碼會呼叫 `UseHttpsRedirection` 類別中的 `Startup` ：</span><span class="sxs-lookup"><span data-stu-id="ad963-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

<span data-ttu-id="ad963-140">上述反白顯示的程式碼：</span><span class="sxs-lookup"><span data-stu-id="ad963-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="ad963-141">使用預設的 [HttpsRedirectionOptions. RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)) 。</span><span class="sxs-lookup"><span data-stu-id="ad963-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="ad963-142">除非由環境變數或 >iserveraddressesfeature 覆寫，否則會使用預設的[HttpsRedirectionOptions. HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) 。 `ASPNETCORE_HTTPS_PORT` [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature)</span><span class="sxs-lookup"><span data-stu-id="ad963-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="ad963-143">我們建議使用暫時重新導向，而不是永久重新導向。</span><span class="sxs-lookup"><span data-stu-id="ad963-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="ad963-144">連結快取可能會在開發環境中造成不穩定的行為。</span><span class="sxs-lookup"><span data-stu-id="ad963-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="ad963-145">如果您想要在應用程式處於非開發環境時傳送永久的重新導向狀態碼，請參閱「 [在生產環境中設定永久重新導向](#configure-permanent-redirects-in-production) 」一節。</span><span class="sxs-lookup"><span data-stu-id="ad963-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="ad963-146">建議您使用 [HSTS](#http-strict-transport-security-protocol-hsts) 來通知用戶端，只將安全的資源要求傳送至應用程式 (只能在生產) 中傳送給應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad963-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="ad963-147">連接埠組態</span><span class="sxs-lookup"><span data-stu-id="ad963-147">Port configuration</span></span>

<span data-ttu-id="ad963-148">必須有埠才能讓中介軟體將不安全的要求重新導向至 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="ad963-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="ad963-149">如果沒有可用的埠：</span><span class="sxs-lookup"><span data-stu-id="ad963-149">If no port is available:</span></span>

* <span data-ttu-id="ad963-150">不會重新導向至 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="ad963-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="ad963-151">中介軟體會記錄「無法判斷重新導向的 HTTPs 埠」警告。</span><span class="sxs-lookup"><span data-stu-id="ad963-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="ad963-152">使用下列任何一種方法來指定 HTTPS 埠：</span><span class="sxs-lookup"><span data-stu-id="ad963-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="ad963-153">設定 [HttpsRedirectionOptions. HttpsPort](#options)。</span><span class="sxs-lookup"><span data-stu-id="ad963-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="ad963-154">設定 `https_port` [主機設定](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#https_port)：</span><span class="sxs-lookup"><span data-stu-id="ad963-154">Set the `https_port` [host setting](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#https_port):</span></span>

  * <span data-ttu-id="ad963-155">在 [主機設定] 中。</span><span class="sxs-lookup"><span data-stu-id="ad963-155">In host configuration.</span></span>
  * <span data-ttu-id="ad963-156">藉由設定 `ASPNETCORE_HTTPS_PORT` 環境變數。</span><span class="sxs-lookup"><span data-stu-id="ad963-156">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="ad963-157">藉由在 *appsettings.js*中新增最上層專案：</span><span class="sxs-lookup"><span data-stu-id="ad963-157">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* <span data-ttu-id="ad963-158">使用 [ASPNETCORE_URLS 環境變數](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#urls)來表示具有安全配置的埠。</span><span class="sxs-lookup"><span data-stu-id="ad963-158">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#urls).</span></span> <span data-ttu-id="ad963-159">環境變數會設定伺服器。</span><span class="sxs-lookup"><span data-stu-id="ad963-159">The environment variable configures the server.</span></span> <span data-ttu-id="ad963-160">中介軟體會透過，間接探索 HTTPS 埠 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> 。</span><span class="sxs-lookup"><span data-stu-id="ad963-160">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="ad963-161">這種方法不適用於反向 proxy 部署。</span><span class="sxs-lookup"><span data-stu-id="ad963-161">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* <span data-ttu-id="ad963-162">設定 `https_port` [主機設定](xref:fundamentals/host/web-host#https-port)：</span><span class="sxs-lookup"><span data-stu-id="ad963-162">Set the `https_port` [host setting](xref:fundamentals/host/web-host#https-port):</span></span>

  * <span data-ttu-id="ad963-163">在 [主機設定] 中。</span><span class="sxs-lookup"><span data-stu-id="ad963-163">In host configuration.</span></span>
  * <span data-ttu-id="ad963-164">藉由設定 `ASPNETCORE_HTTPS_PORT` 環境變數。</span><span class="sxs-lookup"><span data-stu-id="ad963-164">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="ad963-165">藉由在 *appsettings.js*中新增最上層專案：</span><span class="sxs-lookup"><span data-stu-id="ad963-165">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* <span data-ttu-id="ad963-166">使用 [ASPNETCORE_URLS 環境變數](xref:fundamentals/host/web-host#server-urls)來表示具有安全配置的埠。</span><span class="sxs-lookup"><span data-stu-id="ad963-166">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](xref:fundamentals/host/web-host#server-urls).</span></span> <span data-ttu-id="ad963-167">環境變數會設定伺服器。</span><span class="sxs-lookup"><span data-stu-id="ad963-167">The environment variable configures the server.</span></span> <span data-ttu-id="ad963-168">中介軟體會透過，間接探索 HTTPS 埠 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> 。</span><span class="sxs-lookup"><span data-stu-id="ad963-168">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="ad963-169">這種方法不適用於反向 proxy 部署。</span><span class="sxs-lookup"><span data-stu-id="ad963-169">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

* <span data-ttu-id="ad963-170">在開發中，請在 *launchsettings.js*中設定 HTTPS URL。</span><span class="sxs-lookup"><span data-stu-id="ad963-170">In development, set an HTTPS URL in *launchsettings.json*.</span></span> <span data-ttu-id="ad963-171">使用 IIS Express 時啟用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="ad963-171">Enable HTTPS when IIS Express is used.</span></span>

* <span data-ttu-id="ad963-172">針對 [Kestrel](xref:fundamentals/servers/kestrel) server 或 [HTTP.sys](xref:fundamentals/servers/httpsys) server 的公眾面向邊緣部署設定 HTTPS URL 端點。</span><span class="sxs-lookup"><span data-stu-id="ad963-172">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="ad963-173">應用程式只會使用 **一個 HTTPS 埠** 。</span><span class="sxs-lookup"><span data-stu-id="ad963-173">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="ad963-174">中介軟體會透過來探索埠 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> 。</span><span class="sxs-lookup"><span data-stu-id="ad963-174">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="ad963-175">當應用程式在反向 proxy 設定中執行時， <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> 無法使用。</span><span class="sxs-lookup"><span data-stu-id="ad963-175">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="ad963-176">使用本節中所述的其中一種方法來設定埠。</span><span class="sxs-lookup"><span data-stu-id="ad963-176">Set the port using one of the other approaches described in this section.</span></span>

### <a name="edge-deployments"></a><span data-ttu-id="ad963-177">Edge 部署</span><span class="sxs-lookup"><span data-stu-id="ad963-177">Edge deployments</span></span> 

<span data-ttu-id="ad963-178">當 Kestrel 或 HTTP.sys 用作公眾對應的 edge server 時，必須將 Kestrel 或 HTTP.sys 設定為同時接聽兩者：</span><span class="sxs-lookup"><span data-stu-id="ad963-178">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="ad963-179">用戶端重新導向的安全埠 (通常是在生產環境中為443，而開發) 的5001。</span><span class="sxs-lookup"><span data-stu-id="ad963-179">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="ad963-180">不安全的 (埠在生產環境中通常是80，而開發) 是5000。</span><span class="sxs-lookup"><span data-stu-id="ad963-180">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="ad963-181">用戶端必須能夠存取不安全的埠，應用程式才能接收不安全的要求，並將用戶端重新導向至安全的埠。</span><span class="sxs-lookup"><span data-stu-id="ad963-181">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="ad963-182">如需詳細資訊，請參閱 [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) 或 <xref:fundamentals/servers/httpsys> 。</span><span class="sxs-lookup"><span data-stu-id="ad963-182">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="ad963-183">部署案例</span><span class="sxs-lookup"><span data-stu-id="ad963-183">Deployment scenarios</span></span>

<span data-ttu-id="ad963-184">用戶端與伺服器之間的任何防火牆也必須針對流量開啟通訊埠。</span><span class="sxs-lookup"><span data-stu-id="ad963-184">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="ad963-185">如果在反向 proxy 設定中轉送要求，請在呼叫 HTTPS 重新導向中介軟體之前，先使用 [轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer) 。</span><span class="sxs-lookup"><span data-stu-id="ad963-185">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="ad963-186">轉送的標頭中介軟體會 `Request.Scheme` 使用 `X-Forwarded-Proto` 標頭來更新。</span><span class="sxs-lookup"><span data-stu-id="ad963-186">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="ad963-187">中介軟體允許重新導向 Uri 和其他安全性原則正確運作。</span><span class="sxs-lookup"><span data-stu-id="ad963-187">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="ad963-188">未使用轉送的標頭中介軟體時，後端應用程式可能不會收到正確的配置，最後會以重新導向迴圈結束。</span><span class="sxs-lookup"><span data-stu-id="ad963-188">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="ad963-189">常見的終端使用者錯誤訊息是發生太多重新導向。</span><span class="sxs-lookup"><span data-stu-id="ad963-189">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="ad963-190">部署至 Azure App Service 時，請遵循教學課程 [：將現有的自訂 SSL 憑證系結至 Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl)中的指導方針。</span><span class="sxs-lookup"><span data-stu-id="ad963-190">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="ad963-191">選項</span><span class="sxs-lookup"><span data-stu-id="ad963-191">Options</span></span>

<span data-ttu-id="ad963-192">下列反白顯示的程式碼會呼叫 [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) 來設定中介軟體選項：</span><span class="sxs-lookup"><span data-stu-id="ad963-192">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


<span data-ttu-id="ad963-193">`AddHttpsRedirection`只有變更或的值才需要呼叫 `HttpsPort` `RedirectStatusCode` 。</span><span class="sxs-lookup"><span data-stu-id="ad963-193">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="ad963-194">上述反白顯示的程式碼：</span><span class="sxs-lookup"><span data-stu-id="ad963-194">The preceding highlighted code:</span></span>

* <span data-ttu-id="ad963-195">將 [HttpsRedirectionOptions RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) 為 <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect> ，這是預設值。</span><span class="sxs-lookup"><span data-stu-id="ad963-195">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="ad963-196">使用類別的欄位來 <xref:Microsoft.AspNetCore.Http.StatusCodes> 指派給 `RedirectStatusCode` 。</span><span class="sxs-lookup"><span data-stu-id="ad963-196">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="ad963-197">將 HTTPS 埠設定為5001。</span><span class="sxs-lookup"><span data-stu-id="ad963-197">Sets the HTTPS port to 5001.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="ad963-198">在生產環境中設定永久重新導向</span><span class="sxs-lookup"><span data-stu-id="ad963-198">Configure permanent redirects in production</span></span>

<span data-ttu-id="ad963-199">中介軟體預設為傳送具有所有重新導向的 [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) 。</span><span class="sxs-lookup"><span data-stu-id="ad963-199">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="ad963-200">如果您想要在應用程式處於非開發環境時傳送永久的重新導向狀態碼，請在非開發環境的條件式檢查中包裝中介軟體選項設定。</span><span class="sxs-lookup"><span data-stu-id="ad963-200">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ad963-201">在 *Startup.cs*中設定服務時：</span><span class="sxs-lookup"><span data-stu-id="ad963-201">When configuring services in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="ad963-202">在 *Startup.cs*中設定服務時：</span><span class="sxs-lookup"><span data-stu-id="ad963-202">When configuring services in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="ad963-203">HTTPS 重新導向中介軟體替代方法</span><span class="sxs-lookup"><span data-stu-id="ad963-203">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="ad963-204">使用 HTTPS 重新導向中介軟體 () 的替代方式， `UseHttpsRedirection` 是使用 URL 重寫中介軟體 (`AddRedirectToHttps`) 。</span><span class="sxs-lookup"><span data-stu-id="ad963-204">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="ad963-205">`AddRedirectToHttps` 也可以在執行重新導向時設定狀態碼和埠。</span><span class="sxs-lookup"><span data-stu-id="ad963-205">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="ad963-206">如需詳細資訊，請參閱 [URL 重寫中介軟體](xref:fundamentals/url-rewriting)。</span><span class="sxs-lookup"><span data-stu-id="ad963-206">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="ad963-207">重新導向至 HTTPS 而不需要額外的重新導向規則時，建議使用 HTTPS 重新導向中介軟體 (`UseHttpsRedirection`) 本主題中所述。</span><span class="sxs-lookup"><span data-stu-id="ad963-207">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="ad963-208">HTTP Strict Transport Security Protocol (HSTS) </span><span class="sxs-lookup"><span data-stu-id="ad963-208">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="ad963-209">根據 [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project)， [HTTP Strict TRANSPORT Security (HSTS) ](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) 是由 web 應用程式透過使用回應標頭所指定的選擇性加入安全性增強功能。</span><span class="sxs-lookup"><span data-stu-id="ad963-209">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="ad963-210">當 [支援 HSTS 的瀏覽器](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) 收到此標頭時：</span><span class="sxs-lookup"><span data-stu-id="ad963-210">When a [browser that supports HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) receives this header:</span></span>

* <span data-ttu-id="ad963-211">瀏覽器會儲存網域的設定，以防止透過 HTTP 傳送任何通訊。</span><span class="sxs-lookup"><span data-stu-id="ad963-211">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="ad963-212">瀏覽器會強制透過 HTTPS 進行所有通訊。</span><span class="sxs-lookup"><span data-stu-id="ad963-212">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="ad963-213">瀏覽器會防止使用者使用未受信任或不正確憑證。</span><span class="sxs-lookup"><span data-stu-id="ad963-213">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="ad963-214">瀏覽器會停用允許使用者暫時信任這類憑證的提示。</span><span class="sxs-lookup"><span data-stu-id="ad963-214">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="ad963-215">由於 HSTS 是由用戶端強制執行，因此有一些限制：</span><span class="sxs-lookup"><span data-stu-id="ad963-215">Because HSTS is enforced by the client, it has some limitations:</span></span>

* <span data-ttu-id="ad963-216">用戶端必須支援 HSTS。</span><span class="sxs-lookup"><span data-stu-id="ad963-216">The client must support HSTS.</span></span>
* <span data-ttu-id="ad963-217">HSTS 至少需要一個成功的 HTTPS 要求才能建立 HSTS 原則。</span><span class="sxs-lookup"><span data-stu-id="ad963-217">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="ad963-218">應用程式必須檢查每個 HTTP 要求，並重新導向或拒絕 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="ad963-218">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="ad963-219">ASP.NET Core 2.1 和更新版本會使用擴充方法來執行 HSTS `UseHsts` 。</span><span class="sxs-lookup"><span data-stu-id="ad963-219">ASP.NET Core 2.1 and later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="ad963-220">`UseHsts`當應用程式不在[開發模式](xref:fundamentals/environments)時，會呼叫下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="ad963-220">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

<span data-ttu-id="ad963-221">`UseHsts` 在開發中不建議使用，因為 HSTS 設定可由瀏覽器高度快取。</span><span class="sxs-lookup"><span data-stu-id="ad963-221">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="ad963-222">預設會 `UseHsts` 排除本機回送位址。</span><span class="sxs-lookup"><span data-stu-id="ad963-222">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="ad963-223">若是第一次執行 HTTPS 的生產環境，請使用其中一種方法將初始[HstsOptions](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*)設定為較小的值。 <xref:System.TimeSpan></span><span class="sxs-lookup"><span data-stu-id="ad963-223">For production environments that are implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="ad963-224">如果您需要將 HTTPS 基礎結構還原為 HTTP，請將值從小時設定為不超過一天。</span><span class="sxs-lookup"><span data-stu-id="ad963-224">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="ad963-225">在您確信 HTTPS 設定的持續性之後，請增加 HSTS `max-age` 值; 常用的值是一年。</span><span class="sxs-lookup"><span data-stu-id="ad963-225">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS `max-age` value; a commonly used value is one year.</span></span>

<span data-ttu-id="ad963-226">下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="ad963-226">The following code:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* <span data-ttu-id="ad963-227">設定標頭的預先載入參數 `Strict-Transport-Security` 。</span><span class="sxs-lookup"><span data-stu-id="ad963-227">Sets the preload parameter of the `Strict-Transport-Security` header.</span></span> <span data-ttu-id="ad963-228">預先載入不是 [RFC HSTS 規格](https://tools.ietf.org/html/rfc6797)的一部分，但是網頁瀏覽器支援在全新安裝時預先載入 HSTS 網站。</span><span class="sxs-lookup"><span data-stu-id="ad963-228">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="ad963-229">如需詳細資訊，請參閱 [https://hstspreload.org/](https://hstspreload.org/)。</span><span class="sxs-lookup"><span data-stu-id="ad963-229">For more information, see [https://hstspreload.org/](https://hstspreload.org/).</span></span>
* <span data-ttu-id="ad963-230">啟用 [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2)，這會將 HSTS 原則套用至主機子域。</span><span class="sxs-lookup"><span data-stu-id="ad963-230">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="ad963-231">將 `max-age` 標頭的參數明確設定 `Strict-Transport-Security` 為60天。</span><span class="sxs-lookup"><span data-stu-id="ad963-231">Explicitly sets the `max-age` parameter of the `Strict-Transport-Security` header to 60 days.</span></span> <span data-ttu-id="ad963-232">如果未設定，則預設為30天。</span><span class="sxs-lookup"><span data-stu-id="ad963-232">If not set, defaults to 30 days.</span></span> <span data-ttu-id="ad963-233">如需詳細資訊，請參閱 [最大壽命](https://tools.ietf.org/html/rfc6797#section-6.1.1)指示詞。</span><span class="sxs-lookup"><span data-stu-id="ad963-233">For more information, see the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1).</span></span>
* <span data-ttu-id="ad963-234">新增 `example.com` 至要排除的主機清單。</span><span class="sxs-lookup"><span data-stu-id="ad963-234">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="ad963-235">`UseHsts` 排除下列回送主機：</span><span class="sxs-lookup"><span data-stu-id="ad963-235">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="ad963-236">`localhost` ： IPv4 回送位址。</span><span class="sxs-lookup"><span data-stu-id="ad963-236">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="ad963-237">`127.0.0.1` ： IPv4 回送位址。</span><span class="sxs-lookup"><span data-stu-id="ad963-237">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="ad963-238">`[::1]` ： IPv6 回送位址。</span><span class="sxs-lookup"><span data-stu-id="ad963-238">`[::1]` : The IPv6 loopback address.</span></span>

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="ad963-239">在建立專案時退出宣告 HTTPS/HSTS</span><span class="sxs-lookup"><span data-stu-id="ad963-239">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="ad963-240">在某些後端服務案例中，連線安全性是在網路公眾面向的邊緣處理的，因此不需要在每個節點上設定連接安全性。</span><span class="sxs-lookup"><span data-stu-id="ad963-240">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="ad963-241">從 Visual Studio 中的範本或從 [dotnet new](/dotnet/core/tools/dotnet-new) 命令產生的 Web 應用程式會啟用 [HTTPS](#require-https) 重新導向和 [HSTS](#http-strict-transport-security-protocol-hsts)。</span><span class="sxs-lookup"><span data-stu-id="ad963-241">Web apps that are generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="ad963-242">針對不需要這些案例的部署，您可以在從範本建立應用程式時退出宣告 HTTPS/HSTS。</span><span class="sxs-lookup"><span data-stu-id="ad963-242">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="ad963-243">退出宣告 HTTPS/HSTS：</span><span class="sxs-lookup"><span data-stu-id="ad963-243">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad963-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad963-244">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="ad963-245">取消核取 [ **針對 HTTPS 進行設定** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="ad963-245">Uncheck the **Configure for HTTPS** check box.</span></span>

::: moniker range=">= aspnetcore-3.0"

![新的 ASP.NET Core Web 應用程式] 對話方塊，顯示未選取的 [設定 HTTPS] 核取方塊。](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![新的 ASP.NET Core Web 應用程式] 對話方塊，顯示未選取的 [設定 HTTPS] 核取方塊。](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="ad963-248">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ad963-248">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="ad963-249">使用 `--no-https` 選項。</span><span class="sxs-lookup"><span data-stu-id="ad963-249">Use the `--no-https` option.</span></span> <span data-ttu-id="ad963-250">例如</span><span class="sxs-lookup"><span data-stu-id="ad963-250">For example</span></span>

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="ad963-251">信任 Windows 和 macOS 上的 ASP.NET Core HTTPS 開發憑證</span><span class="sxs-lookup"><span data-stu-id="ad963-251">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="ad963-252">.NET Core SDK 包含 HTTPS 開發憑證。</span><span class="sxs-lookup"><span data-stu-id="ad963-252">The .NET Core SDK includes an HTTPS development certificate.</span></span> <span data-ttu-id="ad963-253">憑證會在首次執行體驗中安裝。</span><span class="sxs-lookup"><span data-stu-id="ad963-253">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="ad963-254">例如，會 `dotnet --info` 產生下列輸出的變化：</span><span class="sxs-lookup"><span data-stu-id="ad963-254">For example, `dotnet --info` produces a variation of the following output:</span></span>

```
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="ad963-255">安裝 .NET Core SDK 會將 ASP.NET Core HTTPS 開發憑證安裝至本機使用者憑證存放區。</span><span class="sxs-lookup"><span data-stu-id="ad963-255">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="ad963-256">憑證已安裝，但不受信任。</span><span class="sxs-lookup"><span data-stu-id="ad963-256">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="ad963-257">若要信任憑證，請執行一次性步驟來執行 dotnet `dev-certs` 工具：</span><span class="sxs-lookup"><span data-stu-id="ad963-257">To trust the certificate, perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="ad963-258">下列命令會提供 `dev-certs` 工具的說明：</span><span class="sxs-lookup"><span data-stu-id="ad963-258">The following command provides help on the `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="ad963-259">如何設定 Docker 的開發人員憑證</span><span class="sxs-lookup"><span data-stu-id="ad963-259">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="ad963-260">請參閱[這個 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/6199)。</span><span class="sxs-lookup"><span data-stu-id="ad963-260">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6199).</span></span>

<a name="ssl-linux"></a>

## <a name="trust-https-certificate-on-linux"></a><span data-ttu-id="ad963-261">信賴 Linux 上的 HTTPS 憑證</span><span class="sxs-lookup"><span data-stu-id="ad963-261">Trust HTTPS certificate on Linux</span></span>

<!-- Instructions to be updated by engineering team after 5.0 RTM. -->

<span data-ttu-id="ad963-262">如需 Linux 的指示，請參閱散發檔。</span><span class="sxs-lookup"><span data-stu-id="ad963-262">For instructions on Linux, refer to the distribution documentation.</span></span>

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="ad963-263">信任 Windows 子系統 Linux 版的 HTTPS 憑證</span><span class="sxs-lookup"><span data-stu-id="ad963-263">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="ad963-264">Windows 子系統 Linux 版 (WSL) 會產生 HTTPS 自我簽署憑證。若要設定 Windows 憑證存放區以信任 WSL 憑證：</span><span class="sxs-lookup"><span data-stu-id="ad963-264">The Windows Subsystem for Linux (WSL) generates an HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="ad963-265">執行下列命令以匯出 WSL 產生的憑證：</span><span class="sxs-lookup"><span data-stu-id="ad963-265">Run the following command to export the WSL-generated certificate:</span></span>

  ```
  dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>
  ```
* <span data-ttu-id="ad963-266">在 WSL 視窗中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="ad963-266">In a WSL window, run the following command:</span></span>

  ```
    ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" 
    ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx
    dotnet watch run
  ```

  <span data-ttu-id="ad963-267">上述命令會設定環境變數，讓 Linux 使用 Windows 受信任的憑證。</span><span class="sxs-lookup"><span data-stu-id="ad963-267">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="troubleshoot-certificate-problems"></a><span data-ttu-id="ad963-268">針對憑證問題進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="ad963-268">Troubleshoot certificate problems</span></span>

<span data-ttu-id="ad963-269">本節提供 [安裝和信任](#trust)ASP.NET Core HTTPS 開發憑證時的說明，但您仍有不信任憑證的瀏覽器警告。</span><span class="sxs-lookup"><span data-stu-id="ad963-269">This section provides help when the ASP.NET Core HTTPS development certificate has been [installed and trusted](#trust), but you still have browser warnings that the certificate is not trusted.</span></span> <span data-ttu-id="ad963-270">[Kestrel](xref:fundamentals/servers/kestrel)會使用 ASP.NET Core HTTPS 開發憑證。</span><span class="sxs-lookup"><span data-stu-id="ad963-270">The ASP.NET Core HTTPS development certificate is used by [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

### <a name="all-platforms---certificate-not-trusted"></a><span data-ttu-id="ad963-271">所有平臺-憑證不受信任</span><span class="sxs-lookup"><span data-stu-id="ad963-271">All platforms - certificate not trusted</span></span>

<span data-ttu-id="ad963-272">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="ad963-272">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="ad963-273">關閉任何開啟的瀏覽器實例。</span><span class="sxs-lookup"><span data-stu-id="ad963-273">Close any browser instances open.</span></span> <span data-ttu-id="ad963-274">開啟新的瀏覽器視窗以使用應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad963-274">Open a new browser window to app.</span></span> <span data-ttu-id="ad963-275">瀏覽器會快取憑證信任。</span><span class="sxs-lookup"><span data-stu-id="ad963-275">Certificate trust is cached by browsers.</span></span>

<span data-ttu-id="ad963-276">上述命令會解決大部分的瀏覽器信任問題。</span><span class="sxs-lookup"><span data-stu-id="ad963-276">The preceding commands solve most browser trust issues.</span></span> <span data-ttu-id="ad963-277">如果瀏覽器仍不信任憑證，請遵循下列平臺特定的建議。</span><span class="sxs-lookup"><span data-stu-id="ad963-277">If the browser is still not trusting the certificate, follow the platform-specific suggestions that follow.</span></span>

### <a name="docker---certificate-not-trusted"></a><span data-ttu-id="ad963-278">Docker-憑證不受信任</span><span class="sxs-lookup"><span data-stu-id="ad963-278">Docker - certificate not trusted</span></span>

* <span data-ttu-id="ad963-279">刪除 *C:\Users \{ USER} \AppData\Roaming\ASP.NET\Https* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="ad963-279">Delete the *C:\Users\{USER}\AppData\Roaming\ASP.NET\Https* folder.</span></span>
* <span data-ttu-id="ad963-280">清除方案。</span><span class="sxs-lookup"><span data-stu-id="ad963-280">Clean the solution.</span></span> <span data-ttu-id="ad963-281">刪除 [bin]\*\* 和 [obj]\*\* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="ad963-281">Delete the *bin* and *obj* folders.</span></span>
* <span data-ttu-id="ad963-282">重新開機開發工具。</span><span class="sxs-lookup"><span data-stu-id="ad963-282">Restart the development tool.</span></span> <span data-ttu-id="ad963-283">例如，Visual Studio、Visual Studio Code 或 Visual Studio for Mac。</span><span class="sxs-lookup"><span data-stu-id="ad963-283">For example, Visual Studio, Visual Studio Code, or Visual Studio for Mac.</span></span>

### <a name="windows---certificate-not-trusted"></a><span data-ttu-id="ad963-284">Windows-憑證不受信任</span><span class="sxs-lookup"><span data-stu-id="ad963-284">Windows - certificate not trusted</span></span>

* <span data-ttu-id="ad963-285">檢查證書存儲中的憑證。</span><span class="sxs-lookup"><span data-stu-id="ad963-285">Check the certificates in the certificate store.</span></span> <span data-ttu-id="ad963-286">`localhost` `ASP.NET Core HTTPS development certificate` 在和下應該有易記名稱的憑證 `Current User > Personal > Certificates``Current User > Trusted root certification authorities > Certificates`</span><span class="sxs-lookup"><span data-stu-id="ad963-286">There should be a `localhost` certificate with the `ASP.NET Core HTTPS development certificate` friendly name both under `Current User > Personal > Certificates` and `Current User > Trusted root certification authorities > Certificates`</span></span>
* <span data-ttu-id="ad963-287">從個人和受信任的根憑證授權單位移除所有找到的憑證。</span><span class="sxs-lookup"><span data-stu-id="ad963-287">Remove all the found certificates from both Personal and Trusted root certification authorities.</span></span> <span data-ttu-id="ad963-288">請勿 **移除 IIS Express** localhost 憑證。</span><span class="sxs-lookup"><span data-stu-id="ad963-288">Do **not** remove the IIS Express localhost certificate.</span></span>
* <span data-ttu-id="ad963-289">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="ad963-289">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="ad963-290">關閉任何開啟的瀏覽器實例。</span><span class="sxs-lookup"><span data-stu-id="ad963-290">Close any browser instances open.</span></span> <span data-ttu-id="ad963-291">開啟新的瀏覽器視窗以使用應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad963-291">Open a new browser window to app.</span></span>

### <a name="os-x---certificate-not-trusted"></a><span data-ttu-id="ad963-292">OS X-憑證不受信任</span><span class="sxs-lookup"><span data-stu-id="ad963-292">OS X - certificate not trusted</span></span>

* <span data-ttu-id="ad963-293">開啟 [KeyChain 存取]。</span><span class="sxs-lookup"><span data-stu-id="ad963-293">Open KeyChain Access.</span></span>
* <span data-ttu-id="ad963-294">選取 [系統 keychain]。</span><span class="sxs-lookup"><span data-stu-id="ad963-294">Select the System keychain.</span></span>
* <span data-ttu-id="ad963-295">檢查 localhost 憑證是否存在。</span><span class="sxs-lookup"><span data-stu-id="ad963-295">Check for the presence of a localhost certificate.</span></span>
* <span data-ttu-id="ad963-296">檢查其是否包含 `+` 圖示上的符號，以指出它是針對所有使用者所信任。</span><span class="sxs-lookup"><span data-stu-id="ad963-296">Check that it contains a `+` symbol on the icon to indicate it's trusted for all users.</span></span>
* <span data-ttu-id="ad963-297">從系統 keychain 移除憑證。</span><span class="sxs-lookup"><span data-stu-id="ad963-297">Remove the certificate from the system keychain.</span></span>
* <span data-ttu-id="ad963-298">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="ad963-298">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="ad963-299">關閉任何開啟的瀏覽器實例。</span><span class="sxs-lookup"><span data-stu-id="ad963-299">Close any browser instances open.</span></span> <span data-ttu-id="ad963-300">開啟新的瀏覽器視窗以使用應用程式。</span><span class="sxs-lookup"><span data-stu-id="ad963-300">Open a new browser window to app.</span></span>

<span data-ttu-id="ad963-301">請參閱 [使用 IIS Express (dotnet/AspNetCore #16892) ](https://github.com/dotnet/AspNetCore/issues/16892) 進行 Visual Studio 的憑證問題疑難排解的 HTTPS 錯誤。</span><span class="sxs-lookup"><span data-stu-id="ad963-301">See [HTTPS Error using IIS Express (dotnet/AspNetCore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) for troubleshooting certificate issues with Visual Studio.</span></span>

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a><span data-ttu-id="ad963-302">IIS Express 與 Visual Studio 搭配使用的 SSL 憑證</span><span class="sxs-lookup"><span data-stu-id="ad963-302">IIS Express SSL certificate used with Visual Studio</span></span>

<span data-ttu-id="ad963-303">若要修正 IIS Express 憑證的問題，請選取 Visual Studio 安裝程式中的 [ **修復** ]。</span><span class="sxs-lookup"><span data-stu-id="ad963-303">To fix problems with the IIS Express certificate, select **Repair** from the Visual Studio installer.</span></span> <span data-ttu-id="ad963-304">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/aspnetcore/issues/16892)。</span><span class="sxs-lookup"><span data-stu-id="ad963-304">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/16892).</span></span>

## <a name="additional-information"></a><span data-ttu-id="ad963-305">其他資訊</span><span class="sxs-lookup"><span data-stu-id="ad963-305">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="ad963-306">使用 Apache： HTTPS 設定的 Linux 上的主機 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ad963-306">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="ad963-307">使用 Nginx 的 Linux 上的主機 ASP.NET Core： HTTPS 設定</span><span class="sxs-lookup"><span data-stu-id="ad963-307">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="ad963-308">如何在 IIS 上設定 SSL</span><span class="sxs-lookup"><span data-stu-id="ad963-308">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="ad963-309">OWASP HSTS 瀏覽器支援</span><span class="sxs-lookup"><span data-stu-id="ad963-309">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)