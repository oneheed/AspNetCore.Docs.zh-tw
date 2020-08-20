---
title: ASP.NET Core 中的回應壓縮
author: rick-anderson
description: 了解回應壓縮及如何使用 ASP.NET Core 應用程式中的回應壓縮中介軟體。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/response-compression
ms.openlocfilehash: b8947e3c3c4f634fbd838c22ff60799257143480
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634991"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="4577e-103">ASP.NET Core 中的回應壓縮</span><span class="sxs-lookup"><span data-stu-id="4577e-103">Response compression in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4577e-104">網路頻寬是有限的資源。</span><span class="sxs-lookup"><span data-stu-id="4577e-104">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="4577e-105">減少回應的大小通常會增加應用程式的回應能力，通常會大幅增加。</span><span class="sxs-lookup"><span data-stu-id="4577e-105">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="4577e-106">減少承載大小的其中一種方式是壓縮應用程式的回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-106">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="4577e-107">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="4577e-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="4577e-108">使用回應壓縮中介軟體的時機</span><span class="sxs-lookup"><span data-stu-id="4577e-108">When to use Response Compression Middleware</span></span>

<span data-ttu-id="4577e-109">在 IIS、Apache 或 Nginx 中使用以伺服器為基礎的回應壓縮技術。</span><span class="sxs-lookup"><span data-stu-id="4577e-109">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="4577e-110">中介軟體的效能可能不符合伺服器模組的效能。</span><span class="sxs-lookup"><span data-stu-id="4577e-110">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="4577e-111">[HTTP.sys server](xref:fundamentals/servers/httpsys) Server 和 [Kestrel](xref:fundamentals/servers/kestrel) server 目前未提供內建壓縮支援。</span><span class="sxs-lookup"><span data-stu-id="4577e-111">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="4577e-112">當您在下列情況時，請使用回應壓縮中介軟體：</span><span class="sxs-lookup"><span data-stu-id="4577e-112">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="4577e-113">無法使用下列以伺服器為基礎的壓縮技術：</span><span class="sxs-lookup"><span data-stu-id="4577e-113">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="4577e-114">IIS 動態壓縮模組</span><span class="sxs-lookup"><span data-stu-id="4577e-114">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="4577e-115">Apache mod_deflate 模組</span><span class="sxs-lookup"><span data-stu-id="4577e-115">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="4577e-116">Nginx 壓縮和解壓縮</span><span class="sxs-lookup"><span data-stu-id="4577e-116">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="4577e-117">直接裝載于：</span><span class="sxs-lookup"><span data-stu-id="4577e-117">Hosting directly on:</span></span>
  * <span data-ttu-id="4577e-118">[HTTP.sys server](xref:fundamentals/servers/httpsys) (先前稱為 WebListener) </span><span class="sxs-lookup"><span data-stu-id="4577e-118">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="4577e-119">Kestrel 伺服器</span><span class="sxs-lookup"><span data-stu-id="4577e-119">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="4577e-120">回應壓縮</span><span class="sxs-lookup"><span data-stu-id="4577e-120">Response compression</span></span>

<span data-ttu-id="4577e-121">通常，任何未原生壓縮的回應都可受益于回應壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-121">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="4577e-122">未原生壓縮的回應通常包括： CSS、JavaScript、HTML、XML 和 JSON。</span><span class="sxs-lookup"><span data-stu-id="4577e-122">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="4577e-123">您不應壓縮原生壓縮的資產，例如 PNG 檔案。</span><span class="sxs-lookup"><span data-stu-id="4577e-123">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="4577e-124">如果您嘗試進一步壓縮原生壓縮的回應，則在處理壓縮所花費的時間內，可能會失色任何較小的額外縮減大小和傳輸時間。</span><span class="sxs-lookup"><span data-stu-id="4577e-124">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="4577e-125">請勿壓縮小於大約150-1000 個位元組 (的檔案，視檔案的內容和壓縮) 的效率而定。</span><span class="sxs-lookup"><span data-stu-id="4577e-125">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="4577e-126">壓縮小型檔案的額外負荷可能會產生大於未壓縮檔案的壓縮檔案。</span><span class="sxs-lookup"><span data-stu-id="4577e-126">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="4577e-127">當用戶端可以處理壓縮的內容時，用戶端必須將 `Accept-Encoding` 標頭與要求一起傳送，以通知伺服器其功能。</span><span class="sxs-lookup"><span data-stu-id="4577e-127">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="4577e-128">當伺服器傳送壓縮的內容時，它必須在 `Content-Encoding` 標頭中包含壓縮回應編碼方式的資訊。</span><span class="sxs-lookup"><span data-stu-id="4577e-128">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="4577e-129">下表顯示中介軟體所支援的內容編碼方式。</span><span class="sxs-lookup"><span data-stu-id="4577e-129">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="4577e-130">`Accept-Encoding` 標頭值</span><span class="sxs-lookup"><span data-stu-id="4577e-130">`Accept-Encoding` header values</span></span> | <span data-ttu-id="4577e-131">支援中介軟體</span><span class="sxs-lookup"><span data-stu-id="4577e-131">Middleware Supported</span></span> | <span data-ttu-id="4577e-132">描述</span><span class="sxs-lookup"><span data-stu-id="4577e-132">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="4577e-133">是 (預設)</span><span class="sxs-lookup"><span data-stu-id="4577e-133">Yes (default)</span></span>        | [<span data-ttu-id="4577e-134">Brotli 壓縮的資料格式</span><span class="sxs-lookup"><span data-stu-id="4577e-134">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="4577e-135">否</span><span class="sxs-lookup"><span data-stu-id="4577e-135">No</span></span>                   | [<span data-ttu-id="4577e-136">DEFLATE 壓縮的資料格式</span><span class="sxs-lookup"><span data-stu-id="4577e-136">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="4577e-137">否</span><span class="sxs-lookup"><span data-stu-id="4577e-137">No</span></span>                   | [<span data-ttu-id="4577e-138">W3C 有效率的 XML 交換</span><span class="sxs-lookup"><span data-stu-id="4577e-138">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="4577e-139">是</span><span class="sxs-lookup"><span data-stu-id="4577e-139">Yes</span></span>                  | [<span data-ttu-id="4577e-140">GZIP 檔案格式</span><span class="sxs-lookup"><span data-stu-id="4577e-140">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="4577e-141">是</span><span class="sxs-lookup"><span data-stu-id="4577e-141">Yes</span></span>                  | <span data-ttu-id="4577e-142">「無編碼」識別碼：回應不得編碼。</span><span class="sxs-lookup"><span data-stu-id="4577e-142">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="4577e-143">否</span><span class="sxs-lookup"><span data-stu-id="4577e-143">No</span></span>                   | [<span data-ttu-id="4577e-144">JAVA 封存的網路傳輸格式</span><span class="sxs-lookup"><span data-stu-id="4577e-144">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="4577e-145">是</span><span class="sxs-lookup"><span data-stu-id="4577e-145">Yes</span></span>                  | <span data-ttu-id="4577e-146">未明確要求任何可用的內容編碼</span><span class="sxs-lookup"><span data-stu-id="4577e-146">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="4577e-147">如需詳細資訊，請參閱 [IANA 官方內容編碼清單](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。</span><span class="sxs-lookup"><span data-stu-id="4577e-147">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="4577e-148">中介軟體可讓您為自訂標頭值新增額外的壓縮提供者 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-148">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="4577e-149">如需詳細資訊，請參閱下方的 [自訂提供者](#custom-providers) 。</span><span class="sxs-lookup"><span data-stu-id="4577e-149">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="4577e-150">在用戶端傳送壓縮配置的優先順序時，中介軟體能夠回應品質值 (qvalue， `q`) 加權。</span><span class="sxs-lookup"><span data-stu-id="4577e-150">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="4577e-151">如需詳細資訊，請參閱 [RFC 7231：接受編碼](https://tools.ietf.org/html/rfc7231#section-5.3.4)。</span><span class="sxs-lookup"><span data-stu-id="4577e-151">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="4577e-152">壓縮演算法受限於壓縮速度與壓縮效率之間的取捨。</span><span class="sxs-lookup"><span data-stu-id="4577e-152">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="4577e-153">此內容中的*有效性*是指壓縮之後的輸出大小。</span><span class="sxs-lookup"><span data-stu-id="4577e-153">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="4577e-154">最大的壓縮是以最 *理想* 的壓縮來達成。</span><span class="sxs-lookup"><span data-stu-id="4577e-154">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="4577e-155">下表說明要求、傳送、快取及接收壓縮內容所涉及的標頭。</span><span class="sxs-lookup"><span data-stu-id="4577e-155">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="4577e-156">標頭</span><span class="sxs-lookup"><span data-stu-id="4577e-156">Header</span></span>             | <span data-ttu-id="4577e-157">角色</span><span class="sxs-lookup"><span data-stu-id="4577e-157">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="4577e-158">從用戶端傳送到伺服器，以指出用戶端可接受的內容編碼配置。</span><span class="sxs-lookup"><span data-stu-id="4577e-158">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="4577e-159">從伺服器傳送至用戶端，以指出內容在承載中的編碼方式。</span><span class="sxs-lookup"><span data-stu-id="4577e-159">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="4577e-160">發生壓縮時， `Content-Length` 會移除標頭，因為本文內容會在回應壓縮時變更。</span><span class="sxs-lookup"><span data-stu-id="4577e-160">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="4577e-161">發生壓縮時， `Content-MD5` 會移除標頭，因為本文內容已變更，且雜湊不再有效。</span><span class="sxs-lookup"><span data-stu-id="4577e-161">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="4577e-162">指定內容的 MIME 類型。</span><span class="sxs-lookup"><span data-stu-id="4577e-162">Specifies the MIME type of the content.</span></span> <span data-ttu-id="4577e-163">每個回應都應指定其 `Content-Type` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-163">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="4577e-164">中介軟體會檢查此值，以判斷是否應該壓縮回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-164">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="4577e-165">中介軟體會指定一組可以編碼的 [預設 MIME 類型](#mime-types) ，但是您可以取代或加入 mime 類型。</span><span class="sxs-lookup"><span data-stu-id="4577e-165">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="4577e-166">當伺服器將值的傳 `Accept-Encoding` 給用戶端和 proxy 時， `Vary` 標頭會向用戶端或 proxy 指出它應該快取 (根據要求標頭的值來) 回應 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-166">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="4577e-167">使用標頭傳回內容的結果， `Vary: Accept-Encoding` 是分開快取壓縮和未壓縮的回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-167">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="4577e-168">使用 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)探索回應壓縮中介軟體的功能。</span><span class="sxs-lookup"><span data-stu-id="4577e-168">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="4577e-169">此範例說明：</span><span class="sxs-lookup"><span data-stu-id="4577e-169">The sample illustrates:</span></span>

* <span data-ttu-id="4577e-170">使用 Gzip 和自訂壓縮提供者壓縮應用程式回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-170">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="4577e-171">如何將 MIME 類型加入至預設的 MIME 類型清單以進行壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-171">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="4577e-172">Package</span><span class="sxs-lookup"><span data-stu-id="4577e-172">Package</span></span>

<span data-ttu-id="4577e-173">回應壓縮中介軟體是由 [AspNetCore ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) 封裝所提供，該封裝會隱含地包含在 ASP.NET Core apps 中。</span><span class="sxs-lookup"><span data-stu-id="4577e-173">Response Compression Middleware is provided by the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="configuration"></a><span data-ttu-id="4577e-174">組態</span><span class="sxs-lookup"><span data-stu-id="4577e-174">Configuration</span></span>

<span data-ttu-id="4577e-175">下列程式碼說明如何針對預設的 MIME 類型和壓縮提供者啟用回應壓縮中介軟體 ([Brotli](#brotli-compression-provider) 和 [Gzip](#gzip-compression-provider)) ：</span><span class="sxs-lookup"><span data-stu-id="4577e-175">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

<span data-ttu-id="4577e-176">注意：</span><span class="sxs-lookup"><span data-stu-id="4577e-176">Notes:</span></span>

* <span data-ttu-id="4577e-177">`app.UseResponseCompression` 必須在可壓縮回應的任何中介軟體之前呼叫。</span><span class="sxs-lookup"><span data-stu-id="4577e-177">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="4577e-178">如需詳細資訊，請參閱<xref:fundamentals/middleware/index#middleware-order>。</span><span class="sxs-lookup"><span data-stu-id="4577e-178">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="4577e-179">使用 [Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或 [Postman](https://www.getpostman.com/) 等工具來設定 `Accept-Encoding` 要求標頭，並研究回應標頭、大小和主體。</span><span class="sxs-lookup"><span data-stu-id="4577e-179">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="4577e-180">將要求提交給範例應用程式，而不使用 `Accept-Encoding` 標頭，並觀察回應是否未壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-180">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="4577e-181">`Content-Encoding`和 `Vary` 標頭不存在於回應中。</span><span class="sxs-lookup"><span data-stu-id="4577e-181">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![顯示要求結果（不含接受編碼標頭）的 Fiddler 視窗。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="4577e-184">使用標頭 (Brotli 壓縮) 將要求提交至範例應用程式 `Accept-Encoding: br` ，並觀察回應是否已壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-184">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="4577e-185">`Content-Encoding`和 `Vary` 標頭出現在回應中。</span><span class="sxs-lookup"><span data-stu-id="4577e-185">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![顯示要求結果與接受編碼標頭以及 br 值的 Fiddler 視窗。](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="4577e-189">提供者</span><span class="sxs-lookup"><span data-stu-id="4577e-189">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="4577e-190">Brotli 壓縮提供者</span><span class="sxs-lookup"><span data-stu-id="4577e-190">Brotli Compression Provider</span></span>

<span data-ttu-id="4577e-191">使用壓縮 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> [Brotli 壓縮資料格式](https://tools.ietf.org/html/rfc7932)的回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-191">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="4577e-192">如果未明確將壓縮提供者加入至 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> ：</span><span class="sxs-lookup"><span data-stu-id="4577e-192">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="4577e-193">預設會將 Brotli 壓縮提供者加入至壓縮提供者的陣列，以及 [Gzip 壓縮提供者](#gzip-compression-provider)。</span><span class="sxs-lookup"><span data-stu-id="4577e-193">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="4577e-194">當用戶端支援 Brotli 壓縮資料格式時，壓縮會預設為 Brotli 壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-194">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="4577e-195">如果用戶端不支援 Brotli，當用戶端支援 Gzip 壓縮時，壓縮會預設為 Gzip。</span><span class="sxs-lookup"><span data-stu-id="4577e-195">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="4577e-196">明確新增任何壓縮提供者時，必須新增 Brotli 壓縮提供者：</span><span class="sxs-lookup"><span data-stu-id="4577e-196">The Brotli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="4577e-197">使用設定壓縮等級 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions> 。</span><span class="sxs-lookup"><span data-stu-id="4577e-197">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="4577e-198">Brotli 壓縮提供者預設為最快的壓縮層級 ([CompressionLevel。最快](xref:System.IO.Compression.CompressionLevel) 的) ，可能不會產生最有效率的壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-198">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="4577e-199">如果需要最有效率的壓縮，請設定中介軟體以獲得最佳壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-199">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="4577e-200">Compression Level</span><span class="sxs-lookup"><span data-stu-id="4577e-200">Compression Level</span></span> | <span data-ttu-id="4577e-201">描述</span><span class="sxs-lookup"><span data-stu-id="4577e-201">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="4577e-202">CompressionLevel。最快</span><span class="sxs-lookup"><span data-stu-id="4577e-202">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4577e-203">壓縮應該盡可能快速完成，即使產生的輸出未以最佳方式壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-203">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="4577e-204">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="4577e-204">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4577e-205">不應執行任何壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-205">No compression should be performed.</span></span> |
| [<span data-ttu-id="4577e-206">CompressionLevel。最佳</span><span class="sxs-lookup"><span data-stu-id="4577e-206">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4577e-207">即使壓縮需要更多時間才能完成，回應仍應以最佳方式壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-207">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<BrotliCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="gzip-compression-provider"></a><span data-ttu-id="4577e-208">Gzip 壓縮提供者</span><span class="sxs-lookup"><span data-stu-id="4577e-208">Gzip Compression Provider</span></span>

<span data-ttu-id="4577e-209">使用 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> 來壓縮具有 [GZIP 檔案格式](https://tools.ietf.org/html/rfc1952)的回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-209">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="4577e-210">如果未明確將壓縮提供者加入至 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> ：</span><span class="sxs-lookup"><span data-stu-id="4577e-210">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="4577e-211">Gzip 壓縮提供者預設會加入壓縮提供者的陣列，連同 [Brotli 壓縮提供者](#brotli-compression-provider)。</span><span class="sxs-lookup"><span data-stu-id="4577e-211">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="4577e-212">當用戶端支援 Brotli 壓縮資料格式時，壓縮會預設為 Brotli 壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-212">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="4577e-213">如果用戶端不支援 Brotli，當用戶端支援 Gzip 壓縮時，壓縮會預設為 Gzip。</span><span class="sxs-lookup"><span data-stu-id="4577e-213">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="4577e-214">明確新增任何壓縮提供者時，必須加入 Gzip 壓縮提供者：</span><span class="sxs-lookup"><span data-stu-id="4577e-214">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="4577e-215">使用設定壓縮等級 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> 。</span><span class="sxs-lookup"><span data-stu-id="4577e-215">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="4577e-216">Gzip 壓縮提供者預設為最快的壓縮層級 ([CompressionLevel。最快](xref:System.IO.Compression.CompressionLevel) 的) ，可能不會產生最有效率的壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-216">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="4577e-217">如果需要最有效率的壓縮，請設定中介軟體以獲得最佳壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-217">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="4577e-218">Compression Level</span><span class="sxs-lookup"><span data-stu-id="4577e-218">Compression Level</span></span> | <span data-ttu-id="4577e-219">描述</span><span class="sxs-lookup"><span data-stu-id="4577e-219">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="4577e-220">CompressionLevel。最快</span><span class="sxs-lookup"><span data-stu-id="4577e-220">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4577e-221">壓縮應該盡可能快速完成，即使產生的輸出未以最佳方式壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-221">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="4577e-222">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="4577e-222">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4577e-223">不應執行任何壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-223">No compression should be performed.</span></span> |
| [<span data-ttu-id="4577e-224">CompressionLevel。最佳</span><span class="sxs-lookup"><span data-stu-id="4577e-224">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4577e-225">即使壓縮需要更多時間才能完成，回應仍應以最佳方式壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-225">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a><span data-ttu-id="4577e-226">自訂提供者</span><span class="sxs-lookup"><span data-stu-id="4577e-226">Custom providers</span></span>

<span data-ttu-id="4577e-227">使用建立自訂壓縮 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> 。</span><span class="sxs-lookup"><span data-stu-id="4577e-227">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="4577e-228"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>代表這產生的內容編碼 `ICompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-228">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="4577e-229">中介軟體會使用這項資訊，根據要求標頭中指定的清單來選擇提供者 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-229">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="4577e-230">使用範例應用程式時，用戶端會以 `Accept-Encoding: mycustomcompression` 標頭提交要求。</span><span class="sxs-lookup"><span data-stu-id="4577e-230">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="4577e-231">中介軟體會使用自訂壓縮執行，並傳回含有 `Content-Encoding: mycustomcompression` 標頭的回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-231">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="4577e-232">用戶端必須能夠解壓縮自訂編碼，自訂壓縮執行才能運作。</span><span class="sxs-lookup"><span data-stu-id="4577e-232">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]


<span data-ttu-id="4577e-233">使用標頭將要求提交至範例應用程式 `Accept-Encoding: mycustomcompression` ，並觀察回應標頭。</span><span class="sxs-lookup"><span data-stu-id="4577e-233">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="4577e-234">`Vary`和 `Content-Encoding` 標頭出現在回應中。</span><span class="sxs-lookup"><span data-stu-id="4577e-234">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="4577e-235">未 (顯示的回應主體) 不會被範例壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-235">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="4577e-236">範例的類別中沒有壓縮的執行 `CustomCompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-236">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="4577e-237">不過，此範例會示範您要在哪裡執行這類壓縮演算法。</span><span class="sxs-lookup"><span data-stu-id="4577e-237">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 視窗顯示具有接受編碼標頭和值 mycustomcompression 的要求結果。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="4577e-240">MIME 類型</span><span class="sxs-lookup"><span data-stu-id="4577e-240">MIME types</span></span>

<span data-ttu-id="4577e-241">中介軟體會針對壓縮指定一組預設的 MIME 類型：</span><span class="sxs-lookup"><span data-stu-id="4577e-241">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="4577e-242">以回應壓縮中介軟體選項取代或附加 MIME 類型。</span><span class="sxs-lookup"><span data-stu-id="4577e-242">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="4577e-243">請注意，不支援萬用字元 MIME 類型（例如） `text/*` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-243">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="4577e-244">範例應用程式會新增的 MIME 類型 `image/svg+xml` ，並壓縮並提供 ASP.NET Core 橫幅影像 (的橫幅 *。 svg*) 。</span><span class="sxs-lookup"><span data-stu-id="4577e-244">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="4577e-245">使用安全通訊協定進行壓縮</span><span class="sxs-lookup"><span data-stu-id="4577e-245">Compression with secure protocol</span></span>

<span data-ttu-id="4577e-246">您可以使用預設停用的選項來控制透過安全連線的壓縮回應 `EnableForHttps` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-246">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="4577e-247">使用壓縮動態產生的頁面可能會導致安全性問題，例如 [犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit)) 和 [缺口](https://wikipedia.org/wiki/BREACH_(security_exploit)) 攻擊。</span><span class="sxs-lookup"><span data-stu-id="4577e-247">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="4577e-248">新增 Vary 標頭</span><span class="sxs-lookup"><span data-stu-id="4577e-248">Adding the Vary header</span></span>

<span data-ttu-id="4577e-249">根據標頭壓縮回應時 `Accept-Encoding` ，可能會有多個壓縮版本的回應和未壓縮的版本。</span><span class="sxs-lookup"><span data-stu-id="4577e-249">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="4577e-250">為了指示用戶端和 proxy 快取有多個版本存在且應該儲存， `Vary` 標頭會加上 `Accept-Encoding` 值。</span><span class="sxs-lookup"><span data-stu-id="4577e-250">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="4577e-251">在 ASP.NET Core 2.0 或更新版本中，中介軟體 `Vary` 會在回應壓縮時自動新增標頭。</span><span class="sxs-lookup"><span data-stu-id="4577e-251">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="4577e-252">Nginx 反向 proxy 後方的中介軟體問題</span><span class="sxs-lookup"><span data-stu-id="4577e-252">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="4577e-253">當要求由 Nginx proxy 時， `Accept-Encoding` 會移除標頭。</span><span class="sxs-lookup"><span data-stu-id="4577e-253">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="4577e-254">移除 `Accept-Encoding` 標頭可防止中介軟體壓縮回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-254">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="4577e-255">如需詳細資訊，請參閱 [NGINX：壓縮和解壓縮](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。</span><span class="sxs-lookup"><span data-stu-id="4577e-255">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="4577e-256">此問題的追蹤方式為 [Nginx (aspnet/BasicMiddleware #123) 的傳遞壓縮 ](https://github.com/aspnet/BasicMiddleware/issues/123)。</span><span class="sxs-lookup"><span data-stu-id="4577e-256">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="4577e-257">使用 IIS 動態壓縮</span><span class="sxs-lookup"><span data-stu-id="4577e-257">Working with IIS dynamic compression</span></span>

<span data-ttu-id="4577e-258">如果您在想要針對應用程式停用的伺服器層級上設定作用中的 IIS 動態壓縮模組，請停用 *web.config* 檔案以外的模組。</span><span class="sxs-lookup"><span data-stu-id="4577e-258">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="4577e-259">如需詳細資訊，請參閱[停用 IIS 模組](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="4577e-259">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="4577e-260">疑難排解</span><span class="sxs-lookup"><span data-stu-id="4577e-260">Troubleshooting</span></span>

<span data-ttu-id="4577e-261">使用 [Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或 [Postman](https://www.getpostman.com/)等工具，這可讓您設定 `Accept-Encoding` 要求標頭，並研究回應標頭、大小和主體。</span><span class="sxs-lookup"><span data-stu-id="4577e-261">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="4577e-262">根據預設，回應壓縮中介軟體會壓縮符合下列條件的回應：</span><span class="sxs-lookup"><span data-stu-id="4577e-262">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="4577e-263">`Accept-Encoding`標頭存在，其值為 `br` 、 `gzip` 、 `*` 或自訂編碼，以符合您所建立的自訂壓縮提供者。</span><span class="sxs-lookup"><span data-stu-id="4577e-263">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="4577e-264">值不得為 `identity` 或具有 (qvalue 的品質值， `q`) 設定為 0 (零) 。</span><span class="sxs-lookup"><span data-stu-id="4577e-264">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="4577e-265">必須設定 MIME 類型 (`Content-Type`) ，且必須符合中所設定的 mime 類型 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> 。</span><span class="sxs-lookup"><span data-stu-id="4577e-265">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="4577e-266">要求不能包括 `Content-Range` 標頭。</span><span class="sxs-lookup"><span data-stu-id="4577e-266">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="4577e-267">要求必須使用不安全的通訊協定 (HTTP) ，除非在回應壓縮中介軟體選項中設定了安全通訊協定 (HTTPs) 。</span><span class="sxs-lookup"><span data-stu-id="4577e-267">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="4577e-268">*在啟用安全內容壓縮時，請注意 [上述](#compression-with-secure-protocol) 的風險。*</span><span class="sxs-lookup"><span data-stu-id="4577e-268">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4577e-269">其他資源</span><span class="sxs-lookup"><span data-stu-id="4577e-269">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="4577e-270">Mozilla 開發人員網路：接受編碼</span><span class="sxs-lookup"><span data-stu-id="4577e-270">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="4577e-271">RFC 7231 區段3.1.2.1： Content Codings</span><span class="sxs-lookup"><span data-stu-id="4577e-271">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="4577e-272">RFC 7230 區段4.2.3： Gzip 編碼</span><span class="sxs-lookup"><span data-stu-id="4577e-272">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="4577e-273">GZIP 檔案格式規格版本4。3</span><span class="sxs-lookup"><span data-stu-id="4577e-273">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="4577e-274">網路頻寬是有限的資源。</span><span class="sxs-lookup"><span data-stu-id="4577e-274">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="4577e-275">減少回應的大小通常會增加應用程式的回應能力，通常會大幅增加。</span><span class="sxs-lookup"><span data-stu-id="4577e-275">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="4577e-276">減少承載大小的其中一種方式是壓縮應用程式的回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-276">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="4577e-277">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="4577e-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="4577e-278">使用回應壓縮中介軟體的時機</span><span class="sxs-lookup"><span data-stu-id="4577e-278">When to use Response Compression Middleware</span></span>

<span data-ttu-id="4577e-279">在 IIS、Apache 或 Nginx 中使用以伺服器為基礎的回應壓縮技術。</span><span class="sxs-lookup"><span data-stu-id="4577e-279">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="4577e-280">中介軟體的效能可能不符合伺服器模組的效能。</span><span class="sxs-lookup"><span data-stu-id="4577e-280">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="4577e-281">[HTTP.sys server](xref:fundamentals/servers/httpsys) Server 和 [Kestrel](xref:fundamentals/servers/kestrel) server 目前未提供內建壓縮支援。</span><span class="sxs-lookup"><span data-stu-id="4577e-281">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="4577e-282">當您在下列情況時，請使用回應壓縮中介軟體：</span><span class="sxs-lookup"><span data-stu-id="4577e-282">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="4577e-283">無法使用下列以伺服器為基礎的壓縮技術：</span><span class="sxs-lookup"><span data-stu-id="4577e-283">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="4577e-284">IIS 動態壓縮模組</span><span class="sxs-lookup"><span data-stu-id="4577e-284">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="4577e-285">Apache mod_deflate 模組</span><span class="sxs-lookup"><span data-stu-id="4577e-285">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="4577e-286">Nginx 壓縮和解壓縮</span><span class="sxs-lookup"><span data-stu-id="4577e-286">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="4577e-287">直接裝載于：</span><span class="sxs-lookup"><span data-stu-id="4577e-287">Hosting directly on:</span></span>
  * <span data-ttu-id="4577e-288">[HTTP.sys server](xref:fundamentals/servers/httpsys) (先前稱為 WebListener) </span><span class="sxs-lookup"><span data-stu-id="4577e-288">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="4577e-289">Kestrel 伺服器</span><span class="sxs-lookup"><span data-stu-id="4577e-289">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="4577e-290">回應壓縮</span><span class="sxs-lookup"><span data-stu-id="4577e-290">Response compression</span></span>

<span data-ttu-id="4577e-291">通常，任何未原生壓縮的回應都可受益于回應壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-291">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="4577e-292">未原生壓縮的回應通常包括： CSS、JavaScript、HTML、XML 和 JSON。</span><span class="sxs-lookup"><span data-stu-id="4577e-292">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="4577e-293">您不應壓縮原生壓縮的資產，例如 PNG 檔案。</span><span class="sxs-lookup"><span data-stu-id="4577e-293">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="4577e-294">如果您嘗試進一步壓縮原生壓縮的回應，則在處理壓縮所花費的時間內，可能會失色任何較小的額外縮減大小和傳輸時間。</span><span class="sxs-lookup"><span data-stu-id="4577e-294">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="4577e-295">請勿壓縮小於大約150-1000 個位元組 (的檔案，視檔案的內容和壓縮) 的效率而定。</span><span class="sxs-lookup"><span data-stu-id="4577e-295">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="4577e-296">壓縮小型檔案的額外負荷可能會產生大於未壓縮檔案的壓縮檔案。</span><span class="sxs-lookup"><span data-stu-id="4577e-296">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="4577e-297">當用戶端可以處理壓縮的內容時，用戶端必須將 `Accept-Encoding` 標頭與要求一起傳送，以通知伺服器其功能。</span><span class="sxs-lookup"><span data-stu-id="4577e-297">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="4577e-298">當伺服器傳送壓縮的內容時，它必須在 `Content-Encoding` 標頭中包含壓縮回應編碼方式的資訊。</span><span class="sxs-lookup"><span data-stu-id="4577e-298">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="4577e-299">下表顯示中介軟體所支援的內容編碼方式。</span><span class="sxs-lookup"><span data-stu-id="4577e-299">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="4577e-300">`Accept-Encoding` 標頭值</span><span class="sxs-lookup"><span data-stu-id="4577e-300">`Accept-Encoding` header values</span></span> | <span data-ttu-id="4577e-301">支援中介軟體</span><span class="sxs-lookup"><span data-stu-id="4577e-301">Middleware Supported</span></span> | <span data-ttu-id="4577e-302">描述</span><span class="sxs-lookup"><span data-stu-id="4577e-302">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="4577e-303">是 (預設)</span><span class="sxs-lookup"><span data-stu-id="4577e-303">Yes (default)</span></span>        | [<span data-ttu-id="4577e-304">Brotli 壓縮的資料格式</span><span class="sxs-lookup"><span data-stu-id="4577e-304">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="4577e-305">否</span><span class="sxs-lookup"><span data-stu-id="4577e-305">No</span></span>                   | [<span data-ttu-id="4577e-306">DEFLATE 壓縮的資料格式</span><span class="sxs-lookup"><span data-stu-id="4577e-306">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="4577e-307">否</span><span class="sxs-lookup"><span data-stu-id="4577e-307">No</span></span>                   | [<span data-ttu-id="4577e-308">W3C 有效率的 XML 交換</span><span class="sxs-lookup"><span data-stu-id="4577e-308">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="4577e-309">是</span><span class="sxs-lookup"><span data-stu-id="4577e-309">Yes</span></span>                  | [<span data-ttu-id="4577e-310">GZIP 檔案格式</span><span class="sxs-lookup"><span data-stu-id="4577e-310">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="4577e-311">是</span><span class="sxs-lookup"><span data-stu-id="4577e-311">Yes</span></span>                  | <span data-ttu-id="4577e-312">「無編碼」識別碼：回應不得編碼。</span><span class="sxs-lookup"><span data-stu-id="4577e-312">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="4577e-313">否</span><span class="sxs-lookup"><span data-stu-id="4577e-313">No</span></span>                   | [<span data-ttu-id="4577e-314">JAVA 封存的網路傳輸格式</span><span class="sxs-lookup"><span data-stu-id="4577e-314">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="4577e-315">是</span><span class="sxs-lookup"><span data-stu-id="4577e-315">Yes</span></span>                  | <span data-ttu-id="4577e-316">未明確要求任何可用的內容編碼</span><span class="sxs-lookup"><span data-stu-id="4577e-316">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="4577e-317">如需詳細資訊，請參閱 [IANA 官方內容編碼清單](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。</span><span class="sxs-lookup"><span data-stu-id="4577e-317">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="4577e-318">中介軟體可讓您為自訂標頭值新增額外的壓縮提供者 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-318">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="4577e-319">如需詳細資訊，請參閱下方的 [自訂提供者](#custom-providers) 。</span><span class="sxs-lookup"><span data-stu-id="4577e-319">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="4577e-320">在用戶端傳送壓縮配置的優先順序時，中介軟體能夠回應品質值 (qvalue， `q`) 加權。</span><span class="sxs-lookup"><span data-stu-id="4577e-320">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="4577e-321">如需詳細資訊，請參閱 [RFC 7231：接受編碼](https://tools.ietf.org/html/rfc7231#section-5.3.4)。</span><span class="sxs-lookup"><span data-stu-id="4577e-321">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="4577e-322">壓縮演算法受限於壓縮速度與壓縮效率之間的取捨。</span><span class="sxs-lookup"><span data-stu-id="4577e-322">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="4577e-323">此內容中的*有效性*是指壓縮之後的輸出大小。</span><span class="sxs-lookup"><span data-stu-id="4577e-323">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="4577e-324">最大的壓縮是以最 *理想* 的壓縮來達成。</span><span class="sxs-lookup"><span data-stu-id="4577e-324">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="4577e-325">下表說明要求、傳送、快取及接收壓縮內容所涉及的標頭。</span><span class="sxs-lookup"><span data-stu-id="4577e-325">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="4577e-326">標頭</span><span class="sxs-lookup"><span data-stu-id="4577e-326">Header</span></span>             | <span data-ttu-id="4577e-327">角色</span><span class="sxs-lookup"><span data-stu-id="4577e-327">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="4577e-328">從用戶端傳送到伺服器，以指出用戶端可接受的內容編碼配置。</span><span class="sxs-lookup"><span data-stu-id="4577e-328">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="4577e-329">從伺服器傳送至用戶端，以指出內容在承載中的編碼方式。</span><span class="sxs-lookup"><span data-stu-id="4577e-329">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="4577e-330">發生壓縮時， `Content-Length` 會移除標頭，因為本文內容會在回應壓縮時變更。</span><span class="sxs-lookup"><span data-stu-id="4577e-330">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="4577e-331">發生壓縮時， `Content-MD5` 會移除標頭，因為本文內容已變更，且雜湊不再有效。</span><span class="sxs-lookup"><span data-stu-id="4577e-331">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="4577e-332">指定內容的 MIME 類型。</span><span class="sxs-lookup"><span data-stu-id="4577e-332">Specifies the MIME type of the content.</span></span> <span data-ttu-id="4577e-333">每個回應都應指定其 `Content-Type` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-333">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="4577e-334">中介軟體會檢查此值，以判斷是否應該壓縮回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-334">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="4577e-335">中介軟體會指定一組可以編碼的 [預設 MIME 類型](#mime-types) ，但是您可以取代或加入 mime 類型。</span><span class="sxs-lookup"><span data-stu-id="4577e-335">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="4577e-336">當伺服器將值的傳 `Accept-Encoding` 給用戶端和 proxy 時， `Vary` 標頭會向用戶端或 proxy 指出它應該快取 (根據要求標頭的值來) 回應 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-336">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="4577e-337">使用標頭傳回內容的結果， `Vary: Accept-Encoding` 是分開快取壓縮和未壓縮的回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-337">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="4577e-338">使用 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)探索回應壓縮中介軟體的功能。</span><span class="sxs-lookup"><span data-stu-id="4577e-338">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="4577e-339">此範例說明：</span><span class="sxs-lookup"><span data-stu-id="4577e-339">The sample illustrates:</span></span>

* <span data-ttu-id="4577e-340">使用 Gzip 和自訂壓縮提供者壓縮應用程式回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-340">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="4577e-341">如何將 MIME 類型加入至預設的 MIME 類型清單以進行壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-341">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="4577e-342">Package</span><span class="sxs-lookup"><span data-stu-id="4577e-342">Package</span></span>

<span data-ttu-id="4577e-343">若要在專案中包含中介軟體，請新增 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app)的參考，其中包含 [AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) 套件。</span><span class="sxs-lookup"><span data-stu-id="4577e-343">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="4577e-344">組態</span><span class="sxs-lookup"><span data-stu-id="4577e-344">Configuration</span></span>

<span data-ttu-id="4577e-345">下列程式碼說明如何針對預設的 MIME 類型和壓縮提供者啟用回應壓縮中介軟體 ([Brotli](#brotli-compression-provider) 和 [Gzip](#gzip-compression-provider)) ：</span><span class="sxs-lookup"><span data-stu-id="4577e-345">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

<span data-ttu-id="4577e-346">注意：</span><span class="sxs-lookup"><span data-stu-id="4577e-346">Notes:</span></span>

* <span data-ttu-id="4577e-347">`app.UseResponseCompression` 必須在可壓縮回應的任何中介軟體之前呼叫。</span><span class="sxs-lookup"><span data-stu-id="4577e-347">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="4577e-348">如需詳細資訊，請參閱<xref:fundamentals/middleware/index#middleware-order>。</span><span class="sxs-lookup"><span data-stu-id="4577e-348">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="4577e-349">使用 [Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或 [Postman](https://www.getpostman.com/) 等工具來設定 `Accept-Encoding` 要求標頭，並研究回應標頭、大小和主體。</span><span class="sxs-lookup"><span data-stu-id="4577e-349">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="4577e-350">將要求提交給範例應用程式，而不使用 `Accept-Encoding` 標頭，並觀察回應是否未壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-350">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="4577e-351">`Content-Encoding`和 `Vary` 標頭不存在於回應中。</span><span class="sxs-lookup"><span data-stu-id="4577e-351">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![顯示要求結果（不含接受編碼標頭）的 Fiddler 視窗。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="4577e-354">使用標頭 (Brotli 壓縮) 將要求提交至範例應用程式 `Accept-Encoding: br` ，並觀察回應是否已壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-354">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="4577e-355">`Content-Encoding`和 `Vary` 標頭出現在回應中。</span><span class="sxs-lookup"><span data-stu-id="4577e-355">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![顯示要求結果與接受編碼標頭以及 br 值的 Fiddler 視窗。](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="4577e-359">提供者</span><span class="sxs-lookup"><span data-stu-id="4577e-359">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="4577e-360">Brotli 壓縮提供者</span><span class="sxs-lookup"><span data-stu-id="4577e-360">Brotli Compression Provider</span></span>

<span data-ttu-id="4577e-361">使用壓縮 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> [Brotli 壓縮資料格式](https://tools.ietf.org/html/rfc7932)的回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-361">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="4577e-362">如果未明確將壓縮提供者加入至 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> ：</span><span class="sxs-lookup"><span data-stu-id="4577e-362">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="4577e-363">預設會將 Brotli 壓縮提供者加入至壓縮提供者的陣列，以及 [Gzip 壓縮提供者](#gzip-compression-provider)。</span><span class="sxs-lookup"><span data-stu-id="4577e-363">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="4577e-364">當用戶端支援 Brotli 壓縮資料格式時，壓縮會預設為 Brotli 壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-364">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="4577e-365">如果用戶端不支援 Brotli，當用戶端支援 Gzip 壓縮時，壓縮會預設為 Gzip。</span><span class="sxs-lookup"><span data-stu-id="4577e-365">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="4577e-366">明確新增任何壓縮提供者時，必須新增 Brotli 壓縮提供者：</span><span class="sxs-lookup"><span data-stu-id="4577e-366">The Brotli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="4577e-367">使用設定壓縮等級 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions> 。</span><span class="sxs-lookup"><span data-stu-id="4577e-367">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="4577e-368">Brotli 壓縮提供者預設為最快的壓縮層級 ([CompressionLevel。最快](xref:System.IO.Compression.CompressionLevel) 的) ，可能不會產生最有效率的壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-368">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="4577e-369">如果需要最有效率的壓縮，請設定中介軟體以獲得最佳壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-369">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="4577e-370">Compression Level</span><span class="sxs-lookup"><span data-stu-id="4577e-370">Compression Level</span></span> | <span data-ttu-id="4577e-371">描述</span><span class="sxs-lookup"><span data-stu-id="4577e-371">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="4577e-372">CompressionLevel。最快</span><span class="sxs-lookup"><span data-stu-id="4577e-372">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4577e-373">壓縮應該盡可能快速完成，即使產生的輸出未以最佳方式壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-373">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="4577e-374">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="4577e-374">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4577e-375">不應執行任何壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-375">No compression should be performed.</span></span> |
| [<span data-ttu-id="4577e-376">CompressionLevel。最佳</span><span class="sxs-lookup"><span data-stu-id="4577e-376">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4577e-377">即使壓縮需要更多時間才能完成，回應仍應以最佳方式壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-377">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<BrotliCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="gzip-compression-provider"></a><span data-ttu-id="4577e-378">Gzip 壓縮提供者</span><span class="sxs-lookup"><span data-stu-id="4577e-378">Gzip Compression Provider</span></span>

<span data-ttu-id="4577e-379">使用 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> 來壓縮具有 [GZIP 檔案格式](https://tools.ietf.org/html/rfc1952)的回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-379">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="4577e-380">如果未明確將壓縮提供者加入至 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> ：</span><span class="sxs-lookup"><span data-stu-id="4577e-380">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="4577e-381">Gzip 壓縮提供者預設會加入壓縮提供者的陣列，連同 [Brotli 壓縮提供者](#brotli-compression-provider)。</span><span class="sxs-lookup"><span data-stu-id="4577e-381">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="4577e-382">當用戶端支援 Brotli 壓縮資料格式時，壓縮會預設為 Brotli 壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-382">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="4577e-383">如果用戶端不支援 Brotli，當用戶端支援 Gzip 壓縮時，壓縮會預設為 Gzip。</span><span class="sxs-lookup"><span data-stu-id="4577e-383">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="4577e-384">明確新增任何壓縮提供者時，必須加入 Gzip 壓縮提供者：</span><span class="sxs-lookup"><span data-stu-id="4577e-384">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="4577e-385">使用設定壓縮等級 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> 。</span><span class="sxs-lookup"><span data-stu-id="4577e-385">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="4577e-386">Gzip 壓縮提供者預設為最快的壓縮層級 ([CompressionLevel。最快](xref:System.IO.Compression.CompressionLevel) 的) ，可能不會產生最有效率的壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-386">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="4577e-387">如果需要最有效率的壓縮，請設定中介軟體以獲得最佳壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-387">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="4577e-388">Compression Level</span><span class="sxs-lookup"><span data-stu-id="4577e-388">Compression Level</span></span> | <span data-ttu-id="4577e-389">描述</span><span class="sxs-lookup"><span data-stu-id="4577e-389">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="4577e-390">CompressionLevel。最快</span><span class="sxs-lookup"><span data-stu-id="4577e-390">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4577e-391">壓縮應該盡可能快速完成，即使產生的輸出未以最佳方式壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-391">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="4577e-392">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="4577e-392">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4577e-393">不應執行任何壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-393">No compression should be performed.</span></span> |
| [<span data-ttu-id="4577e-394">CompressionLevel。最佳</span><span class="sxs-lookup"><span data-stu-id="4577e-394">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4577e-395">即使壓縮需要更多時間才能完成，回應仍應以最佳方式壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-395">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a><span data-ttu-id="4577e-396">自訂提供者</span><span class="sxs-lookup"><span data-stu-id="4577e-396">Custom providers</span></span>

<span data-ttu-id="4577e-397">使用建立自訂壓縮 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> 。</span><span class="sxs-lookup"><span data-stu-id="4577e-397">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="4577e-398"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>代表這產生的內容編碼 `ICompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-398">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="4577e-399">中介軟體會使用這項資訊，根據要求標頭中指定的清單來選擇提供者 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-399">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="4577e-400">使用範例應用程式時，用戶端會以 `Accept-Encoding: mycustomcompression` 標頭提交要求。</span><span class="sxs-lookup"><span data-stu-id="4577e-400">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="4577e-401">中介軟體會使用自訂壓縮執行，並傳回含有 `Content-Encoding: mycustomcompression` 標頭的回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-401">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="4577e-402">用戶端必須能夠解壓縮自訂編碼，自訂壓縮執行才能運作。</span><span class="sxs-lookup"><span data-stu-id="4577e-402">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="4577e-403">使用標頭將要求提交至範例應用程式 `Accept-Encoding: mycustomcompression` ，並觀察回應標頭。</span><span class="sxs-lookup"><span data-stu-id="4577e-403">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="4577e-404">`Vary`和 `Content-Encoding` 標頭出現在回應中。</span><span class="sxs-lookup"><span data-stu-id="4577e-404">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="4577e-405">未 (顯示的回應主體) 不會被範例壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-405">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="4577e-406">範例的類別中沒有壓縮的執行 `CustomCompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-406">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="4577e-407">不過，此範例會示範您要在哪裡執行這類壓縮演算法。</span><span class="sxs-lookup"><span data-stu-id="4577e-407">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 視窗顯示具有接受編碼標頭和值 mycustomcompression 的要求結果。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="4577e-410">MIME 類型</span><span class="sxs-lookup"><span data-stu-id="4577e-410">MIME types</span></span>

<span data-ttu-id="4577e-411">中介軟體會針對壓縮指定一組預設的 MIME 類型：</span><span class="sxs-lookup"><span data-stu-id="4577e-411">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="4577e-412">以回應壓縮中介軟體選項取代或附加 MIME 類型。</span><span class="sxs-lookup"><span data-stu-id="4577e-412">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="4577e-413">請注意，不支援萬用字元 MIME 類型（例如） `text/*` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-413">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="4577e-414">範例應用程式會新增的 MIME 類型 `image/svg+xml` ，並壓縮並提供 ASP.NET Core 橫幅影像 (的橫幅 *。 svg*) 。</span><span class="sxs-lookup"><span data-stu-id="4577e-414">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="4577e-415">使用安全通訊協定進行壓縮</span><span class="sxs-lookup"><span data-stu-id="4577e-415">Compression with secure protocol</span></span>

<span data-ttu-id="4577e-416">您可以使用預設停用的選項來控制透過安全連線的壓縮回應 `EnableForHttps` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-416">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="4577e-417">使用壓縮動態產生的頁面可能會導致安全性問題，例如 [犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit)) 和 [缺口](https://wikipedia.org/wiki/BREACH_(security_exploit)) 攻擊。</span><span class="sxs-lookup"><span data-stu-id="4577e-417">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="4577e-418">新增 Vary 標頭</span><span class="sxs-lookup"><span data-stu-id="4577e-418">Adding the Vary header</span></span>

<span data-ttu-id="4577e-419">根據標頭壓縮回應時 `Accept-Encoding` ，可能會有多個壓縮版本的回應和未壓縮的版本。</span><span class="sxs-lookup"><span data-stu-id="4577e-419">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="4577e-420">為了指示用戶端和 proxy 快取有多個版本存在且應該儲存， `Vary` 標頭會加上 `Accept-Encoding` 值。</span><span class="sxs-lookup"><span data-stu-id="4577e-420">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="4577e-421">在 ASP.NET Core 2.0 或更新版本中，中介軟體 `Vary` 會在回應壓縮時自動新增標頭。</span><span class="sxs-lookup"><span data-stu-id="4577e-421">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="4577e-422">Nginx 反向 proxy 後方的中介軟體問題</span><span class="sxs-lookup"><span data-stu-id="4577e-422">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="4577e-423">當要求由 Nginx proxy 時， `Accept-Encoding` 會移除標頭。</span><span class="sxs-lookup"><span data-stu-id="4577e-423">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="4577e-424">移除 `Accept-Encoding` 標頭可防止中介軟體壓縮回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-424">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="4577e-425">如需詳細資訊，請參閱 [NGINX：壓縮和解壓縮](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。</span><span class="sxs-lookup"><span data-stu-id="4577e-425">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="4577e-426">此問題的追蹤方式為 [Nginx (aspnet/BasicMiddleware #123) 的傳遞壓縮 ](https://github.com/aspnet/BasicMiddleware/issues/123)。</span><span class="sxs-lookup"><span data-stu-id="4577e-426">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="4577e-427">使用 IIS 動態壓縮</span><span class="sxs-lookup"><span data-stu-id="4577e-427">Working with IIS dynamic compression</span></span>

<span data-ttu-id="4577e-428">如果您在想要針對應用程式停用的伺服器層級上設定作用中的 IIS 動態壓縮模組，請停用 *web.config* 檔案以外的模組。</span><span class="sxs-lookup"><span data-stu-id="4577e-428">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="4577e-429">如需詳細資訊，請參閱[停用 IIS 模組](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="4577e-429">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="4577e-430">疑難排解</span><span class="sxs-lookup"><span data-stu-id="4577e-430">Troubleshooting</span></span>

<span data-ttu-id="4577e-431">使用 [Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或 [Postman](https://www.getpostman.com/)等工具，這可讓您設定 `Accept-Encoding` 要求標頭，並研究回應標頭、大小和主體。</span><span class="sxs-lookup"><span data-stu-id="4577e-431">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="4577e-432">根據預設，回應壓縮中介軟體會壓縮符合下列條件的回應：</span><span class="sxs-lookup"><span data-stu-id="4577e-432">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="4577e-433">`Accept-Encoding`標頭存在，其值為 `br` 、 `gzip` 、 `*` 或自訂編碼，以符合您所建立的自訂壓縮提供者。</span><span class="sxs-lookup"><span data-stu-id="4577e-433">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="4577e-434">值不得為 `identity` 或具有 (qvalue 的品質值， `q`) 設定為 0 (零) 。</span><span class="sxs-lookup"><span data-stu-id="4577e-434">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="4577e-435">必須設定 MIME 類型 (`Content-Type`) ，且必須符合中所設定的 mime 類型 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> 。</span><span class="sxs-lookup"><span data-stu-id="4577e-435">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="4577e-436">要求不能包括 `Content-Range` 標頭。</span><span class="sxs-lookup"><span data-stu-id="4577e-436">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="4577e-437">要求必須使用不安全的通訊協定 (HTTP) ，除非在回應壓縮中介軟體選項中設定了安全通訊協定 (HTTPs) 。</span><span class="sxs-lookup"><span data-stu-id="4577e-437">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="4577e-438">*在啟用安全內容壓縮時，請注意 [上述](#compression-with-secure-protocol) 的風險。*</span><span class="sxs-lookup"><span data-stu-id="4577e-438">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4577e-439">其他資源</span><span class="sxs-lookup"><span data-stu-id="4577e-439">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="4577e-440">Mozilla 開發人員網路：接受編碼</span><span class="sxs-lookup"><span data-stu-id="4577e-440">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="4577e-441">RFC 7231 區段3.1.2.1： Content Codings</span><span class="sxs-lookup"><span data-stu-id="4577e-441">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="4577e-442">RFC 7230 區段4.2.3： Gzip 編碼</span><span class="sxs-lookup"><span data-stu-id="4577e-442">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="4577e-443">GZIP 檔案格式規格版本4。3</span><span class="sxs-lookup"><span data-stu-id="4577e-443">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="4577e-444">網路頻寬是有限的資源。</span><span class="sxs-lookup"><span data-stu-id="4577e-444">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="4577e-445">減少回應的大小通常會增加應用程式的回應能力，通常會大幅增加。</span><span class="sxs-lookup"><span data-stu-id="4577e-445">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="4577e-446">減少承載大小的其中一種方式是壓縮應用程式的回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-446">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="4577e-447">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="4577e-447">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="4577e-448">使用回應壓縮中介軟體的時機</span><span class="sxs-lookup"><span data-stu-id="4577e-448">When to use Response Compression Middleware</span></span>

<span data-ttu-id="4577e-449">在 IIS、Apache 或 Nginx 中使用以伺服器為基礎的回應壓縮技術。</span><span class="sxs-lookup"><span data-stu-id="4577e-449">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="4577e-450">中介軟體的效能可能不符合伺服器模組的效能。</span><span class="sxs-lookup"><span data-stu-id="4577e-450">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="4577e-451">[HTTP.sys server](xref:fundamentals/servers/httpsys) Server 和 [Kestrel](xref:fundamentals/servers/kestrel) server 目前未提供內建壓縮支援。</span><span class="sxs-lookup"><span data-stu-id="4577e-451">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="4577e-452">當您在下列情況時，請使用回應壓縮中介軟體：</span><span class="sxs-lookup"><span data-stu-id="4577e-452">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="4577e-453">無法使用下列以伺服器為基礎的壓縮技術：</span><span class="sxs-lookup"><span data-stu-id="4577e-453">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="4577e-454">IIS 動態壓縮模組</span><span class="sxs-lookup"><span data-stu-id="4577e-454">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="4577e-455">Apache mod_deflate 模組</span><span class="sxs-lookup"><span data-stu-id="4577e-455">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="4577e-456">Nginx 壓縮和解壓縮</span><span class="sxs-lookup"><span data-stu-id="4577e-456">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="4577e-457">直接裝載于：</span><span class="sxs-lookup"><span data-stu-id="4577e-457">Hosting directly on:</span></span>
  * <span data-ttu-id="4577e-458">[HTTP.sys server](xref:fundamentals/servers/httpsys) (先前稱為 WebListener) </span><span class="sxs-lookup"><span data-stu-id="4577e-458">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="4577e-459">Kestrel 伺服器</span><span class="sxs-lookup"><span data-stu-id="4577e-459">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="4577e-460">回應壓縮</span><span class="sxs-lookup"><span data-stu-id="4577e-460">Response compression</span></span>

<span data-ttu-id="4577e-461">通常，任何未原生壓縮的回應都可受益于回應壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-461">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="4577e-462">未原生壓縮的回應通常包括： CSS、JavaScript、HTML、XML 和 JSON。</span><span class="sxs-lookup"><span data-stu-id="4577e-462">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="4577e-463">您不應壓縮原生壓縮的資產，例如 PNG 檔案。</span><span class="sxs-lookup"><span data-stu-id="4577e-463">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="4577e-464">如果您嘗試進一步壓縮原生壓縮的回應，則在處理壓縮所花費的時間內，可能會失色任何較小的額外縮減大小和傳輸時間。</span><span class="sxs-lookup"><span data-stu-id="4577e-464">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="4577e-465">請勿壓縮小於大約150-1000 個位元組 (的檔案，視檔案的內容和壓縮) 的效率而定。</span><span class="sxs-lookup"><span data-stu-id="4577e-465">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="4577e-466">壓縮小型檔案的額外負荷可能會產生大於未壓縮檔案的壓縮檔案。</span><span class="sxs-lookup"><span data-stu-id="4577e-466">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="4577e-467">當用戶端可以處理壓縮的內容時，用戶端必須將 `Accept-Encoding` 標頭與要求一起傳送，以通知伺服器其功能。</span><span class="sxs-lookup"><span data-stu-id="4577e-467">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="4577e-468">當伺服器傳送壓縮的內容時，它必須在 `Content-Encoding` 標頭中包含壓縮回應編碼方式的資訊。</span><span class="sxs-lookup"><span data-stu-id="4577e-468">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="4577e-469">下表顯示中介軟體所支援的內容編碼方式。</span><span class="sxs-lookup"><span data-stu-id="4577e-469">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="4577e-470">`Accept-Encoding` 標頭值</span><span class="sxs-lookup"><span data-stu-id="4577e-470">`Accept-Encoding` header values</span></span> | <span data-ttu-id="4577e-471">支援中介軟體</span><span class="sxs-lookup"><span data-stu-id="4577e-471">Middleware Supported</span></span> | <span data-ttu-id="4577e-472">描述</span><span class="sxs-lookup"><span data-stu-id="4577e-472">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="4577e-473">否</span><span class="sxs-lookup"><span data-stu-id="4577e-473">No</span></span>                   | [<span data-ttu-id="4577e-474">Brotli 壓縮的資料格式</span><span class="sxs-lookup"><span data-stu-id="4577e-474">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="4577e-475">否</span><span class="sxs-lookup"><span data-stu-id="4577e-475">No</span></span>                   | [<span data-ttu-id="4577e-476">DEFLATE 壓縮的資料格式</span><span class="sxs-lookup"><span data-stu-id="4577e-476">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="4577e-477">否</span><span class="sxs-lookup"><span data-stu-id="4577e-477">No</span></span>                   | [<span data-ttu-id="4577e-478">W3C 有效率的 XML 交換</span><span class="sxs-lookup"><span data-stu-id="4577e-478">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="4577e-479">是 (預設)</span><span class="sxs-lookup"><span data-stu-id="4577e-479">Yes (default)</span></span>        | [<span data-ttu-id="4577e-480">GZIP 檔案格式</span><span class="sxs-lookup"><span data-stu-id="4577e-480">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="4577e-481">是</span><span class="sxs-lookup"><span data-stu-id="4577e-481">Yes</span></span>                  | <span data-ttu-id="4577e-482">「無編碼」識別碼：回應不得編碼。</span><span class="sxs-lookup"><span data-stu-id="4577e-482">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="4577e-483">否</span><span class="sxs-lookup"><span data-stu-id="4577e-483">No</span></span>                   | [<span data-ttu-id="4577e-484">JAVA 封存的網路傳輸格式</span><span class="sxs-lookup"><span data-stu-id="4577e-484">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="4577e-485">是</span><span class="sxs-lookup"><span data-stu-id="4577e-485">Yes</span></span>                  | <span data-ttu-id="4577e-486">未明確要求任何可用的內容編碼</span><span class="sxs-lookup"><span data-stu-id="4577e-486">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="4577e-487">如需詳細資訊，請參閱 [IANA 官方內容編碼清單](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。</span><span class="sxs-lookup"><span data-stu-id="4577e-487">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="4577e-488">中介軟體可讓您為自訂標頭值新增額外的壓縮提供者 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-488">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="4577e-489">如需詳細資訊，請參閱下方的 [自訂提供者](#custom-providers) 。</span><span class="sxs-lookup"><span data-stu-id="4577e-489">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="4577e-490">在用戶端傳送壓縮配置的優先順序時，中介軟體能夠回應品質值 (qvalue， `q`) 加權。</span><span class="sxs-lookup"><span data-stu-id="4577e-490">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="4577e-491">如需詳細資訊，請參閱 [RFC 7231：接受編碼](https://tools.ietf.org/html/rfc7231#section-5.3.4)。</span><span class="sxs-lookup"><span data-stu-id="4577e-491">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="4577e-492">壓縮演算法受限於壓縮速度與壓縮效率之間的取捨。</span><span class="sxs-lookup"><span data-stu-id="4577e-492">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="4577e-493">此內容中的*有效性*是指壓縮之後的輸出大小。</span><span class="sxs-lookup"><span data-stu-id="4577e-493">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="4577e-494">最大的壓縮是以最 *理想* 的壓縮來達成。</span><span class="sxs-lookup"><span data-stu-id="4577e-494">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="4577e-495">下表說明要求、傳送、快取及接收壓縮內容所涉及的標頭。</span><span class="sxs-lookup"><span data-stu-id="4577e-495">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="4577e-496">標頭</span><span class="sxs-lookup"><span data-stu-id="4577e-496">Header</span></span>             | <span data-ttu-id="4577e-497">角色</span><span class="sxs-lookup"><span data-stu-id="4577e-497">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="4577e-498">從用戶端傳送到伺服器，以指出用戶端可接受的內容編碼配置。</span><span class="sxs-lookup"><span data-stu-id="4577e-498">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="4577e-499">從伺服器傳送至用戶端，以指出內容在承載中的編碼方式。</span><span class="sxs-lookup"><span data-stu-id="4577e-499">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="4577e-500">發生壓縮時， `Content-Length` 會移除標頭，因為本文內容會在回應壓縮時變更。</span><span class="sxs-lookup"><span data-stu-id="4577e-500">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="4577e-501">發生壓縮時， `Content-MD5` 會移除標頭，因為本文內容已變更，且雜湊不再有效。</span><span class="sxs-lookup"><span data-stu-id="4577e-501">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="4577e-502">指定內容的 MIME 類型。</span><span class="sxs-lookup"><span data-stu-id="4577e-502">Specifies the MIME type of the content.</span></span> <span data-ttu-id="4577e-503">每個回應都應指定其 `Content-Type` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-503">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="4577e-504">中介軟體會檢查此值，以判斷是否應該壓縮回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-504">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="4577e-505">中介軟體會指定一組可以編碼的 [預設 MIME 類型](#mime-types) ，但是您可以取代或加入 mime 類型。</span><span class="sxs-lookup"><span data-stu-id="4577e-505">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="4577e-506">當伺服器將值的傳 `Accept-Encoding` 給用戶端和 proxy 時， `Vary` 標頭會向用戶端或 proxy 指出它應該快取 (根據要求標頭的值來) 回應 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-506">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="4577e-507">使用標頭傳回內容的結果， `Vary: Accept-Encoding` 是分開快取壓縮和未壓縮的回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-507">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="4577e-508">使用 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)探索回應壓縮中介軟體的功能。</span><span class="sxs-lookup"><span data-stu-id="4577e-508">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="4577e-509">此範例說明：</span><span class="sxs-lookup"><span data-stu-id="4577e-509">The sample illustrates:</span></span>

* <span data-ttu-id="4577e-510">使用 Gzip 和自訂壓縮提供者壓縮應用程式回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-510">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="4577e-511">如何將 MIME 類型加入至預設的 MIME 類型清單以進行壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-511">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="4577e-512">Package</span><span class="sxs-lookup"><span data-stu-id="4577e-512">Package</span></span>

<span data-ttu-id="4577e-513">若要在專案中包含中介軟體，請新增 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app)的參考，其中包含 [AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) 套件。</span><span class="sxs-lookup"><span data-stu-id="4577e-513">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="4577e-514">組態</span><span class="sxs-lookup"><span data-stu-id="4577e-514">Configuration</span></span>

<span data-ttu-id="4577e-515">下列程式碼說明如何啟用預設 MIME 類型和 [Gzip 壓縮提供者](#gzip-compression-provider)的回應壓縮中介軟體：</span><span class="sxs-lookup"><span data-stu-id="4577e-515">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

<span data-ttu-id="4577e-516">注意：</span><span class="sxs-lookup"><span data-stu-id="4577e-516">Notes:</span></span>

* <span data-ttu-id="4577e-517">`app.UseResponseCompression` 必須在可壓縮回應的任何中介軟體之前呼叫。</span><span class="sxs-lookup"><span data-stu-id="4577e-517">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="4577e-518">如需詳細資訊，請參閱<xref:fundamentals/middleware/index#middleware-order>。</span><span class="sxs-lookup"><span data-stu-id="4577e-518">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="4577e-519">使用 [Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或 [Postman](https://www.getpostman.com/) 等工具來設定 `Accept-Encoding` 要求標頭，並研究回應標頭、大小和主體。</span><span class="sxs-lookup"><span data-stu-id="4577e-519">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="4577e-520">將要求提交給範例應用程式，而不使用 `Accept-Encoding` 標頭，並觀察回應是否未壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-520">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="4577e-521">`Content-Encoding`和 `Vary` 標頭不存在於回應中。</span><span class="sxs-lookup"><span data-stu-id="4577e-521">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![顯示要求結果（不含接受編碼標頭）的 Fiddler 視窗。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="4577e-524">使用標頭將要求提交至範例應用程式 `Accept-Encoding: gzip` ，並觀察回應已壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-524">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="4577e-525">`Content-Encoding`和 `Vary` 標頭出現在回應中。</span><span class="sxs-lookup"><span data-stu-id="4577e-525">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler 視窗顯示具有接受編碼標頭和值 gzip 的要求結果。](response-compression/_static/request-compressed.png)

## <a name="providers"></a><span data-ttu-id="4577e-529">提供者</span><span class="sxs-lookup"><span data-stu-id="4577e-529">Providers</span></span>

### <a name="gzip-compression-provider"></a><span data-ttu-id="4577e-530">Gzip 壓縮提供者</span><span class="sxs-lookup"><span data-stu-id="4577e-530">Gzip Compression Provider</span></span>

<span data-ttu-id="4577e-531">使用 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> 來壓縮具有 [GZIP 檔案格式](https://tools.ietf.org/html/rfc1952)的回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-531">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="4577e-532">如果未明確將壓縮提供者加入至 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> ：</span><span class="sxs-lookup"><span data-stu-id="4577e-532">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="4577e-533">預設會將 Gzip 壓縮提供者加入壓縮提供者的陣列中。</span><span class="sxs-lookup"><span data-stu-id="4577e-533">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="4577e-534">當用戶端支援 Gzip 壓縮時，壓縮預設為 Gzip。</span><span class="sxs-lookup"><span data-stu-id="4577e-534">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="4577e-535">明確新增任何壓縮提供者時，必須加入 Gzip 壓縮提供者：</span><span class="sxs-lookup"><span data-stu-id="4577e-535">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="4577e-536">使用設定壓縮等級 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> 。</span><span class="sxs-lookup"><span data-stu-id="4577e-536">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="4577e-537">Gzip 壓縮提供者預設為最快的壓縮層級 ([CompressionLevel。最快](xref:System.IO.Compression.CompressionLevel) 的) ，可能不會產生最有效率的壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-537">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="4577e-538">如果需要最有效率的壓縮，請設定中介軟體以獲得最佳壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-538">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="4577e-539">Compression Level</span><span class="sxs-lookup"><span data-stu-id="4577e-539">Compression Level</span></span> | <span data-ttu-id="4577e-540">描述</span><span class="sxs-lookup"><span data-stu-id="4577e-540">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="4577e-541">CompressionLevel。最快</span><span class="sxs-lookup"><span data-stu-id="4577e-541">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4577e-542">壓縮應該盡可能快速完成，即使產生的輸出未以最佳方式壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-542">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="4577e-543">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="4577e-543">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4577e-544">不應執行任何壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-544">No compression should be performed.</span></span> |
| [<span data-ttu-id="4577e-545">CompressionLevel。最佳</span><span class="sxs-lookup"><span data-stu-id="4577e-545">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4577e-546">即使壓縮需要更多時間才能完成，回應仍應以最佳方式壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-546">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a><span data-ttu-id="4577e-547">自訂提供者</span><span class="sxs-lookup"><span data-stu-id="4577e-547">Custom providers</span></span>

<span data-ttu-id="4577e-548">使用建立自訂壓縮 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> 。</span><span class="sxs-lookup"><span data-stu-id="4577e-548">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="4577e-549"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>代表這產生的內容編碼 `ICompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-549">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="4577e-550">中介軟體會使用這項資訊，根據要求標頭中指定的清單來選擇提供者 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-550">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="4577e-551">使用範例應用程式時，用戶端會以 `Accept-Encoding: mycustomcompression` 標頭提交要求。</span><span class="sxs-lookup"><span data-stu-id="4577e-551">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="4577e-552">中介軟體會使用自訂壓縮執行，並傳回含有 `Content-Encoding: mycustomcompression` 標頭的回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-552">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="4577e-553">用戶端必須能夠解壓縮自訂編碼，自訂壓縮執行才能運作。</span><span class="sxs-lookup"><span data-stu-id="4577e-553">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="4577e-554">使用標頭將要求提交至範例應用程式 `Accept-Encoding: mycustomcompression` ，並觀察回應標頭。</span><span class="sxs-lookup"><span data-stu-id="4577e-554">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="4577e-555">`Vary`和 `Content-Encoding` 標頭出現在回應中。</span><span class="sxs-lookup"><span data-stu-id="4577e-555">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="4577e-556">未 (顯示的回應主體) 不會被範例壓縮。</span><span class="sxs-lookup"><span data-stu-id="4577e-556">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="4577e-557">範例的類別中沒有壓縮的執行 `CustomCompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-557">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="4577e-558">不過，此範例會示範您要在哪裡執行這類壓縮演算法。</span><span class="sxs-lookup"><span data-stu-id="4577e-558">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 視窗顯示具有接受編碼標頭和值 mycustomcompression 的要求結果。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="4577e-561">MIME 類型</span><span class="sxs-lookup"><span data-stu-id="4577e-561">MIME types</span></span>

<span data-ttu-id="4577e-562">中介軟體會針對壓縮指定一組預設的 MIME 類型：</span><span class="sxs-lookup"><span data-stu-id="4577e-562">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="4577e-563">以回應壓縮中介軟體選項取代或附加 MIME 類型。</span><span class="sxs-lookup"><span data-stu-id="4577e-563">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="4577e-564">請注意，不支援萬用字元 MIME 類型（例如） `text/*` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-564">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="4577e-565">範例應用程式會新增的 MIME 類型 `image/svg+xml` ，並壓縮並提供 ASP.NET Core 橫幅影像 (的橫幅 *。 svg*) 。</span><span class="sxs-lookup"><span data-stu-id="4577e-565">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="4577e-566">使用安全通訊協定進行壓縮</span><span class="sxs-lookup"><span data-stu-id="4577e-566">Compression with secure protocol</span></span>

<span data-ttu-id="4577e-567">您可以使用預設停用的選項來控制透過安全連線的壓縮回應 `EnableForHttps` 。</span><span class="sxs-lookup"><span data-stu-id="4577e-567">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="4577e-568">使用壓縮動態產生的頁面可能會導致安全性問題，例如 [犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit)) 和 [缺口](https://wikipedia.org/wiki/BREACH_(security_exploit)) 攻擊。</span><span class="sxs-lookup"><span data-stu-id="4577e-568">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="4577e-569">新增 Vary 標頭</span><span class="sxs-lookup"><span data-stu-id="4577e-569">Adding the Vary header</span></span>

<span data-ttu-id="4577e-570">根據標頭壓縮回應時 `Accept-Encoding` ，可能會有多個壓縮版本的回應和未壓縮的版本。</span><span class="sxs-lookup"><span data-stu-id="4577e-570">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="4577e-571">為了指示用戶端和 proxy 快取有多個版本存在且應該儲存， `Vary` 標頭會加上 `Accept-Encoding` 值。</span><span class="sxs-lookup"><span data-stu-id="4577e-571">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="4577e-572">在 ASP.NET Core 2.0 或更新版本中，中介軟體 `Vary` 會在回應壓縮時自動新增標頭。</span><span class="sxs-lookup"><span data-stu-id="4577e-572">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="4577e-573">Nginx 反向 proxy 後方的中介軟體問題</span><span class="sxs-lookup"><span data-stu-id="4577e-573">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="4577e-574">當要求由 Nginx proxy 時， `Accept-Encoding` 會移除標頭。</span><span class="sxs-lookup"><span data-stu-id="4577e-574">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="4577e-575">移除 `Accept-Encoding` 標頭可防止中介軟體壓縮回應。</span><span class="sxs-lookup"><span data-stu-id="4577e-575">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="4577e-576">如需詳細資訊，請參閱 [NGINX：壓縮和解壓縮](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。</span><span class="sxs-lookup"><span data-stu-id="4577e-576">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="4577e-577">此問題的追蹤方式為 [Nginx (aspnet/BasicMiddleware #123) 的傳遞壓縮 ](https://github.com/aspnet/BasicMiddleware/issues/123)。</span><span class="sxs-lookup"><span data-stu-id="4577e-577">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="4577e-578">使用 IIS 動態壓縮</span><span class="sxs-lookup"><span data-stu-id="4577e-578">Working with IIS dynamic compression</span></span>

<span data-ttu-id="4577e-579">如果您在想要針對應用程式停用的伺服器層級上設定作用中的 IIS 動態壓縮模組，請停用 *web.config* 檔案以外的模組。</span><span class="sxs-lookup"><span data-stu-id="4577e-579">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="4577e-580">如需詳細資訊，請參閱[停用 IIS 模組](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="4577e-580">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="4577e-581">疑難排解</span><span class="sxs-lookup"><span data-stu-id="4577e-581">Troubleshooting</span></span>

<span data-ttu-id="4577e-582">使用 [Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或 [Postman](https://www.getpostman.com/)等工具，這可讓您設定 `Accept-Encoding` 要求標頭，並研究回應標頭、大小和主體。</span><span class="sxs-lookup"><span data-stu-id="4577e-582">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="4577e-583">根據預設，回應壓縮中介軟體會壓縮符合下列條件的回應：</span><span class="sxs-lookup"><span data-stu-id="4577e-583">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="4577e-584">`Accept-Encoding`標頭存在，其值為 `gzip` 、 `*` 或自訂編碼符合您所建立的自訂壓縮提供者。</span><span class="sxs-lookup"><span data-stu-id="4577e-584">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="4577e-585">值不得為 `identity` 或具有 (qvalue 的品質值， `q`) 設定為 0 (零) 。</span><span class="sxs-lookup"><span data-stu-id="4577e-585">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="4577e-586">必須設定 MIME 類型 (`Content-Type`) ，且必須符合中所設定的 mime 類型 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> 。</span><span class="sxs-lookup"><span data-stu-id="4577e-586">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="4577e-587">要求不能包括 `Content-Range` 標頭。</span><span class="sxs-lookup"><span data-stu-id="4577e-587">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="4577e-588">要求必須使用不安全的通訊協定 (HTTP) ，除非在回應壓縮中介軟體選項中設定了安全通訊協定 (HTTPs) 。</span><span class="sxs-lookup"><span data-stu-id="4577e-588">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="4577e-589">*在啟用安全內容壓縮時，請注意 [上述](#compression-with-secure-protocol) 的風險。*</span><span class="sxs-lookup"><span data-stu-id="4577e-589">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4577e-590">其他資源</span><span class="sxs-lookup"><span data-stu-id="4577e-590">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="4577e-591">Mozilla 開發人員網路：接受編碼</span><span class="sxs-lookup"><span data-stu-id="4577e-591">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="4577e-592">RFC 7231 區段3.1.2.1： Content Codings</span><span class="sxs-lookup"><span data-stu-id="4577e-592">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="4577e-593">RFC 7230 區段4.2.3： Gzip 編碼</span><span class="sxs-lookup"><span data-stu-id="4577e-593">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="4577e-594">GZIP 檔案格式規格版本4。3</span><span class="sxs-lookup"><span data-stu-id="4577e-594">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end
