---
title: ASP.NET Core 中的回應壓縮
author: rick-anderson
description: 了解回應壓縮及如何使用 ASP.NET Core 應用程式中的回應壓縮中介軟體。
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
uid: performance/response-compression
ms.openlocfilehash: 9327c98c22a4d42d31ea8ba1eb8337153040b5b5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056968"
---
# <a name="response-compression-in-aspnet-core"></a>ASP.NET Core 中的回應壓縮

::: moniker range=">= aspnetcore-3.0"

網路頻寬是有限的資源。 減少回應的大小通常會增加應用程式的回應能力，通常會大幅增加。 減少承載大小的其中一種方式是壓縮應用程式的回應。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="when-to-use-response-compression-middleware"></a>使用回應壓縮中介軟體的時機

在 IIS、Apache 或 Nginx 中使用以伺服器為基礎的回應壓縮技術。 中介軟體的效能可能不符合伺服器模組的效能。 [HTTP.sys server](xref:fundamentals/servers/httpsys) Server 和 [Kestrel](xref:fundamentals/servers/kestrel) server 目前未提供內建壓縮支援。

當您在下列情況時，請使用回應壓縮中介軟體：

* 無法使用下列以伺服器為基礎的壓縮技術：
  * [IIS 動態壓縮模組](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Apache mod_deflate 模組](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Nginx 壓縮和解壓縮](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* 直接裝載于：
  * [HTTP.sys server](xref:fundamentals/servers/httpsys) (先前稱為 WebListener) 
  * [Kestrel 伺服器](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>回應壓縮

通常，任何未原生壓縮的回應都可受益于回應壓縮。 未原生壓縮的回應通常包括： CSS、JavaScript、HTML、XML 和 JSON。 您不應壓縮原生壓縮的資產，例如 PNG 檔案。 如果您嘗試進一步壓縮原生壓縮的回應，則在處理壓縮所花費的時間內，可能會失色任何較小的額外縮減大小和傳輸時間。 請勿壓縮小於大約150-1000 個位元組 (的檔案，視檔案的內容和壓縮) 的效率而定。 壓縮小型檔案的額外負荷可能會產生大於未壓縮檔案的壓縮檔案。

當用戶端可以處理壓縮的內容時，用戶端必須將 `Accept-Encoding` 標頭與要求一起傳送，以通知伺服器其功能。 當伺服器傳送壓縮的內容時，它必須在 `Content-Encoding` 標頭中包含壓縮回應編碼方式的資訊。 下表顯示中介軟體所支援的內容編碼方式。

| `Accept-Encoding` 標頭值 | 支援中介軟體 | 描述 |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | 是 (預設)        | [Brotli 壓縮的資料格式](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | 否                   | [DEFLATE 壓縮的資料格式](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | 否                   | [W3C 有效率的 XML 交換](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | 是                  | [GZIP 檔案格式](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | 是                  | 「無編碼」識別碼：回應不得編碼。 |
| `pack200-gzip`                  | 否                   | [JAVA 封存的網路傳輸格式](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | 是                  | 未明確要求任何可用的內容編碼 |

如需詳細資訊，請參閱 [IANA 官方內容編碼清單](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。

中介軟體可讓您為自訂標頭值新增額外的壓縮提供者 `Accept-Encoding` 。 如需詳細資訊，請參閱下方的 [自訂提供者](#custom-providers) 。

在用戶端傳送壓縮配置的優先順序時，中介軟體能夠回應品質值 (qvalue， `q`) 加權。 如需詳細資訊，請參閱 [RFC 7231：接受編碼](https://tools.ietf.org/html/rfc7231#section-5.3.4)。

壓縮演算法受限於壓縮速度與壓縮效率之間的取捨。 此內容中的 *有效性* 是指壓縮之後的輸出大小。 最大的壓縮是以最 *理想* 的壓縮來達成。

下表說明要求、傳送、快取及接收壓縮內容所涉及的標頭。

| 標頭             | 角色 |
| ------------------ | ---- |
| `Accept-Encoding`  | 從用戶端傳送到伺服器，以指出用戶端可接受的內容編碼配置。 |
| `Content-Encoding` | 從伺服器傳送至用戶端，以指出內容在承載中的編碼方式。 |
| `Content-Length`   | 發生壓縮時， `Content-Length` 會移除標頭，因為本文內容會在回應壓縮時變更。 |
| `Content-MD5`      | 發生壓縮時， `Content-MD5` 會移除標頭，因為本文內容已變更，且雜湊不再有效。 |
| `Content-Type`     | 指定內容的 MIME 類型。 每個回應都應指定其 `Content-Type` 。 中介軟體會檢查此值，以判斷是否應該壓縮回應。 中介軟體會指定一組可以編碼的 [預設 MIME 類型](#mime-types) ，但是您可以取代或加入 mime 類型。 |
| `Vary`             | 當伺服器將值的傳 `Accept-Encoding` 給用戶端和 proxy 時， `Vary` 標頭會向用戶端或 proxy 指出它應該快取 (根據要求標頭的值來) 回應 `Accept-Encoding` 。 使用標頭傳回內容的結果， `Vary: Accept-Encoding` 是分開快取壓縮和未壓縮的回應。 |

使用 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)探索回應壓縮中介軟體的功能。 此範例說明：

* 使用 Gzip 和自訂壓縮提供者壓縮應用程式回應。
* 如何將 MIME 類型加入至預設的 MIME 類型清單以進行壓縮。

## <a name="package"></a>套件

回應壓縮中介軟體是由 [AspNetCore ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) 封裝所提供，該封裝會隱含地包含在 ASP.NET Core apps 中。

## <a name="configuration"></a>組態

下列程式碼說明如何針對預設的 MIME 類型和壓縮提供者啟用回應壓縮中介軟體 ([Brotli](#brotli-compression-provider) 和 [Gzip](#gzip-compression-provider)) ：

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

注意：

* `app.UseResponseCompression` 必須在可壓縮回應的任何中介軟體之前呼叫。 如需詳細資訊，請參閱<xref:fundamentals/middleware/index#middleware-order>。
* 使用 [Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或 [Postman](https://www.getpostman.com/) 等工具來設定 `Accept-Encoding` 要求標頭，並研究回應標頭、大小和主體。

將要求提交給範例應用程式，而不使用 `Accept-Encoding` 標頭，並觀察回應是否未壓縮。 `Content-Encoding`和 `Vary` 標頭不存在於回應中。

![Fiddler 視窗，顯示沒有 Accept-Encoding 標頭的要求結果。 未壓縮回應。](response-compression/_static/request-uncompressed.png)

使用標頭 (Brotli 壓縮) 將要求提交至範例應用程式 `Accept-Encoding: br` ，並觀察回應是否已壓縮。 `Content-Encoding`和 `Vary` 標頭出現在回應中。

![Fiddler 視窗顯示要求的結果，其中 Accept-Encoding 標頭和值為 br。 變更和內容編碼標頭會新增至回應。 回應會壓縮。](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a>提供者

### <a name="brotli-compression-provider"></a>Brotli 壓縮提供者

使用壓縮 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> [Brotli 壓縮資料格式](https://tools.ietf.org/html/rfc7932)的回應。

如果未明確將壓縮提供者加入至 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> ：

* 預設會將 Brotli 壓縮提供者加入至壓縮提供者的陣列，以及 [Gzip 壓縮提供者](#gzip-compression-provider)。
* 當用戶端支援 Brotli 壓縮資料格式時，壓縮會預設為 Brotli 壓縮。 如果用戶端不支援 Brotli，當用戶端支援 Gzip 壓縮時，壓縮會預設為 Gzip。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

明確新增任何壓縮提供者時，必須新增 Brotli 壓縮提供者：

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

使用設定壓縮等級 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions> 。 Brotli 壓縮提供者預設為最快的壓縮層級 ([CompressionLevel。最快](xref:System.IO.Compression.CompressionLevel) 的) ，可能不會產生最有效率的壓縮。 如果需要最有效率的壓縮，請設定中介軟體以獲得最佳壓縮。

| Compression Level | 描述 |
| ----------------- | ----------- |
| [CompressionLevel。最快](xref:System.IO.Compression.CompressionLevel) | 壓縮應該盡可能快速完成，即使產生的輸出未以最佳方式壓縮。 |
| [CompressionLevel. NoCompression](xref:System.IO.Compression.CompressionLevel) | 不應執行任何壓縮。 |
| [CompressionLevel。最佳](xref:System.IO.Compression.CompressionLevel) | 即使壓縮需要更多時間才能完成，回應仍應以最佳方式壓縮。 |

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

### <a name="gzip-compression-provider"></a>Gzip 壓縮提供者

使用 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> 來壓縮具有 [GZIP 檔案格式](https://tools.ietf.org/html/rfc1952)的回應。

如果未明確將壓縮提供者加入至 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> ：

* Gzip 壓縮提供者預設會加入壓縮提供者的陣列，連同 [Brotli 壓縮提供者](#brotli-compression-provider)。
* 當用戶端支援 Brotli 壓縮資料格式時，壓縮會預設為 Brotli 壓縮。 如果用戶端不支援 Brotli，當用戶端支援 Gzip 壓縮時，壓縮會預設為 Gzip。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

明確新增任何壓縮提供者時，必須加入 Gzip 壓縮提供者：

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

使用設定壓縮等級 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> 。 Gzip 壓縮提供者預設為最快的壓縮層級 ([CompressionLevel。最快](xref:System.IO.Compression.CompressionLevel) 的) ，可能不會產生最有效率的壓縮。 如果需要最有效率的壓縮，請設定中介軟體以獲得最佳壓縮。

| Compression Level | 描述 |
| ----------------- | ----------- |
| [CompressionLevel。最快](xref:System.IO.Compression.CompressionLevel) | 壓縮應該盡可能快速完成，即使產生的輸出未以最佳方式壓縮。 |
| [CompressionLevel. NoCompression](xref:System.IO.Compression.CompressionLevel) | 不應執行任何壓縮。 |
| [CompressionLevel。最佳](xref:System.IO.Compression.CompressionLevel) | 即使壓縮需要更多時間才能完成，回應仍應以最佳方式壓縮。 |

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

### <a name="custom-providers"></a>自訂提供者

使用建立自訂壓縮 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> 。 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>代表這產生的內容編碼 `ICompressionProvider` 。 中介軟體會使用這項資訊，根據要求標頭中指定的清單來選擇提供者 `Accept-Encoding` 。

使用範例應用程式時，用戶端會以 `Accept-Encoding: mycustomcompression` 標頭提交要求。 中介軟體會使用自訂壓縮執行，並傳回含有 `Content-Encoding: mycustomcompression` 標頭的回應。 用戶端必須能夠解壓縮自訂編碼，自訂壓縮執行才能運作。

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]


使用標頭將要求提交至範例應用程式 `Accept-Encoding: mycustomcompression` ，並觀察回應標頭。 `Vary`和 `Content-Encoding` 標頭出現在回應中。 未 (顯示的回應主體) 不會被範例壓縮。 範例的類別中沒有壓縮的執行 `CustomCompressionProvider` 。 不過，此範例會示範您要在哪裡執行這類壓縮演算法。

![Fiddler 視窗顯示要求的結果，其中 Accept-Encoding 標頭和值為 mycustomcompression。 變更和內容編碼標頭會新增至回應。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>MIME 類型

中介軟體會針對壓縮指定一組預設的 MIME 類型：

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

以回應壓縮中介軟體選項取代或附加 MIME 類型。 請注意，不支援萬用字元 MIME 類型（例如） `text/*` 。 範例應用程式會新增的 MIME 類型 `image/svg+xml` ，並壓縮並提供 ASP.NET Core 橫幅影像 (的橫幅 *。 svg* ) 。

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a>使用安全通訊協定進行壓縮

您可以使用預設停用的選項來控制透過安全連線的壓縮回應 `EnableForHttps` 。 使用壓縮動態產生的頁面可能會導致安全性問題，例如 [犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit)) 和 [缺口](https://wikipedia.org/wiki/BREACH_(security_exploit)) 攻擊。

## <a name="adding-the-vary-header"></a>新增 Vary 標頭

根據標頭壓縮回應時 `Accept-Encoding` ，可能會有多個壓縮版本的回應和未壓縮的版本。 為了指示用戶端和 proxy 快取有多個版本存在且應該儲存， `Vary` 標頭會加上 `Accept-Encoding` 值。 在 ASP.NET Core 2.0 或更新版本中，中介軟體 `Vary` 會在回應壓縮時自動新增標頭。

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Nginx 反向 proxy 後方的中介軟體問題

當要求由 Nginx proxy 時， `Accept-Encoding` 會移除標頭。 移除 `Accept-Encoding` 標頭可防止中介軟體壓縮回應。 如需詳細資訊，請參閱 [NGINX：壓縮和解壓縮](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。 此問題的追蹤方式為 [Nginx (aspnet/BasicMiddleware #123) 的傳遞壓縮 ](https://github.com/aspnet/BasicMiddleware/issues/123)。

## <a name="working-with-iis-dynamic-compression"></a>使用 IIS 動態壓縮

如果您在想要針對應用程式停用的伺服器層級上設定作用中的 IIS 動態壓縮模組，請停用 *web.config* 檔案以外的模組。 如需詳細資訊，請參閱[停用 IIS 模組](xref:host-and-deploy/iis/modules#disabling-iis-modules)。

## <a name="troubleshooting"></a>疑難排解

使用 [Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或 [Postman](https://www.getpostman.com/)等工具，這可讓您設定 `Accept-Encoding` 要求標頭，並研究回應標頭、大小和主體。 根據預設，回應壓縮中介軟體會壓縮符合下列條件的回應：

* `Accept-Encoding`標頭存在，其值為 `br` 、 `gzip` 、 `*` 或自訂編碼，以符合您所建立的自訂壓縮提供者。 值不得為 `identity` 或具有 (qvalue 的品質值， `q`) 設定為 0 (零) 。
* 必須設定 MIME 類型 (`Content-Type`) ，且必須符合中所設定的 mime 類型 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> 。
* 要求不能包括 `Content-Range` 標頭。
* 要求必須使用不安全的通訊協定 (HTTP) ，除非在回應壓縮中介軟體選項中設定了安全通訊協定 (HTTPs) 。 *在啟用安全內容壓縮時，請注意 [上述](#compression-with-secure-protocol) 的風險。*

## <a name="additional-resources"></a>其他資源

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Mozilla 開發人員網路：接受編碼](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [RFC 7231 區段3.1.2.1： Content Codings](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230 區段4.2.3： Gzip 編碼](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [GZIP 檔案格式規格版本4。3](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

網路頻寬是有限的資源。 減少回應的大小通常會增加應用程式的回應能力，通常會大幅增加。 減少承載大小的其中一種方式是壓縮應用程式的回應。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="when-to-use-response-compression-middleware"></a>使用回應壓縮中介軟體的時機

在 IIS、Apache 或 Nginx 中使用以伺服器為基礎的回應壓縮技術。 中介軟體的效能可能不符合伺服器模組的效能。 [HTTP.sys server](xref:fundamentals/servers/httpsys) Server 和 [Kestrel](xref:fundamentals/servers/kestrel) server 目前未提供內建壓縮支援。

當您在下列情況時，請使用回應壓縮中介軟體：

* 無法使用下列以伺服器為基礎的壓縮技術：
  * [IIS 動態壓縮模組](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Apache mod_deflate 模組](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Nginx 壓縮和解壓縮](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* 直接裝載于：
  * [HTTP.sys server](xref:fundamentals/servers/httpsys) (先前稱為 WebListener) 
  * [Kestrel 伺服器](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>回應壓縮

通常，任何未原生壓縮的回應都可受益于回應壓縮。 未原生壓縮的回應通常包括： CSS、JavaScript、HTML、XML 和 JSON。 您不應壓縮原生壓縮的資產，例如 PNG 檔案。 如果您嘗試進一步壓縮原生壓縮的回應，則在處理壓縮所花費的時間內，可能會失色任何較小的額外縮減大小和傳輸時間。 請勿壓縮小於大約150-1000 個位元組 (的檔案，視檔案的內容和壓縮) 的效率而定。 壓縮小型檔案的額外負荷可能會產生大於未壓縮檔案的壓縮檔案。

當用戶端可以處理壓縮的內容時，用戶端必須將 `Accept-Encoding` 標頭與要求一起傳送，以通知伺服器其功能。 當伺服器傳送壓縮的內容時，它必須在 `Content-Encoding` 標頭中包含壓縮回應編碼方式的資訊。 下表顯示中介軟體所支援的內容編碼方式。

| `Accept-Encoding` 標頭值 | 支援中介軟體 | 描述 |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | 是 (預設)        | [Brotli 壓縮的資料格式](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | 否                   | [DEFLATE 壓縮的資料格式](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | 否                   | [W3C 有效率的 XML 交換](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | 是                  | [GZIP 檔案格式](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | 是                  | 「無編碼」識別碼：回應不得編碼。 |
| `pack200-gzip`                  | 否                   | [JAVA 封存的網路傳輸格式](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | 是                  | 未明確要求任何可用的內容編碼 |

如需詳細資訊，請參閱 [IANA 官方內容編碼清單](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。

中介軟體可讓您為自訂標頭值新增額外的壓縮提供者 `Accept-Encoding` 。 如需詳細資訊，請參閱下方的 [自訂提供者](#custom-providers) 。

在用戶端傳送壓縮配置的優先順序時，中介軟體能夠回應品質值 (qvalue， `q`) 加權。 如需詳細資訊，請參閱 [RFC 7231：接受編碼](https://tools.ietf.org/html/rfc7231#section-5.3.4)。

壓縮演算法受限於壓縮速度與壓縮效率之間的取捨。 此內容中的 *有效性* 是指壓縮之後的輸出大小。 最大的壓縮是以最 *理想* 的壓縮來達成。

下表說明要求、傳送、快取及接收壓縮內容所涉及的標頭。

| 標頭             | 角色 |
| ------------------ | ---- |
| `Accept-Encoding`  | 從用戶端傳送到伺服器，以指出用戶端可接受的內容編碼配置。 |
| `Content-Encoding` | 從伺服器傳送至用戶端，以指出內容在承載中的編碼方式。 |
| `Content-Length`   | 發生壓縮時， `Content-Length` 會移除標頭，因為本文內容會在回應壓縮時變更。 |
| `Content-MD5`      | 發生壓縮時， `Content-MD5` 會移除標頭，因為本文內容已變更，且雜湊不再有效。 |
| `Content-Type`     | 指定內容的 MIME 類型。 每個回應都應指定其 `Content-Type` 。 中介軟體會檢查此值，以判斷是否應該壓縮回應。 中介軟體會指定一組可以編碼的 [預設 MIME 類型](#mime-types) ，但是您可以取代或加入 mime 類型。 |
| `Vary`             | 當伺服器將值的傳 `Accept-Encoding` 給用戶端和 proxy 時， `Vary` 標頭會向用戶端或 proxy 指出它應該快取 (根據要求標頭的值來) 回應 `Accept-Encoding` 。 使用標頭傳回內容的結果， `Vary: Accept-Encoding` 是分開快取壓縮和未壓縮的回應。 |

使用 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)探索回應壓縮中介軟體的功能。 此範例說明：

* 使用 Gzip 和自訂壓縮提供者壓縮應用程式回應。
* 如何將 MIME 類型加入至預設的 MIME 類型清單以進行壓縮。

## <a name="package"></a>套件

若要在專案中包含中介軟體，請新增 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app)的參考，其中包含 [AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) 套件。

## <a name="configuration"></a>組態

下列程式碼說明如何針對預設的 MIME 類型和壓縮提供者啟用回應壓縮中介軟體 ([Brotli](#brotli-compression-provider) 和 [Gzip](#gzip-compression-provider)) ：

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

注意：

* `app.UseResponseCompression` 必須在可壓縮回應的任何中介軟體之前呼叫。 如需詳細資訊，請參閱<xref:fundamentals/middleware/index#middleware-order>。
* 使用 [Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或 [Postman](https://www.getpostman.com/) 等工具來設定 `Accept-Encoding` 要求標頭，並研究回應標頭、大小和主體。

將要求提交給範例應用程式，而不使用 `Accept-Encoding` 標頭，並觀察回應是否未壓縮。 `Content-Encoding`和 `Vary` 標頭不存在於回應中。

![Fiddler 視窗，顯示沒有 Accept-Encoding 標頭的要求結果。 未壓縮回應。](response-compression/_static/request-uncompressed.png)

使用標頭 (Brotli 壓縮) 將要求提交至範例應用程式 `Accept-Encoding: br` ，並觀察回應是否已壓縮。 `Content-Encoding`和 `Vary` 標頭出現在回應中。

![Fiddler 視窗顯示要求的結果，其中 Accept-Encoding 標頭和值為 br。 變更和內容編碼標頭會新增至回應。 回應會壓縮。](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a>提供者

### <a name="brotli-compression-provider"></a>Brotli 壓縮提供者

使用壓縮 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> [Brotli 壓縮資料格式](https://tools.ietf.org/html/rfc7932)的回應。

如果未明確將壓縮提供者加入至 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> ：

* 預設會將 Brotli 壓縮提供者加入至壓縮提供者的陣列，以及 [Gzip 壓縮提供者](#gzip-compression-provider)。
* 當用戶端支援 Brotli 壓縮資料格式時，壓縮會預設為 Brotli 壓縮。 如果用戶端不支援 Brotli，當用戶端支援 Gzip 壓縮時，壓縮會預設為 Gzip。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

明確新增任何壓縮提供者時，必須新增 Brotli 壓縮提供者：

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

使用設定壓縮等級 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions> 。 Brotli 壓縮提供者預設為最快的壓縮層級 ([CompressionLevel。最快](xref:System.IO.Compression.CompressionLevel) 的) ，可能不會產生最有效率的壓縮。 如果需要最有效率的壓縮，請設定中介軟體以獲得最佳壓縮。

| Compression Level | 描述 |
| ----------------- | ----------- |
| [CompressionLevel。最快](xref:System.IO.Compression.CompressionLevel) | 壓縮應該盡可能快速完成，即使產生的輸出未以最佳方式壓縮。 |
| [CompressionLevel. NoCompression](xref:System.IO.Compression.CompressionLevel) | 不應執行任何壓縮。 |
| [CompressionLevel。最佳](xref:System.IO.Compression.CompressionLevel) | 即使壓縮需要更多時間才能完成，回應仍應以最佳方式壓縮。 |

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

### <a name="gzip-compression-provider"></a>Gzip 壓縮提供者

使用 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> 來壓縮具有 [GZIP 檔案格式](https://tools.ietf.org/html/rfc1952)的回應。

如果未明確將壓縮提供者加入至 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> ：

* Gzip 壓縮提供者預設會加入壓縮提供者的陣列，連同 [Brotli 壓縮提供者](#brotli-compression-provider)。
* 當用戶端支援 Brotli 壓縮資料格式時，壓縮會預設為 Brotli 壓縮。 如果用戶端不支援 Brotli，當用戶端支援 Gzip 壓縮時，壓縮會預設為 Gzip。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

明確新增任何壓縮提供者時，必須加入 Gzip 壓縮提供者：

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

使用設定壓縮等級 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> 。 Gzip 壓縮提供者預設為最快的壓縮層級 ([CompressionLevel。最快](xref:System.IO.Compression.CompressionLevel) 的) ，可能不會產生最有效率的壓縮。 如果需要最有效率的壓縮，請設定中介軟體以獲得最佳壓縮。

| Compression Level | 描述 |
| ----------------- | ----------- |
| [CompressionLevel。最快](xref:System.IO.Compression.CompressionLevel) | 壓縮應該盡可能快速完成，即使產生的輸出未以最佳方式壓縮。 |
| [CompressionLevel. NoCompression](xref:System.IO.Compression.CompressionLevel) | 不應執行任何壓縮。 |
| [CompressionLevel。最佳](xref:System.IO.Compression.CompressionLevel) | 即使壓縮需要更多時間才能完成，回應仍應以最佳方式壓縮。 |

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

### <a name="custom-providers"></a>自訂提供者

使用建立自訂壓縮 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> 。 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>代表這產生的內容編碼 `ICompressionProvider` 。 中介軟體會使用這項資訊，根據要求標頭中指定的清單來選擇提供者 `Accept-Encoding` 。

使用範例應用程式時，用戶端會以 `Accept-Encoding: mycustomcompression` 標頭提交要求。 中介軟體會使用自訂壓縮執行，並傳回含有 `Content-Encoding: mycustomcompression` 標頭的回應。 用戶端必須能夠解壓縮自訂編碼，自訂壓縮執行才能運作。

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

使用標頭將要求提交至範例應用程式 `Accept-Encoding: mycustomcompression` ，並觀察回應標頭。 `Vary`和 `Content-Encoding` 標頭出現在回應中。 未 (顯示的回應主體) 不會被範例壓縮。 範例的類別中沒有壓縮的執行 `CustomCompressionProvider` 。 不過，此範例會示範您要在哪裡執行這類壓縮演算法。

![Fiddler 視窗顯示要求的結果，其中 Accept-Encoding 標頭和值為 mycustomcompression。 變更和內容編碼標頭會新增至回應。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>MIME 類型

中介軟體會針對壓縮指定一組預設的 MIME 類型：

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

以回應壓縮中介軟體選項取代或附加 MIME 類型。 請注意，不支援萬用字元 MIME 類型（例如） `text/*` 。 範例應用程式會新增的 MIME 類型 `image/svg+xml` ，並壓縮並提供 ASP.NET Core 橫幅影像 (的橫幅 *。 svg* ) 。

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a>使用安全通訊協定進行壓縮

您可以使用預設停用的選項來控制透過安全連線的壓縮回應 `EnableForHttps` 。 使用壓縮動態產生的頁面可能會導致安全性問題，例如 [犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit)) 和 [缺口](https://wikipedia.org/wiki/BREACH_(security_exploit)) 攻擊。

## <a name="adding-the-vary-header"></a>新增 Vary 標頭

根據標頭壓縮回應時 `Accept-Encoding` ，可能會有多個壓縮版本的回應和未壓縮的版本。 為了指示用戶端和 proxy 快取有多個版本存在且應該儲存， `Vary` 標頭會加上 `Accept-Encoding` 值。 在 ASP.NET Core 2.0 或更新版本中，中介軟體 `Vary` 會在回應壓縮時自動新增標頭。

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Nginx 反向 proxy 後方的中介軟體問題

當要求由 Nginx proxy 時， `Accept-Encoding` 會移除標頭。 移除 `Accept-Encoding` 標頭可防止中介軟體壓縮回應。 如需詳細資訊，請參閱 [NGINX：壓縮和解壓縮](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。 此問題的追蹤方式為 [Nginx (aspnet/BasicMiddleware #123) 的傳遞壓縮 ](https://github.com/aspnet/BasicMiddleware/issues/123)。

## <a name="working-with-iis-dynamic-compression"></a>使用 IIS 動態壓縮

如果您在想要針對應用程式停用的伺服器層級上設定作用中的 IIS 動態壓縮模組，請停用 *web.config* 檔案以外的模組。 如需詳細資訊，請參閱[停用 IIS 模組](xref:host-and-deploy/iis/modules#disabling-iis-modules)。

## <a name="troubleshooting"></a>疑難排解

使用 [Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或 [Postman](https://www.getpostman.com/)等工具，這可讓您設定 `Accept-Encoding` 要求標頭，並研究回應標頭、大小和主體。 根據預設，回應壓縮中介軟體會壓縮符合下列條件的回應：

* `Accept-Encoding`標頭存在，其值為 `br` 、 `gzip` 、 `*` 或自訂編碼，以符合您所建立的自訂壓縮提供者。 值不得為 `identity` 或具有 (qvalue 的品質值， `q`) 設定為 0 (零) 。
* 必須設定 MIME 類型 (`Content-Type`) ，且必須符合中所設定的 mime 類型 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> 。
* 要求不能包括 `Content-Range` 標頭。
* 要求必須使用不安全的通訊協定 (HTTP) ，除非在回應壓縮中介軟體選項中設定了安全通訊協定 (HTTPs) 。 *在啟用安全內容壓縮時，請注意 [上述](#compression-with-secure-protocol) 的風險。*

## <a name="additional-resources"></a>其他資源

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Mozilla 開發人員網路：接受編碼](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [RFC 7231 區段3.1.2.1： Content Codings](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230 區段4.2.3： Gzip 編碼](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [GZIP 檔案格式規格版本4。3](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

網路頻寬是有限的資源。 減少回應的大小通常會增加應用程式的回應能力，通常會大幅增加。 減少承載大小的其中一種方式是壓縮應用程式的回應。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="when-to-use-response-compression-middleware"></a>使用回應壓縮中介軟體的時機

在 IIS、Apache 或 Nginx 中使用以伺服器為基礎的回應壓縮技術。 中介軟體的效能可能不符合伺服器模組的效能。 [HTTP.sys server](xref:fundamentals/servers/httpsys) Server 和 [Kestrel](xref:fundamentals/servers/kestrel) server 目前未提供內建壓縮支援。

當您在下列情況時，請使用回應壓縮中介軟體：

* 無法使用下列以伺服器為基礎的壓縮技術：
  * [IIS 動態壓縮模組](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Apache mod_deflate 模組](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Nginx 壓縮和解壓縮](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* 直接裝載于：
  * [HTTP.sys server](xref:fundamentals/servers/httpsys) (先前稱為 WebListener) 
  * [Kestrel 伺服器](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>回應壓縮

通常，任何未原生壓縮的回應都可受益于回應壓縮。 未原生壓縮的回應通常包括： CSS、JavaScript、HTML、XML 和 JSON。 您不應壓縮原生壓縮的資產，例如 PNG 檔案。 如果您嘗試進一步壓縮原生壓縮的回應，則在處理壓縮所花費的時間內，可能會失色任何較小的額外縮減大小和傳輸時間。 請勿壓縮小於大約150-1000 個位元組 (的檔案，視檔案的內容和壓縮) 的效率而定。 壓縮小型檔案的額外負荷可能會產生大於未壓縮檔案的壓縮檔案。

當用戶端可以處理壓縮的內容時，用戶端必須將 `Accept-Encoding` 標頭與要求一起傳送，以通知伺服器其功能。 當伺服器傳送壓縮的內容時，它必須在 `Content-Encoding` 標頭中包含壓縮回應編碼方式的資訊。 下表顯示中介軟體所支援的內容編碼方式。

| `Accept-Encoding` 標頭值 | 支援中介軟體 | 描述 |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | 否                   | [Brotli 壓縮的資料格式](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | 否                   | [DEFLATE 壓縮的資料格式](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | 否                   | [W3C 有效率的 XML 交換](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | 是 (預設)        | [GZIP 檔案格式](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | 是                  | 「無編碼」識別碼：回應不得編碼。 |
| `pack200-gzip`                  | 否                   | [JAVA 封存的網路傳輸格式](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | 是                  | 未明確要求任何可用的內容編碼 |

如需詳細資訊，請參閱 [IANA 官方內容編碼清單](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。

中介軟體可讓您為自訂標頭值新增額外的壓縮提供者 `Accept-Encoding` 。 如需詳細資訊，請參閱下方的 [自訂提供者](#custom-providers) 。

在用戶端傳送壓縮配置的優先順序時，中介軟體能夠回應品質值 (qvalue， `q`) 加權。 如需詳細資訊，請參閱 [RFC 7231：接受編碼](https://tools.ietf.org/html/rfc7231#section-5.3.4)。

壓縮演算法受限於壓縮速度與壓縮效率之間的取捨。 此內容中的 *有效性* 是指壓縮之後的輸出大小。 最大的壓縮是以最 *理想* 的壓縮來達成。

下表說明要求、傳送、快取及接收壓縮內容所涉及的標頭。

| 標頭             | 角色 |
| ------------------ | ---- |
| `Accept-Encoding`  | 從用戶端傳送到伺服器，以指出用戶端可接受的內容編碼配置。 |
| `Content-Encoding` | 從伺服器傳送至用戶端，以指出內容在承載中的編碼方式。 |
| `Content-Length`   | 發生壓縮時， `Content-Length` 會移除標頭，因為本文內容會在回應壓縮時變更。 |
| `Content-MD5`      | 發生壓縮時， `Content-MD5` 會移除標頭，因為本文內容已變更，且雜湊不再有效。 |
| `Content-Type`     | 指定內容的 MIME 類型。 每個回應都應指定其 `Content-Type` 。 中介軟體會檢查此值，以判斷是否應該壓縮回應。 中介軟體會指定一組可以編碼的 [預設 MIME 類型](#mime-types) ，但是您可以取代或加入 mime 類型。 |
| `Vary`             | 當伺服器將值的傳 `Accept-Encoding` 給用戶端和 proxy 時， `Vary` 標頭會向用戶端或 proxy 指出它應該快取 (根據要求標頭的值來) 回應 `Accept-Encoding` 。 使用標頭傳回內容的結果， `Vary: Accept-Encoding` 是分開快取壓縮和未壓縮的回應。 |

使用 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)探索回應壓縮中介軟體的功能。 此範例說明：

* 使用 Gzip 和自訂壓縮提供者壓縮應用程式回應。
* 如何將 MIME 類型加入至預設的 MIME 類型清單以進行壓縮。

## <a name="package"></a>套件

若要在專案中包含中介軟體，請新增 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app)的參考，其中包含 [AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) 套件。

## <a name="configuration"></a>組態

下列程式碼說明如何啟用預設 MIME 類型和 [Gzip 壓縮提供者](#gzip-compression-provider)的回應壓縮中介軟體：

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

注意：

* `app.UseResponseCompression` 必須在可壓縮回應的任何中介軟體之前呼叫。 如需詳細資訊，請參閱<xref:fundamentals/middleware/index#middleware-order>。
* 使用 [Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或 [Postman](https://www.getpostman.com/) 等工具來設定 `Accept-Encoding` 要求標頭，並研究回應標頭、大小和主體。

將要求提交給範例應用程式，而不使用 `Accept-Encoding` 標頭，並觀察回應是否未壓縮。 `Content-Encoding`和 `Vary` 標頭不存在於回應中。

![Fiddler 視窗，顯示沒有 Accept-Encoding 標頭的要求結果。 未壓縮回應。](response-compression/_static/request-uncompressed.png)

使用標頭將要求提交至範例應用程式 `Accept-Encoding: gzip` ，並觀察回應已壓縮。 `Content-Encoding`和 `Vary` 標頭出現在回應中。

![Fiddler 視窗顯示要求的結果，其中 Accept-Encoding 標頭和值為 gzip。 變更和內容編碼標頭會新增至回應。 回應會壓縮。](response-compression/_static/request-compressed.png)

## <a name="providers"></a>提供者

### <a name="gzip-compression-provider"></a>Gzip 壓縮提供者

使用 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> 來壓縮具有 [GZIP 檔案格式](https://tools.ietf.org/html/rfc1952)的回應。

如果未明確將壓縮提供者加入至 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> ：

* 預設會將 Gzip 壓縮提供者加入壓縮提供者的陣列中。
* 當用戶端支援 Gzip 壓縮時，壓縮預設為 Gzip。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

明確新增任何壓縮提供者時，必須加入 Gzip 壓縮提供者：

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

使用設定壓縮等級 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> 。 Gzip 壓縮提供者預設為最快的壓縮層級 ([CompressionLevel。最快](xref:System.IO.Compression.CompressionLevel) 的) ，可能不會產生最有效率的壓縮。 如果需要最有效率的壓縮，請設定中介軟體以獲得最佳壓縮。

| Compression Level | 描述 |
| ----------------- | ----------- |
| [CompressionLevel。最快](xref:System.IO.Compression.CompressionLevel) | 壓縮應該盡可能快速完成，即使產生的輸出未以最佳方式壓縮。 |
| [CompressionLevel. NoCompression](xref:System.IO.Compression.CompressionLevel) | 不應執行任何壓縮。 |
| [CompressionLevel。最佳](xref:System.IO.Compression.CompressionLevel) | 即使壓縮需要更多時間才能完成，回應仍應以最佳方式壓縮。 |

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

### <a name="custom-providers"></a>自訂提供者

使用建立自訂壓縮 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> 。 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>代表這產生的內容編碼 `ICompressionProvider` 。 中介軟體會使用這項資訊，根據要求標頭中指定的清單來選擇提供者 `Accept-Encoding` 。

使用範例應用程式時，用戶端會以 `Accept-Encoding: mycustomcompression` 標頭提交要求。 中介軟體會使用自訂壓縮執行，並傳回含有 `Content-Encoding: mycustomcompression` 標頭的回應。 用戶端必須能夠解壓縮自訂編碼，自訂壓縮執行才能運作。

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

使用標頭將要求提交至範例應用程式 `Accept-Encoding: mycustomcompression` ，並觀察回應標頭。 `Vary`和 `Content-Encoding` 標頭出現在回應中。 未 (顯示的回應主體) 不會被範例壓縮。 範例的類別中沒有壓縮的執行 `CustomCompressionProvider` 。 不過，此範例會示範您要在哪裡執行這類壓縮演算法。

![Fiddler 視窗顯示要求的結果，其中 Accept-Encoding 標頭和值為 mycustomcompression。 變更和內容編碼標頭會新增至回應。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>MIME 類型

中介軟體會針對壓縮指定一組預設的 MIME 類型：

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

以回應壓縮中介軟體選項取代或附加 MIME 類型。 請注意，不支援萬用字元 MIME 類型（例如） `text/*` 。 範例應用程式會新增的 MIME 類型 `image/svg+xml` ，並壓縮並提供 ASP.NET Core 橫幅影像 (的橫幅 *。 svg* ) 。

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a>使用安全通訊協定進行壓縮

您可以使用預設停用的選項來控制透過安全連線的壓縮回應 `EnableForHttps` 。 使用壓縮動態產生的頁面可能會導致安全性問題，例如 [犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit)) 和 [缺口](https://wikipedia.org/wiki/BREACH_(security_exploit)) 攻擊。

## <a name="adding-the-vary-header"></a>新增 Vary 標頭

根據標頭壓縮回應時 `Accept-Encoding` ，可能會有多個壓縮版本的回應和未壓縮的版本。 為了指示用戶端和 proxy 快取有多個版本存在且應該儲存， `Vary` 標頭會加上 `Accept-Encoding` 值。 在 ASP.NET Core 2.0 或更新版本中，中介軟體 `Vary` 會在回應壓縮時自動新增標頭。

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Nginx 反向 proxy 後方的中介軟體問題

當要求由 Nginx proxy 時， `Accept-Encoding` 會移除標頭。 移除 `Accept-Encoding` 標頭可防止中介軟體壓縮回應。 如需詳細資訊，請參閱 [NGINX：壓縮和解壓縮](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。 此問題的追蹤方式為 [Nginx (aspnet/BasicMiddleware #123) 的傳遞壓縮 ](https://github.com/aspnet/BasicMiddleware/issues/123)。

## <a name="working-with-iis-dynamic-compression"></a>使用 IIS 動態壓縮

如果您在想要針對應用程式停用的伺服器層級上設定作用中的 IIS 動態壓縮模組，請停用 *web.config* 檔案以外的模組。 如需詳細資訊，請參閱[停用 IIS 模組](xref:host-and-deploy/iis/modules#disabling-iis-modules)。

## <a name="troubleshooting"></a>疑難排解

使用 [Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或 [Postman](https://www.getpostman.com/)等工具，這可讓您設定 `Accept-Encoding` 要求標頭，並研究回應標頭、大小和主體。 根據預設，回應壓縮中介軟體會壓縮符合下列條件的回應：

* `Accept-Encoding`標頭存在，其值為 `gzip` 、 `*` 或自訂編碼符合您所建立的自訂壓縮提供者。 值不得為 `identity` 或具有 (qvalue 的品質值， `q`) 設定為 0 (零) 。
* 必須設定 MIME 類型 (`Content-Type`) ，且必須符合中所設定的 mime 類型 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> 。
* 要求不能包括 `Content-Range` 標頭。
* 要求必須使用不安全的通訊協定 (HTTP) ，除非在回應壓縮中介軟體選項中設定了安全通訊協定 (HTTPs) 。 *在啟用安全內容壓縮時，請注意 [上述](#compression-with-secure-protocol) 的風險。*

## <a name="additional-resources"></a>其他資源

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Mozilla 開發人員網路：接受編碼](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [RFC 7231 區段3.1.2.1： Content Codings](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230 區段4.2.3： Gzip 編碼](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [GZIP 檔案格式規格版本4。3](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end
