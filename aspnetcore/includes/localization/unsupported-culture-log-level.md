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
ms.openlocfilehash: 7caea4089d3624d4c02db4b8adbe9edb73f3d31a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552467"
---
> [!NOTE]
> <span data-ttu-id="7b6cf-101">在 ASP.NET Core 3.0 之前，web 應用程式 `LogLevel.Warning` 會在不支援所要求的文化特性時，為每個要求撰寫一個類型的記錄。</span><span class="sxs-lookup"><span data-stu-id="7b6cf-101">Prior to ASP.NET Core 3.0 web apps write one log of type `LogLevel.Warning` per request if the requested culture is unsupported.</span></span> <span data-ttu-id="7b6cf-102">`LogLevel.Warning`每個要求記錄一個會產生具有重複資訊的大型記錄檔。</span><span class="sxs-lookup"><span data-stu-id="7b6cf-102">Logging one `LogLevel.Warning` per request is can make large log files with redundant information.</span></span> <span data-ttu-id="7b6cf-103">此行為已在 ASP.NET 3.0 中變更。</span><span class="sxs-lookup"><span data-stu-id="7b6cf-103">This behavior has been changed in ASP.NET 3.0.</span></span> <span data-ttu-id="7b6cf-104">會 `RequestLocalizationMiddleware` 寫入類型的記錄檔 `LogLevel.Debug` ，以減少生產記錄的大小。</span><span class="sxs-lookup"><span data-stu-id="7b6cf-104">The `RequestLocalizationMiddleware` writes a log of type `LogLevel.Debug`, which reduces the size of production logs.</span></span>
