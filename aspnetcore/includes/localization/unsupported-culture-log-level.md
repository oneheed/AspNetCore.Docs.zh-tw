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
> 在 ASP.NET Core 3.0 之前，web 應用程式 `LogLevel.Warning` 會在不支援所要求的文化特性時，為每個要求撰寫一個類型的記錄。 `LogLevel.Warning`每個要求記錄一個會產生具有重複資訊的大型記錄檔。 此行為已在 ASP.NET 3.0 中變更。 會 `RequestLocalizationMiddleware` 寫入類型的記錄檔 `LogLevel.Debug` ，以減少生產記錄的大小。
