---
title: 第6部分：新增搜尋
author: rick-anderson
description: 頁面上教學課程系列的第6部分 Razor 。
ms.author: riande
ms.date: 12/05/2019
ms.custom: contperf-fy21q2
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
ms.openlocfilehash: 19e20923d5f4cd2bf8671df7558995d34da2cdf6
ms.sourcegitcommit: d5fa39765959738eed4bcf5ee0b207cefddb4873
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2021
ms.locfileid: "103460426"
---
# <a name="part-6-add-search-to-aspnet-core-razor-pages"></a><span data-ttu-id="9d561-103">第6部分：將搜尋新增至 ASP.NET 核心 Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="9d561-103">Part 6, add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="9d561-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9d561-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9d561-105">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9d561-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="9d561-106">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9d561-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9d561-107">在下列各節中，會新增依「內容類型」或「名稱」搜尋電影。</span><span class="sxs-lookup"><span data-stu-id="9d561-107">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="9d561-108">將下列反白顯示的 using 語句和屬性新增至 *Pages/電影/ Index . cshtml.cs*：</span><span class="sxs-lookup"><span data-stu-id="9d561-108">Add the following highlighted using statement and properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=3,23,24,25,26,27)]

<span data-ttu-id="9d561-109">在先前的程式碼中：</span><span class="sxs-lookup"><span data-stu-id="9d561-109">In the previous code:</span></span>

* <span data-ttu-id="9d561-110">`SearchString`：包含使用者在 [搜尋] 文字方塊中輸入的文字。</span><span class="sxs-lookup"><span data-stu-id="9d561-110">`SearchString`: Contains the text users enter in the search text box.</span></span> <span data-ttu-id="9d561-111">`SearchString` 具有 [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 屬性。</span><span class="sxs-lookup"><span data-stu-id="9d561-111">`SearchString` has the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute.</span></span> <span data-ttu-id="9d561-112">`[BindProperty]` 使用與屬性相同的名稱來繫結表單值和查詢字串。</span><span class="sxs-lookup"><span data-stu-id="9d561-112">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="9d561-113">`[BindProperty(SupportsGet = true)]` 在 HTTP GET 要求上進行系結是必要的。</span><span class="sxs-lookup"><span data-stu-id="9d561-113">`[BindProperty(SupportsGet = true)]` is required for binding on HTTP GET requests.</span></span>
* <span data-ttu-id="9d561-114">`Genres`：包含內容清單。</span><span class="sxs-lookup"><span data-stu-id="9d561-114">`Genres`: Contains the list of genres.</span></span> <span data-ttu-id="9d561-115">`Genres` 可讓使用者從清單中選取內容類型。</span><span class="sxs-lookup"><span data-stu-id="9d561-115">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="9d561-116">`SelectList` 需要 `using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="9d561-116">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="9d561-117">`MovieGenre`：包含使用者選取的特定內容類型。</span><span class="sxs-lookup"><span data-stu-id="9d561-117">`MovieGenre`: Contains the specific genre the user selects.</span></span> <span data-ttu-id="9d561-118">例如，"西方"。</span><span class="sxs-lookup"><span data-stu-id="9d561-118">For example, "Western".</span></span>
* <span data-ttu-id="9d561-119">稍後在本教學課程中將會使用 `Genres` 和 `MovieGenre`。</span><span class="sxs-lookup"><span data-stu-id="9d561-119">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="9d561-120">Index以下列程式碼更新頁面的 `OnGetAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="9d561-120">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="9d561-121">`OnGetAsync` 方法的第一行會建立 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 查詢，以選取電影：</span><span class="sxs-lookup"><span data-stu-id="9d561-121">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="9d561-122">此查詢僅限 \***定義** _，此時會針對資料庫執行 _ \*_not_\*\*。</span><span class="sxs-lookup"><span data-stu-id="9d561-122">The query is only ***defined** _ at this point, it has _ *_not_** been run against the database.</span></span>

<span data-ttu-id="9d561-123">如果 `SearchString` 屬性不是 Null 或空白，則會修改電影查詢來篩選搜尋字串：</span><span class="sxs-lookup"><span data-stu-id="9d561-123">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="9d561-124">`s => s.Title.Contains()` 程式碼是一種 [Lambda 運算式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)。</span><span class="sxs-lookup"><span data-stu-id="9d561-124">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="9d561-125">在以方法為基礎的 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 查詢中，lambda 會用來作為標準查詢運算子方法的引數，例如 [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) 方法或 `Contains` 。</span><span class="sxs-lookup"><span data-stu-id="9d561-125">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains`.</span></span> <span data-ttu-id="9d561-126">在定義 LINQ 查詢時，或藉由呼叫方法（例如、或）進行修改時，不會執行 LINQ 查詢 `Where` `Contains` `OrderBy` 。</span><span class="sxs-lookup"><span data-stu-id="9d561-126">LINQ queries are not executed when they're defined or when they're modified by calling a method, such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="9d561-127">而會延後執行查詢。</span><span class="sxs-lookup"><span data-stu-id="9d561-127">Rather, query execution is deferred.</span></span> <span data-ttu-id="9d561-128">運算式的評估會延遲，直到反覆運算其實現值或 `ToListAsync` 呼叫方法為止。</span><span class="sxs-lookup"><span data-stu-id="9d561-128">The evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="9d561-129">如需詳細資訊，請參閱[查詢執行](/dotnet/framework/data/adonet/ef/language-reference/query-execution)。</span><span class="sxs-lookup"><span data-stu-id="9d561-129">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9d561-130">[Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 方法是在資料庫上執行，而不是在 C# 程式碼中執行。</span><span class="sxs-lookup"><span data-stu-id="9d561-130">The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="9d561-131">查詢是否區分大小寫取決於資料庫和定序。</span><span class="sxs-lookup"><span data-stu-id="9d561-131">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="9d561-132">在 SQL Server 上，`Contains` 對應至 [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql)，因此不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="9d561-132">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="9d561-133">而在 SQLlite 中，由於使用預設定序，因此會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="9d561-133">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="9d561-134">流覽至 [電影] 頁面，並將查詢字串（例如）附加 `?searchString=Ghost` 至 URL。</span><span class="sxs-lookup"><span data-stu-id="9d561-134">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="9d561-135">例如： `https://localhost:5001/Movies?searchString=Ghost` 。</span><span class="sxs-lookup"><span data-stu-id="9d561-135">For example, `https://localhost:5001/Movies?searchString=Ghost`.</span></span> <span data-ttu-id="9d561-136">隨即顯示篩選過的電影。</span><span class="sxs-lookup"><span data-stu-id="9d561-136">The filtered movies are displayed.</span></span>

![：：：非 loc (索引) ：：： view](search/_static/ghost.png)

<span data-ttu-id="9d561-138">如果將下列路由範本新增至 Index 頁面，則可以將搜尋字串作為 URL 區段傳遞。</span><span class="sxs-lookup"><span data-stu-id="9d561-138">If the following route template is added to the Index page, the search string can be passed as a URL segment.</span></span> <span data-ttu-id="9d561-139">例如： `https://localhost:5001/Movies/Ghost` 。</span><span class="sxs-lookup"><span data-stu-id="9d561-139">For example, `https://localhost:5001/Movies/Ghost`.</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="9d561-140">上述的路由條件約束可讓您以路由資料的形式 (URL 區段) 搜尋標題，而不是以查詢字串值的形式。</span><span class="sxs-lookup"><span data-stu-id="9d561-140">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="9d561-141">在 `?` 中，`"{searchString?}"` 表示此為選擇性的路由參數。</span><span class="sxs-lookup"><span data-stu-id="9d561-141">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![：：：非 loc (索引) ：：： view 加上自動加到 Url 的字組和傳回的電影清單（Ghostbusters 和 Ghostbusters 2）](search/_static/g2.png)

<span data-ttu-id="9d561-143">ASP.NET Core 執行階段使用[模型繫結](xref:mvc/models/model-binding)來設定查詢字串 (`?searchString=Ghost`) 中的 `SearchString` 屬性值或路由傳送資料 (`https://localhost:5001/Movies/Ghost`)。</span><span class="sxs-lookup"><span data-stu-id="9d561-143">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="9d561-144">模型系結 ***不*** 區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="9d561-144">Model binding is ***not*** case sensitive.</span></span>

<span data-ttu-id="9d561-145">但是，使用者無法修改 URL 來搜尋電影。</span><span class="sxs-lookup"><span data-stu-id="9d561-145">However, users cannot be expected to modify the URL to search for a movie.</span></span> <span data-ttu-id="9d561-146">在此步驟中，會新增用來篩選電影的 UI。</span><span class="sxs-lookup"><span data-stu-id="9d561-146">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="9d561-147">如果您已新增路由條件約束 `"{searchString?}"`，請將它移除。</span><span class="sxs-lookup"><span data-stu-id="9d561-147">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="9d561-148">開啟 *Pages/電影/ Index cshtml* 檔案，並新增下列程式碼中反白顯示的標記：</span><span class="sxs-lookup"><span data-stu-id="9d561-148">Open the *Pages/Movies/Index.cshtml* file, and add the markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="9d561-149">HTML `<form>` 標籤會使用下列[標籤協助程式](xref:mvc/views/tag-helpers/intro)：</span><span class="sxs-lookup"><span data-stu-id="9d561-149">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="9d561-150">[表單標記](xref:mvc/views/working-with-forms#the-form-tag-helper)協助程式。</span><span class="sxs-lookup"><span data-stu-id="9d561-150">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="9d561-151">提交表單時，會透過查詢字串將篩選字串傳送至 *頁面/電影 Index /* 頁面。</span><span class="sxs-lookup"><span data-stu-id="9d561-151">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="9d561-152">輸入標記協助程式</span><span class="sxs-lookup"><span data-stu-id="9d561-152">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="9d561-153">儲存變更並測試篩選條件。</span><span class="sxs-lookup"><span data-stu-id="9d561-153">Save the changes and test the filter.</span></span>

![：：：非 loc (索引) ：：： view 加上具類型的字組至標題篩選文字方塊](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="9d561-155">依內容類型搜尋</span><span class="sxs-lookup"><span data-stu-id="9d561-155">Search by genre</span></span>

<span data-ttu-id="9d561-156">Index以下列程式碼更新頁面的 `OnGetAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="9d561-156">Update the Index page's `OnGetAsync` method with the following code:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="9d561-157">下列程式碼是一種 LINQ 查詢，其會從資料庫中擷取所有的內容類型。</span><span class="sxs-lookup"><span data-stu-id="9d561-157">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="9d561-158">內容類型的 `SelectList` 是由投影不同的內容類型來建立。</span><span class="sxs-lookup"><span data-stu-id="9d561-158">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="9d561-159">將依內容類型搜尋新增至 Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="9d561-159">Add search by genre to the Razor Page</span></span>

1. <span data-ttu-id="9d561-160">在下列標記中，以反白顯示的方式更新 *Index cshtml* [ `<form>` 元素](https://developer.mozilla.org/docs/Web/HTML/Element/form)：</span><span class="sxs-lookup"><span data-stu-id="9d561-160">Update the *Index.cshtml* [`<form>` element](https://developer.mozilla.org/docs/Web/HTML/Element/form) as highlighted in the following markup:</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

1. <span data-ttu-id="9d561-161">依據內容類型、電影標題和這兩者進行搜尋，藉以測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="9d561-161">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9d561-162">其他資源</span><span class="sxs-lookup"><span data-stu-id="9d561-162">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9d561-163">[上一步：更新頁面](xref:tutorials/razor-pages/da1) 
> [下一步：新增欄位](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="9d561-163">[Previous: Update the pages](xref:tutorials/razor-pages/da1)
[Next: Add a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9d561-164">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9d561-164">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="9d561-165">在下列各節中，會新增依「內容類型」或「名稱」搜尋電影。</span><span class="sxs-lookup"><span data-stu-id="9d561-165">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="9d561-166">將下列反白顯示的屬性新增至 *Pages/電影/ Index . cshtml.cs*：</span><span class="sxs-lookup"><span data-stu-id="9d561-166">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="9d561-167">`SearchString`：包含使用者在 [搜尋] 文字方塊中輸入的文字。</span><span class="sxs-lookup"><span data-stu-id="9d561-167">`SearchString`: Contains the text users enter in the search text box.</span></span> <span data-ttu-id="9d561-168">`SearchString` 具有 [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 屬性。</span><span class="sxs-lookup"><span data-stu-id="9d561-168">`SearchString` has the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute.</span></span> <span data-ttu-id="9d561-169">`[BindProperty]` 使用與屬性相同的名稱來繫結表單值和查詢字串。</span><span class="sxs-lookup"><span data-stu-id="9d561-169">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="9d561-170">`[BindProperty(SupportsGet = true)]` 在 HTTP GET 要求上進行系結是必要的。</span><span class="sxs-lookup"><span data-stu-id="9d561-170">`[BindProperty(SupportsGet = true)]` is required for binding on HTTP GET requests.</span></span>
* <span data-ttu-id="9d561-171">`Genres`：包含內容清單。</span><span class="sxs-lookup"><span data-stu-id="9d561-171">`Genres`: Contains the list of genres.</span></span> <span data-ttu-id="9d561-172">`Genres` 可讓使用者從清單中選取內容類型。</span><span class="sxs-lookup"><span data-stu-id="9d561-172">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="9d561-173">`SelectList` 需要 `using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="9d561-173">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="9d561-174">`MovieGenre`：包含使用者選取的特定內容類型。</span><span class="sxs-lookup"><span data-stu-id="9d561-174">`MovieGenre`: Contains the specific genre the user selects.</span></span> <span data-ttu-id="9d561-175">例如，"西方"。</span><span class="sxs-lookup"><span data-stu-id="9d561-175">For example, "Western".</span></span>
* <span data-ttu-id="9d561-176">稍後在本教學課程中將會使用 `Genres` 和 `MovieGenre`。</span><span class="sxs-lookup"><span data-stu-id="9d561-176">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="9d561-177">Index以下列程式碼更新頁面的 `OnGetAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="9d561-177">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="9d561-178">`OnGetAsync` 方法的第一行會建立 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 查詢，以選取電影：</span><span class="sxs-lookup"><span data-stu-id="9d561-178">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="9d561-179">這時候，系統只會「定義」查詢，而尚 **未** 對資料庫執行查詢。</span><span class="sxs-lookup"><span data-stu-id="9d561-179">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="9d561-180">如果 `SearchString` 屬性不是 Null 或空白，則會修改電影查詢來篩選搜尋字串：</span><span class="sxs-lookup"><span data-stu-id="9d561-180">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="9d561-181">`s => s.Title.Contains()` 程式碼是一種 [Lambda 運算式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)。</span><span class="sxs-lookup"><span data-stu-id="9d561-181">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="9d561-182">在以方法為基礎的 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 查詢中，lambda 會用來作為標準查詢運算子方法的引數，例如 [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) 方法或 `Contains` 。</span><span class="sxs-lookup"><span data-stu-id="9d561-182">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains`.</span></span> <span data-ttu-id="9d561-183">在定義 LINQ 查詢或藉由呼叫方法（例如或）進行修改時，不會執行 LINQ `Where` 查詢 `Contains` `OrderBy` 。</span><span class="sxs-lookup"><span data-stu-id="9d561-183">LINQ queries are not executed when they're defined or when they're modified by calling a method, such as `Where`, `Contains`  or `OrderBy`.</span></span> <span data-ttu-id="9d561-184">而會延後執行查詢。</span><span class="sxs-lookup"><span data-stu-id="9d561-184">Rather, query execution is deferred.</span></span> <span data-ttu-id="9d561-185">運算式的評估會延遲，直到反覆運算其實現值或 `ToListAsync` 呼叫方法為止。</span><span class="sxs-lookup"><span data-stu-id="9d561-185">The evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="9d561-186">如需詳細資訊，請參閱[查詢執行](/dotnet/framework/data/adonet/ef/language-reference/query-execution)。</span><span class="sxs-lookup"><span data-stu-id="9d561-186">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="9d561-187">**注意：**[Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 方法是在資料庫上執行，而不是在 C# 程式碼中執行。</span><span class="sxs-lookup"><span data-stu-id="9d561-187">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="9d561-188">查詢是否區分大小寫取決於資料庫和定序。</span><span class="sxs-lookup"><span data-stu-id="9d561-188">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="9d561-189">在 SQL Server 上，`Contains` 對應至 [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql)，因此不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="9d561-189">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="9d561-190">而在 SQLlite 中，由於使用預設定序，因此會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="9d561-190">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="9d561-191">流覽至 [電影] 頁面，並將查詢字串（例如）附加 `?searchString=Ghost` 至 URL。</span><span class="sxs-lookup"><span data-stu-id="9d561-191">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="9d561-192">例如： `https://localhost:5001/Movies?searchString=Ghost` 。</span><span class="sxs-lookup"><span data-stu-id="9d561-192">For example, `https://localhost:5001/Movies?searchString=Ghost`.</span></span> <span data-ttu-id="9d561-193">隨即顯示篩選過的電影。</span><span class="sxs-lookup"><span data-stu-id="9d561-193">The filtered movies are displayed.</span></span>

![：：：非 loc (索引) ：：： view](search/_static/ghost.png)

<span data-ttu-id="9d561-195">如果將下列路由範本新增至 Index 頁面，則可以將搜尋字串作為 URL 區段傳遞。</span><span class="sxs-lookup"><span data-stu-id="9d561-195">If the following route template is added to the Index page, the search string can be passed as a URL segment.</span></span> <span data-ttu-id="9d561-196">例如： `https://localhost:5001/Movies/Ghost` 。</span><span class="sxs-lookup"><span data-stu-id="9d561-196">For example, `https://localhost:5001/Movies/Ghost`.</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="9d561-197">上述的路由條件約束可讓您以路由資料的形式 (URL 區段) 搜尋標題，而不是以查詢字串值的形式。</span><span class="sxs-lookup"><span data-stu-id="9d561-197">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="9d561-198">在 `?` 中，`"{searchString?}"` 表示此為選擇性的路由參數。</span><span class="sxs-lookup"><span data-stu-id="9d561-198">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![：：：非 loc (索引) ：：： view 加上自動加到 Url 的字組和傳回的電影清單（Ghostbusters 和 Ghostbusters 2）](search/_static/g2.png)

<span data-ttu-id="9d561-200">ASP.NET Core 執行階段使用[模型繫結](xref:mvc/models/model-binding)來設定查詢字串 (`?searchString=Ghost`) 中的 `SearchString` 屬性值或路由傳送資料 (`https://localhost:5001/Movies/Ghost`)。</span><span class="sxs-lookup"><span data-stu-id="9d561-200">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="9d561-201">模型系結 ***不*** 區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="9d561-201">Model binding is ***not*** case sensitive.</span></span>

<span data-ttu-id="9d561-202">但是，使用者無法修改 URL 來搜尋電影。</span><span class="sxs-lookup"><span data-stu-id="9d561-202">However, users can't be expected to modify the URL to search for a movie.</span></span> <span data-ttu-id="9d561-203">在此步驟中，會新增用來篩選電影的 UI。</span><span class="sxs-lookup"><span data-stu-id="9d561-203">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="9d561-204">如果您已新增路由條件約束 `"{searchString?}"`，請將它移除。</span><span class="sxs-lookup"><span data-stu-id="9d561-204">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="9d561-205">開啟 *Pages/電影/ Index cshtml* 檔案，並新增 `<form>` 下列程式碼中反白顯示的標記：</span><span class="sxs-lookup"><span data-stu-id="9d561-205">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="9d561-206">HTML `<form>` 標籤會使用下列[標籤協助程式](xref:mvc/views/tag-helpers/intro)：</span><span class="sxs-lookup"><span data-stu-id="9d561-206">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="9d561-207">[表單標記](xref:mvc/views/working-with-forms#the-form-tag-helper)協助程式。</span><span class="sxs-lookup"><span data-stu-id="9d561-207">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="9d561-208">提交表單時，會透過查詢字串將篩選字串傳送至 *頁面/電影 Index /* 頁面。</span><span class="sxs-lookup"><span data-stu-id="9d561-208">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="9d561-209">輸入標記協助程式</span><span class="sxs-lookup"><span data-stu-id="9d561-209">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="9d561-210">儲存變更並測試篩選條件。</span><span class="sxs-lookup"><span data-stu-id="9d561-210">Save the changes and test the filter.</span></span>

![：：：非 loc (索引) ：：： view 加上具類型的字組至標題篩選文字方塊](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="9d561-212">依內容類型搜尋</span><span class="sxs-lookup"><span data-stu-id="9d561-212">Search by genre</span></span>

<span data-ttu-id="9d561-213">以下列程式碼更新 `OnGetAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="9d561-213">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="9d561-214">下列程式碼是一種 LINQ 查詢，其會從資料庫中擷取所有的內容類型。</span><span class="sxs-lookup"><span data-stu-id="9d561-214">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="9d561-215">內容類型的 `SelectList` 是由投影不同的內容類型來建立。</span><span class="sxs-lookup"><span data-stu-id="9d561-215">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="9d561-216">將依內容類型搜尋新增至 Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="9d561-216">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="9d561-217">將 *Index cshtml* [ `<form>` element] (更新 https://developer.mozilla.org/docs/Web/HTML/Element/form) 為在下列標記中反白顯示：</span><span class="sxs-lookup"><span data-stu-id="9d561-217">Update the *Index.cshtml* [`<form>` element] (https://developer.mozilla.org/docs/Web/HTML/Element/form) as highlighted in the following markup:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="9d561-218">依據內容類型、電影標題和這兩者進行搜尋，藉以測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="9d561-218">Test the app by searching by genre, by movie title, and by both.</span></span>
<span data-ttu-id="9d561-219">上述程式碼使用 [選取標記](xref:mvc/views/working-with-forms#the-select-tag-helper) 協助程式和選項標記協助程式。</span><span class="sxs-lookup"><span data-stu-id="9d561-219">The preceding code uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) and Option Tag Helper.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9d561-220">其他資源</span><span class="sxs-lookup"><span data-stu-id="9d561-220">Additional resources</span></span>

* [<span data-ttu-id="9d561-221">本教學課程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="9d561-221">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="9d561-222">[上一步：更新頁面](xref:tutorials/razor-pages/da1) 
> [下一步：新增欄位](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="9d561-222">[Previous: Update the pages](xref:tutorials/razor-pages/da1)
[Next: Add a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end
