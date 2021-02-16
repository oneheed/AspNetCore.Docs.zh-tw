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
ms.openlocfilehash: 6435d39b6d442f0d4643d77d9111d11b50781544
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536276"
---
<span data-ttu-id="bb839-101">`:`分隔符號不適用於所有平臺上的環境變數階層式索引鍵。</span><span class="sxs-lookup"><span data-stu-id="bb839-101">The `:` separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="bb839-102">`__`（雙底線）為：</span><span class="sxs-lookup"><span data-stu-id="bb839-102">`__`, the double underscore, is:</span></span>

* <span data-ttu-id="bb839-103">所有平臺都支援。</span><span class="sxs-lookup"><span data-stu-id="bb839-103">Supported by all platforms.</span></span> <span data-ttu-id="bb839-104">例如， `:` [Bash](https://linuxhint.com/bash-environment-variables/)不支援分隔符號，但 `__` 為。</span><span class="sxs-lookup"><span data-stu-id="bb839-104">For example, the `:` separator is not supported by [Bash](https://linuxhint.com/bash-environment-variables/), but `__` is.</span></span>
* <span data-ttu-id="bb839-105">自動取代為 `:`</span><span class="sxs-lookup"><span data-stu-id="bb839-105">Automatically replaced by a `:`</span></span>