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
ms.openlocfilehash: 9ca3b6ef5f0d4c68aaaf54ef3b458ba66fc43e97
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711605"
---
<a name="sqlite-dev"></a>
### <a name="use-sqlite-for-development-sql-server-for-production"></a>使用 SQLite 進行開發，SQL Server 生產環境

選取 SQLite 時，範本產生的程式碼就可以開始開發。 下列程式碼將示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 至 `Startup` 。 `IWebHostEnvironment` 已插入，因此 `ConfigureServices` 可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]
