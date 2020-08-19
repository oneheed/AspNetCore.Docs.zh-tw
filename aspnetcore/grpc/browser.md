---
title: 在瀏覽器應用程式中使用 gRPC
author: jamesnk
description: 瞭解如何在 ASP.NET Core 上設定 gRPC services，以便從使用 gRPC 的瀏覽器應用程式呼叫。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/30/2020
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
uid: grpc/browser
ms.openlocfilehash: 8d1f761731ab3840d009eba1ff5316808bafec40
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634406"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="51da4-103">在瀏覽器應用程式中使用 gRPC</span><span class="sxs-lookup"><span data-stu-id="51da4-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="51da4-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="51da4-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

 <span data-ttu-id="51da4-105">瞭解如何使用 [GRPC Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) 通訊協定，將現有的 ASP.NET Core gRPC 服務設定為可從瀏覽器應用程式呼叫。</span><span class="sxs-lookup"><span data-stu-id="51da4-105">Learn how to configure an existing ASP.NET Core gRPC service to be callable from browser apps, using the [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) protocol.</span></span> <span data-ttu-id="51da4-106">gRPC Web 可讓瀏覽器 JavaScript 和 Blazor 應用程式呼叫 gRPC services。</span><span class="sxs-lookup"><span data-stu-id="51da4-106">gRPC-Web allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="51da4-107">您無法從瀏覽器型應用程式呼叫 HTTP/2 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="51da4-107">It's not possible to call an HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="51da4-108">ASP.NET Core 中裝載的 gRPC 服務可以設定為支援 gRPC Web 和 HTTP/2 gRPC。</span><span class="sxs-lookup"><span data-stu-id="51da4-108">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span>


<span data-ttu-id="51da4-109">如需將 gRPC 服務新增至現有 ASP.NET Core 應用程式的指示，請參閱 [將 gRPC 服務新增至 ASP.NET Core 應用程式](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app)。</span><span class="sxs-lookup"><span data-stu-id="51da4-109">For instructions on adding a gRPC service to an existing ASP.NET Core app, see [Add gRPC services to an ASP.NET Core app](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span></span>

<span data-ttu-id="51da4-110">如需建立 gRPC 專案的指示，請參閱 <xref:tutorials/grpc/grpc-start> 。</span><span class="sxs-lookup"><span data-stu-id="51da4-110">For instructions on creating a gRPC project, see <xref:tutorials/grpc/grpc-start>.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="51da4-111">gRPC-Web in ASP.NET Core 與 Envoy</span><span class="sxs-lookup"><span data-stu-id="51da4-111">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="51da4-112">有兩個選項可讓您選擇如何將 gRPC 新增至 ASP.NET Core 應用程式：</span><span class="sxs-lookup"><span data-stu-id="51da4-112">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="51da4-113">在 ASP.NET Core 中支援 gRPC-Web 與 gRPC HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="51da4-113">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="51da4-114">此選項會使用封裝所提供的中介軟體 `Grpc.AspNetCore.Web` 。</span><span class="sxs-lookup"><span data-stu-id="51da4-114">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="51da4-115">使用 [Envoy proxy 的](https://www.envoyproxy.io/) gRPC web 支援將 gRPC 轉譯成 gRPC HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="51da4-115">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="51da4-116">接著會將已轉譯的呼叫轉送至 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="51da4-116">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="51da4-117">每種方法都有優缺點。</span><span class="sxs-lookup"><span data-stu-id="51da4-117">There are pros and cons to each approach.</span></span> <span data-ttu-id="51da4-118">如果應用程式的環境已經使用 Envoy 作為 proxy，也可以使用 Envoy 來提供 gRPC Web 支援。</span><span class="sxs-lookup"><span data-stu-id="51da4-118">If an app's environment is already using Envoy as a proxy, it might make sense to also use Envoy to provide gRPC-Web support.</span></span> <span data-ttu-id="51da4-119">針對僅需要 ASP.NET Core 的 gRPC Web 基本解決方案， `Grpc.AspNetCore.Web` 是不錯的選擇。</span><span class="sxs-lookup"><span data-stu-id="51da4-119">For a basic solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="51da4-120">在 ASP.NET Core 中設定 gRPC Web</span><span class="sxs-lookup"><span data-stu-id="51da4-120">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="51da4-121">ASP.NET Core 中裝載的 gRPC 服務可以設定為支援 gRPC Web 和 HTTP/2 gRPC。</span><span class="sxs-lookup"><span data-stu-id="51da4-121">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="51da4-122">gRPC-Web 不需要對服務進行任何變更。</span><span class="sxs-lookup"><span data-stu-id="51da4-122">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="51da4-123">唯一的修改是啟動設定。</span><span class="sxs-lookup"><span data-stu-id="51da4-123">The only modification is startup configuration.</span></span>

<span data-ttu-id="51da4-124">若要使用 ASP.NET Core gRPC 服務來啟用 gRPC-Web：</span><span class="sxs-lookup"><span data-stu-id="51da4-124">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="51da4-125">將參考新增至 [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) 封裝。</span><span class="sxs-lookup"><span data-stu-id="51da4-125">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="51da4-126">將應用程式新增至 Startup.cs，以將應用程式設定為使用 gRPC-Web `UseGrpcWeb` `EnableGrpcWeb` ： *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="51da4-126">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="51da4-127">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="51da4-127">The preceding code:</span></span>

* <span data-ttu-id="51da4-128">新增 gRPC-Web 中介軟體、 `UseGrpcWeb` 路由之後和端點之前。</span><span class="sxs-lookup"><span data-stu-id="51da4-128">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="51da4-129">指定 `endpoints.MapGrpcService<GreeterService>()` 方法支援 GRPC Web with `EnableGrpcWeb` 。</span><span class="sxs-lookup"><span data-stu-id="51da4-129">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="51da4-130">或者，您可以設定 gRPC Web 中介軟體，讓所有服務預設都支援 gRPC-Web，而 `EnableGrpcWeb` 不需要。</span><span class="sxs-lookup"><span data-stu-id="51da4-130">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="51da4-131">指定 `new GrpcWebOptions { DefaultEnabled = true }` 加入中介軟體的時間。</span><span class="sxs-lookup"><span data-stu-id="51da4-131">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="51da4-132">有一個已知的問題，會導致 gRPC 在 .NET Core 3.x 中 [由 Http.sys](xref:fundamentals/servers/httpsys) 裝載時失敗。</span><span class="sxs-lookup"><span data-stu-id="51da4-132">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="51da4-133">您可以從 [這裡](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)取得 Http.sys 的 gRPC Web 工作使用方法。</span><span class="sxs-lookup"><span data-stu-id="51da4-133">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="51da4-134">gRPC-Web 和 CORS</span><span class="sxs-lookup"><span data-stu-id="51da4-134">gRPC-Web and CORS</span></span>

<span data-ttu-id="51da4-135">瀏覽器安全性可防止網頁提出要求，而不是與提供網頁的不同網域。</span><span class="sxs-lookup"><span data-stu-id="51da4-135">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="51da4-136">這種限制適用于使用瀏覽器應用程式進行 gRPC 對 Web 的呼叫。</span><span class="sxs-lookup"><span data-stu-id="51da4-136">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="51da4-137">例如，由所提供的瀏覽器應用程式被 `https://www.contoso.com` 封鎖，無法呼叫裝載于的 GRPC Web 服務 `https://services.contoso.com` 。</span><span class="sxs-lookup"><span data-stu-id="51da4-137">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="51da4-138">跨原始資源分享 (CORS) 可用於放寬這種限制。</span><span class="sxs-lookup"><span data-stu-id="51da4-138">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="51da4-139">若要允許瀏覽器應用程式進行跨原始 gRPC Web 呼叫，請 [在 ASP.NET Core 中設定 CORS](xref:security/cors)。</span><span class="sxs-lookup"><span data-stu-id="51da4-139">To allow a browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="51da4-140">使用內建 CORS 支援，並使用來公開 gRPC 特定的標頭 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A> 。</span><span class="sxs-lookup"><span data-stu-id="51da4-140">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="51da4-141">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="51da4-141">The preceding code:</span></span>

* <span data-ttu-id="51da4-142">呼叫 `AddCors` 以新增 cors 服務，並設定可公開 gRPC 特定標頭的 cors 原則。</span><span class="sxs-lookup"><span data-stu-id="51da4-142">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="51da4-143">`UseCors`在路由之後和端點之前新增 CORS 中介軟體的呼叫。</span><span class="sxs-lookup"><span data-stu-id="51da4-143">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="51da4-144">指定 `endpoints.MapGrpcService<GreeterService>()` 使用支援 CORS 的方法 `RequiresCors` 。</span><span class="sxs-lookup"><span data-stu-id="51da4-144">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

### <a name="grpc-web-and-streaming"></a><span data-ttu-id="51da4-145">gRPC-Web 和串流</span><span class="sxs-lookup"><span data-stu-id="51da4-145">gRPC-Web and streaming</span></span>

<span data-ttu-id="51da4-146">傳統的 gRPC over HTTP/2 支援所有方向的串流。</span><span class="sxs-lookup"><span data-stu-id="51da4-146">Traditional gRPC over HTTP/2 supports streaming in all directions.</span></span> <span data-ttu-id="51da4-147">gRPC-Web 提供有限的串流支援：</span><span class="sxs-lookup"><span data-stu-id="51da4-147">gRPC-Web offers limited support for streaming:</span></span>

* <span data-ttu-id="51da4-148">gRPC-網頁瀏覽器用戶端不支援呼叫用戶端串流和雙向串流方法。</span><span class="sxs-lookup"><span data-stu-id="51da4-148">gRPC-Web browser clients don't support calling client streaming and bidirectional streaming methods.</span></span>
* <span data-ttu-id="51da4-149">ASP.NET Core 裝載于 Azure App Service 上的 gRPC 服務，而且 IIS 不支援雙向串流。</span><span class="sxs-lookup"><span data-stu-id="51da4-149">ASP.NET Core gRPC services hosted on Azure App Service and IIS don't support bidirectional streaming.</span></span>

<span data-ttu-id="51da4-150">使用 gRPC Web 時，我們只建議使用一元方法和伺服器串流方法。</span><span class="sxs-lookup"><span data-stu-id="51da4-150">When using gRPC-Web, we only recommend the use of unary methods and server streaming methods.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="51da4-151">從瀏覽器呼叫 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="51da4-151">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="51da4-152">瀏覽器應用程式可以使用 gRPC-Web 來呼叫 gRPC services。</span><span class="sxs-lookup"><span data-stu-id="51da4-152">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="51da4-153">從瀏覽器呼叫 gRPC 服務與 gRPC 時，有一些需求和限制：</span><span class="sxs-lookup"><span data-stu-id="51da4-153">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="51da4-154">伺服器必須設定為支援 gRPC Web。</span><span class="sxs-lookup"><span data-stu-id="51da4-154">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="51da4-155">不支援用戶端串流和雙向串流呼叫。</span><span class="sxs-lookup"><span data-stu-id="51da4-155">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="51da4-156">支援伺服器串流。</span><span class="sxs-lookup"><span data-stu-id="51da4-156">Server streaming is supported.</span></span>
* <span data-ttu-id="51da4-157">在不同的網域上呼叫 gRPC 服務需要在伺服器上設定 [CORS](xref:security/cors) 。</span><span class="sxs-lookup"><span data-stu-id="51da4-157">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="51da4-158">JavaScript gRPC-Web 用戶端</span><span class="sxs-lookup"><span data-stu-id="51da4-158">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="51da4-159">有一個 JavaScript gRPC Web 用戶端。</span><span class="sxs-lookup"><span data-stu-id="51da4-159">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="51da4-160">如需如何從 JavaScript 使用 gRPC Web 的指示，請參閱 [使用 gRPC 撰寫 javascript 用戶端程式代碼](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)。</span><span class="sxs-lookup"><span data-stu-id="51da4-160">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="51da4-161">使用 .NET gRPC 用戶端設定 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="51da4-161">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="51da4-162">您可以設定 .NET gRPC 用戶端來進行 gRPC Web 呼叫。</span><span class="sxs-lookup"><span data-stu-id="51da4-162">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="51da4-163">這適用于裝載 [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) 于瀏覽器中的應用程式，且具有 JavaScript 程式碼的相同 HTTP 限制。</span><span class="sxs-lookup"><span data-stu-id="51da4-163">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="51da4-164">使用 .NET 用戶端呼叫 gRPC Web 與 [HTTP/2 gRPC 相同](xref:grpc/client)。</span><span class="sxs-lookup"><span data-stu-id="51da4-164">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="51da4-165">唯一的修改是通道的建立方式。</span><span class="sxs-lookup"><span data-stu-id="51da4-165">The only modification is how the channel is created.</span></span>

<span data-ttu-id="51da4-166">若要使用 gRPC-Web：</span><span class="sxs-lookup"><span data-stu-id="51da4-166">To use gRPC-Web:</span></span>

* <span data-ttu-id="51da4-167">將參考新增至 [Grpc .net. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) 封裝。</span><span class="sxs-lookup"><span data-stu-id="51da4-167">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="51da4-168">確定 [Grpc .net](https://www.nuget.org/packages/Grpc.Net.Client) 的參考是2.29.0 或更高。</span><span class="sxs-lookup"><span data-stu-id="51da4-168">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="51da4-169">將通道設定為使用 `GrpcWebHandler` ：</span><span class="sxs-lookup"><span data-stu-id="51da4-169">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="51da4-170">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="51da4-170">The preceding code:</span></span>

* <span data-ttu-id="51da4-171">設定通道以使用 gRPC Web。</span><span class="sxs-lookup"><span data-stu-id="51da4-171">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="51da4-172">建立用戶端，並使用通道進行呼叫。</span><span class="sxs-lookup"><span data-stu-id="51da4-172">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="51da4-173">`GrpcWebHandler` 具有下列設定選項：</span><span class="sxs-lookup"><span data-stu-id="51da4-173">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="51da4-174">**InnerHandler**： <xref:System.Net.Http.HttpMessageHandler> 建立 gRPC HTTP 要求的基礎，例如 `HttpClientHandler` 。</span><span class="sxs-lookup"><span data-stu-id="51da4-174">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="51da4-175">**GrpcWebMode**：列舉型別，這個型別會指定 gRPC HTTP 要求是否 `Content-Type` 為 `application/grpc-web` 或 `application/grpc-web-text` 。</span><span class="sxs-lookup"><span data-stu-id="51da4-175">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="51da4-176">`GrpcWebMode.GrpcWeb` 設定要在不編碼的情況下傳送的內容。</span><span class="sxs-lookup"><span data-stu-id="51da4-176">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="51da4-177">預設值。</span><span class="sxs-lookup"><span data-stu-id="51da4-177">Default value.</span></span>
    * <span data-ttu-id="51da4-178">`GrpcWebMode.GrpcWebText` 將內容設定為 base64 編碼。</span><span class="sxs-lookup"><span data-stu-id="51da4-178">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="51da4-179">在瀏覽器中進行伺服器串流呼叫的必要項。</span><span class="sxs-lookup"><span data-stu-id="51da4-179">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="51da4-180">**HttpVersion**： HTTP 通訊協定， `Version` 用來設定基礎 gRPC Http 要求的[HttpRequestMessage。](xref:System.Net.Http.HttpRequestMessage.Version)</span><span class="sxs-lookup"><span data-stu-id="51da4-180">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="51da4-181">gRPC-Web 不需要特定版本，除非已指定，否則不會覆寫預設值。</span><span class="sxs-lookup"><span data-stu-id="51da4-181">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="51da4-182">產生的 gRPC 用戶端具有可呼叫一元方法的同步和非同步方法。</span><span class="sxs-lookup"><span data-stu-id="51da4-182">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="51da4-183">例如， `SayHello` 為 sync 且 `SayHelloAsync` 為 async。</span><span class="sxs-lookup"><span data-stu-id="51da4-183">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="51da4-184">在應用程式中呼叫同步方法 Blazor WebAssembly 會導致應用程式沒有回應。</span><span class="sxs-lookup"><span data-stu-id="51da4-184">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="51da4-185">非同步方法必須一律用於中 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="51da4-185">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="51da4-186">其他資源</span><span class="sxs-lookup"><span data-stu-id="51da4-186">Additional resources</span></span>

* [<span data-ttu-id="51da4-187">適用于 Web 用戶端 GitHub 專案的 gRPC</span><span class="sxs-lookup"><span data-stu-id="51da4-187">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
