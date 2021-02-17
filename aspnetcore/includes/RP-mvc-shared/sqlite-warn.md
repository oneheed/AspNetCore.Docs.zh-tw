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
ms.openlocfilehash: e8c5bd00178aefb91ab0e7066c5490ceba315530
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552619"
---
> [!NOTE]
> <span data-ttu-id="dc9ad-101">在本教學課程中，請盡可能使用 Entity Framework Core「移轉」功能。</span><span class="sxs-lookup"><span data-stu-id="dc9ad-101">For this tutorial you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="dc9ad-102">移轉可更新資料庫結構描述，以符合資料模型中的變更。</span><span class="sxs-lookup"><span data-stu-id="dc9ad-102">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="dc9ad-103">不過，移轉只能進行 EF Core 提供者支援的變更類型，SQLite 提供者的功能則有限制。</span><span class="sxs-lookup"><span data-stu-id="dc9ad-103">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="dc9ad-104">例如，其支援新增資料行，但不支援移除或變更資料行。</span><span class="sxs-lookup"><span data-stu-id="dc9ad-104">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="dc9ad-105">如果您建立移轉來移除或變更資料行，`ef migrations add` 命令會成功，但 `ef database update` 命令會失敗。</span><span class="sxs-lookup"><span data-stu-id="dc9ad-105">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="dc9ad-106">由於這些限制，本教學課程不會將移轉用於 SQLite 結構描述變更。</span><span class="sxs-lookup"><span data-stu-id="dc9ad-106">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="dc9ad-107">反之，當結構描述變更時，請您卸除並重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="dc9ad-107">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="dc9ad-108">SQLite 限制的因應措施是手動撰寫移轉程式碼，以在資料表有所變更時執行資料表重建。</span><span class="sxs-lookup"><span data-stu-id="dc9ad-108">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="dc9ad-109">重建資料表包含：</span><span class="sxs-lookup"><span data-stu-id="dc9ad-109">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="dc9ad-110">建立新的資料表。</span><span class="sxs-lookup"><span data-stu-id="dc9ad-110">Creating a new table.</span></span>
>* <span data-ttu-id="dc9ad-111">將資料從舊的資料表複製到新的資料表。</span><span class="sxs-lookup"><span data-stu-id="dc9ad-111">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="dc9ad-112">卸除舊的資料表。</span><span class="sxs-lookup"><span data-stu-id="dc9ad-112">Dropping the old table.</span></span>
>* <span data-ttu-id="dc9ad-113">重新命名新資料表。</span><span class="sxs-lookup"><span data-stu-id="dc9ad-113">Renaming the new table.</span></span>
>
><span data-ttu-id="dc9ad-114">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="dc9ad-114">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="dc9ad-115">SQLite EF Core 資料庫提供者限制</span><span class="sxs-lookup"><span data-stu-id="dc9ad-115">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="dc9ad-116">自訂移轉程式碼</span><span class="sxs-lookup"><span data-stu-id="dc9ad-116">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="dc9ad-117">資料植入</span><span class="sxs-lookup"><span data-stu-id="dc9ad-117">Data seeding</span></span>](/ef/core/modeling/data-seeding)
  * <span data-ttu-id="dc9ad-118">[SQLite ALTER TABLE 陳述式](https://sqlite.org/lang_altertable.html) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="dc9ad-118">[SQLite ALTER TABLE statement](https://sqlite.org/lang_altertable.html)</span></span>