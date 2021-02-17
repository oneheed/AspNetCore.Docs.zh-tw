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
ms.openlocfilehash: ff0502f68546213d76fe33f45574b5d8654b522b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551243"
---
### <a name="view-the-identity-database"></a>查看 Identity 資料庫

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

* 從 [ **View** ] 功能表中，選取 [ **SQL Server 物件總管** (SSOX]) 。
* 流覽至 **(localdb) MSSQLLocalDB (SQL Server 13)**。 以滑鼠右鍵按一下 **dbo。AspNetUsers**  >  **View Data**：

![SQL Server 物件總管中 AspNetUsers 資料表上的內容功能表](~/security/authentication/accconfirm/_static/ssox.png)

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

您可以下載許多協力廠商工具來管理和查看 SQLite 資料庫，例如適用于 [sqlite 的資料庫瀏覽器](https://sqlitebrowser.org/)。

---