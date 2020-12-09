---
title: 第6部分：新增搜尋
author: rick-anderson
description: 頁面上教學課程系列的第6部分 Razor 。
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Index
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
uid: tutorials/razor-pages/search
ms.openlocfilehash: 1e571966b78f93e29e7901dd9648fbe3aca52726
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855478"
---
# <a name="part-6-add-search-to-aspnet-core-no-locrazor-pages"></a>第6部分：將搜尋新增至 ASP.NET Core Razor 頁面

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([如何下載](xref:index#how-to-download-a-sample))。

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([如何下載](xref:index#how-to-download-a-sample))。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

在下列各節中，會新增依「內容類型」或「名稱」搜尋電影。

將下列反白顯示的 using 語句和屬性新增至 *Pages/電影/ Index . cshtml.cs*：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=3,23,24,25,26,27)]

在先前的程式碼中：

* `SearchString`：包含使用者在 [搜尋] 文字方塊中輸入的文字。 `SearchString` 具有 [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 屬性。 `[BindProperty]` 使用與屬性相同的名稱來繫結表單值和查詢字串。 `[BindProperty(SupportsGet = true)]` 在 HTTP GET 要求上進行系結是必要的。
* `Genres`：包含內容清單。 `Genres` 可讓使用者從清單中選取內容類型。 `SelectList` 需要 `using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`：包含使用者選取的特定內容類型。 例如，"西方"。
* 稍後在本教學課程中將會使用 `Genres` 和 `MovieGenre`。

[!INCLUDE[](~/includes/bind-get.md)]

Index以下列程式碼更新頁面的 `OnGetAsync` 方法：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

`OnGetAsync` 方法的第一行會建立 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 查詢，以選取電影：

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

此查詢僅限 ***定義** _， _*_此時尚未針對_*_ 資料庫執行。

如果 `SearchString` 屬性不是 Null 或空白，則會修改電影查詢來篩選搜尋字串：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()` 程式碼是一種 [Lambda 運算式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)。 在以方法為基礎的 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 查詢中，lambda 會用來作為標準查詢運算子方法的引數，例如 [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) 方法或 `Contains` 。 在定義 LINQ 查詢時，或藉由呼叫方法（例如、或）進行修改時，不會執行 LINQ 查詢 `Where` `Contains` `OrderBy` 。 而會延後執行查詢。 運算式的評估會延遲，直到反覆運算其實現值或 `ToListAsync` 呼叫方法為止。 如需詳細資訊，請參閱[查詢執行](/dotnet/framework/data/adonet/ef/language-reference/query-execution)。

> [!NOTE]
> [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 方法是在資料庫上執行，而不是在 C# 程式碼中執行。 查詢是否區分大小寫取決於資料庫和定序。 在 SQL Server 上，`Contains` 對應至 [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql)，因此不區分大小寫。 而在 SQLlite 中，由於使用預設定序，因此會區分大小寫。

流覽至 [電影] 頁面，並將查詢字串（例如）附加 `?searchString=Ghost` 至 URL。 例如： `https://localhost:5001/Movies?searchString=Ghost` 。 隨即顯示篩選過的電影。

![：：：非 loc (索引) ：：： view](search/_static/ghost.png)

如果將下列路由範本新增至 Index 頁面，則可以將搜尋字串作為 URL 區段傳遞。 例如： `https://localhost:5001/Movies/Ghost` 。

```cshtml
@page "{searchString?}"
```

上述的路由條件約束可讓您以路由資料的形式 (URL 區段) 搜尋標題，而不是以查詢字串值的形式。  在 `?` 中，`"{searchString?}"` 表示此為選擇性的路由參數。

![：：：非 loc (索引) ：：： view 加上自動加到 Url 的字組和傳回的電影清單（Ghostbusters 和 Ghostbusters 2）](search/_static/g2.png)

ASP.NET Core 執行階段使用[模型繫結](xref:mvc/models/model-binding)來設定查詢字串 (`?searchString=Ghost`) 中的 `SearchString` 屬性值或路由傳送資料 (`https://localhost:5001/Movies/Ghost`)。 模型系結 _*_不_*_ 區分大小寫。

但是，使用者無法修改 URL 來搜尋電影。 在此步驟中，會新增用來篩選電影的 UI。 如果您已新增路由條件約束 `"{searchString?}"`，請將它移除。

開啟 _Pages/Movies/] 檔案 Index ，並新增在下列程式碼中反白顯示的標記：

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

HTML `<form>` 標籤會使用下列[標籤協助程式](xref:mvc/views/tag-helpers/intro)：

* [表單標記](xref:mvc/views/working-with-forms#the-form-tag-helper)協助程式。 提交表單時，會透過查詢字串將篩選字串傳送至 *頁面/電影 Index /* 頁面。
* [輸入標記協助程式](xref:mvc/views/working-with-forms#the-input-tag-helper)

儲存變更並測試篩選條件。

![：：：非 loc (索引) ：：： view 加上具類型的字組至標題篩選文字方塊](search/_static/filter.png)

## <a name="search-by-genre"></a>依內容類型搜尋

Index以下列程式碼更新頁面的 `OnGetAsync` 方法：

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

下列程式碼是一種 LINQ 查詢，其會從資料庫中擷取所有的內容類型。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

內容類型的 `SelectList` 是由投影不同的內容類型來建立。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a>將依內容類型搜尋新增至 Razor 頁面

1. 將 *Index cshtml* [ `<form>` element] (更新 https://developer.mozilla.org/docs/Web/HTML/Element/form) 為在下列標記中反白顯示：

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

1. 依據內容類型、電影標題和這兩者進行搜尋，藉以測試應用程式。

## <a name="additional-resources"></a>其他資源

> [!div class="step-by-step"]
> [上一步：更新頁面](xref:tutorials/razor-pages/da1) 
> [下一步：新增欄位](xref:tutorials/razor-pages/new-field)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([如何下載](xref:index#how-to-download-a-sample))。

在下列各節中，會新增依「內容類型」或「名稱」搜尋電影。

將下列反白顯示的屬性新增至 *Pages/電影/ Index . cshtml.cs*：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`：包含使用者在 [搜尋] 文字方塊中輸入的文字。 `SearchString` 具有 [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 屬性。 `[BindProperty]` 使用與屬性相同的名稱來繫結表單值和查詢字串。 `[BindProperty(SupportsGet = true)]` 在 HTTP GET 要求上進行系結是必要的。
* `Genres`：包含內容清單。 `Genres` 可讓使用者從清單中選取內容類型。 `SelectList` 需要 `using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`：包含使用者選取的特定內容類型。 例如，"西方"。
* 稍後在本教學課程中將會使用 `Genres` 和 `MovieGenre`。

[!INCLUDE[](~/includes/bind-get.md)]

Index以下列程式碼更新頁面的 `OnGetAsync` 方法：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

`OnGetAsync` 方法的第一行會建立 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 查詢，以選取電影：

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

這時候，系統只會「定義」查詢，而尚 **未** 對資料庫執行查詢。

如果 `SearchString` 屬性不是 Null 或空白，則會修改電影查詢來篩選搜尋字串：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()` 程式碼是一種 [Lambda 運算式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)。 在以方法為基礎的 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 查詢中，lambda 會用來作為標準查詢運算子方法的引數，例如 [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) 方法或 `Contains` 。 在定義 LINQ 查詢或藉由呼叫方法（例如或）進行修改時，不會執行 LINQ `Where` 查詢 `Contains` `OrderBy` 。 而會延後執行查詢。 運算式的評估會延遲，直到反覆運算其實現值或 `ToListAsync` 呼叫方法為止。 如需詳細資訊，請參閱[查詢執行](/dotnet/framework/data/adonet/ef/language-reference/query-execution)。

**注意：**[Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 方法是在資料庫上執行，而不是在 C# 程式碼中執行。 查詢是否區分大小寫取決於資料庫和定序。 在 SQL Server 上，`Contains` 對應至 [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql)，因此不區分大小寫。 而在 SQLlite 中，由於使用預設定序，因此會區分大小寫。

流覽至 [電影] 頁面，並將查詢字串（例如）附加 `?searchString=Ghost` 至 URL。 例如： `https://localhost:5001/Movies?searchString=Ghost` 。 隨即顯示篩選過的電影。

![：：：非 loc (索引) ：：： view](search/_static/ghost.png)

如果將下列路由範本新增至 Index 頁面，則可以將搜尋字串作為 URL 區段傳遞。 例如： `https://localhost:5001/Movies/Ghost` 。

```cshtml
@page "{searchString?}"
```

上述的路由條件約束可讓您以路由資料的形式 (URL 區段) 搜尋標題，而不是以查詢字串值的形式。  在 `?` 中，`"{searchString?}"` 表示此為選擇性的路由參數。

![：：：非 loc (索引) ：：： view 加上自動加到 Url 的字組和傳回的電影清單（Ghostbusters 和 Ghostbusters 2）](search/_static/g2.png)

ASP.NET Core 執行階段使用[模型繫結](xref:mvc/models/model-binding)來設定查詢字串 (`?searchString=Ghost`) 中的 `SearchString` 屬性值或路由傳送資料 (`https://localhost:5001/Movies/Ghost`)。 模型系 *_結不是_* 區分大小寫。

但是，使用者無法修改 URL 來搜尋電影。 在此步驟中，會新增用來篩選電影的 UI。 如果您已新增路由條件約束 `"{searchString?}"`，請將它移除。

開啟 _Pages/Movies/] 檔案 Index ，並新增 `<form>` 在下列程式碼中反白顯示的標記：

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

HTML `<form>` 標籤會使用下列[標籤協助程式](xref:mvc/views/tag-helpers/intro)：

* [表單標記](xref:mvc/views/working-with-forms#the-form-tag-helper)協助程式。 提交表單時，會透過查詢字串將篩選字串傳送至 *頁面/電影 Index /* 頁面。
* [輸入標記協助程式](xref:mvc/views/working-with-forms#the-input-tag-helper)

儲存變更並測試篩選條件。

![：：：非 loc (索引) ：：： view 加上具類型的字組至標題篩選文字方塊](search/_static/filter.png)

## <a name="search-by-genre"></a>依內容類型搜尋

以下列程式碼更新 `OnGetAsync` 方法：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

下列程式碼是一種 LINQ 查詢，其會從資料庫中擷取所有的內容類型。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

內容類型的 `SelectList` 是由投影不同的內容類型來建立。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a>將依內容類型搜尋新增至 Razor 頁面

將 *Index cshtml* [ `<form>` element] (更新 https://developer.mozilla.org/docs/Web/HTML/Element/form) 為在下列標記中反白顯示：

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

依據內容類型、電影標題和這兩者進行搜尋，藉以測試應用程式。
上述程式碼使用 [選取標記](xref:mvc/views/working-with-forms#the-select-tag-helper) 協助程式和選項標記協助程式。

## <a name="additional-resources"></a>其他資源

* [本教學課程的 YouTube 版本](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [上一步：更新頁面](xref:tutorials/razor-pages/da1) 
> [下一步：新增欄位](xref:tutorials/razor-pages/new-field)

::: moniker-end
