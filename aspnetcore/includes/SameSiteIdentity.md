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
ms.openlocfilehash: b6f6bc2e094c9070e0ea57b507f558313f19bc15
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551461"
---
ASP.NET Core [Identity](xref:security/authentication/identity) 大部分不會受到 [SameSite cookie ](xref:security/samesite) 的影響，但像是 `IFrames` 或整合的 advanced 案例除外 `OpenIdConnect` 。

使用時 `Identity` ，請勿新增任何 cookie 提供者或呼叫 ` services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)` ，就 `Identity` 會處理這項作業。