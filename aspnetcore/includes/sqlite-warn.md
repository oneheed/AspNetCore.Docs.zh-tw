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
> **SQLite 限制**
>
> 本教學課程盡可能使用 Entity Framework Core「移轉」[](/ef/core/managing-schemas/migrations/?tabs=dotnet-core-cli)功能。 移轉可更新資料庫結構描述，以符合資料模型中的變更。 不過，移轉只會執行資料庫引擎支援的變更種類，而 SQLite 的結構描述變更功能會受到限制。 例如，其支援新增資料行，但不支援移除資料行。 如果您建立移轉來移除資料行，`ef migrations add` 命令會成功；但 `ef database update` 命令會失敗。 
>
> SQLite 限制的因應措施是手動撰寫移轉程式碼，以在資料表有所變更時執行資料表重建。 程式碼會進入 `Up` 和 `Down` 方法以進行遷移，並包括：
>
> * 建立新的資料表。
> * 將資料從舊的資料表複製到新的資料表。
> * 卸除舊的資料表。
> * 重新命名新資料表。
>
> 撰寫此型別的資料庫特定程式碼不在本教學課程範圍內。 反之，當嘗試套用移轉失敗時，本教學課程會卸除並重新建立資料庫。 如需詳細資訊，請參閱下列資源：
>
> * [SQLite EF Core 資料庫提供者限制](/ef/core/providers/sqlite/limitations)
> * [自訂移轉程式碼](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [資料植入](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE 陳述式](https://sqlite.org/lang_altertable.html) \(英文\)