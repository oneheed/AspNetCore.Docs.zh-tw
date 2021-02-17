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
ms.openlocfilehash: b2519eaab7a83d66e60be05b1c2deeb8094b016f
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551922"
---
[索引] 頁面 (`wwwroot/index.html`) ] 頁面包含可 `AuthenticationService` 在 JavaScript 中定義的腳本。 `AuthenticationService` 處理 OIDC 通訊協定的低層級詳細資料。 應用程式會在內部呼叫腳本中定義的方法，以執行驗證作業。

```html
<script src="_content/Microsoft.AspNetCore.Components.WebAssembly.Authentication/
    AuthenticationService.js"></script>
```
