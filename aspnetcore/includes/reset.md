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
Reset 可讓伺服器重設具有指定錯誤碼的 HTTP/2 要求。 重設要求會視為已中止。

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

`Reset` 在上述程式碼範例中，指定 `INTERNAL_ERROR` 錯誤碼。 如需有關 HTTP/2 錯誤碼的詳細資訊，請造訪 [HTTP/2 規格錯誤碼一節](https://tools.ietf.org/html/rfc7540#page-50)。