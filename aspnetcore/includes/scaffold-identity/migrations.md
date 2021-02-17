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
產生的 Identity 資料庫程式碼需要 [Entity Framework Core 的遷移](/ef/core/managing-schemas/migrations/)。 建立遷移並更新資料庫。 例如，執行下列命令：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在 Visual Studio **封裝管理員主控台** 中：

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

命令的 "Create Identity Schema" name 參數 `Add-Migration` 是任意的。 `"CreateIdentitySchema"` 描述遷移。
