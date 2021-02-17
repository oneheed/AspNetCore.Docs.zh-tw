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
ms.openlocfilehash: 087c5a7dd20bc653a05c337dde905716032e00b3
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552615"
---
## <a name="debug-diagnostics"></a><span data-ttu-id="8bc73-101">Debug 診斷</span><span class="sxs-lookup"><span data-stu-id="8bc73-101">Debug diagnostics</span></span>

<span data-ttu-id="8bc73-102">如需詳細的路由診斷輸出，請將設定 `Logging:LogLevel:Microsoft` 為 `Debug` 。</span><span class="sxs-lookup"><span data-stu-id="8bc73-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="8bc73-103">在開發環境中，將 *appsettings.Development.js* 中的記錄層級設定為：</span><span class="sxs-lookup"><span data-stu-id="8bc73-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
