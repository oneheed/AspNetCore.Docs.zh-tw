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
HTTP 結尾與 HTTP 標頭類似，不同之處在于它們會在傳送回應主體之後傳送。 針對 IIS 和 HTTP.sys，只支援 HTTP/2 回應尾端。

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

在上述的範例程式碼中：

* `SupportsTrailers` 確保回應支援尾端。
* `DeclareTrailer` 將指定的尾端名稱加入至 `Trailer` 回應標頭。 宣告回應的尾端是選擇性的，但建議使用。 如果 `DeclareTrailer` 呼叫，則必須在傳送回應標頭之前。
* `AppendTrailer` 附加尾端。
