---
title: 第5部分：更新產生的頁面
author: rick-anderson
description: '頁面上教學課程系列的第5部分 :::no-loc(Razor)::: 。'
ms.author: riande
ms.date: 09/20/2020
no-loc:
- ':::no-loc(Index):::'
- ':::no-loc(Create):::'
- ':::no-loc(Delete):::'
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 7146c1955a578502a63578de4f1abce932cb8b32
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360595"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="6f683-103">第5部分：在 ASP.NET Core 應用程式中更新產生的頁面</span><span class="sxs-lookup"><span data-stu-id="6f683-103">Part 5, update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="6f683-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6f683-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6f683-105">Scaffolded 電影應用程式是一個不錯的起點，但其呈現效果卻不理想。</span><span class="sxs-lookup"><span data-stu-id="6f683-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="6f683-106">**ReleaseDate** 應該是兩個單字，也就是 **發行日期** 。</span><span class="sxs-lookup"><span data-stu-id="6f683-106">**ReleaseDate** should be two words, **Release Date**.</span></span>

![在 Chrome 中開啟的電影應用程式](sql/_static/5/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="6f683-108">更新產生的程式碼</span><span class="sxs-lookup"><span data-stu-id="6f683-108">Update the generated code</span></span>

<span data-ttu-id="6f683-109">開啟 *Models/Movie.cs* 檔案，然後新增下列程式碼中顯示的醒目提示行：</span><span class="sxs-lookup"><span data-stu-id="6f683-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="6f683-110">在先前的程式碼中：</span><span class="sxs-lookup"><span data-stu-id="6f683-110">In the previous code:</span></span>

* <span data-ttu-id="6f683-111">`[Column(TypeName = "decimal(18, 2)")]` 資料註解可讓 Entity Framework Core 將 `Price` 正確對應到資料庫中的貨幣。</span><span class="sxs-lookup"><span data-stu-id="6f683-111">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="6f683-112">如需詳細資訊，請參閱[資料類型](/ef/core/modeling/relational/data-types)。</span><span class="sxs-lookup"><span data-stu-id="6f683-112">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>
* <span data-ttu-id="6f683-113">[[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata)屬性指定欄位的顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="6f683-113">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies the display name of a field.</span></span> <span data-ttu-id="6f683-114">在上述程式碼中，[發行日期]，而不是 "ReleaseDate"。</span><span class="sxs-lookup"><span data-stu-id="6f683-114">In the preceding code, "Release Date" instead of "ReleaseDate".</span></span>
* <span data-ttu-id="6f683-115">[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="6f683-115">The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="6f683-116">不會顯示儲存在欄位中的時間資訊。</span><span class="sxs-lookup"><span data-stu-id="6f683-116">The time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="6f683-117">接下來的教學課程會涵蓋 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6)。</span><span class="sxs-lookup"><span data-stu-id="6f683-117">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span>

<span data-ttu-id="6f683-118">流覽至 *頁面/影片* ，並將滑鼠停留在 **編輯** 連結上，以查看目標 URL。</span><span class="sxs-lookup"><span data-stu-id="6f683-118">Browse to *Pages/Movies* and hover over an **Edit** link to see the target URL.</span></span>

![滑鼠停留在 Edit 連結並顯示 https://localhost:1234/Movies/Edit/5 的 Url 的瀏覽器視窗](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="6f683-120">[ **編輯** ]、[ **詳細資料** ] 和 **:::no-loc(Delete):::** [連結] 是由 *Pages/電影/ :::no-loc(Index)::: cshtml* 檔案中的 [錨點標記](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)協助程式所產生。</span><span class="sxs-lookup"><span data-stu-id="6f683-120">The **Edit** , **Details** , and **:::no-loc(Delete):::** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/:::no-loc(Index):::.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/:::no-loc(Index):::.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="6f683-121">[標記](xref:mvc/views/tag-helpers/intro) 協助程式可讓伺服器端程式碼參與建立和轉譯檔案中的 HTML 元素 :::no-loc(Razor)::: 。</span><span class="sxs-lookup"><span data-stu-id="6f683-121">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in :::no-loc(Razor)::: files.</span></span>

<span data-ttu-id="6f683-122">在上述程式碼中， [錨點](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) 標籤協助程式會以動態方式 `href` 從頁面產生 HTML 屬性值 :::no-loc(Razor)::: (路由是相對) 、 `asp-page` 和路由識別碼 (`asp-route-id`) 。</span><span class="sxs-lookup"><span data-stu-id="6f683-122">In the preceding code, the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dynamically generates the HTML `href` attribute value from the :::no-loc(Razor)::: Page (the route is relative), the `asp-page`, and the route identifier (`asp-route-id`).</span></span> <span data-ttu-id="6f683-123">如需詳細資訊，請參閱 [頁面的 URL 產生](xref:razor-pages/index#url-generation-for-pages)。</span><span class="sxs-lookup"><span data-stu-id="6f683-123">For more information, see [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages).</span></span>

<span data-ttu-id="6f683-124">使用瀏覽器的 **視圖來源** 來檢查產生的標記。</span><span class="sxs-lookup"><span data-stu-id="6f683-124">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="6f683-125">產生的 HTML 部分如下所示：</span><span class="sxs-lookup"><span data-stu-id="6f683-125">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/:::no-loc(Delete):::?id=1">:::no-loc(Delete):::</a>
</td>
```

   <span data-ttu-id="6f683-126">動態產生的連結會傳遞含有查詢字串的電影識別碼。</span><span class="sxs-lookup"><span data-stu-id="6f683-126">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="6f683-127">例如， `?id=1` 在中 `https://localhost:5001/Movies/Details?id=1` 。</span><span class="sxs-lookup"><span data-stu-id="6f683-127">For example, the `?id=1` in `https://localhost:5001/Movies/Details?id=1`.</span></span>

### <a name="add-route-template"></a><span data-ttu-id="6f683-128">新增路由範本</span><span class="sxs-lookup"><span data-stu-id="6f683-128">Add route template</span></span>

<span data-ttu-id="6f683-129">更新 [編輯]、[詳細資料] 和 :::no-loc(Delete)::: :::no-loc(Razor)::: [頁面] 以使用 `{id:int}` 路由範本。</span><span class="sxs-lookup"><span data-stu-id="6f683-129">Update the Edit, Details, and :::no-loc(Delete)::: :::no-loc(Razor)::: Pages to use the `{id:int}` route template.</span></span> <span data-ttu-id="6f683-130">將這些頁面每一頁的頁面指示詞從 `@page` 變更為 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="6f683-130">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="6f683-131">執行應用程式，然後檢視原始檔。</span><span class="sxs-lookup"><span data-stu-id="6f683-131">Run the app and then view source.</span></span>

<span data-ttu-id="6f683-132">產生的 HTML 將識別碼新增至 URL 的路徑部分：</span><span class="sxs-lookup"><span data-stu-id="6f683-132">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/:::no-loc(Delete):::/1">:::no-loc(Delete):::</a>
</td>
```

<span data-ttu-id="6f683-133">具有不 `{id:int}` 包含整數之路由範本的頁面要求， **not** 將會傳回 HTTP 404 (找不到) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="6f683-133">A request to the page with the `{id:int}` route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="6f683-134">例如，`https://localhost:5001/Movies/Details` 會傳回 404 錯誤。</span><span class="sxs-lookup"><span data-stu-id="6f683-134">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="6f683-135">若要使識別碼成為選擇性，請將 `?` 附加至路由條件約束：</span><span class="sxs-lookup"><span data-stu-id="6f683-135">To make the ID optional, append `?` to the route constraint:</span></span>

```cshtml
@page "{id:int?}"
```

<span data-ttu-id="6f683-136">測試 `@page "{id:int?}"` 下列行為：</span><span class="sxs-lookup"><span data-stu-id="6f683-136">Test the behavior of `@page "{id:int?}"`:</span></span>

1. <span data-ttu-id="6f683-137">將 *Pages/Movies/Details.cshtml* 中的頁面指示詞設定為 `@page "{id:int?}"`。</span><span class="sxs-lookup"><span data-stu-id="6f683-137">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
1. <span data-ttu-id="6f683-138">在 [ `public async Task<IActionResult> OnGetAsync(int? id)` *頁面/電影/詳細資料* ] 中，設定中斷點。</span><span class="sxs-lookup"><span data-stu-id="6f683-138">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
1. <span data-ttu-id="6f683-139">瀏覽至 `https://localhost:5001/Movies/Details/`。</span><span class="sxs-lookup"><span data-stu-id="6f683-139">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="6f683-140">使用 `@page "{id:int}"` 指示詞，永遠不會叫用中斷點。</span><span class="sxs-lookup"><span data-stu-id="6f683-140">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="6f683-141">路由引擎會傳回 HTTP 404。</span><span class="sxs-lookup"><span data-stu-id="6f683-141">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="6f683-142">使用時 `@page "{id:int?}"` ，此方法會傳回 `OnGetAsync` `NotFound` HTTP 404)  (：</span><span class="sxs-lookup"><span data-stu-id="6f683-142">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="6f683-143">檢閱並行存取例外狀況處理</span><span class="sxs-lookup"><span data-stu-id="6f683-143">Review concurrency exception handling</span></span>

<span data-ttu-id="6f683-144">在 *Pages/Movies/Edit.cshtml.cs* 檔案中檢閱 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="6f683-144">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="6f683-145">先前的程式碼會在一個用戶端刪除電影，而其他用戶端將變更張貼到電影時，偵測並行例外狀況。</span><span class="sxs-lookup"><span data-stu-id="6f683-145">The previous code detects concurrency exceptions when one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="6f683-146">若要測試 `catch` 區段：</span><span class="sxs-lookup"><span data-stu-id="6f683-146">To test the `catch` block:</span></span>

1. <span data-ttu-id="6f683-147">在上設定中斷點 `catch (DbUpdateConcurrencyException)` 。</span><span class="sxs-lookup"><span data-stu-id="6f683-147">Set a breakpoint on `catch (DbUpdateConcurrencyException)`.</span></span>
1. <span data-ttu-id="6f683-148">針對電影選取 [編輯]，進行變更，但不要輸入 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="6f683-148">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
1. <span data-ttu-id="6f683-149">在另一個瀏覽器視窗中，選取 **:::no-loc(Delete):::** 相同電影的連結，然後刪除電影。</span><span class="sxs-lookup"><span data-stu-id="6f683-149">In another browser window, select the **:::no-loc(Delete):::** link for the same movie, and then delete the movie.</span></span>
1. <span data-ttu-id="6f683-150">在先前的瀏覽器視窗中，發佈對電影的變更。</span><span class="sxs-lookup"><span data-stu-id="6f683-150">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="6f683-151">實際執行程式碼可能需要偵測並行存取衝突。</span><span class="sxs-lookup"><span data-stu-id="6f683-151">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="6f683-152">如需詳細資訊，請參閱[處理並行存取衝突](xref:data/ef-rp/concurrency)。</span><span class="sxs-lookup"><span data-stu-id="6f683-152">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="6f683-153">發佈和繫結檢閱內容</span><span class="sxs-lookup"><span data-stu-id="6f683-153">Posting and binding review</span></span>

<span data-ttu-id="6f683-154">檢查 *Pages/Movies/Edit.cshtml.cs* 檔案：</span><span class="sxs-lookup"><span data-stu-id="6f683-154">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="6f683-155">當對電影/編輯頁面提出 HTTP GET 要求時，例如 `https://localhost:5001/Movies/Edit/3` ：</span><span class="sxs-lookup"><span data-stu-id="6f683-155">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="6f683-156">`OnGetAsync` 方法會從資料庫擷取電影，並傳回 `Page` 方法。</span><span class="sxs-lookup"><span data-stu-id="6f683-156">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="6f683-157">方法會轉譯 `Page` *頁面/電影/編輯的 cshtml* :::no-loc(Razor)::: 頁面。</span><span class="sxs-lookup"><span data-stu-id="6f683-157">The `Page` method renders the *Pages/Movies/Edit.cshtml* :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="6f683-158">*Pages/movie/Edit. cshtml* 檔案包含模型指示詞 `@model :::no-loc(Razor):::PagesMovie.Pages.Movies.EditModel` ，可讓電影模型可在頁面上使用。</span><span class="sxs-lookup"><span data-stu-id="6f683-158">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model :::no-loc(Razor):::PagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="6f683-159">Edit 表單會顯示來自電影的值。</span><span class="sxs-lookup"><span data-stu-id="6f683-159">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="6f683-160">發佈 Movies/Edit 頁面時：</span><span class="sxs-lookup"><span data-stu-id="6f683-160">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="6f683-161">頁面上的表單值會繫結至 `Movie` 屬性。</span><span class="sxs-lookup"><span data-stu-id="6f683-161">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="6f683-162">`[BindProperty]` 屬性可讓[模型繫結](xref:mvc/models/model-binding)。</span><span class="sxs-lookup"><span data-stu-id="6f683-162">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="6f683-163">如果模型狀態中有錯誤（例如， `ReleaseDate` 無法轉換為日期），則會以提交的值重新顯示表單。</span><span class="sxs-lookup"><span data-stu-id="6f683-163">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="6f683-164">如果沒有任何模型錯誤，則會儲存電影。</span><span class="sxs-lookup"><span data-stu-id="6f683-164">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="6f683-165">、和頁面中的 HTTP GET 方法會 :::no-loc(Index)::: :::no-loc(Create)::: :::no-loc(Delete)::: :::no-loc(Razor)::: 遵循類似的模式。</span><span class="sxs-lookup"><span data-stu-id="6f683-165">The HTTP GET methods in the :::no-loc(Index):::, :::no-loc(Create):::, and :::no-loc(Delete)::: :::no-loc(Razor)::: pages follow a similar pattern.</span></span> <span data-ttu-id="6f683-166">頁面中的 HTTP POST `OnPostAsync` 方法會 :::no-loc(Create)::: :::no-loc(Razor)::: 遵循 `OnPostAsync` [編輯] 頁面中方法的類似模式 :::no-loc(Razor)::: 。</span><span class="sxs-lookup"><span data-stu-id="6f683-166">The HTTP POST `OnPostAsync` method in the :::no-loc(Create)::: :::no-loc(Razor)::: Page follows a similar pattern to the `OnPostAsync` method in the Edit :::no-loc(Razor)::: Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f683-167">其他資源</span><span class="sxs-lookup"><span data-stu-id="6f683-167">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="6f683-168">[上一步：使用資料庫](xref:tutorials/razor-pages/sql) 
> [下一步：新增搜尋](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="6f683-168">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6f683-169">Scaffolded 電影應用程式是一個不錯的起點，但其呈現效果卻不理想。</span><span class="sxs-lookup"><span data-stu-id="6f683-169">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="6f683-170">**ReleaseDate** 應該是兩個單字，也就是 **發行日期** 。</span><span class="sxs-lookup"><span data-stu-id="6f683-170">**ReleaseDate** should be two words, **Release Date**.</span></span>

![在 Chrome 中開啟的電影應用程式](sql/_static/m55https.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="6f683-172">更新產生的程式碼</span><span class="sxs-lookup"><span data-stu-id="6f683-172">Update the generated code</span></span>

<span data-ttu-id="6f683-173">開啟 *Models/Movie.cs* 檔案，然後新增下列程式碼中顯示的醒目提示行：</span><span class="sxs-lookup"><span data-stu-id="6f683-173">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="6f683-174">`[Column(TypeName = "decimal(18, 2)")]` 資料註解可讓 Entity Framework Core 將 `Price` 正確對應到資料庫中的貨幣。</span><span class="sxs-lookup"><span data-stu-id="6f683-174">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="6f683-175">如需詳細資訊，請參閱[資料類型](/ef/core/modeling/relational/data-types)。</span><span class="sxs-lookup"><span data-stu-id="6f683-175">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="6f683-176">接下來的教學課程會涵蓋 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6)。</span><span class="sxs-lookup"><span data-stu-id="6f683-176">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="6f683-177">[[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata)屬性會指定要針對欄位名稱顯示的內容，在此案例中為「發行日期」，而不是「ReleaseDate」。</span><span class="sxs-lookup"><span data-stu-id="6f683-177">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies what to display for the name of a field, in this case "Release Date" instead of "ReleaseDate".</span></span> <span data-ttu-id="6f683-178">[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)屬性指定資料 (的類型 `Date`) ，因此不會顯示儲存在欄位中的時間資訊。</span><span class="sxs-lookup"><span data-stu-id="6f683-178">The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="6f683-179">瀏覽至 Pages/Movies，然後將滑鼠停留在 **Edit** 連結，以查看目標 URL。</span><span class="sxs-lookup"><span data-stu-id="6f683-179">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![滑鼠停留在 Edit 連結並顯示 http://localhost:1234/Movies/Edit/5 的 Url 的瀏覽器視窗](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="6f683-181">[ **編輯** ]、[ **詳細資料** ] 和 **:::no-loc(Delete):::** [連結] 是由 *Pages/電影/ :::no-loc(Index)::: cshtml* 檔案中的 [錨點標記](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)協助程式所產生。</span><span class="sxs-lookup"><span data-stu-id="6f683-181">The **Edit** , **Details** , and **:::no-loc(Delete):::** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/:::no-loc(Index):::.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/:::no-loc(Index):::.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="6f683-182">[標記](xref:mvc/views/tag-helpers/intro) 協助程式可讓伺服器端程式碼參與建立和轉譯檔案中的 HTML 元素 :::no-loc(Razor)::: 。</span><span class="sxs-lookup"><span data-stu-id="6f683-182">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in :::no-loc(Razor)::: files.</span></span> <span data-ttu-id="6f683-183">在上述程式碼中， `AnchorTagHelper` 會以動態方式 `href` 從頁面產生 HTML 屬性值 :::no-loc(Razor)::: (路由是相對) 、 `asp-page` 和路由識別碼 (`asp-route-id`) 。</span><span class="sxs-lookup"><span data-stu-id="6f683-183">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the :::no-loc(Razor)::: Page (the route is relative), the `asp-page`, and the route id (`asp-route-id`).</span></span> <span data-ttu-id="6f683-184">如需詳細資訊，請參閱[頁面的 URL 產生](xref:razor-pages/index#url-generation-for-pages)。</span><span class="sxs-lookup"><span data-stu-id="6f683-184">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="6f683-185">使用瀏覽器的 **視圖來源** 來檢查產生的標記。</span><span class="sxs-lookup"><span data-stu-id="6f683-185">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="6f683-186">產生的 HTML 部分如下所示：</span><span class="sxs-lookup"><span data-stu-id="6f683-186">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/:::no-loc(Delete):::?id=1">:::no-loc(Delete):::</a>
</td>
```

<span data-ttu-id="6f683-187">動態產生的連結會傳遞含有查詢字串的電影識別碼。</span><span class="sxs-lookup"><span data-stu-id="6f683-187">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="6f683-188">例如， `?id=1` 在中  `https://localhost:5001/Movies/Details?id=1` 。</span><span class="sxs-lookup"><span data-stu-id="6f683-188">For example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`.</span></span>

<span data-ttu-id="6f683-189">更新 [編輯]、[詳細資料 :::no-loc(Delete)::: :::no-loc(Razor)::: ] 和 [頁面]，以使用 "{id： int}" 路由範本。</span><span class="sxs-lookup"><span data-stu-id="6f683-189">Update the Edit, Details, and :::no-loc(Delete)::: :::no-loc(Razor)::: Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="6f683-190">將這些頁面每一頁的頁面指示詞從 `@page` 變更為 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="6f683-190">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="6f683-191">執行應用程式，然後檢視原始檔。</span><span class="sxs-lookup"><span data-stu-id="6f683-191">Run the app and then view source.</span></span> <span data-ttu-id="6f683-192">產生的 HTML 將識別碼新增至 URL 的路徑部分：</span><span class="sxs-lookup"><span data-stu-id="6f683-192">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/:::no-loc(Delete):::/1">:::no-loc(Delete):::</a>
</td>
```

<span data-ttu-id="6f683-193">對使用 "{id:int}" 路由範本的頁面提出的要求若 **未** 包含整數，將傳回 HTTP 404 (找不到) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="6f683-193">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="6f683-194">例如，`https://localhost:5001/Movies/Details` 會傳回 404 錯誤。</span><span class="sxs-lookup"><span data-stu-id="6f683-194">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="6f683-195">若要使識別碼成為選擇性，請將 `?` 附加至路由條件約束：</span><span class="sxs-lookup"><span data-stu-id="6f683-195">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="6f683-196">若要測試 `@page "{id:int?}"` 的行為：</span><span class="sxs-lookup"><span data-stu-id="6f683-196">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="6f683-197">將 *Pages/Movies/Details.cshtml* 中的頁面指示詞設定為 `@page "{id:int?}"`。</span><span class="sxs-lookup"><span data-stu-id="6f683-197">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="6f683-198">在 [ `public async Task<IActionResult> OnGetAsync(int? id)` *頁面/電影/詳細資料* ] 中，設定中斷點。</span><span class="sxs-lookup"><span data-stu-id="6f683-198">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
* <span data-ttu-id="6f683-199">瀏覽至 `https://localhost:5001/Movies/Details/`。</span><span class="sxs-lookup"><span data-stu-id="6f683-199">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="6f683-200">使用 `@page "{id:int}"` 指示詞，永遠不會叫用中斷點。</span><span class="sxs-lookup"><span data-stu-id="6f683-200">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="6f683-201">路由引擎會傳回 HTTP 404。</span><span class="sxs-lookup"><span data-stu-id="6f683-201">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="6f683-202">使用時 `@page "{id:int?}"` ，此方法會傳回 `OnGetAsync` `NotFound` HTTP 404)  (：</span><span class="sxs-lookup"><span data-stu-id="6f683-202">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="6f683-203">檢閱並行存取例外狀況處理</span><span class="sxs-lookup"><span data-stu-id="6f683-203">Review concurrency exception handling</span></span>

<span data-ttu-id="6f683-204">在 *Pages/Movies/Edit.cshtml.cs* 檔案中檢閱 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="6f683-204">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="6f683-205">當一個用戶端刪除電影，而另一個用戶端發佈對電影的變更時，先前的程式碼會偵測並行存取例外狀況。</span><span class="sxs-lookup"><span data-stu-id="6f683-205">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="6f683-206">若要測試 `catch` 區段：</span><span class="sxs-lookup"><span data-stu-id="6f683-206">To test the `catch` block:</span></span>

* <span data-ttu-id="6f683-207">在 `catch (DbUpdateConcurrencyException)`上設定中斷點</span><span class="sxs-lookup"><span data-stu-id="6f683-207">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="6f683-208">針對電影選取 [編輯]，進行變更，但不要輸入 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="6f683-208">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="6f683-209">在另一個瀏覽器視窗中，選取 **:::no-loc(Delete):::** 相同電影的連結，然後刪除電影。</span><span class="sxs-lookup"><span data-stu-id="6f683-209">In another browser window, select the **:::no-loc(Delete):::** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="6f683-210">在先前的瀏覽器視窗中，發佈對電影的變更。</span><span class="sxs-lookup"><span data-stu-id="6f683-210">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="6f683-211">實際執行程式碼可能需要偵測並行存取衝突。</span><span class="sxs-lookup"><span data-stu-id="6f683-211">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="6f683-212">如需詳細資訊，請參閱[處理並行存取衝突](xref:data/ef-rp/concurrency)。</span><span class="sxs-lookup"><span data-stu-id="6f683-212">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="6f683-213">發佈和繫結檢閱內容</span><span class="sxs-lookup"><span data-stu-id="6f683-213">Posting and binding review</span></span>

<span data-ttu-id="6f683-214">檢查 *Pages/Movies/Edit.cshtml.cs* 檔案：</span><span class="sxs-lookup"><span data-stu-id="6f683-214">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="6f683-215">當對電影/編輯頁面提出 HTTP GET 要求時，例如 `https://localhost:5001/Movies/Edit/3` ：</span><span class="sxs-lookup"><span data-stu-id="6f683-215">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="6f683-216">`OnGetAsync` 方法會從資料庫擷取電影，並傳回 `Page` 方法。</span><span class="sxs-lookup"><span data-stu-id="6f683-216">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="6f683-217">方法會轉譯 `Page` *頁面/電影/編輯的 cshtml* :::no-loc(Razor)::: 頁面。</span><span class="sxs-lookup"><span data-stu-id="6f683-217">The `Page` method renders the *Pages/Movies/Edit.cshtml* :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="6f683-218">*Pages/movie/Edit. cshtml* 檔案包含模型指示詞 `@model :::no-loc(Razor):::PagesMovie.Pages.Movies.EditModel` ，可讓電影模型可在頁面上使用。</span><span class="sxs-lookup"><span data-stu-id="6f683-218">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model :::no-loc(Razor):::PagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="6f683-219">Edit 表單會顯示來自電影的值。</span><span class="sxs-lookup"><span data-stu-id="6f683-219">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="6f683-220">發佈 Movies/Edit 頁面時：</span><span class="sxs-lookup"><span data-stu-id="6f683-220">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="6f683-221">頁面上的表單值會繫結至 `Movie` 屬性。</span><span class="sxs-lookup"><span data-stu-id="6f683-221">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="6f683-222">`[BindProperty]` 屬性可讓[模型繫結](xref:mvc/models/model-binding)。</span><span class="sxs-lookup"><span data-stu-id="6f683-222">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="6f683-223">如果模型狀態中有錯誤（例如， `ReleaseDate` 無法轉換為日期），則會以提交的值來顯示表單。</span><span class="sxs-lookup"><span data-stu-id="6f683-223">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="6f683-224">如果沒有任何模型錯誤，則會儲存電影。</span><span class="sxs-lookup"><span data-stu-id="6f683-224">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="6f683-225">、和頁面中的 HTTP GET 方法會 :::no-loc(Index)::: :::no-loc(Create)::: :::no-loc(Delete)::: :::no-loc(Razor)::: 遵循類似的模式。</span><span class="sxs-lookup"><span data-stu-id="6f683-225">The HTTP GET methods in the :::no-loc(Index):::, :::no-loc(Create):::, and :::no-loc(Delete)::: :::no-loc(Razor)::: pages follow a similar pattern.</span></span> <span data-ttu-id="6f683-226">頁面中的 HTTP POST `OnPostAsync` 方法會 :::no-loc(Create)::: :::no-loc(Razor)::: 遵循 `OnPostAsync` [編輯] 頁面中方法的類似模式 :::no-loc(Razor)::: 。</span><span class="sxs-lookup"><span data-stu-id="6f683-226">The HTTP POST `OnPostAsync` method in the :::no-loc(Create)::: :::no-loc(Razor)::: Page follows a similar pattern to the `OnPostAsync` method in the Edit :::no-loc(Razor)::: Page.</span></span>

<span data-ttu-id="6f683-227">搜尋會在接下來的教學課程中新增。</span><span class="sxs-lookup"><span data-stu-id="6f683-227">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f683-228">其他資源</span><span class="sxs-lookup"><span data-stu-id="6f683-228">Additional resources</span></span>

* [<span data-ttu-id="6f683-229">本教學課程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="6f683-229">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="6f683-230">[上一步：使用資料庫](xref:tutorials/razor-pages/sql) 
> [下一步：新增搜尋](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="6f683-230">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
