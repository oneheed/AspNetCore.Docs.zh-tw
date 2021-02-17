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
<span data-ttu-id="a4204-101">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="a4204-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="a4204-102">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="a4204-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="a4204-103">`Id` 欄位，此為資料庫需要針對主索引鍵使用的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="a4204-103">The `Id` field which is required by the database for the primary key.</span></span>
* <span data-ttu-id="a4204-104">`[DataType(DataType.Date)]`： [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="a4204-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="a4204-105">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="a4204-105">With this attribute:</span></span>

  * <span data-ttu-id="a4204-106">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="a4204-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="a4204-107">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="a4204-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="a4204-108">稍後的教學課程會涵蓋 [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations)。</span><span class="sxs-lookup"><span data-stu-id="a4204-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>