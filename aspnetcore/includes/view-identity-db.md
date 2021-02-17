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
### <a name="view-the-identity-database"></a><span data-ttu-id="58012-101">查看 Identity 資料庫</span><span class="sxs-lookup"><span data-stu-id="58012-101">View the Identity database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58012-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58012-102">Visual Studio</span></span>](#tab/visual-studio) 

* <span data-ttu-id="58012-103">從 [ **View** ] 功能表中，選取 [ **SQL Server 物件總管** (SSOX]) 。</span><span class="sxs-lookup"><span data-stu-id="58012-103">From the **View** menu, select **SQL Server Object Explorer** (SSOX).</span></span>
* <span data-ttu-id="58012-104">流覽至 **(localdb) MSSQLLocalDB (SQL Server 13)**。</span><span class="sxs-lookup"><span data-stu-id="58012-104">Navigate to **(localdb)MSSQLLocalDB(SQL Server 13)**.</span></span> <span data-ttu-id="58012-105">以滑鼠右鍵按一下 **dbo。AspNetUsers**  >  **View Data**：</span><span class="sxs-lookup"><span data-stu-id="58012-105">Right-click on **dbo.AspNetUsers** > **View Data**:</span></span>

![SQL Server 物件總管中 AspNetUsers 資料表上的內容功能表](~/security/authentication/accconfirm/_static/ssox.png)

# <a name="net-core-cli"></a>[<span data-ttu-id="58012-107">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="58012-107">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="58012-108">您可以下載許多協力廠商工具來管理和查看 SQLite 資料庫，例如適用于 [sqlite 的資料庫瀏覽器](https://sqlitebrowser.org/)。</span><span class="sxs-lookup"><span data-stu-id="58012-108">There are many third party tools you can download to manage and view a SQLite database, for example [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

---