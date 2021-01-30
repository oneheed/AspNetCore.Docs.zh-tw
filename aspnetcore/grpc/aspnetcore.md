---
title: 搭配 ASP.NET Core 的 gRPC 服務
author: juntaoluo
description: 瞭解使用 ASP.NET Core 撰寫 gRPC 服務時的基本概念。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 01/29/2021
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
uid: grpc/aspnetcore
ms.openlocfilehash: 57edfa31079cb3fca6e9e8d0fa55bcbb8bbfefca
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057472"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="10c07-103">搭配 ASP.NET Core 的 gRPC 服務</span><span class="sxs-lookup"><span data-stu-id="10c07-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="10c07-104">本檔說明如何使用 ASP.NET Core 開始使用 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="10c07-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a><span data-ttu-id="10c07-105">必要條件</span><span class="sxs-lookup"><span data-stu-id="10c07-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="10c07-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10c07-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="10c07-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="10c07-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="10c07-108">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="10c07-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="10c07-109">開始在 ASP.NET Core 中使用 gRPC 服務</span><span class="sxs-lookup"><span data-stu-id="10c07-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="10c07-110">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="10c07-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="10c07-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10c07-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="10c07-112">如需如何建立 gRPC 專案的詳細指示，請參閱 [開始使用 gRPC services](xref:tutorials/grpc/grpc-start) 。</span><span class="sxs-lookup"><span data-stu-id="10c07-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="10c07-113">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="10c07-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="10c07-114">從命令列執行 `dotnet new grpc -o GrpcGreeter`。</span><span class="sxs-lookup"><span data-stu-id="10c07-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="10c07-115">將 gRPC 服務新增至 ASP.NET Core 應用程式</span><span class="sxs-lookup"><span data-stu-id="10c07-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="10c07-116">gRPC 需要 [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) 套件。</span><span class="sxs-lookup"><span data-stu-id="10c07-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="10c07-117">設定 gRPC</span><span class="sxs-lookup"><span data-stu-id="10c07-117">Configure gRPC</span></span>

<span data-ttu-id="10c07-118">在 *Startup.cs* 中：</span><span class="sxs-lookup"><span data-stu-id="10c07-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="10c07-119">使用方法可啟用 gRPC `AddGrpc` 。</span><span class="sxs-lookup"><span data-stu-id="10c07-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="10c07-120">每個 gRPC 服務都會透過方法新增至路由管線 `MapGrpcService` 。</span><span class="sxs-lookup"><span data-stu-id="10c07-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="10c07-121">ASP.NET Core 中介軟體和功能共用路由管線，因此可以將應用程式設定為可提供額外的要求處理常式。</span><span class="sxs-lookup"><span data-stu-id="10c07-121">ASP.NET Core middleware and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="10c07-122">其他的要求處理常式（例如 MVC 控制器）會與已設定的 gRPC 服務平行運作。</span><span class="sxs-lookup"><span data-stu-id="10c07-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="server-options"></a><span data-ttu-id="10c07-123">伺服器選項</span><span class="sxs-lookup"><span data-stu-id="10c07-123">Server options</span></span>

<span data-ttu-id="10c07-124">gRPC 服務可以由所有內建的 ASP.NET Core 伺服器主控。</span><span class="sxs-lookup"><span data-stu-id="10c07-124">gRPC services can be hosted by all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="10c07-125">Kestrel</span><span class="sxs-lookup"><span data-stu-id="10c07-125">Kestrel</span></span>
> * <span data-ttu-id="10c07-126">TestServer</span><span class="sxs-lookup"><span data-stu-id="10c07-126">TestServer</span></span>
> * <span data-ttu-id="10c07-127">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="10c07-127">IIS&dagger;</span></span>
> * <span data-ttu-id="10c07-128">HTTP.sys&dagger;</span><span class="sxs-lookup"><span data-stu-id="10c07-128">HTTP.sys&dagger;</span></span>

<span data-ttu-id="10c07-129">&dagger;IIS 和 HTTP.sys 需要 .NET 5 和 Windows 10 組建20241或更新版本。</span><span class="sxs-lookup"><span data-stu-id="10c07-129">&dagger;IIS and HTTP.sys require .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="10c07-130">如需為 ASP.NET Core 應用程式選擇正確伺服器的詳細資訊，請參閱 <xref:fundamentals/servers/index> 。</span><span class="sxs-lookup"><span data-stu-id="10c07-130">For more information about choosing the right server for an ASP.NET Core app, see <xref:fundamentals/servers/index>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="10c07-131">Kestrel</span><span class="sxs-lookup"><span data-stu-id="10c07-131">Kestrel</span></span>

<span data-ttu-id="10c07-132">[Kestrel](xref:fundamentals/servers/kestrel) 是 ASP.NET Core 的跨平臺 web 伺服器。</span><span class="sxs-lookup"><span data-stu-id="10c07-132">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="10c07-133">Kestrel 可提供最佳的效能和記憶體使用率，但它沒有一些 HTTP.sys 的 advanced 功能，例如埠共用。</span><span class="sxs-lookup"><span data-stu-id="10c07-133">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="10c07-134">Kestrel gRPC 端點：</span><span class="sxs-lookup"><span data-stu-id="10c07-134">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="10c07-135">需要 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="10c07-135">Require HTTP/2.</span></span>
* <span data-ttu-id="10c07-136">應透過 [傳輸層安全性 (TLS) ](https://tools.ietf.org/html/rfc5246)來保護。</span><span class="sxs-lookup"><span data-stu-id="10c07-136">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="10c07-137">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="10c07-137">HTTP/2</span></span>

<span data-ttu-id="10c07-138">gRPC 需要 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="10c07-138">gRPC requires HTTP/2.</span></span> <span data-ttu-id="10c07-139">ASP.NET Core 的 gRPC 會驗證[HttpRequest。](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) `HTTP/2`</span><span class="sxs-lookup"><span data-stu-id="10c07-139">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="10c07-140">Kestrel 支援大部分新式作業系統上的 [HTTP/2](xref:fundamentals/servers/kestrel/http2) 。</span><span class="sxs-lookup"><span data-stu-id="10c07-140">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel/http2) on most modern operating systems.</span></span> <span data-ttu-id="10c07-141">預設會將 Kestrel 端點設定為支援 HTTP/1.1 和 HTTP/2 連接。</span><span class="sxs-lookup"><span data-stu-id="10c07-141">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="10c07-142">TLS</span><span class="sxs-lookup"><span data-stu-id="10c07-142">TLS</span></span>

<span data-ttu-id="10c07-143">用於 gRPC 的 Kestrel 端點應使用 TLS 來保護。</span><span class="sxs-lookup"><span data-stu-id="10c07-143">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="10c07-144">在開發期間，會在 `https://localhost:5001` ASP.NET Core 開發憑證存在時自動建立以 TLS 保護的端點。</span><span class="sxs-lookup"><span data-stu-id="10c07-144">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="10c07-145">不需要組態。</span><span class="sxs-lookup"><span data-stu-id="10c07-145">No configuration is required.</span></span> <span data-ttu-id="10c07-146">`https`前置詞會驗證 Kestrel 端點是否使用 TLS。</span><span class="sxs-lookup"><span data-stu-id="10c07-146">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="10c07-147">在生產環境中，必須明確設定 TLS。</span><span class="sxs-lookup"><span data-stu-id="10c07-147">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="10c07-148">下列 *appsettings.json* 範例會提供以 TLS 保護的 HTTP/2 端點：</span><span class="sxs-lookup"><span data-stu-id="10c07-148">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="10c07-149">或者，您也可以在 *Program.cs* 中設定 Kestrel 端點：</span><span class="sxs-lookup"><span data-stu-id="10c07-149">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="10c07-150">通訊協定交涉</span><span class="sxs-lookup"><span data-stu-id="10c07-150">Protocol negotiation</span></span>

<span data-ttu-id="10c07-151">TLS 是用來保護通訊安全。</span><span class="sxs-lookup"><span data-stu-id="10c07-151">TLS is used for more than securing communication.</span></span> <span data-ttu-id="10c07-152">當端點支援多個通訊協定時，會使用 TLS [應用層通訊協定協商 (ALPN) ](https://tools.ietf.org/html/rfc7301#section-3) 交握來協調用戶端與伺服器之間的連接通訊協定。</span><span class="sxs-lookup"><span data-stu-id="10c07-152">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="10c07-153">此協商會判斷連接使用的是 HTTP/1.1 或 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="10c07-153">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="10c07-154">如果 HTTP/2 端點設定為沒有 TLS，則端點的 [>listenoptions](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) 必須設定為 `HttpProtocols.Http2` 。</span><span class="sxs-lookup"><span data-stu-id="10c07-154">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="10c07-155">具有多個通訊協定的端點 (例如， `HttpProtocols.Http1AndHttp2`) 無法在沒有 TLS 的情況下使用，因為沒有任何協調。</span><span class="sxs-lookup"><span data-stu-id="10c07-155">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="10c07-156">所有不安全端點的連接都會預設為 HTTP/1.1，且 gRPC 呼叫會失敗。</span><span class="sxs-lookup"><span data-stu-id="10c07-156">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="10c07-157">如需有關使用 Kestrel 啟用 HTTP/2 和 TLS 的詳細資訊，請參閱 [Kestrel 端點](xref:fundamentals/servers/kestrel/endpoints)設定。</span><span class="sxs-lookup"><span data-stu-id="10c07-157">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

> [!NOTE]
> <span data-ttu-id="10c07-158">macOS 不支援具有 TLS 的 ASP.NET Core gRPC。</span><span class="sxs-lookup"><span data-stu-id="10c07-158">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="10c07-159">您需要額外的組態才能在 macOS 上成功執行 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="10c07-159">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="10c07-160">如需詳細資訊，請參閱[無法在 macOS 上啟動 ASP.NET Core gRPC 應用程式](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。</span><span class="sxs-lookup"><span data-stu-id="10c07-160">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="iis"></a><span data-ttu-id="10c07-161">IIS</span><span class="sxs-lookup"><span data-stu-id="10c07-161">IIS</span></span>

<span data-ttu-id="10c07-162">[Internet Information Services (IIS) ](xref:host-and-deploy/iis/index) 是可裝載 web 應用程式（包括 ASP.NET Core）的彈性、安全且可管理的 web 伺服器。</span><span class="sxs-lookup"><span data-stu-id="10c07-162">[Internet Information Services (IIS)](xref:host-and-deploy/iis/index) is a flexible, secure and manageable Web Server for hosting web apps, including ASP.NET Core.</span></span> <span data-ttu-id="10c07-163">需要 .NET 5 和 Windows 10 組建20241或更新版本，才能使用 IIS 裝載 gRPC services。</span><span class="sxs-lookup"><span data-stu-id="10c07-163">.NET 5 and Windows 10 Build 20241 or later are required to host gRPC services with IIS.</span></span>

<span data-ttu-id="10c07-164">IIS 必須設定為使用 TLS 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="10c07-164">IIS must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="10c07-165">如需詳細資訊，請參閱<xref:host-and-deploy/iis/protocols>。</span><span class="sxs-lookup"><span data-stu-id="10c07-165">For more information, see <xref:host-and-deploy/iis/protocols>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="10c07-166">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10c07-166">HTTP.sys</span></span>

<span data-ttu-id="10c07-167">[HTTP.sys](xref:fundamentals/servers/httpsys) 是只在 Windows 上執行的 ASP.NET Core 網頁伺服器。</span><span class="sxs-lookup"><span data-stu-id="10c07-167">[HTTP.sys](xref:fundamentals/servers/httpsys) is a web server for ASP.NET Core that only runs on Windows.</span></span> <span data-ttu-id="10c07-168">需要 .NET 5 和 Windows 10 組建20241或更新版本，才能裝載具有 HTTP.sys 的 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="10c07-168">.NET 5 and Windows 10 Build 20241 or later are required to host gRPC services with HTTP.sys.</span></span>

<span data-ttu-id="10c07-169">HTTP.sys 必須設定為使用 TLS 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="10c07-169">HTTP.sys must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="10c07-170">如需詳細資訊，請參閱  [HTTP.sys 網頁伺服器 HTTP/2 支援](xref:fundamentals/servers/httpsys#http2-support)。</span><span class="sxs-lookup"><span data-stu-id="10c07-170">For more information, see  [HTTP.sys web server HTTP/2 support](xref:fundamentals/servers/httpsys#http2-support).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="10c07-171">Kestrel</span><span class="sxs-lookup"><span data-stu-id="10c07-171">Kestrel</span></span>

<span data-ttu-id="10c07-172">[Kestrel](xref:fundamentals/servers/kestrel) 是 ASP.NET Core 的跨平臺 web 伺服器。</span><span class="sxs-lookup"><span data-stu-id="10c07-172">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="10c07-173">Kestrel 可提供最佳的效能和記憶體使用率，但它沒有一些 HTTP.sys 的 advanced 功能，例如埠共用。</span><span class="sxs-lookup"><span data-stu-id="10c07-173">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="10c07-174">Kestrel gRPC 端點：</span><span class="sxs-lookup"><span data-stu-id="10c07-174">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="10c07-175">需要 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="10c07-175">Require HTTP/2.</span></span>
* <span data-ttu-id="10c07-176">應透過 [傳輸層安全性 (TLS) ](https://tools.ietf.org/html/rfc5246)來保護。</span><span class="sxs-lookup"><span data-stu-id="10c07-176">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="10c07-177">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="10c07-177">HTTP/2</span></span>

<span data-ttu-id="10c07-178">gRPC 需要 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="10c07-178">gRPC requires HTTP/2.</span></span> <span data-ttu-id="10c07-179">ASP.NET Core 的 gRPC 會驗證[HttpRequest。](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) `HTTP/2`</span><span class="sxs-lookup"><span data-stu-id="10c07-179">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="10c07-180">Kestrel 支援大部分新式作業系統上的 [HTTP/2](xref:fundamentals/servers/kestrel#http2-support) 。</span><span class="sxs-lookup"><span data-stu-id="10c07-180">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="10c07-181">預設會將 Kestrel 端點設定為支援 HTTP/1.1 和 HTTP/2 連接。</span><span class="sxs-lookup"><span data-stu-id="10c07-181">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="10c07-182">TLS</span><span class="sxs-lookup"><span data-stu-id="10c07-182">TLS</span></span>

<span data-ttu-id="10c07-183">用於 gRPC 的 Kestrel 端點應使用 TLS 來保護。</span><span class="sxs-lookup"><span data-stu-id="10c07-183">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="10c07-184">在開發期間，會在 `https://localhost:5001` ASP.NET Core 開發憑證存在時自動建立以 TLS 保護的端點。</span><span class="sxs-lookup"><span data-stu-id="10c07-184">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="10c07-185">不需要組態。</span><span class="sxs-lookup"><span data-stu-id="10c07-185">No configuration is required.</span></span> <span data-ttu-id="10c07-186">`https`前置詞會驗證 Kestrel 端點是否使用 TLS。</span><span class="sxs-lookup"><span data-stu-id="10c07-186">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="10c07-187">在生產環境中，必須明確設定 TLS。</span><span class="sxs-lookup"><span data-stu-id="10c07-187">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="10c07-188">下列 *appsettings.json* 範例會提供以 TLS 保護的 HTTP/2 端點：</span><span class="sxs-lookup"><span data-stu-id="10c07-188">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="10c07-189">或者，您也可以在 *Program.cs* 中設定 Kestrel 端點：</span><span class="sxs-lookup"><span data-stu-id="10c07-189">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="10c07-190">通訊協定交涉</span><span class="sxs-lookup"><span data-stu-id="10c07-190">Protocol negotiation</span></span>

<span data-ttu-id="10c07-191">TLS 是用來保護通訊安全。</span><span class="sxs-lookup"><span data-stu-id="10c07-191">TLS is used for more than securing communication.</span></span> <span data-ttu-id="10c07-192">當端點支援多個通訊協定時，會使用 TLS [應用層通訊協定協商 (ALPN) ](https://tools.ietf.org/html/rfc7301#section-3) 交握來協調用戶端與伺服器之間的連接通訊協定。</span><span class="sxs-lookup"><span data-stu-id="10c07-192">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="10c07-193">此協商會判斷連接使用的是 HTTP/1.1 或 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="10c07-193">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="10c07-194">如果 HTTP/2 端點設定為沒有 TLS，則端點的 [>listenoptions](xref:fundamentals/servers/kestrel#listenoptionsprotocols) 必須設定為 `HttpProtocols.Http2` 。</span><span class="sxs-lookup"><span data-stu-id="10c07-194">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="10c07-195">具有多個通訊協定的端點 (例如， `HttpProtocols.Http1AndHttp2`) 無法在沒有 TLS 的情況下使用，因為沒有任何協調。</span><span class="sxs-lookup"><span data-stu-id="10c07-195">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="10c07-196">所有不安全端點的連接都會預設為 HTTP/1.1，且 gRPC 呼叫會失敗。</span><span class="sxs-lookup"><span data-stu-id="10c07-196">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="10c07-197">如需有關使用 Kestrel 啟用 HTTP/2 和 TLS 的詳細資訊，請參閱 [Kestrel 端點](xref:fundamentals/servers/kestrel#endpoint-configuration)設定。</span><span class="sxs-lookup"><span data-stu-id="10c07-197">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="10c07-198">macOS 不支援具有 TLS 的 ASP.NET Core gRPC。</span><span class="sxs-lookup"><span data-stu-id="10c07-198">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="10c07-199">您需要額外的組態才能在 macOS 上成功執行 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="10c07-199">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="10c07-200">如需詳細資訊，請參閱[無法在 macOS 上啟動 ASP.NET Core gRPC 應用程式](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。</span><span class="sxs-lookup"><span data-stu-id="10c07-200">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="10c07-201">與 ASP.NET Core Api 整合</span><span class="sxs-lookup"><span data-stu-id="10c07-201">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="10c07-202">gRPC services 具有 ASP.NET Core 功能的完整存取權，例如相依性 [插入](xref:fundamentals/dependency-injection) (DI) 和 [記錄](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="10c07-202">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="10c07-203">例如，服務執行可透過此函式從 DI 容器解析記錄器服務：</span><span class="sxs-lookup"><span data-stu-id="10c07-203">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="10c07-204">根據預設，gRPC 服務執行可以使用任何存留期來解析其他 DI 服務 (單一、範圍或暫時性) 。</span><span class="sxs-lookup"><span data-stu-id="10c07-204">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="10c07-205">解析 gRPC 方法中的 HttpCoNtext</span><span class="sxs-lookup"><span data-stu-id="10c07-205">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="10c07-206">GRPC API 可讓您存取某些 HTTP/2 訊息資料，例如方法、主機、標頭和結尾。</span><span class="sxs-lookup"><span data-stu-id="10c07-206">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="10c07-207">存取是透過 `ServerCallContext` 傳遞給每個 gRPC 方法的引數：</span><span class="sxs-lookup"><span data-stu-id="10c07-207">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="10c07-208">`ServerCallContext` 未提供 `HttpContext` 所有 ASP.NET api 的完整存取權。</span><span class="sxs-lookup"><span data-stu-id="10c07-208">`ServerCallContext` doesn't provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="10c07-209">`GetHttpContext`擴充方法會提供完整的存取權，以 `HttpContext` 代表 ASP.NET api 中的基礎 HTTP/2 訊息：</span><span class="sxs-lookup"><span data-stu-id="10c07-209">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a><span data-ttu-id="10c07-210">其他資源</span><span class="sxs-lookup"><span data-stu-id="10c07-210">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
