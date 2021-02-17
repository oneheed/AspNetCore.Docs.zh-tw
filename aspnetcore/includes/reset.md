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
ms.openlocfilehash: 164c9e8f73502fc627c4514bd683dc183d318b1d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551242"
---
<span data-ttu-id="7a297-101">Reset 可讓伺服器重設具有指定錯誤碼的 HTTP/2 要求。</span><span class="sxs-lookup"><span data-stu-id="7a297-101">Reset allows for the server to reset a HTTP/2 request with a specified error code.</span></span> <span data-ttu-id="7a297-102">重設要求會視為已中止。</span><span class="sxs-lookup"><span data-stu-id="7a297-102">A reset request is considered aborted.</span></span>

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

<span data-ttu-id="7a297-103">`Reset` 在上述程式碼範例中，指定 `INTERNAL_ERROR` 錯誤碼。</span><span class="sxs-lookup"><span data-stu-id="7a297-103">`Reset` in the preceding code example specifies the `INTERNAL_ERROR` error code.</span></span> <span data-ttu-id="7a297-104">如需有關 HTTP/2 錯誤碼的詳細資訊，請造訪 [HTTP/2 規格錯誤碼一節](https://tools.ietf.org/html/rfc7540#page-50)。</span><span class="sxs-lookup"><span data-stu-id="7a297-104">For more information about HTTP/2 error codes, visit the [HTTP/2 specification error code section](https://tools.ietf.org/html/rfc7540#page-50).</span></span>