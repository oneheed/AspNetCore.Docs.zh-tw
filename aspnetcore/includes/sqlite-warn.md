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
ms.openlocfilehash: 0cb1b72cf0e2d76b7f5752ce1314c70ff7789418
ms.sourcegitcommit: fafcf015d64aa2388bacee16ba38799daf06a4f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957494"
---
> [!NOTE]
> 
> <span data-ttu-id="69987-101">**SQLite 限制**</span><span class="sxs-lookup"><span data-stu-id="69987-101">**SQLite limitations**</span></span>
>
> <span data-ttu-id="69987-102">本教學課程盡可能使用 Entity Framework Core「移轉」[](/ef/core/managing-schemas/migrations/?tabs=dotnet-core-cli)功能。</span><span class="sxs-lookup"><span data-stu-id="69987-102">This tutorial uses the Entity Framework Core [migrations](/ef/core/managing-schemas/migrations/?tabs=dotnet-core-cli) feature where possible.</span></span> <span data-ttu-id="69987-103">移轉可更新資料庫結構描述，以符合資料模型中的變更。</span><span class="sxs-lookup"><span data-stu-id="69987-103">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="69987-104">不過，移轉只會執行資料庫引擎支援的變更種類，而 SQLite 的結構描述變更功能會受到限制。</span><span class="sxs-lookup"><span data-stu-id="69987-104">However, migrations only does the kinds of changes that the database engine supports, and SQLite's schema change capabilities are limited.</span></span> <span data-ttu-id="69987-105">例如，其支援新增資料行，但不支援移除資料行。</span><span class="sxs-lookup"><span data-stu-id="69987-105">For example, adding a column is supported, but removing a column is not supported.</span></span> <span data-ttu-id="69987-106">如果您建立移轉來移除資料行，`ef migrations add` 命令會成功；但 `ef database update` 命令會失敗。</span><span class="sxs-lookup"><span data-stu-id="69987-106">If a migration is created to remove a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> 
>
> <span data-ttu-id="69987-107">SQLite 限制的因應措施是手動撰寫移轉程式碼，以在資料表有所變更時執行資料表重建。</span><span class="sxs-lookup"><span data-stu-id="69987-107">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="69987-108">程式碼會進入 `Up` 和 `Down` 方法以進行遷移，並包括：</span><span class="sxs-lookup"><span data-stu-id="69987-108">The code goes in the `Up` and `Down` methods for a migration and involves:</span></span>
>
> * <span data-ttu-id="69987-109">建立新的資料表。</span><span class="sxs-lookup"><span data-stu-id="69987-109">Creating a new table.</span></span>
> * <span data-ttu-id="69987-110">將資料從舊的資料表複製到新的資料表。</span><span class="sxs-lookup"><span data-stu-id="69987-110">Copying data from the old table to the new table.</span></span>
> * <span data-ttu-id="69987-111">卸除舊的資料表。</span><span class="sxs-lookup"><span data-stu-id="69987-111">Dropping the old table.</span></span>
> * <span data-ttu-id="69987-112">重新命名新資料表。</span><span class="sxs-lookup"><span data-stu-id="69987-112">Renaming the new table.</span></span>
>
> <span data-ttu-id="69987-113">撰寫此型別的資料庫特定程式碼不在本教學課程範圍內。</span><span class="sxs-lookup"><span data-stu-id="69987-113">Writing database-specific code of this type is outside the scope of this tutorial.</span></span> <span data-ttu-id="69987-114">反之，當嘗試套用移轉失敗時，本教學課程會卸除並重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="69987-114">Instead, this tutorial drops and re-creates the database whenever an attempt to apply a migration would fail.</span></span> <span data-ttu-id="69987-115">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="69987-115">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="69987-116">SQLite EF Core 資料庫提供者限制</span><span class="sxs-lookup"><span data-stu-id="69987-116">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="69987-117">自訂移轉程式碼</span><span class="sxs-lookup"><span data-stu-id="69987-117">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="69987-118">資料植入</span><span class="sxs-lookup"><span data-stu-id="69987-118">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * <span data-ttu-id="69987-119">[SQLite ALTER TABLE 陳述式](https://sqlite.org/lang_altertable.html) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="69987-119">[SQLite ALTER TABLE statement](https://sqlite.org/lang_altertable.html)</span></span>