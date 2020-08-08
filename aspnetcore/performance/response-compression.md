---
title: ASP.NET Core 中的回應壓縮
author: rick-anderson
description: 了解回應壓縮及如何使用 ASP.NET Core 應用程式中的回應壓縮中介軟體。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
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
ms.openlocfilehash: 1dd931d0ee654b888814df8a0d0675d32b5c3a20
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020960"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="6f7f6-103">ASP.NET Core 中的回應壓縮</span><span class="sxs-lookup"><span data-stu-id="6f7f6-103">Response compression in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6f7f6-104">網路頻寬是有限的資源。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-104">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="6f7f6-105">減少回應的大小通常會增加應用程式的回應性，通常會大幅提升。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-105">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="6f7f6-106">減少承載大小的其中一種方法是壓縮應用程式的回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-106">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="6f7f6-107">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="6f7f6-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="6f7f6-108">使用回應壓縮中介軟體的時機</span><span class="sxs-lookup"><span data-stu-id="6f7f6-108">When to use Response Compression Middleware</span></span>

<span data-ttu-id="6f7f6-109">在 IIS、Apache 或 Nginx 中使用以伺服器為基礎的回應壓縮技術。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-109">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="6f7f6-110">中介軟體的效能可能與伺服器模組不相符。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-110">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="6f7f6-111">[HTTP.sys 伺服器](xref:fundamentals/servers/httpsys)伺服器和[Kestrel](xref:fundamentals/servers/kestrel)伺服器目前未提供內建的壓縮支援。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-111">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="6f7f6-112">當您是時，請使用回應壓縮中介軟體：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-112">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="6f7f6-113">無法使用下列以伺服器為基礎的壓縮技術：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-113">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="6f7f6-114">IIS 動態壓縮模組</span><span class="sxs-lookup"><span data-stu-id="6f7f6-114">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="6f7f6-115">Apache mod_deflate 模組</span><span class="sxs-lookup"><span data-stu-id="6f7f6-115">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="6f7f6-116">Nginx 壓縮和解壓縮</span><span class="sxs-lookup"><span data-stu-id="6f7f6-116">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="6f7f6-117">直接裝載于：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-117">Hosting directly on:</span></span>
  * <span data-ttu-id="6f7f6-118">[HTTP.sys server](xref:fundamentals/servers/httpsys) (先前稱為 WebListener) </span><span class="sxs-lookup"><span data-stu-id="6f7f6-118">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="6f7f6-119">Kestrel 伺服器</span><span class="sxs-lookup"><span data-stu-id="6f7f6-119">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="6f7f6-120">回應壓縮</span><span class="sxs-lookup"><span data-stu-id="6f7f6-120">Response compression</span></span>

<span data-ttu-id="6f7f6-121">通常，任何未原生壓縮的回應都可以因回應壓縮而受益。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-121">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="6f7f6-122">原本不壓縮的回應通常包括： CSS、JavaScript、HTML、XML 和 JSON。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-122">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="6f7f6-123">您不應該壓縮原生壓縮的資產，例如 PNG 檔案。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-123">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="6f7f6-124">如果您嘗試進一步壓縮原生壓縮的回應，則在處理壓縮所花費的時間內，任何小型額外的大小和傳輸時間縮減可能會失色。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-124">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="6f7f6-125">請根據檔案的內容和壓縮) 的效率，不要壓縮小於150-1000 個位元組的檔案 (。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-125">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="6f7f6-126">壓縮小型檔案的額外負荷可能會產生比未壓縮檔案更大的壓縮檔案。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-126">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="6f7f6-127">當用戶端可以處理壓縮內容時，用戶端必須透過要求傳送標頭，以通知伺服器其功能 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-127">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="6f7f6-128">當伺服器傳送壓縮的內容時，它必須在標頭中包含有關 `Content-Encoding` 壓縮回應編碼方式的資訊。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-128">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="6f7f6-129">下表顯示中介軟體支援的內容編碼方式。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-129">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="6f7f6-130">`Accept-Encoding`標頭值</span><span class="sxs-lookup"><span data-stu-id="6f7f6-130">`Accept-Encoding` header values</span></span> | <span data-ttu-id="6f7f6-131">支援的中介軟體</span><span class="sxs-lookup"><span data-stu-id="6f7f6-131">Middleware Supported</span></span> | <span data-ttu-id="6f7f6-132">描述</span><span class="sxs-lookup"><span data-stu-id="6f7f6-132">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="6f7f6-133">是 (預設)</span><span class="sxs-lookup"><span data-stu-id="6f7f6-133">Yes (default)</span></span>        | [<span data-ttu-id="6f7f6-134">Brotli 壓縮資料格式</span><span class="sxs-lookup"><span data-stu-id="6f7f6-134">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="6f7f6-135">否</span><span class="sxs-lookup"><span data-stu-id="6f7f6-135">No</span></span>                   | [<span data-ttu-id="6f7f6-136">DEFLATE 壓縮資料格式</span><span class="sxs-lookup"><span data-stu-id="6f7f6-136">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="6f7f6-137">否</span><span class="sxs-lookup"><span data-stu-id="6f7f6-137">No</span></span>                   | [<span data-ttu-id="6f7f6-138">W3C 有效率的 XML 交換</span><span class="sxs-lookup"><span data-stu-id="6f7f6-138">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="6f7f6-139">是</span><span class="sxs-lookup"><span data-stu-id="6f7f6-139">Yes</span></span>                  | [<span data-ttu-id="6f7f6-140">GZIP 檔案格式</span><span class="sxs-lookup"><span data-stu-id="6f7f6-140">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="6f7f6-141">是</span><span class="sxs-lookup"><span data-stu-id="6f7f6-141">Yes</span></span>                  | <span data-ttu-id="6f7f6-142">「無編碼」識別碼：回應不得編碼。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-142">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="6f7f6-143">否</span><span class="sxs-lookup"><span data-stu-id="6f7f6-143">No</span></span>                   | [<span data-ttu-id="6f7f6-144">JAVA 封存的網路傳輸格式</span><span class="sxs-lookup"><span data-stu-id="6f7f6-144">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="6f7f6-145">是</span><span class="sxs-lookup"><span data-stu-id="6f7f6-145">Yes</span></span>                  | <span data-ttu-id="6f7f6-146">未明確要求任何可用的內容編碼</span><span class="sxs-lookup"><span data-stu-id="6f7f6-146">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="6f7f6-147">如需詳細資訊，請參閱[IANA 官方內容編碼清單](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-147">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="6f7f6-148">中介軟體可讓您新增自訂標頭值的其他壓縮提供者 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-148">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="6f7f6-149">如需詳細資訊，請參閱下面的[自訂提供者](#custom-providers)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-149">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="6f7f6-150">中介軟體能夠回應品質值 (qvalue，並 `q` 在用戶端傳送時) 加權，以設定壓縮配置的優先順序。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-150">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="6f7f6-151">如需詳細資訊，請參閱[RFC 7231：接受編碼](https://tools.ietf.org/html/rfc7231#section-5.3.4)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-151">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="6f7f6-152">壓縮演算法會受到壓縮速度與壓縮效率之間的取捨。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-152">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="6f7f6-153">此內容中的*有效性*是指壓縮之後的輸出大小。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-153">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="6f7f6-154">最大的大小是透過*最佳*壓縮來達成。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-154">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="6f7f6-155">下表說明有關要求、傳送、快取和接收壓縮內容的標頭。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-155">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="6f7f6-156">標頭</span><span class="sxs-lookup"><span data-stu-id="6f7f6-156">Header</span></span>             | <span data-ttu-id="6f7f6-157">角色</span><span class="sxs-lookup"><span data-stu-id="6f7f6-157">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="6f7f6-158">從用戶端傳送到伺服器，以指示用戶端可接受的內容編碼配置。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-158">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="6f7f6-159">從伺服器傳送到用戶端，以指出內容在承載中的編碼方式。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-159">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="6f7f6-160">當進行壓縮時，會 `Content-Length` 移除標頭，因為當回應壓縮時，本文內容會變更。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-160">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="6f7f6-161">當進行壓縮時，會 `Content-MD5` 移除標頭，因為本文內容已變更，而且雜湊已不再有效。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-161">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="6f7f6-162">指定內容的 MIME 類型。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-162">Specifies the MIME type of the content.</span></span> <span data-ttu-id="6f7f6-163">每個回應都應該指定其 `Content-Type` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-163">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="6f7f6-164">中介軟體會檢查此值，以判斷是否應該壓縮回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-164">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="6f7f6-165">中介軟體會指定一組可編碼的[預設 MIME 類型](#mime-types)，但您可以取代或加入 MIME 類型。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-165">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="6f7f6-166">當伺服器將的值傳送 `Accept-Encoding` 給用戶端和 proxy 時， `Vary` 標頭會向用戶端或 proxy 指出它應該快取的 (根據要求的標頭值來改變) 回應 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-166">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="6f7f6-167">傳回具有標頭之內容的結果 `Vary: Accept-Encoding` 是，會分別快取壓縮和未壓縮的回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-167">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="6f7f6-168">使用[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)探索回應壓縮中介軟體的功能。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-168">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="6f7f6-169">此範例說明：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-169">The sample illustrates:</span></span>

* <span data-ttu-id="6f7f6-170">使用 Gzip 和自訂壓縮提供者來壓縮應用程式回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-170">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="6f7f6-171">如何將 MIME 類型新增至 MIME 類型的預設清單以進行壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-171">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="6f7f6-172">Package</span><span class="sxs-lookup"><span data-stu-id="6f7f6-172">Package</span></span>

<span data-ttu-id="6f7f6-173">回應壓縮中介軟體是由[AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)套件所提供，它會隱含地包含在 ASP.NET Core 應用程式中。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-173">Response Compression Middleware is provided by the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="configuration"></a><span data-ttu-id="6f7f6-174">組態</span><span class="sxs-lookup"><span data-stu-id="6f7f6-174">Configuration</span></span>

<span data-ttu-id="6f7f6-175">下列程式碼示範如何針對預設 MIME 類型和壓縮提供者，啟用 ([Brotli](#brotli-compression-provider)和[Gzip](#gzip-compression-provider)) 的回應壓縮中介軟體：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-175">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

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

<span data-ttu-id="6f7f6-176">注意：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-176">Notes:</span></span>

* <span data-ttu-id="6f7f6-177">`app.UseResponseCompression`必須在壓縮回應的任何中介軟體之前呼叫。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-177">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="6f7f6-178">如需詳細資訊，請參閱<xref:fundamentals/middleware/index#middleware-order>。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-178">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="6f7f6-179">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)之類的工具來設定 `Accept-Encoding` 要求標頭，並研究回應標頭、大小和主體。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-179">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="6f7f6-180">不使用標頭將要求提交至範例應用程式 `Accept-Encoding` ，並觀察回應是否已解壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-180">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="6f7f6-181">`Content-Encoding`和 `Vary` 標頭不存在於回應中。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-181">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![顯示要求不含接受編碼標頭之結果的 Fiddler 視窗。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="6f7f6-184">使用標頭 (Brotli 壓縮) 將要求提交至範例應用程式 `Accept-Encoding: br` ，並觀察回應是否已壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-184">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="6f7f6-185">`Content-Encoding`和 `Vary` 標頭出現在回應中。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-185">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler 視窗，顯示具有接受編碼標頭和值為 br 之要求的結果。](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="6f7f6-189">提供者</span><span class="sxs-lookup"><span data-stu-id="6f7f6-189">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="6f7f6-190">Brotli 壓縮提供者</span><span class="sxs-lookup"><span data-stu-id="6f7f6-190">Brotli Compression Provider</span></span>

<span data-ttu-id="6f7f6-191">使用 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> 壓縮[Brotli 壓縮資料格式](https://tools.ietf.org/html/rfc7932)的回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-191">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="6f7f6-192">如果未將任何壓縮提供者明確新增至 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> ：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-192">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="6f7f6-193">Brotli 壓縮提供者預設會加入至壓縮提供者陣列，以及[Gzip 壓縮提供者](#gzip-compression-provider)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-193">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="6f7f6-194">當用戶端支援 Brotli 壓縮資料格式時，壓縮會預設為 Brotli 壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-194">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="6f7f6-195">如果用戶端不支援 Brotli，當用戶端支援 Gzip 壓縮時，壓縮會預設為 Gzip。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-195">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="6f7f6-196">明確新增任何壓縮提供者時，必須新增 Brotli 壓縮提供者：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-196">The Brotli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="6f7f6-197">使用設定壓縮等級 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions> 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-197">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="6f7f6-198">Brotli 壓縮提供者預設為最快速的壓縮層級 ([CompressionLevel。最快](xref:System.IO.Compression.CompressionLevel)的) ，這可能不會產生最有效率的壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-198">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="6f7f6-199">如果需要最有效率的壓縮，請設定中介軟體以獲得最佳壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-199">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="6f7f6-200">Compression Level</span><span class="sxs-lookup"><span data-stu-id="6f7f6-200">Compression Level</span></span> | <span data-ttu-id="6f7f6-201">描述</span><span class="sxs-lookup"><span data-stu-id="6f7f6-201">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="6f7f6-202">CompressionLevel。最快</span><span class="sxs-lookup"><span data-stu-id="6f7f6-202">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6f7f6-203">即使產生的輸出未以最佳方式壓縮，壓縮也應該儘快完成。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-203">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="6f7f6-204">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="6f7f6-204">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6f7f6-205">不應該執行壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-205">No compression should be performed.</span></span> |
| [<span data-ttu-id="6f7f6-206">CompressionLevel。最佳</span><span class="sxs-lookup"><span data-stu-id="6f7f6-206">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6f7f6-207">回應應以最佳方式壓縮，即使壓縮需要較長的時間才能完成也一樣。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-207">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="6f7f6-208">Gzip 壓縮提供者</span><span class="sxs-lookup"><span data-stu-id="6f7f6-208">Gzip Compression Provider</span></span>

<span data-ttu-id="6f7f6-209">使用 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> 來壓縮具有[GZIP 檔案格式](https://tools.ietf.org/html/rfc1952)的回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-209">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="6f7f6-210">如果未將任何壓縮提供者明確新增至 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> ：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-210">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="6f7f6-211">Gzip 壓縮提供者預設會加入壓縮提供者的陣列，以及[Brotli 壓縮提供者](#brotli-compression-provider)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-211">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="6f7f6-212">當用戶端支援 Brotli 壓縮資料格式時，壓縮會預設為 Brotli 壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-212">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="6f7f6-213">如果用戶端不支援 Brotli，當用戶端支援 Gzip 壓縮時，壓縮會預設為 Gzip。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-213">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="6f7f6-214">當明確新增任何壓縮提供者時，必須加入 Gzip 壓縮提供者：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-214">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="6f7f6-215">使用設定壓縮等級 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-215">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="6f7f6-216">Gzip 壓縮提供者預設為最快的壓縮層級 ([CompressionLevel。最快](xref:System.IO.Compression.CompressionLevel)的) ，這可能不會產生最有效率的壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-216">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="6f7f6-217">如果需要最有效率的壓縮，請設定中介軟體以獲得最佳壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-217">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="6f7f6-218">Compression Level</span><span class="sxs-lookup"><span data-stu-id="6f7f6-218">Compression Level</span></span> | <span data-ttu-id="6f7f6-219">描述</span><span class="sxs-lookup"><span data-stu-id="6f7f6-219">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="6f7f6-220">CompressionLevel。最快</span><span class="sxs-lookup"><span data-stu-id="6f7f6-220">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6f7f6-221">即使產生的輸出未以最佳方式壓縮，壓縮也應該儘快完成。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-221">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="6f7f6-222">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="6f7f6-222">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6f7f6-223">不應該執行壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-223">No compression should be performed.</span></span> |
| [<span data-ttu-id="6f7f6-224">CompressionLevel。最佳</span><span class="sxs-lookup"><span data-stu-id="6f7f6-224">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6f7f6-225">回應應以最佳方式壓縮，即使壓縮需要較長的時間才能完成也一樣。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-225">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="6f7f6-226">自訂提供者</span><span class="sxs-lookup"><span data-stu-id="6f7f6-226">Custom providers</span></span>

<span data-ttu-id="6f7f6-227">使用建立自訂壓縮 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-227">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="6f7f6-228"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>表示此產生的內容編碼 `ICompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-228">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="6f7f6-229">中介軟體會使用這項資訊，根據要求標頭中所指定的清單來選擇提供者 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-229">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="6f7f6-230">使用範例應用程式時，用戶端會提交含有 `Accept-Encoding: mycustomcompression` 標頭的要求。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-230">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="6f7f6-231">中介軟體會使用自訂壓縮實作為，並傳回具有 `Content-Encoding: mycustomcompression` 標頭的回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-231">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="6f7f6-232">用戶端必須能夠解壓縮自訂編碼，才能讓自訂壓縮實行正常執行。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-232">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]


<span data-ttu-id="6f7f6-233">使用標頭將要求提交至範例應用程式 `Accept-Encoding: mycustomcompression` ，並觀察回應標頭。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-233">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="6f7f6-234">`Vary`和 `Content-Encoding` 標頭出現在回應中。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-234">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="6f7f6-235"> (不會顯示回應主體，) 不會受到此範例的壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-235">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="6f7f6-236">範例的類別中沒有壓縮的執行 `CustomCompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-236">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="6f7f6-237">不過，此範例會顯示您將在哪裡執行這種壓縮演算法。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-237">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 視窗，顯示具有接受編碼標頭和值為 mycustomcompression 之要求的結果。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="6f7f6-240">MIME 類型</span><span class="sxs-lookup"><span data-stu-id="6f7f6-240">MIME types</span></span>

<span data-ttu-id="6f7f6-241">中介軟體會針對壓縮指定一組預設的 MIME 類型：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-241">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="6f7f6-242">以回應壓縮中介軟體選項取代或附加 MIME 類型。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-242">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="6f7f6-243">請注意，不支援萬用字元 MIME 類型，例如 `text/*` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-243">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="6f7f6-244">範例應用程式會為新增 MIME 類型 `image/svg+xml` ，並將 ASP.NET Core 橫幅影像壓縮並提供給 (的*橫幅。 svg*) 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-244">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="6f7f6-245">使用安全通訊協定進行壓縮</span><span class="sxs-lookup"><span data-stu-id="6f7f6-245">Compression with secure protocol</span></span>

<span data-ttu-id="6f7f6-246">透過安全連線的壓縮回應可以使用選項來控制 `EnableForHttps` ，預設為停用。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-246">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="6f7f6-247">以動態產生的頁面使用壓縮，可能會導致安全性問題，例如[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))和[入侵](https://wikipedia.org/wiki/BREACH_(security_exploit))攻擊。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-247">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="6f7f6-248">新增 Vary 標頭</span><span class="sxs-lookup"><span data-stu-id="6f7f6-248">Adding the Vary header</span></span>

<span data-ttu-id="6f7f6-249">根據標頭壓縮回應時 `Accept-Encoding` ，可能會有多個壓縮版本的回應和未壓縮的版本。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-249">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="6f7f6-250">若要指示用戶端和 proxy 快取有多個版本存在且應該儲存，則 `Vary` 標頭會加上 `Accept-Encoding` 值。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-250">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="6f7f6-251">在 ASP.NET Core 2.0 或更新版本中，中介軟體 `Vary` 會在回應壓縮時自動新增標頭。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-251">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="6f7f6-252">Nginx 反向 proxy 後方發生中介軟體問題</span><span class="sxs-lookup"><span data-stu-id="6f7f6-252">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="6f7f6-253">當要求由 Nginx proxy 時， `Accept-Encoding` 會移除標頭。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-253">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="6f7f6-254">移除 `Accept-Encoding` 標頭可防止中介軟體壓縮回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-254">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="6f7f6-255">如需詳細資訊，請參閱[NGINX：壓縮和解壓縮](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-255">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="6f7f6-256">此問題的追蹤方式是藉由[瞭解 Nginx (aspnet/BasicMiddleware #123) 的傳遞壓縮](https://github.com/aspnet/BasicMiddleware/issues/123)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-256">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="6f7f6-257">使用 IIS 動態壓縮</span><span class="sxs-lookup"><span data-stu-id="6f7f6-257">Working with IIS dynamic compression</span></span>

<span data-ttu-id="6f7f6-258">如果您在想要針對應用程式停用的伺服器層級上設定作用中的 IIS 動態壓縮模組，請停用*web.config*檔案以外的模組。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-258">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="6f7f6-259">如需詳細資訊，請參閱[停用 IIS 模組](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-259">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="6f7f6-260">疑難排解</span><span class="sxs-lookup"><span data-stu-id="6f7f6-260">Troubleshooting</span></span>

<span data-ttu-id="6f7f6-261">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)等工具，可讓您設定 `Accept-Encoding` 要求標頭，並研究回應標頭、大小和主體。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-261">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="6f7f6-262">根據預設，回應壓縮中介軟體會壓縮符合下列條件的回應：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-262">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="6f7f6-263">`Accept-Encoding`標頭存在，其值為 `br` 、 `gzip` 、 `*` 或自訂編碼，其符合您所建立的自訂壓縮提供者。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-263">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="6f7f6-264">此值不得為 `identity` 或具有品質值 (qvalue， `q`) 設定為 0 (零) 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-264">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="6f7f6-265">必須設定 MIME 類型 (`Content-Type`) ，而且必須符合在上設定的 mime 類型 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-265">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="6f7f6-266">要求不能包含 `Content-Range` 標頭。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-266">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="6f7f6-267">除非在回應壓縮中介軟體選項中設定了安全通訊協定 (HTTPs) ，否則要求必須使用不安全的通訊協定 (HTTP) 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-267">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="6f7f6-268">*請注意，在啟用安全內容壓縮時，[上述](#compression-with-secure-protocol)的危險。*</span><span class="sxs-lookup"><span data-stu-id="6f7f6-268">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f7f6-269">其他資源</span><span class="sxs-lookup"><span data-stu-id="6f7f6-269">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="6f7f6-270">Mozilla 開發人員網路：接受編碼</span><span class="sxs-lookup"><span data-stu-id="6f7f6-270">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="6f7f6-271">RFC 7231 區段3.1.2.1： Content Codings</span><span class="sxs-lookup"><span data-stu-id="6f7f6-271">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="6f7f6-272">RFC 7230 區段4.2.3： Gzip 編碼</span><span class="sxs-lookup"><span data-stu-id="6f7f6-272">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="6f7f6-273">GZIP 檔案格式規格版本4。3</span><span class="sxs-lookup"><span data-stu-id="6f7f6-273">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="6f7f6-274">網路頻寬是有限的資源。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-274">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="6f7f6-275">減少回應的大小通常會增加應用程式的回應性，通常會大幅提升。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-275">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="6f7f6-276">減少承載大小的其中一種方法是壓縮應用程式的回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-276">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="6f7f6-277">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="6f7f6-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="6f7f6-278">使用回應壓縮中介軟體的時機</span><span class="sxs-lookup"><span data-stu-id="6f7f6-278">When to use Response Compression Middleware</span></span>

<span data-ttu-id="6f7f6-279">在 IIS、Apache 或 Nginx 中使用以伺服器為基礎的回應壓縮技術。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-279">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="6f7f6-280">中介軟體的效能可能與伺服器模組不相符。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-280">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="6f7f6-281">[HTTP.sys 伺服器](xref:fundamentals/servers/httpsys)伺服器和[Kestrel](xref:fundamentals/servers/kestrel)伺服器目前未提供內建的壓縮支援。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-281">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="6f7f6-282">當您是時，請使用回應壓縮中介軟體：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-282">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="6f7f6-283">無法使用下列以伺服器為基礎的壓縮技術：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-283">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="6f7f6-284">IIS 動態壓縮模組</span><span class="sxs-lookup"><span data-stu-id="6f7f6-284">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="6f7f6-285">Apache mod_deflate 模組</span><span class="sxs-lookup"><span data-stu-id="6f7f6-285">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="6f7f6-286">Nginx 壓縮和解壓縮</span><span class="sxs-lookup"><span data-stu-id="6f7f6-286">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="6f7f6-287">直接裝載于：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-287">Hosting directly on:</span></span>
  * <span data-ttu-id="6f7f6-288">[HTTP.sys server](xref:fundamentals/servers/httpsys) (先前稱為 WebListener) </span><span class="sxs-lookup"><span data-stu-id="6f7f6-288">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="6f7f6-289">Kestrel 伺服器</span><span class="sxs-lookup"><span data-stu-id="6f7f6-289">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="6f7f6-290">回應壓縮</span><span class="sxs-lookup"><span data-stu-id="6f7f6-290">Response compression</span></span>

<span data-ttu-id="6f7f6-291">通常，任何未原生壓縮的回應都可以因回應壓縮而受益。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-291">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="6f7f6-292">原本不壓縮的回應通常包括： CSS、JavaScript、HTML、XML 和 JSON。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-292">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="6f7f6-293">您不應該壓縮原生壓縮的資產，例如 PNG 檔案。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-293">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="6f7f6-294">如果您嘗試進一步壓縮原生壓縮的回應，則在處理壓縮所花費的時間內，任何小型額外的大小和傳輸時間縮減可能會失色。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-294">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="6f7f6-295">請根據檔案的內容和壓縮) 的效率，不要壓縮小於150-1000 個位元組的檔案 (。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-295">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="6f7f6-296">壓縮小型檔案的額外負荷可能會產生比未壓縮檔案更大的壓縮檔案。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-296">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="6f7f6-297">當用戶端可以處理壓縮內容時，用戶端必須透過要求傳送標頭，以通知伺服器其功能 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-297">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="6f7f6-298">當伺服器傳送壓縮的內容時，它必須在標頭中包含有關 `Content-Encoding` 壓縮回應編碼方式的資訊。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-298">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="6f7f6-299">下表顯示中介軟體支援的內容編碼方式。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-299">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="6f7f6-300">`Accept-Encoding`標頭值</span><span class="sxs-lookup"><span data-stu-id="6f7f6-300">`Accept-Encoding` header values</span></span> | <span data-ttu-id="6f7f6-301">支援的中介軟體</span><span class="sxs-lookup"><span data-stu-id="6f7f6-301">Middleware Supported</span></span> | <span data-ttu-id="6f7f6-302">描述</span><span class="sxs-lookup"><span data-stu-id="6f7f6-302">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="6f7f6-303">是 (預設)</span><span class="sxs-lookup"><span data-stu-id="6f7f6-303">Yes (default)</span></span>        | [<span data-ttu-id="6f7f6-304">Brotli 壓縮資料格式</span><span class="sxs-lookup"><span data-stu-id="6f7f6-304">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="6f7f6-305">否</span><span class="sxs-lookup"><span data-stu-id="6f7f6-305">No</span></span>                   | [<span data-ttu-id="6f7f6-306">DEFLATE 壓縮資料格式</span><span class="sxs-lookup"><span data-stu-id="6f7f6-306">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="6f7f6-307">否</span><span class="sxs-lookup"><span data-stu-id="6f7f6-307">No</span></span>                   | [<span data-ttu-id="6f7f6-308">W3C 有效率的 XML 交換</span><span class="sxs-lookup"><span data-stu-id="6f7f6-308">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="6f7f6-309">是</span><span class="sxs-lookup"><span data-stu-id="6f7f6-309">Yes</span></span>                  | [<span data-ttu-id="6f7f6-310">GZIP 檔案格式</span><span class="sxs-lookup"><span data-stu-id="6f7f6-310">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="6f7f6-311">是</span><span class="sxs-lookup"><span data-stu-id="6f7f6-311">Yes</span></span>                  | <span data-ttu-id="6f7f6-312">「無編碼」識別碼：回應不得編碼。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-312">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="6f7f6-313">否</span><span class="sxs-lookup"><span data-stu-id="6f7f6-313">No</span></span>                   | [<span data-ttu-id="6f7f6-314">JAVA 封存的網路傳輸格式</span><span class="sxs-lookup"><span data-stu-id="6f7f6-314">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="6f7f6-315">是</span><span class="sxs-lookup"><span data-stu-id="6f7f6-315">Yes</span></span>                  | <span data-ttu-id="6f7f6-316">未明確要求任何可用的內容編碼</span><span class="sxs-lookup"><span data-stu-id="6f7f6-316">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="6f7f6-317">如需詳細資訊，請參閱[IANA 官方內容編碼清單](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-317">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="6f7f6-318">中介軟體可讓您新增自訂標頭值的其他壓縮提供者 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-318">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="6f7f6-319">如需詳細資訊，請參閱下面的[自訂提供者](#custom-providers)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-319">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="6f7f6-320">中介軟體能夠回應品質值 (qvalue，並 `q` 在用戶端傳送時) 加權，以設定壓縮配置的優先順序。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-320">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="6f7f6-321">如需詳細資訊，請參閱[RFC 7231：接受編碼](https://tools.ietf.org/html/rfc7231#section-5.3.4)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-321">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="6f7f6-322">壓縮演算法會受到壓縮速度與壓縮效率之間的取捨。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-322">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="6f7f6-323">此內容中的*有效性*是指壓縮之後的輸出大小。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-323">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="6f7f6-324">最大的大小是透過*最佳*壓縮來達成。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-324">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="6f7f6-325">下表說明有關要求、傳送、快取和接收壓縮內容的標頭。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-325">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="6f7f6-326">標頭</span><span class="sxs-lookup"><span data-stu-id="6f7f6-326">Header</span></span>             | <span data-ttu-id="6f7f6-327">角色</span><span class="sxs-lookup"><span data-stu-id="6f7f6-327">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="6f7f6-328">從用戶端傳送到伺服器，以指示用戶端可接受的內容編碼配置。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-328">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="6f7f6-329">從伺服器傳送到用戶端，以指出內容在承載中的編碼方式。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-329">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="6f7f6-330">當進行壓縮時，會 `Content-Length` 移除標頭，因為當回應壓縮時，本文內容會變更。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-330">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="6f7f6-331">當進行壓縮時，會 `Content-MD5` 移除標頭，因為本文內容已變更，而且雜湊已不再有效。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-331">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="6f7f6-332">指定內容的 MIME 類型。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-332">Specifies the MIME type of the content.</span></span> <span data-ttu-id="6f7f6-333">每個回應都應該指定其 `Content-Type` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-333">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="6f7f6-334">中介軟體會檢查此值，以判斷是否應該壓縮回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-334">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="6f7f6-335">中介軟體會指定一組可編碼的[預設 MIME 類型](#mime-types)，但您可以取代或加入 MIME 類型。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-335">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="6f7f6-336">當伺服器將的值傳送 `Accept-Encoding` 給用戶端和 proxy 時， `Vary` 標頭會向用戶端或 proxy 指出它應該快取的 (根據要求的標頭值來改變) 回應 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-336">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="6f7f6-337">傳回具有標頭之內容的結果 `Vary: Accept-Encoding` 是，會分別快取壓縮和未壓縮的回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-337">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="6f7f6-338">使用[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)探索回應壓縮中介軟體的功能。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-338">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="6f7f6-339">此範例說明：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-339">The sample illustrates:</span></span>

* <span data-ttu-id="6f7f6-340">使用 Gzip 和自訂壓縮提供者來壓縮應用程式回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-340">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="6f7f6-341">如何將 MIME 類型新增至 MIME 類型的預設清單以進行壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-341">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="6f7f6-342">Package</span><span class="sxs-lookup"><span data-stu-id="6f7f6-342">Package</span></span>

<span data-ttu-id="6f7f6-343">若要在專案中包含中介軟體，請新增[AspNetCore 應用程式中繼套件](xref:fundamentals/metapackage-app)的參考，其中包含[AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)封裝。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-343">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="6f7f6-344">組態</span><span class="sxs-lookup"><span data-stu-id="6f7f6-344">Configuration</span></span>

<span data-ttu-id="6f7f6-345">下列程式碼示範如何針對預設 MIME 類型和壓縮提供者，啟用 ([Brotli](#brotli-compression-provider)和[Gzip](#gzip-compression-provider)) 的回應壓縮中介軟體：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-345">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

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

<span data-ttu-id="6f7f6-346">注意：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-346">Notes:</span></span>

* <span data-ttu-id="6f7f6-347">`app.UseResponseCompression`必須在壓縮回應的任何中介軟體之前呼叫。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-347">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="6f7f6-348">如需詳細資訊，請參閱<xref:fundamentals/middleware/index#middleware-order>。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-348">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="6f7f6-349">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)之類的工具來設定 `Accept-Encoding` 要求標頭，並研究回應標頭、大小和主體。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-349">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="6f7f6-350">不使用標頭將要求提交至範例應用程式 `Accept-Encoding` ，並觀察回應是否已解壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-350">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="6f7f6-351">`Content-Encoding`和 `Vary` 標頭不存在於回應中。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-351">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![顯示要求不含接受編碼標頭之結果的 Fiddler 視窗。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="6f7f6-354">使用標頭 (Brotli 壓縮) 將要求提交至範例應用程式 `Accept-Encoding: br` ，並觀察回應是否已壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-354">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="6f7f6-355">`Content-Encoding`和 `Vary` 標頭出現在回應中。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-355">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler 視窗，顯示具有接受編碼標頭和值為 br 之要求的結果。](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="6f7f6-359">提供者</span><span class="sxs-lookup"><span data-stu-id="6f7f6-359">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="6f7f6-360">Brotli 壓縮提供者</span><span class="sxs-lookup"><span data-stu-id="6f7f6-360">Brotli Compression Provider</span></span>

<span data-ttu-id="6f7f6-361">使用 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> 壓縮[Brotli 壓縮資料格式](https://tools.ietf.org/html/rfc7932)的回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-361">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="6f7f6-362">如果未將任何壓縮提供者明確新增至 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> ：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-362">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="6f7f6-363">Brotli 壓縮提供者預設會加入至壓縮提供者陣列，以及[Gzip 壓縮提供者](#gzip-compression-provider)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-363">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="6f7f6-364">當用戶端支援 Brotli 壓縮資料格式時，壓縮會預設為 Brotli 壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-364">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="6f7f6-365">如果用戶端不支援 Brotli，當用戶端支援 Gzip 壓縮時，壓縮會預設為 Gzip。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-365">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="6f7f6-366">明確新增任何壓縮提供者時，必須新增 Brotli 壓縮提供者：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-366">The Brotli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="6f7f6-367">使用設定壓縮等級 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions> 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-367">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="6f7f6-368">Brotli 壓縮提供者預設為最快速的壓縮層級 ([CompressionLevel。最快](xref:System.IO.Compression.CompressionLevel)的) ，這可能不會產生最有效率的壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-368">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="6f7f6-369">如果需要最有效率的壓縮，請設定中介軟體以獲得最佳壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-369">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="6f7f6-370">Compression Level</span><span class="sxs-lookup"><span data-stu-id="6f7f6-370">Compression Level</span></span> | <span data-ttu-id="6f7f6-371">描述</span><span class="sxs-lookup"><span data-stu-id="6f7f6-371">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="6f7f6-372">CompressionLevel。最快</span><span class="sxs-lookup"><span data-stu-id="6f7f6-372">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6f7f6-373">即使產生的輸出未以最佳方式壓縮，壓縮也應該儘快完成。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-373">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="6f7f6-374">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="6f7f6-374">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6f7f6-375">不應該執行壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-375">No compression should be performed.</span></span> |
| [<span data-ttu-id="6f7f6-376">CompressionLevel。最佳</span><span class="sxs-lookup"><span data-stu-id="6f7f6-376">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6f7f6-377">回應應以最佳方式壓縮，即使壓縮需要較長的時間才能完成也一樣。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-377">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="6f7f6-378">Gzip 壓縮提供者</span><span class="sxs-lookup"><span data-stu-id="6f7f6-378">Gzip Compression Provider</span></span>

<span data-ttu-id="6f7f6-379">使用 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> 來壓縮具有[GZIP 檔案格式](https://tools.ietf.org/html/rfc1952)的回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-379">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="6f7f6-380">如果未將任何壓縮提供者明確新增至 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> ：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-380">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="6f7f6-381">Gzip 壓縮提供者預設會加入壓縮提供者的陣列，以及[Brotli 壓縮提供者](#brotli-compression-provider)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-381">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="6f7f6-382">當用戶端支援 Brotli 壓縮資料格式時，壓縮會預設為 Brotli 壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-382">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="6f7f6-383">如果用戶端不支援 Brotli，當用戶端支援 Gzip 壓縮時，壓縮會預設為 Gzip。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-383">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="6f7f6-384">當明確新增任何壓縮提供者時，必須加入 Gzip 壓縮提供者：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-384">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="6f7f6-385">使用設定壓縮等級 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-385">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="6f7f6-386">Gzip 壓縮提供者預設為最快的壓縮層級 ([CompressionLevel。最快](xref:System.IO.Compression.CompressionLevel)的) ，這可能不會產生最有效率的壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-386">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="6f7f6-387">如果需要最有效率的壓縮，請設定中介軟體以獲得最佳壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-387">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="6f7f6-388">Compression Level</span><span class="sxs-lookup"><span data-stu-id="6f7f6-388">Compression Level</span></span> | <span data-ttu-id="6f7f6-389">描述</span><span class="sxs-lookup"><span data-stu-id="6f7f6-389">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="6f7f6-390">CompressionLevel。最快</span><span class="sxs-lookup"><span data-stu-id="6f7f6-390">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6f7f6-391">即使產生的輸出未以最佳方式壓縮，壓縮也應該儘快完成。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-391">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="6f7f6-392">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="6f7f6-392">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6f7f6-393">不應該執行壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-393">No compression should be performed.</span></span> |
| [<span data-ttu-id="6f7f6-394">CompressionLevel。最佳</span><span class="sxs-lookup"><span data-stu-id="6f7f6-394">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6f7f6-395">回應應以最佳方式壓縮，即使壓縮需要較長的時間才能完成也一樣。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-395">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="6f7f6-396">自訂提供者</span><span class="sxs-lookup"><span data-stu-id="6f7f6-396">Custom providers</span></span>

<span data-ttu-id="6f7f6-397">使用建立自訂壓縮 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-397">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="6f7f6-398"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>表示此產生的內容編碼 `ICompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-398">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="6f7f6-399">中介軟體會使用這項資訊，根據要求標頭中所指定的清單來選擇提供者 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-399">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="6f7f6-400">使用範例應用程式時，用戶端會提交含有 `Accept-Encoding: mycustomcompression` 標頭的要求。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-400">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="6f7f6-401">中介軟體會使用自訂壓縮實作為，並傳回具有 `Content-Encoding: mycustomcompression` 標頭的回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-401">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="6f7f6-402">用戶端必須能夠解壓縮自訂編碼，才能讓自訂壓縮實行正常執行。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-402">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="6f7f6-403">使用標頭將要求提交至範例應用程式 `Accept-Encoding: mycustomcompression` ，並觀察回應標頭。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-403">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="6f7f6-404">`Vary`和 `Content-Encoding` 標頭出現在回應中。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-404">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="6f7f6-405"> (不會顯示回應主體，) 不會受到此範例的壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-405">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="6f7f6-406">範例的類別中沒有壓縮的執行 `CustomCompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-406">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="6f7f6-407">不過，此範例會顯示您將在哪裡執行這種壓縮演算法。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-407">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 視窗，顯示具有接受編碼標頭和值為 mycustomcompression 之要求的結果。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="6f7f6-410">MIME 類型</span><span class="sxs-lookup"><span data-stu-id="6f7f6-410">MIME types</span></span>

<span data-ttu-id="6f7f6-411">中介軟體會針對壓縮指定一組預設的 MIME 類型：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-411">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="6f7f6-412">以回應壓縮中介軟體選項取代或附加 MIME 類型。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-412">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="6f7f6-413">請注意，不支援萬用字元 MIME 類型，例如 `text/*` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-413">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="6f7f6-414">範例應用程式會為新增 MIME 類型 `image/svg+xml` ，並將 ASP.NET Core 橫幅影像壓縮並提供給 (的*橫幅。 svg*) 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-414">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="6f7f6-415">使用安全通訊協定進行壓縮</span><span class="sxs-lookup"><span data-stu-id="6f7f6-415">Compression with secure protocol</span></span>

<span data-ttu-id="6f7f6-416">透過安全連線的壓縮回應可以使用選項來控制 `EnableForHttps` ，預設為停用。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-416">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="6f7f6-417">以動態產生的頁面使用壓縮，可能會導致安全性問題，例如[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))和[入侵](https://wikipedia.org/wiki/BREACH_(security_exploit))攻擊。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-417">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="6f7f6-418">新增 Vary 標頭</span><span class="sxs-lookup"><span data-stu-id="6f7f6-418">Adding the Vary header</span></span>

<span data-ttu-id="6f7f6-419">根據標頭壓縮回應時 `Accept-Encoding` ，可能會有多個壓縮版本的回應和未壓縮的版本。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-419">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="6f7f6-420">若要指示用戶端和 proxy 快取有多個版本存在且應該儲存，則 `Vary` 標頭會加上 `Accept-Encoding` 值。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-420">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="6f7f6-421">在 ASP.NET Core 2.0 或更新版本中，中介軟體 `Vary` 會在回應壓縮時自動新增標頭。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-421">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="6f7f6-422">Nginx 反向 proxy 後方發生中介軟體問題</span><span class="sxs-lookup"><span data-stu-id="6f7f6-422">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="6f7f6-423">當要求由 Nginx proxy 時， `Accept-Encoding` 會移除標頭。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-423">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="6f7f6-424">移除 `Accept-Encoding` 標頭可防止中介軟體壓縮回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-424">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="6f7f6-425">如需詳細資訊，請參閱[NGINX：壓縮和解壓縮](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-425">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="6f7f6-426">此問題的追蹤方式是藉由[瞭解 Nginx (aspnet/BasicMiddleware #123) 的傳遞壓縮](https://github.com/aspnet/BasicMiddleware/issues/123)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-426">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="6f7f6-427">使用 IIS 動態壓縮</span><span class="sxs-lookup"><span data-stu-id="6f7f6-427">Working with IIS dynamic compression</span></span>

<span data-ttu-id="6f7f6-428">如果您在想要針對應用程式停用的伺服器層級上設定作用中的 IIS 動態壓縮模組，請停用*web.config*檔案以外的模組。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-428">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="6f7f6-429">如需詳細資訊，請參閱[停用 IIS 模組](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-429">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="6f7f6-430">疑難排解</span><span class="sxs-lookup"><span data-stu-id="6f7f6-430">Troubleshooting</span></span>

<span data-ttu-id="6f7f6-431">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)等工具，可讓您設定 `Accept-Encoding` 要求標頭，並研究回應標頭、大小和主體。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-431">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="6f7f6-432">根據預設，回應壓縮中介軟體會壓縮符合下列條件的回應：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-432">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="6f7f6-433">`Accept-Encoding`標頭存在，其值為 `br` 、 `gzip` 、 `*` 或自訂編碼，其符合您所建立的自訂壓縮提供者。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-433">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="6f7f6-434">此值不得為 `identity` 或具有品質值 (qvalue， `q`) 設定為 0 (零) 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-434">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="6f7f6-435">必須設定 MIME 類型 (`Content-Type`) ，而且必須符合在上設定的 mime 類型 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-435">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="6f7f6-436">要求不能包含 `Content-Range` 標頭。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-436">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="6f7f6-437">除非在回應壓縮中介軟體選項中設定了安全通訊協定 (HTTPs) ，否則要求必須使用不安全的通訊協定 (HTTP) 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-437">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="6f7f6-438">*請注意，在啟用安全內容壓縮時，[上述](#compression-with-secure-protocol)的危險。*</span><span class="sxs-lookup"><span data-stu-id="6f7f6-438">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f7f6-439">其他資源</span><span class="sxs-lookup"><span data-stu-id="6f7f6-439">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="6f7f6-440">Mozilla 開發人員網路：接受編碼</span><span class="sxs-lookup"><span data-stu-id="6f7f6-440">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="6f7f6-441">RFC 7231 區段3.1.2.1： Content Codings</span><span class="sxs-lookup"><span data-stu-id="6f7f6-441">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="6f7f6-442">RFC 7230 區段4.2.3： Gzip 編碼</span><span class="sxs-lookup"><span data-stu-id="6f7f6-442">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="6f7f6-443">GZIP 檔案格式規格版本4。3</span><span class="sxs-lookup"><span data-stu-id="6f7f6-443">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="6f7f6-444">網路頻寬是有限的資源。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-444">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="6f7f6-445">減少回應的大小通常會增加應用程式的回應性，通常會大幅提升。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-445">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="6f7f6-446">減少承載大小的其中一種方法是壓縮應用程式的回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-446">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="6f7f6-447">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="6f7f6-447">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="6f7f6-448">使用回應壓縮中介軟體的時機</span><span class="sxs-lookup"><span data-stu-id="6f7f6-448">When to use Response Compression Middleware</span></span>

<span data-ttu-id="6f7f6-449">在 IIS、Apache 或 Nginx 中使用以伺服器為基礎的回應壓縮技術。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-449">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="6f7f6-450">中介軟體的效能可能與伺服器模組不相符。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-450">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="6f7f6-451">[HTTP.sys 伺服器](xref:fundamentals/servers/httpsys)伺服器和[Kestrel](xref:fundamentals/servers/kestrel)伺服器目前未提供內建的壓縮支援。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-451">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="6f7f6-452">當您是時，請使用回應壓縮中介軟體：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-452">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="6f7f6-453">無法使用下列以伺服器為基礎的壓縮技術：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-453">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="6f7f6-454">IIS 動態壓縮模組</span><span class="sxs-lookup"><span data-stu-id="6f7f6-454">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="6f7f6-455">Apache mod_deflate 模組</span><span class="sxs-lookup"><span data-stu-id="6f7f6-455">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="6f7f6-456">Nginx 壓縮和解壓縮</span><span class="sxs-lookup"><span data-stu-id="6f7f6-456">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="6f7f6-457">直接裝載于：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-457">Hosting directly on:</span></span>
  * <span data-ttu-id="6f7f6-458">[HTTP.sys server](xref:fundamentals/servers/httpsys) (先前稱為 WebListener) </span><span class="sxs-lookup"><span data-stu-id="6f7f6-458">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="6f7f6-459">Kestrel 伺服器</span><span class="sxs-lookup"><span data-stu-id="6f7f6-459">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="6f7f6-460">回應壓縮</span><span class="sxs-lookup"><span data-stu-id="6f7f6-460">Response compression</span></span>

<span data-ttu-id="6f7f6-461">通常，任何未原生壓縮的回應都可以因回應壓縮而受益。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-461">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="6f7f6-462">原本不壓縮的回應通常包括： CSS、JavaScript、HTML、XML 和 JSON。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-462">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="6f7f6-463">您不應該壓縮原生壓縮的資產，例如 PNG 檔案。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-463">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="6f7f6-464">如果您嘗試進一步壓縮原生壓縮的回應，則在處理壓縮所花費的時間內，任何小型額外的大小和傳輸時間縮減可能會失色。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-464">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="6f7f6-465">請根據檔案的內容和壓縮) 的效率，不要壓縮小於150-1000 個位元組的檔案 (。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-465">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="6f7f6-466">壓縮小型檔案的額外負荷可能會產生比未壓縮檔案更大的壓縮檔案。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-466">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="6f7f6-467">當用戶端可以處理壓縮內容時，用戶端必須透過要求傳送標頭，以通知伺服器其功能 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-467">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="6f7f6-468">當伺服器傳送壓縮的內容時，它必須在標頭中包含有關 `Content-Encoding` 壓縮回應編碼方式的資訊。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-468">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="6f7f6-469">下表顯示中介軟體支援的內容編碼方式。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-469">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="6f7f6-470">`Accept-Encoding`標頭值</span><span class="sxs-lookup"><span data-stu-id="6f7f6-470">`Accept-Encoding` header values</span></span> | <span data-ttu-id="6f7f6-471">支援的中介軟體</span><span class="sxs-lookup"><span data-stu-id="6f7f6-471">Middleware Supported</span></span> | <span data-ttu-id="6f7f6-472">描述</span><span class="sxs-lookup"><span data-stu-id="6f7f6-472">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="6f7f6-473">否</span><span class="sxs-lookup"><span data-stu-id="6f7f6-473">No</span></span>                   | [<span data-ttu-id="6f7f6-474">Brotli 壓縮資料格式</span><span class="sxs-lookup"><span data-stu-id="6f7f6-474">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="6f7f6-475">否</span><span class="sxs-lookup"><span data-stu-id="6f7f6-475">No</span></span>                   | [<span data-ttu-id="6f7f6-476">DEFLATE 壓縮資料格式</span><span class="sxs-lookup"><span data-stu-id="6f7f6-476">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="6f7f6-477">否</span><span class="sxs-lookup"><span data-stu-id="6f7f6-477">No</span></span>                   | [<span data-ttu-id="6f7f6-478">W3C 有效率的 XML 交換</span><span class="sxs-lookup"><span data-stu-id="6f7f6-478">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="6f7f6-479">是 (預設)</span><span class="sxs-lookup"><span data-stu-id="6f7f6-479">Yes (default)</span></span>        | [<span data-ttu-id="6f7f6-480">GZIP 檔案格式</span><span class="sxs-lookup"><span data-stu-id="6f7f6-480">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="6f7f6-481">是</span><span class="sxs-lookup"><span data-stu-id="6f7f6-481">Yes</span></span>                  | <span data-ttu-id="6f7f6-482">「無編碼」識別碼：回應不得編碼。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-482">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="6f7f6-483">否</span><span class="sxs-lookup"><span data-stu-id="6f7f6-483">No</span></span>                   | [<span data-ttu-id="6f7f6-484">JAVA 封存的網路傳輸格式</span><span class="sxs-lookup"><span data-stu-id="6f7f6-484">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="6f7f6-485">是</span><span class="sxs-lookup"><span data-stu-id="6f7f6-485">Yes</span></span>                  | <span data-ttu-id="6f7f6-486">未明確要求任何可用的內容編碼</span><span class="sxs-lookup"><span data-stu-id="6f7f6-486">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="6f7f6-487">如需詳細資訊，請參閱[IANA 官方內容編碼清單](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-487">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="6f7f6-488">中介軟體可讓您新增自訂標頭值的其他壓縮提供者 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-488">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="6f7f6-489">如需詳細資訊，請參閱下面的[自訂提供者](#custom-providers)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-489">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="6f7f6-490">中介軟體能夠回應品質值 (qvalue，並 `q` 在用戶端傳送時) 加權，以設定壓縮配置的優先順序。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-490">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="6f7f6-491">如需詳細資訊，請參閱[RFC 7231：接受編碼](https://tools.ietf.org/html/rfc7231#section-5.3.4)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-491">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="6f7f6-492">壓縮演算法會受到壓縮速度與壓縮效率之間的取捨。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-492">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="6f7f6-493">此內容中的*有效性*是指壓縮之後的輸出大小。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-493">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="6f7f6-494">最大的大小是透過*最佳*壓縮來達成。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-494">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="6f7f6-495">下表說明有關要求、傳送、快取和接收壓縮內容的標頭。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-495">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="6f7f6-496">標頭</span><span class="sxs-lookup"><span data-stu-id="6f7f6-496">Header</span></span>             | <span data-ttu-id="6f7f6-497">角色</span><span class="sxs-lookup"><span data-stu-id="6f7f6-497">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="6f7f6-498">從用戶端傳送到伺服器，以指示用戶端可接受的內容編碼配置。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-498">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="6f7f6-499">從伺服器傳送到用戶端，以指出內容在承載中的編碼方式。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-499">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="6f7f6-500">當進行壓縮時，會 `Content-Length` 移除標頭，因為當回應壓縮時，本文內容會變更。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-500">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="6f7f6-501">當進行壓縮時，會 `Content-MD5` 移除標頭，因為本文內容已變更，而且雜湊已不再有效。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-501">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="6f7f6-502">指定內容的 MIME 類型。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-502">Specifies the MIME type of the content.</span></span> <span data-ttu-id="6f7f6-503">每個回應都應該指定其 `Content-Type` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-503">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="6f7f6-504">中介軟體會檢查此值，以判斷是否應該壓縮回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-504">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="6f7f6-505">中介軟體會指定一組可編碼的[預設 MIME 類型](#mime-types)，但您可以取代或加入 MIME 類型。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-505">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="6f7f6-506">當伺服器將的值傳送 `Accept-Encoding` 給用戶端和 proxy 時， `Vary` 標頭會向用戶端或 proxy 指出它應該快取的 (根據要求的標頭值來改變) 回應 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-506">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="6f7f6-507">傳回具有標頭之內容的結果 `Vary: Accept-Encoding` 是，會分別快取壓縮和未壓縮的回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-507">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="6f7f6-508">使用[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)探索回應壓縮中介軟體的功能。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-508">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="6f7f6-509">此範例說明：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-509">The sample illustrates:</span></span>

* <span data-ttu-id="6f7f6-510">使用 Gzip 和自訂壓縮提供者來壓縮應用程式回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-510">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="6f7f6-511">如何將 MIME 類型新增至 MIME 類型的預設清單以進行壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-511">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="6f7f6-512">Package</span><span class="sxs-lookup"><span data-stu-id="6f7f6-512">Package</span></span>

<span data-ttu-id="6f7f6-513">若要在專案中包含中介軟體，請新增[AspNetCore 應用程式中繼套件](xref:fundamentals/metapackage-app)的參考，其中包含[AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)封裝。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-513">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="6f7f6-514">組態</span><span class="sxs-lookup"><span data-stu-id="6f7f6-514">Configuration</span></span>

<span data-ttu-id="6f7f6-515">下列程式碼示範如何為預設 MIME 類型和[Gzip 壓縮提供者](#gzip-compression-provider)啟用回應壓縮中介軟體：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-515">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

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

<span data-ttu-id="6f7f6-516">注意：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-516">Notes:</span></span>

* <span data-ttu-id="6f7f6-517">`app.UseResponseCompression`必須在壓縮回應的任何中介軟體之前呼叫。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-517">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="6f7f6-518">如需詳細資訊，請參閱<xref:fundamentals/middleware/index#middleware-order>。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-518">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="6f7f6-519">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)之類的工具來設定 `Accept-Encoding` 要求標頭，並研究回應標頭、大小和主體。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-519">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="6f7f6-520">不使用標頭將要求提交至範例應用程式 `Accept-Encoding` ，並觀察回應是否已解壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-520">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="6f7f6-521">`Content-Encoding`和 `Vary` 標頭不存在於回應中。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-521">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![顯示要求不含接受編碼標頭之結果的 Fiddler 視窗。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="6f7f6-524">使用標頭將要求提交至範例應用程式 `Accept-Encoding: gzip` ，並觀察回應是否已壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-524">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="6f7f6-525">`Content-Encoding`和 `Vary` 標頭出現在回應中。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-525">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler 視窗，顯示具有接受編碼標頭和 gzip 值之要求的結果。](response-compression/_static/request-compressed.png)

## <a name="providers"></a><span data-ttu-id="6f7f6-529">提供者</span><span class="sxs-lookup"><span data-stu-id="6f7f6-529">Providers</span></span>

### <a name="gzip-compression-provider"></a><span data-ttu-id="6f7f6-530">Gzip 壓縮提供者</span><span class="sxs-lookup"><span data-stu-id="6f7f6-530">Gzip Compression Provider</span></span>

<span data-ttu-id="6f7f6-531">使用 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> 來壓縮具有[GZIP 檔案格式](https://tools.ietf.org/html/rfc1952)的回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-531">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="6f7f6-532">如果未將任何壓縮提供者明確新增至 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> ：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-532">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="6f7f6-533">Gzip 壓縮提供者預設會加入至壓縮提供者陣列。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-533">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="6f7f6-534">當用戶端支援 Gzip 壓縮時，壓縮會預設為 Gzip。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-534">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="6f7f6-535">當明確新增任何壓縮提供者時，必須加入 Gzip 壓縮提供者：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-535">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="6f7f6-536">使用設定壓縮等級 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-536">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="6f7f6-537">Gzip 壓縮提供者預設為最快的壓縮層級 ([CompressionLevel。最快](xref:System.IO.Compression.CompressionLevel)的) ，這可能不會產生最有效率的壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-537">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="6f7f6-538">如果需要最有效率的壓縮，請設定中介軟體以獲得最佳壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-538">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="6f7f6-539">Compression Level</span><span class="sxs-lookup"><span data-stu-id="6f7f6-539">Compression Level</span></span> | <span data-ttu-id="6f7f6-540">描述</span><span class="sxs-lookup"><span data-stu-id="6f7f6-540">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="6f7f6-541">CompressionLevel。最快</span><span class="sxs-lookup"><span data-stu-id="6f7f6-541">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6f7f6-542">即使產生的輸出未以最佳方式壓縮，壓縮也應該儘快完成。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-542">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="6f7f6-543">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="6f7f6-543">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6f7f6-544">不應該執行壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-544">No compression should be performed.</span></span> |
| [<span data-ttu-id="6f7f6-545">CompressionLevel。最佳</span><span class="sxs-lookup"><span data-stu-id="6f7f6-545">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6f7f6-546">回應應以最佳方式壓縮，即使壓縮需要較長的時間才能完成也一樣。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-546">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="6f7f6-547">自訂提供者</span><span class="sxs-lookup"><span data-stu-id="6f7f6-547">Custom providers</span></span>

<span data-ttu-id="6f7f6-548">使用建立自訂壓縮 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-548">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="6f7f6-549"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>表示此產生的內容編碼 `ICompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-549">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="6f7f6-550">中介軟體會使用這項資訊，根據要求標頭中所指定的清單來選擇提供者 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-550">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="6f7f6-551">使用範例應用程式時，用戶端會提交含有 `Accept-Encoding: mycustomcompression` 標頭的要求。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-551">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="6f7f6-552">中介軟體會使用自訂壓縮實作為，並傳回具有 `Content-Encoding: mycustomcompression` 標頭的回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-552">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="6f7f6-553">用戶端必須能夠解壓縮自訂編碼，才能讓自訂壓縮實行正常執行。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-553">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="6f7f6-554">使用標頭將要求提交至範例應用程式 `Accept-Encoding: mycustomcompression` ，並觀察回應標頭。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-554">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="6f7f6-555">`Vary`和 `Content-Encoding` 標頭出現在回應中。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-555">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="6f7f6-556"> (不會顯示回應主體，) 不會受到此範例的壓縮。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-556">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="6f7f6-557">範例的類別中沒有壓縮的執行 `CustomCompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-557">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="6f7f6-558">不過，此範例會顯示您將在哪裡執行這種壓縮演算法。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-558">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 視窗，顯示具有接受編碼標頭和值為 mycustomcompression 之要求的結果。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="6f7f6-561">MIME 類型</span><span class="sxs-lookup"><span data-stu-id="6f7f6-561">MIME types</span></span>

<span data-ttu-id="6f7f6-562">中介軟體會針對壓縮指定一組預設的 MIME 類型：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-562">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="6f7f6-563">以回應壓縮中介軟體選項取代或附加 MIME 類型。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-563">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="6f7f6-564">請注意，不支援萬用字元 MIME 類型，例如 `text/*` 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-564">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="6f7f6-565">範例應用程式會為新增 MIME 類型 `image/svg+xml` ，並將 ASP.NET Core 橫幅影像壓縮並提供給 (的*橫幅。 svg*) 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-565">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="6f7f6-566">使用安全通訊協定進行壓縮</span><span class="sxs-lookup"><span data-stu-id="6f7f6-566">Compression with secure protocol</span></span>

<span data-ttu-id="6f7f6-567">透過安全連線的壓縮回應可以使用選項來控制 `EnableForHttps` ，預設為停用。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-567">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="6f7f6-568">以動態產生的頁面使用壓縮，可能會導致安全性問題，例如[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))和[入侵](https://wikipedia.org/wiki/BREACH_(security_exploit))攻擊。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-568">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="6f7f6-569">新增 Vary 標頭</span><span class="sxs-lookup"><span data-stu-id="6f7f6-569">Adding the Vary header</span></span>

<span data-ttu-id="6f7f6-570">根據標頭壓縮回應時 `Accept-Encoding` ，可能會有多個壓縮版本的回應和未壓縮的版本。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-570">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="6f7f6-571">若要指示用戶端和 proxy 快取有多個版本存在且應該儲存，則 `Vary` 標頭會加上 `Accept-Encoding` 值。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-571">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="6f7f6-572">在 ASP.NET Core 2.0 或更新版本中，中介軟體 `Vary` 會在回應壓縮時自動新增標頭。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-572">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="6f7f6-573">Nginx 反向 proxy 後方發生中介軟體問題</span><span class="sxs-lookup"><span data-stu-id="6f7f6-573">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="6f7f6-574">當要求由 Nginx proxy 時， `Accept-Encoding` 會移除標頭。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-574">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="6f7f6-575">移除 `Accept-Encoding` 標頭可防止中介軟體壓縮回應。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-575">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="6f7f6-576">如需詳細資訊，請參閱[NGINX：壓縮和解壓縮](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-576">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="6f7f6-577">此問題的追蹤方式是藉由[瞭解 Nginx (aspnet/BasicMiddleware #123) 的傳遞壓縮](https://github.com/aspnet/BasicMiddleware/issues/123)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-577">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="6f7f6-578">使用 IIS 動態壓縮</span><span class="sxs-lookup"><span data-stu-id="6f7f6-578">Working with IIS dynamic compression</span></span>

<span data-ttu-id="6f7f6-579">如果您在想要針對應用程式停用的伺服器層級上設定作用中的 IIS 動態壓縮模組，請停用*web.config*檔案以外的模組。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-579">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="6f7f6-580">如需詳細資訊，請參閱[停用 IIS 模組](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-580">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="6f7f6-581">疑難排解</span><span class="sxs-lookup"><span data-stu-id="6f7f6-581">Troubleshooting</span></span>

<span data-ttu-id="6f7f6-582">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)等工具，可讓您設定 `Accept-Encoding` 要求標頭，並研究回應標頭、大小和主體。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-582">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="6f7f6-583">根據預設，回應壓縮中介軟體會壓縮符合下列條件的回應：</span><span class="sxs-lookup"><span data-stu-id="6f7f6-583">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="6f7f6-584">`Accept-Encoding`標頭存在，其值為 `gzip` 、 `*` 或符合您所建立之自訂壓縮提供者的自訂編碼。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-584">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="6f7f6-585">此值不得為 `identity` 或具有品質值 (qvalue， `q`) 設定為 0 (零) 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-585">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="6f7f6-586">必須設定 MIME 類型 (`Content-Type`) ，而且必須符合在上設定的 mime 類型 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-586">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="6f7f6-587">要求不能包含 `Content-Range` 標頭。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-587">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="6f7f6-588">除非在回應壓縮中介軟體選項中設定了安全通訊協定 (HTTPs) ，否則要求必須使用不安全的通訊協定 (HTTP) 。</span><span class="sxs-lookup"><span data-stu-id="6f7f6-588">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="6f7f6-589">*請注意，在啟用安全內容壓縮時，[上述](#compression-with-secure-protocol)的危險。*</span><span class="sxs-lookup"><span data-stu-id="6f7f6-589">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f7f6-590">其他資源</span><span class="sxs-lookup"><span data-stu-id="6f7f6-590">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="6f7f6-591">Mozilla 開發人員網路：接受編碼</span><span class="sxs-lookup"><span data-stu-id="6f7f6-591">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="6f7f6-592">RFC 7231 區段3.1.2.1： Content Codings</span><span class="sxs-lookup"><span data-stu-id="6f7f6-592">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="6f7f6-593">RFC 7230 區段4.2.3： Gzip 編碼</span><span class="sxs-lookup"><span data-stu-id="6f7f6-593">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="6f7f6-594">GZIP 檔案格式規格版本4。3</span><span class="sxs-lookup"><span data-stu-id="6f7f6-594">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end
