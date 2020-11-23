---
title: 第3部分： scaffold Razor 頁面
author: rick-anderson
description: 頁面上教學課程系列的第3部分 Razor 。
ms.author: riande
ms.date: 09/25/2020
no-loc:
- Index
- Create
- Delete
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
uid: tutorials/razor-pages/page
ms.openlocfilehash: d655be26a794f87a0be07046ae1d6415256d592c
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417626"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="82722-103">第3部分： Razor ASP.NET Core 中的 scaffold 頁面</span><span class="sxs-lookup"><span data-stu-id="82722-103">Part 3, scaffolded Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="82722-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="82722-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="82722-105">本教學課程會檢查 Razor [先前教學](xref:tutorials/razor-pages/model)課程中的樣板所建立的頁面。</span><span class="sxs-lookup"><span data-stu-id="82722-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="82722-106">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="82722-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="82722-107">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="82722-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="the-no-loccreate-no-locdelete-details-and-edit-pages"></a><span data-ttu-id="82722-108">[、]、[ Create Delete 詳細資料] 和 [編輯] 頁面</span><span class="sxs-lookup"><span data-stu-id="82722-108">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="82722-109">檢查 *Pages/電影/ Index Cshtml.cs* 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="82722-109">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="82722-110">Razor 頁面衍生自 `PageModel` 。</span><span class="sxs-lookup"><span data-stu-id="82722-110">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="82722-111">依照慣例， `PageModel` 衍生的類別會命名為 `<PageName>Model` 。</span><span class="sxs-lookup"><span data-stu-id="82722-111">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="82722-112">此函式會使用相依性 [插入](xref:fundamentals/dependency-injection) 將加入 `RazorPagesMovieContext` 至頁面：</span><span class="sxs-lookup"><span data-stu-id="82722-112">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet1&highlight=5)]

<span data-ttu-id="82722-113">如需使用 Entity Framework 進行非同步程式設計的詳細資訊，請參閱[非同步程式碼](xref:data/ef-rp/intro#asynchronous-code)。</span><span class="sxs-lookup"><span data-stu-id="82722-113">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="82722-114">當對頁面提出要求時，方法會將 `OnGetAsync` 電影清單傳回 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="82722-114">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="82722-115">在 Razor 頁面上， `OnGetAsync` 或 `OnGet` 被呼叫來初始化頁面的狀態。</span><span class="sxs-lookup"><span data-stu-id="82722-115">On a Razor Page, `OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="82722-116">在此情況下，`OnGetAsync` 會取得電影清單並加以顯示。</span><span class="sxs-lookup"><span data-stu-id="82722-116">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="82722-117">當傳回或傳回時 `OnGet` `void` `OnGetAsync` `Task` ，不會使用 return 語句。</span><span class="sxs-lookup"><span data-stu-id="82722-117">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return statement is used.</span></span> <span data-ttu-id="82722-118">例如，隱私權頁面：</span><span class="sxs-lookup"><span data-stu-id="82722-118">For example the Privacy Page:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="82722-119">當傳回型別是 `IActionResult` 或 `Task<IActionResult>` 時，必須提供傳回陳述式。</span><span class="sxs-lookup"><span data-stu-id="82722-119">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="82722-120">例如， *Pages/電影/ Create . cshtml.cs* `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="82722-120">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="82722-121">檢查 *Pages/電影/ Index cshtml* Razor 頁面：</span><span class="sxs-lookup"><span data-stu-id="82722-121">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="82722-122">Razor 可以從 HTML 轉換成 c # 或 Razor 特定標記。</span><span class="sxs-lookup"><span data-stu-id="82722-122">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="82722-123">當 `@` 符號後面接著[ Razor 保留關鍵字](xref:mvc/views/razor#razor-reserved-keywords)時，它會轉換為 Razor 特定標記，否則會轉換成 c #。</span><span class="sxs-lookup"><span data-stu-id="82722-123">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="82722-124">@page 指示詞</span><span class="sxs-lookup"><span data-stu-id="82722-124">The @page directive</span></span>

<span data-ttu-id="82722-125">指示詞 `@page` Razor 會讓檔案成為 MVC 動作，這表示它可以處理要求。</span><span class="sxs-lookup"><span data-stu-id="82722-125">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="82722-126">`@page` 必須是頁面上的第一個指示詞 Razor 。</span><span class="sxs-lookup"><span data-stu-id="82722-126">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="82722-127">`@page` 和 `@model` 是轉換為 Razor 特定標記的範例。</span><span class="sxs-lookup"><span data-stu-id="82722-127">`@page` and `@model` are examples of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="82722-128">如需詳細資訊，請參閱[ Razor 語法](xref:mvc/views/razor#razor-syntax)。</span><span class="sxs-lookup"><span data-stu-id="82722-128">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="82722-129">@model 指示詞</span><span class="sxs-lookup"><span data-stu-id="82722-129">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="82722-130">指示詞會 `@model` 指定傳遞至頁面的模型類型 Razor 。</span><span class="sxs-lookup"><span data-stu-id="82722-130">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="82722-131">在上述範例中，這 `@model` 一行讓 `PageModel` 衍生的類別可供頁面使用 Razor 。</span><span class="sxs-lookup"><span data-stu-id="82722-131">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="82722-132">此模型用於頁面上的 `@Html.DisplayNameFor` 和 `@Html.DisplayFor` [HTML 協助程式](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)。</span><span class="sxs-lookup"><span data-stu-id="82722-132">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>


<span data-ttu-id="82722-133">檢查下列 HTML 協助程式中使用的 Lambda 運算式：</span><span class="sxs-lookup"><span data-stu-id="82722-133">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="82722-134"><xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML 協助程式會檢查 Lambda 運算式中參考的 `Title` 屬性來判斷顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="82722-134">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="82722-135">Lambda 運算式是進行檢查而不是評估。</span><span class="sxs-lookup"><span data-stu-id="82722-135">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="82722-136">這表示當 `model` 、 `model.Movie` 或 `model.Movie[0]` 為 `null` 或空白時，不會發生存取違規。</span><span class="sxs-lookup"><span data-stu-id="82722-136">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="82722-137">例如，在評估 lambda 運算式時（例如）， `@Html.DisplayFor(modelItem => item.Title)` 會評估模型的屬性值。</span><span class="sxs-lookup"><span data-stu-id="82722-137">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="82722-138">版面配置頁</span><span class="sxs-lookup"><span data-stu-id="82722-138">The layout page</span></span>

<span data-ttu-id="82722-139">選取功能表連結 **Razor PagesMovie**、**首頁** 和 **隱私權**。</span><span class="sxs-lookup"><span data-stu-id="82722-139">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="82722-140">每個頁面會顯示相同的功能表配置。</span><span class="sxs-lookup"><span data-stu-id="82722-140">Each page shows the same menu layout.</span></span> <span data-ttu-id="82722-141">功能表配置會在 *Pages/Shared/_Layout.cshtml* 檔案中實作。</span><span class="sxs-lookup"><span data-stu-id="82722-141">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="82722-142">開啟並檢查 *Pages/Shared/_Layout 的 cshtml* 檔案。</span><span class="sxs-lookup"><span data-stu-id="82722-142">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="82722-143">[版面配置](xref:mvc/views/layout)範本可讓 HTML 容器版面配置：</span><span class="sxs-lookup"><span data-stu-id="82722-143">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="82722-144">指定在一個位置。</span><span class="sxs-lookup"><span data-stu-id="82722-144">Specified in one place.</span></span>
* <span data-ttu-id="82722-145">套用於網站中的多個頁面。</span><span class="sxs-lookup"><span data-stu-id="82722-145">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="82722-146">找到 `@RenderBody()` 這行。</span><span class="sxs-lookup"><span data-stu-id="82722-146">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="82722-147">`RenderBody` 是一個預留位置，可供顯示所有頁面特定檢視 (「包裝」在版面配置頁面中)。</span><span class="sxs-lookup"><span data-stu-id="82722-147">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="82722-148">例如，選取 [隱私權] 連結，就會在 `RenderBody` 方法內轉譯 *Pages/Privacy.cshtml* 檢視。</span><span class="sxs-lookup"><span data-stu-id="82722-148">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="82722-149">ViewData 和 Layout</span><span class="sxs-lookup"><span data-stu-id="82722-149">ViewData and layout</span></span>

<span data-ttu-id="82722-150">請考慮下列 *Pages/電影/ Index cshtml* 檔案的標記：</span><span class="sxs-lookup"><span data-stu-id="82722-150">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="82722-151">上述反白顯示的標記是 Razor 轉換成 c # 的範例。</span><span class="sxs-lookup"><span data-stu-id="82722-151">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="82722-152">`{` 和 `}` 字元中含括 C# 程式碼的區塊。</span><span class="sxs-lookup"><span data-stu-id="82722-152">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="82722-153">`PageModel`基類包含 `ViewData` 字典屬性，可用來將資料傳遞給視圖。</span><span class="sxs-lookup"><span data-stu-id="82722-153">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="82722-154">`ViewData`使用 \* 索引 **鍵值** _ 模式，將物件新增至字典。</span><span class="sxs-lookup"><span data-stu-id="82722-154">Objects are added to the `ViewData` dictionary using a \***key value** _ pattern.</span></span> <span data-ttu-id="82722-155">在上述範例中，`Title` 屬性會新增至 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="82722-155">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="82722-156">`Title`屬性會在 _Pages/shared/_Layout. cshtml \* 檔案中使用。</span><span class="sxs-lookup"><span data-stu-id="82722-156">The `Title` property is used in the _Pages/Shared/_Layout.cshtml\* file.</span></span> <span data-ttu-id="82722-157">下列標記會顯示 *_Layout.cshtml* 檔案的前幾行。</span><span class="sxs-lookup"><span data-stu-id="82722-157">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="82722-158">這一行 `@*Markup removed for brevity.*@` 是 Razor 批註。</span><span class="sxs-lookup"><span data-stu-id="82722-158">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="82722-159">與 HTML 批註不同的 `<!-- -->` Razor 是，不會將批註傳送給用戶端。</span><span class="sxs-lookup"><span data-stu-id="82722-159">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="82722-160">如需詳細資訊，請參閱 [MDN web 檔：開始使用 HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) 。</span><span class="sxs-lookup"><span data-stu-id="82722-160">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="82722-161">更新配置</span><span class="sxs-lookup"><span data-stu-id="82722-161">Update the layout</span></span>

1. <span data-ttu-id="82722-162">變更 `<title>` *Pages/Shared/_Layout cshtml* 檔案中的專案，以顯示 **電影**，而不是 **Razor PagesMovie**。</span><span class="sxs-lookup"><span data-stu-id="82722-162">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

1. <span data-ttu-id="82722-163">在 *Pages/Shared/_Layout.cshtml* 檔案中尋找下列錨點元素。</span><span class="sxs-lookup"><span data-stu-id="82722-163">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

   ```cshtml
   <a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
   ```

1. <span data-ttu-id="82722-164">以下列標記來取代上述元素：</span><span class="sxs-lookup"><span data-stu-id="82722-164">Replace the preceding element with the following markup:</span></span>

   ```cshtml
   <a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
   ```

   <span data-ttu-id="82722-165">上述的錨點項目是[標記協助程式](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="82722-165">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="82722-166">在此情況下，它是[錨點標記協助程式](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="82722-166">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="82722-167">標籤協助程式 `asp-page="/Movies/Index"` 屬性和值會建立頁面的連結 `/Movies/Index` Razor 。</span><span class="sxs-lookup"><span data-stu-id="82722-167">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="82722-168">`asp-area` 屬性值為空白，因此不會在連結中使用該區域。</span><span class="sxs-lookup"><span data-stu-id="82722-168">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="82722-169">如需詳細資訊，請參閱[區域](xref:mvc/controllers/areas)。</span><span class="sxs-lookup"><span data-stu-id="82722-169">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

1. <span data-ttu-id="82722-170">儲存變更，然後選取 [ **>rpmovie** ] 連結來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="82722-170">Save the changes and test the app by selecting the **RpMovie** link.</span></span> <span data-ttu-id="82722-171">如有任何問題，請參閱 GitHub 中的 [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) 檔案。</span><span class="sxs-lookup"><span data-stu-id="82722-171">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

1. <span data-ttu-id="82722-172">測試 **首頁**、 **>rpmovie**、 **Create** **編輯** 和 **Delete** 連結。</span><span class="sxs-lookup"><span data-stu-id="82722-172">Test the **Home**, **RpMovie**, **Create**, **Edit**, and **Delete** links.</span></span> <span data-ttu-id="82722-173">每個頁面都會設定標題，您可以在 [瀏覽器] 索引標籤中看到此標題。當您將頁面加入書簽時，此標題會用於書簽。</span><span class="sxs-lookup"><span data-stu-id="82722-173">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="82722-174">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="82722-174">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="82722-175">若要針對使用逗號 ( "，" ) 作為小數點的非英文地區設定和非 US-English 日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/) ，您必須採取步驟來全球化應用程式。</span><span class="sxs-lookup"><span data-stu-id="82722-175">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="82722-176">請參閱此 [GitHub 問題 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)，以取得加入十進位逗號的指示。</span><span class="sxs-lookup"><span data-stu-id="82722-176">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="82722-177">`Layout` 屬性是在 *Pages/_ViewStart.cshtml* 檔案中設定：</span><span class="sxs-lookup"><span data-stu-id="82722-177">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="82722-178">上述標記會將 Pages 資料夾下的所有檔案的版面配置檔案設定為 *pages/Shared/_Layout。* Razor *Pages*</span><span class="sxs-lookup"><span data-stu-id="82722-178">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="82722-179">如需詳細資訊，請參閱 [Layout](xref:razor-pages/index#layout)。</span><span class="sxs-lookup"><span data-stu-id="82722-179">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-no-loccreate-page-model"></a><span data-ttu-id="82722-180">Create頁面模型</span><span class="sxs-lookup"><span data-stu-id="82722-180">The Create page model</span></span>

<span data-ttu-id="82722-181">檢查 *Pages/電影/ Create cshtml.cs* 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="82722-181">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="82722-182">`OnGet` 方法會初始化頁面所需的任何狀態。</span><span class="sxs-lookup"><span data-stu-id="82722-182">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="82722-183">Create頁面沒有任何要初始化的狀態，因此 `Page` 會傳回。</span><span class="sxs-lookup"><span data-stu-id="82722-183">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="82722-184">稍後在此教學課程中，會顯示 `OnGet` 初始化狀態的範例。</span><span class="sxs-lookup"><span data-stu-id="82722-184">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="82722-185">`Page`方法 `PageResult` 會建立可轉譯 *Create cshtml* 頁面的物件。</span><span class="sxs-lookup"><span data-stu-id="82722-185">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="82722-186">`Movie`屬性使用[[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute)屬性加入[模型](xref:mvc/models/model-binding)系結。</span><span class="sxs-lookup"><span data-stu-id="82722-186">The `Movie` property uses the [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="82722-187">當 Create 表單張貼表單值時，ASP.NET Core 執行時間會將張貼的值系結至 `Movie` 模型。</span><span class="sxs-lookup"><span data-stu-id="82722-187">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="82722-188">當頁面發佈表單資料時，即會執行 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="82722-188">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="82722-189">如果沒有任何模型錯誤，將會重新顯示表單，以及任何發佈的表單資料。</span><span class="sxs-lookup"><span data-stu-id="82722-189">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="82722-190">大部分的模型錯誤可以在發佈表單之前，於用戶端上攔截到。</span><span class="sxs-lookup"><span data-stu-id="82722-190">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="82722-191">模型錯誤的範例為針對日期欄位發佈無法轉換為日期的值。</span><span class="sxs-lookup"><span data-stu-id="82722-191">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="82722-192">稍後的教學課程中將討論用戶端驗證和模型驗證。</span><span class="sxs-lookup"><span data-stu-id="82722-192">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="82722-193">如果沒有任何模型錯誤：</span><span class="sxs-lookup"><span data-stu-id="82722-193">If there are no model errors:</span></span>

* <span data-ttu-id="82722-194">儲存資料。</span><span class="sxs-lookup"><span data-stu-id="82722-194">The data is saved.</span></span>
* <span data-ttu-id="82722-195">瀏覽器會重新導向至該 Index 頁面。</span><span class="sxs-lookup"><span data-stu-id="82722-195">The browser is redirected to the Index page.</span></span>

### <a name="the-no-loccreate-no-locrazor-page"></a><span data-ttu-id="82722-196">Create Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="82722-196">The Create Razor Page</span></span>

<span data-ttu-id="82722-197">檢查 *Pages/電影/ Create cshtml* Razor 分頁檔案：</span><span class="sxs-lookup"><span data-stu-id="82722-197">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="82722-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="82722-198">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="82722-199">Visual Studio 會以用於標籤協助程式的特殊粗體字型顯示下列標籤：</span><span class="sxs-lookup"><span data-stu-id="82722-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![VS17 view of：：：非 loc (Create) ：：：. cshtml 頁面](page/_static/th3.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="82722-201">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="82722-201">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="82722-202">上述標記中會顯示下列標籤協助程式：</span><span class="sxs-lookup"><span data-stu-id="82722-202">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="82722-203">`<form method="post">` 項目是[表單標記協助程式](xref:mvc/views/working-with-forms#the-form-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="82722-203">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="82722-204">表單標記協助程式會自動包含 [antiforgery 語彙基元](xref:security/anti-request-forgery)。</span><span class="sxs-lookup"><span data-stu-id="82722-204">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="82722-205">此範例引擎會 Razor 針對模型中的每個欄位建立標記，但識別碼除外，如下所示：</span><span class="sxs-lookup"><span data-stu-id="82722-205">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="82722-206">[驗證](xref:mvc/views/working-with-forms#the-validation-tag-helpers)標籤協助程式 (`<div asp-validation-summary` ，並 `<span asp-validation-for`) 顯示驗證錯誤。</span><span class="sxs-lookup"><span data-stu-id="82722-206">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="82722-207">驗證將於本文稍後詳細討論到。</span><span class="sxs-lookup"><span data-stu-id="82722-207">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="82722-208">[標籤標記](xref:mvc/views/working-with-forms#the-label-tag-helper)協助 `<label asp-for="Movie.Title" class="control-label"></label>` 程式 () 會產生屬性的標籤標題和 `[for]` 屬性 `Title` 。</span><span class="sxs-lookup"><span data-stu-id="82722-208">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="82722-209">[輸入標記](xref:mvc/views/working-with-forms)協助程式 (`<input asp-for="Movie.Title" class="form-control">`) 會使用[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6)屬性，並產生在用戶端上進行 JQUERY 驗證所需的 HTML 屬性。</span><span class="sxs-lookup"><span data-stu-id="82722-209">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="82722-210">如需標籤協助程式 (例如 `<form method="post">`) 的詳細資訊，請參閱 [ASP.NET Core 中的標籤協助程式](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="82722-210">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="82722-211">其他資源</span><span class="sxs-lookup"><span data-stu-id="82722-211">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="82722-212">[上一步：新增模型](xref:tutorials/razor-pages/model) 
> [下一步：使用資料庫](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="82722-212">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-no-loccreate-no-locdelete-details-and-edit-pages"></a><span data-ttu-id="82722-213">[、]、[ Create Delete 詳細資料] 和 [編輯] 頁面</span><span class="sxs-lookup"><span data-stu-id="82722-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="82722-214">檢查 *Pages/電影/ Index Cshtml.cs* 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="82722-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="82722-215">Razor 頁面衍生自 `PageModel` 。</span><span class="sxs-lookup"><span data-stu-id="82722-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="82722-216">依照慣例， `PageModel` 衍生的類別會命名為 `<PageName>Model` 。</span><span class="sxs-lookup"><span data-stu-id="82722-216">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="82722-217">建構函式會使用[相依性插入](xref:fundamentals/dependency-injection)將 `RazorPagesMovieContext` 新增至頁面中。</span><span class="sxs-lookup"><span data-stu-id="82722-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="82722-218">Scaffold 頁面會遵循此模式。</span><span class="sxs-lookup"><span data-stu-id="82722-218">The scaffolded pages follow this pattern.</span></span> <span data-ttu-id="82722-219">如需使用 Entity Framework 進行非同步程式設計的詳細資訊，請參閱[非同步程式碼](xref:data/ef-rp/intro#asynchronous-code)。</span><span class="sxs-lookup"><span data-stu-id="82722-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="82722-220">當對頁面提出要求時，方法會將 `OnGetAsync` 電影清單傳回 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="82722-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="82722-221">`OnGetAsync` 或 `OnGet` 在頁面上呼叫， Razor 以初始化頁面的狀態。</span><span class="sxs-lookup"><span data-stu-id="82722-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="82722-222">在此情況下，`OnGetAsync` 會取得電影清單並加以顯示。</span><span class="sxs-lookup"><span data-stu-id="82722-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="82722-223">當傳回或傳回時 `OnGet` `void` `OnGetAsync` `Task` ，不會使用傳回方法。</span><span class="sxs-lookup"><span data-stu-id="82722-223">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return method is used.</span></span> <span data-ttu-id="82722-224">當傳回型別是 `IActionResult` 或 `Task<IActionResult>` 時，必須提供傳回陳述式。</span><span class="sxs-lookup"><span data-stu-id="82722-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="82722-225">例如， *Pages/電影/ Create . cshtml.cs* `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="82722-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="82722-226">檢查 *Pages/電影/ Index cshtml* Razor 頁面：</span><span class="sxs-lookup"><span data-stu-id="82722-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="82722-227">Razor 可以從 HTML 轉換成 c # 或 Razor 特定標記。</span><span class="sxs-lookup"><span data-stu-id="82722-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="82722-228">當 `@` 符號後面接著[ Razor 保留關鍵字](xref:mvc/views/razor#razor-reserved-keywords)時，它會轉換為 Razor 特定標記，否則會轉換成 c #。</span><span class="sxs-lookup"><span data-stu-id="82722-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="82722-229">指示詞會將檔案 `@page` Razor 變成 MVC 動作，這表示它可以處理要求。</span><span class="sxs-lookup"><span data-stu-id="82722-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="82722-230">`@page` 必須是頁面上的第一個指示詞 Razor 。</span><span class="sxs-lookup"><span data-stu-id="82722-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="82722-231">`@page` 是轉換為 Razor 特定標記的範例。</span><span class="sxs-lookup"><span data-stu-id="82722-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="82722-232">如需詳細資訊，請參閱[ Razor 語法](xref:mvc/views/razor#razor-syntax)。</span><span class="sxs-lookup"><span data-stu-id="82722-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="82722-233">@model 指示詞</span><span class="sxs-lookup"><span data-stu-id="82722-233">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="82722-234">指示詞會 `@model` 指定傳遞至頁面的模型類型 Razor 。</span><span class="sxs-lookup"><span data-stu-id="82722-234">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="82722-235">在上述範例中，這 `@model` 一行讓 `PageModel` 衍生的類別可供頁面使用 Razor 。</span><span class="sxs-lookup"><span data-stu-id="82722-235">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="82722-236">此模型用於頁面上的 `@Html.DisplayNameFor` 和 `@Html.DisplayFor` [HTML 協助程式](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)。</span><span class="sxs-lookup"><span data-stu-id="82722-236">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="html-helpers"></a><span data-ttu-id="82722-237">HTML 協助程式</span><span class="sxs-lookup"><span data-stu-id="82722-237">HTML Helpers</span></span>

<span data-ttu-id="82722-238">檢查下列 HTML 協助程式中使用的 Lambda 運算式：</span><span class="sxs-lookup"><span data-stu-id="82722-238">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="82722-239"><xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML 協助程式會檢查 Lambda 運算式中參考的 `Title` 屬性來判斷顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="82722-239">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="82722-240">Lambda 運算式是進行檢查而不是評估。</span><span class="sxs-lookup"><span data-stu-id="82722-240">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="82722-241">這表示當 `model`、`model.Movie` 或 `model.Movie[0]` 是 `null` 或空白時，不會有任何存取違規。</span><span class="sxs-lookup"><span data-stu-id="82722-241">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="82722-242">例如，在評估 lambda 運算式時（例如）， `@Html.DisplayFor(modelItem => item.Title)` 會評估模型的屬性值。</span><span class="sxs-lookup"><span data-stu-id="82722-242">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>
### <a name="the-layout-page"></a><span data-ttu-id="82722-243">版面配置頁</span><span class="sxs-lookup"><span data-stu-id="82722-243">The layout page</span></span>

<span data-ttu-id="82722-244">選取功能表連結 **Razor PagesMovie**、**首頁** 和 **隱私權**。</span><span class="sxs-lookup"><span data-stu-id="82722-244">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="82722-245">每個頁面會顯示相同的功能表配置。</span><span class="sxs-lookup"><span data-stu-id="82722-245">Each page shows the same menu layout.</span></span> <span data-ttu-id="82722-246">功能表配置會在 *Pages/Shared/_Layout.cshtml* 檔案中實作。</span><span class="sxs-lookup"><span data-stu-id="82722-246">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="82722-247">開啟並檢查 *Pages/Shared/_Layout 的 cshtml* 檔案。</span><span class="sxs-lookup"><span data-stu-id="82722-247">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="82722-248">[版面](xref:mvc/views/layout) 配置範本可讓您在一個地方指定網站的 HTML 容器配置，然後將它套用到網站中的多個頁面。</span><span class="sxs-lookup"><span data-stu-id="82722-248">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of a site in one place and then apply it across multiple pages in the site.</span></span> <span data-ttu-id="82722-249">找到 `@RenderBody()` 這行。</span><span class="sxs-lookup"><span data-stu-id="82722-249">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="82722-250">`RenderBody` 是一個「包裝」在版面配置頁中的預留位置，可供顯示您建立的所有頁面特定檢視。</span><span class="sxs-lookup"><span data-stu-id="82722-250">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="82722-251">例如，如果您選取 [Privacy] 連結，就會在 `RenderBody` 方法內呈現 **Pages/Privacy.cshtml** 檢視。</span><span class="sxs-lookup"><span data-stu-id="82722-251">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="82722-252">ViewData 和 Layout</span><span class="sxs-lookup"><span data-stu-id="82722-252">ViewData and layout</span></span>

<span data-ttu-id="82722-253">請從 *Pages/影片/ Index cshtml* 檔案考慮下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="82722-253">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="82722-254">上述反白顯示的程式碼是 Razor 轉換成 c # 的範例。</span><span class="sxs-lookup"><span data-stu-id="82722-254">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="82722-255">`{` 和 `}` 字元中含括 C# 程式碼的區塊。</span><span class="sxs-lookup"><span data-stu-id="82722-255">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="82722-256">`PageModel` 基底類別有 `ViewData` 字典屬性，可用來新增至您想要傳遞到檢視的資料。</span><span class="sxs-lookup"><span data-stu-id="82722-256">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="82722-257">您可以使用索引鍵/值模式將物件新增至 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="82722-257">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="82722-258">在上述範例中，`Title` 屬性會新增至 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="82722-258">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="82722-259">*Pages/Shared/_Layout.cshtml* 檔案中使用 `Title` 屬性。</span><span class="sxs-lookup"><span data-stu-id="82722-259">The `Title` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="82722-260">下列標記會顯示 *_Layout.cshtml* 檔案的前幾行。</span><span class="sxs-lookup"><span data-stu-id="82722-260">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="82722-261">這一行 `@*Markup removed for brevity.*@` 是 Razor 批註。</span><span class="sxs-lookup"><span data-stu-id="82722-261">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="82722-262">與 HTML 批註不同的 `<!-- -->` Razor 是，不會將批註傳送給用戶端。</span><span class="sxs-lookup"><span data-stu-id="82722-262">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="82722-263">如需詳細資訊，請參閱 [MDN web 檔：開始使用 HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) 。</span><span class="sxs-lookup"><span data-stu-id="82722-263">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="82722-264">更新配置</span><span class="sxs-lookup"><span data-stu-id="82722-264">Update the layout</span></span>

<span data-ttu-id="82722-265">變更 `<title>` *Pages/Shared/_Layout cshtml* 檔案中的專案，以顯示 **電影**，而不是 **Razor PagesMovie**。</span><span class="sxs-lookup"><span data-stu-id="82722-265">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="82722-266">在 *Pages/Shared/_Layout.cshtml* 檔案中尋找下列錨點元素。</span><span class="sxs-lookup"><span data-stu-id="82722-266">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="82722-267">以下列標記來取代上述項目。</span><span class="sxs-lookup"><span data-stu-id="82722-267">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="82722-268">上述的錨點項目是[標記協助程式](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="82722-268">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="82722-269">在此情況下，它是[錨點標記協助程式](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="82722-269">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="82722-270">標籤協助程式 `asp-page="/Movies/Index"` 屬性和值會建立頁面的連結 `/Movies/Index` Razor 。</span><span class="sxs-lookup"><span data-stu-id="82722-270">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="82722-271">`asp-area` 屬性值為空白，因此不會在連結中使用該區域。</span><span class="sxs-lookup"><span data-stu-id="82722-271">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="82722-272">如需詳細資訊，請參閱[區域](xref:mvc/controllers/areas)。</span><span class="sxs-lookup"><span data-stu-id="82722-272">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="82722-273">儲存變更，並按一下 **RpMovie** 連結來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="82722-273">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="82722-274">如有任何問題，請參閱 GitHub 中的 [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) 檔案。</span><span class="sxs-lookup"><span data-stu-id="82722-274">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="82722-275">測試其他連結 (**Home**、 **>rpmovie**、 **Create** 、 **Edit** 和 **Delete**) 。</span><span class="sxs-lookup"><span data-stu-id="82722-275">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="82722-276">每個頁面都會設定標題，您可以在 [瀏覽器] 索引標籤中看到此標題。當您將頁面加入書簽時，此標題會用於書簽。</span><span class="sxs-lookup"><span data-stu-id="82722-276">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="82722-277">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="82722-277">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="82722-278">若要針對使用逗號 ( "，" ) 作為小數點的非英文地區設定和非 US-English 日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/) ，您必須採取步驟來全球化應用程式。</span><span class="sxs-lookup"><span data-stu-id="82722-278">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="82722-279">這個 [GitHub 問題 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) 有加入小數逗號的指示。</span><span class="sxs-lookup"><span data-stu-id="82722-279">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="82722-280">`Layout` 屬性是在 *Pages/_ViewStart.cshtml* 檔案中設定：</span><span class="sxs-lookup"><span data-stu-id="82722-280">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="82722-281">上述標記會將 Pages 資料夾下的所有檔案的版面配置檔案設定為 *pages/Shared/_Layout。* Razor *Pages*</span><span class="sxs-lookup"><span data-stu-id="82722-281">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="82722-282">如需詳細資訊，請參閱 [Layout](xref:razor-pages/index#layout)。</span><span class="sxs-lookup"><span data-stu-id="82722-282">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-no-loccreate-page-model"></a><span data-ttu-id="82722-283">Create頁面模型</span><span class="sxs-lookup"><span data-stu-id="82722-283">The Create page model</span></span>

<span data-ttu-id="82722-284">檢查 *Pages/電影/ Create cshtml.cs* 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="82722-284">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="82722-285">`OnGet` 方法會初始化頁面所需的任何狀態。</span><span class="sxs-lookup"><span data-stu-id="82722-285">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="82722-286">Create頁面沒有任何要初始化的狀態，因此 `Page` 會傳回。</span><span class="sxs-lookup"><span data-stu-id="82722-286">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="82722-287">稍後在本教學課程中您會看到 `OnGet` 方法初始化狀態。</span><span class="sxs-lookup"><span data-stu-id="82722-287">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="82722-288">`Page`方法 `PageResult` 會建立可轉譯 *Create cshtml* 頁面的物件。</span><span class="sxs-lookup"><span data-stu-id="82722-288">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="82722-289">`Movie`屬性使用 [[BindProperty]] <xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> 屬性加入[模型](xref:mvc/models/model-binding)系結。</span><span class="sxs-lookup"><span data-stu-id="82722-289">The `Movie` property uses the [[BindProperty]]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="82722-290">當 Create 表單張貼表單值時，ASP.NET Core 執行時間會將張貼的值系結至 `Movie` 模型。</span><span class="sxs-lookup"><span data-stu-id="82722-290">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="82722-291">當頁面發佈表單資料時，即會執行 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="82722-291">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="82722-292">如果沒有任何模型錯誤，將會重新顯示表單，以及任何發佈的表單資料。</span><span class="sxs-lookup"><span data-stu-id="82722-292">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="82722-293">大部分的模型錯誤可以在發佈表單之前，於用戶端上攔截到。</span><span class="sxs-lookup"><span data-stu-id="82722-293">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="82722-294">模型錯誤的範例為針對日期欄位發佈無法轉換為日期的值。</span><span class="sxs-lookup"><span data-stu-id="82722-294">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="82722-295">稍後的教學課程中將討論用戶端驗證和模型驗證。</span><span class="sxs-lookup"><span data-stu-id="82722-295">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="82722-296">如果沒有任何模型錯誤，則會儲存資料，並將瀏覽器重新導向至 Index 頁面。</span><span class="sxs-lookup"><span data-stu-id="82722-296">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-no-loccreate-no-locrazor-page"></a><span data-ttu-id="82722-297">Create Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="82722-297">The Create Razor Page</span></span>

<span data-ttu-id="82722-298">檢查 *Pages/電影/ Create cshtml* Razor 分頁檔案：</span><span class="sxs-lookup"><span data-stu-id="82722-298">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="82722-299">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="82722-299">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="82722-300">Visual Studio 會以特別的粗體字型顯示 `<form method="post">` 標籤，用於標籤協助程式：</span><span class="sxs-lookup"><span data-stu-id="82722-300">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![VS17 view of：：：非 loc (Create) ：：：. cshtml 頁面](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="82722-302">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="82722-302">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="82722-303">如需標籤協助程式 (例如 `<form method="post">`) 的詳細資訊，請參閱 [ASP.NET Core 中的標籤協助程式](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="82722-303">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="82722-304">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="82722-304">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="82722-305">Visual Studio for Mac 會以特別的粗體字型顯示 `<form method="post">` 標籤，用於標籤協助程式。</span><span class="sxs-lookup"><span data-stu-id="82722-305">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="82722-306">`<form method="post">` 項目是[表單標記協助程式](xref:mvc/views/working-with-forms#the-form-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="82722-306">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="82722-307">表單標記協助程式會自動包含 [antiforgery 語彙基元](xref:security/anti-request-forgery)。</span><span class="sxs-lookup"><span data-stu-id="82722-307">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="82722-308">此範例引擎會 Razor 針對模型中的每個欄位建立標記，但識別碼除外，如下所示：</span><span class="sxs-lookup"><span data-stu-id="82722-308">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="82722-309">[驗證](xref:mvc/views/working-with-forms#the-validation-tag-helpers)標籤協助程式 (`<div asp-validation-summary` ，並 `<span asp-validation-for`) 顯示驗證錯誤。</span><span class="sxs-lookup"><span data-stu-id="82722-309">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="82722-310">驗證將於本文稍後詳細討論到。</span><span class="sxs-lookup"><span data-stu-id="82722-310">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="82722-311">[標籤標記](xref:mvc/views/working-with-forms#the-label-tag-helper)協助 `<label asp-for="Movie.Title" class="control-label"></label>` 程式 () 會產生屬性的標籤標題和 `[for]` 屬性 `Title` 。</span><span class="sxs-lookup"><span data-stu-id="82722-311">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="82722-312">[輸入標記](xref:mvc/views/working-with-forms)協助程式 (`<input asp-for="Movie.Title" class="form-control">`) 會使用[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6)屬性，並產生在用戶端上進行 JQUERY 驗證所需的 HTML 屬性。</span><span class="sxs-lookup"><span data-stu-id="82722-312">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="82722-313">其他資源</span><span class="sxs-lookup"><span data-stu-id="82722-313">Additional resources</span></span>

* [<span data-ttu-id="82722-314">本教學課程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="82722-314">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="82722-315">[上一步：新增模型](xref:tutorials/razor-pages/model) 
> [下一步：使用資料庫](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="82722-315">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
