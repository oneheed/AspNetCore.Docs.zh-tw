---
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
ms.openlocfilehash: d5e1630d6a9e412c40831aa3a66aaed7524ff501
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551398"
---
<span data-ttu-id="00640-101">HTTP 結尾與 HTTP 標頭類似，不同之處在于它們會在傳送回應主體之後傳送。</span><span class="sxs-lookup"><span data-stu-id="00640-101">HTTP Trailers are similar to HTTP Headers, except they are sent after the response body is sent.</span></span> <span data-ttu-id="00640-102">針對 IIS 和 HTTP.sys，只支援 HTTP/2 回應尾端。</span><span class="sxs-lookup"><span data-stu-id="00640-102">For IIS and HTTP.sys, only HTTP/2 response trailers are supported.</span></span>

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

<span data-ttu-id="00640-103">在上述的範例程式碼中：</span><span class="sxs-lookup"><span data-stu-id="00640-103">In the preceding example code:</span></span>

* <span data-ttu-id="00640-104">`SupportsTrailers` 確保回應支援尾端。</span><span class="sxs-lookup"><span data-stu-id="00640-104">`SupportsTrailers` ensures that trailers are supported for the response.</span></span>
* <span data-ttu-id="00640-105">`DeclareTrailer` 將指定的尾端名稱加入至 `Trailer` 回應標頭。</span><span class="sxs-lookup"><span data-stu-id="00640-105">`DeclareTrailer` adds the given trailer name to the `Trailer` response header.</span></span> <span data-ttu-id="00640-106">宣告回應的尾端是選擇性的，但建議使用。</span><span class="sxs-lookup"><span data-stu-id="00640-106">Declaring a response's trailers is optional, but recommended.</span></span> <span data-ttu-id="00640-107">如果 `DeclareTrailer` 呼叫，則必須在傳送回應標頭之前。</span><span class="sxs-lookup"><span data-stu-id="00640-107">If `DeclareTrailer` is called, it must be before the response headers are sent.</span></span>
* <span data-ttu-id="00640-108">`AppendTrailer` 附加尾端。</span><span class="sxs-lookup"><span data-stu-id="00640-108">`AppendTrailer` appends the trailer.</span></span>
