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
ms.openlocfilehash: 1700adafb58cad57ea1becbf53cad45edd047962
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552408"
---
<span data-ttu-id="06622-101">產生的 Identity 資料庫程式碼需要 [Entity Framework Core 的遷移](/ef/core/managing-schemas/migrations/)。</span><span class="sxs-lookup"><span data-stu-id="06622-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="06622-102">建立遷移並更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="06622-102">Create a migration and update the database.</span></span> <span data-ttu-id="06622-103">例如，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="06622-103">For example, run the following commands:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06622-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06622-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="06622-105">在 Visual Studio **封裝管理員主控台** 中：</span><span class="sxs-lookup"><span data-stu-id="06622-105">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="06622-106">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="06622-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="06622-107">命令的 "Create Identity Schema" name 參數 `Add-Migration` 是任意的。</span><span class="sxs-lookup"><span data-stu-id="06622-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="06622-108">`"CreateIdentitySchema"` 描述遷移。</span><span class="sxs-lookup"><span data-stu-id="06622-108">`"CreateIdentitySchema"` describes the migration.</span></span>
