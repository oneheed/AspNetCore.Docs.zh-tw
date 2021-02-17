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
ms.openlocfilehash: 3c21d2a5604c2dfc96624fb5d161537797925fef
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552665"
---
將下列屬性新增至 `Movie` 類別：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

`Movie` 類別包含：

* `Id` 欄位，此為資料庫需要針對主索引鍵使用的必要欄位。
* `[DataType(DataType.Date)]`： [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) 屬性指定資料 (的類型 `Date`) 。 使用此屬性：

  * 使用者不需要在日期欄位中輸入時間資訊。
  * 只會顯示日期，不會顯示時間資訊。

稍後的教學課程會涵蓋 [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations)。