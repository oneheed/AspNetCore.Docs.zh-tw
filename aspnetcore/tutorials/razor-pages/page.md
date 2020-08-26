---
title: 第3部分： Razor ASP.NET Core 中的 scaffold 頁面
author: rick-anderson
description: 頁面上教學課程系列的第3部分 Razor 。
ms.author: riande
ms.date: 08/17/2019
no-loc:
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
ms.openlocfilehash: 03febbd71df19cd3524d26e229a8bd8798a874b5
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865117"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="1bf04-103">第3部分： Razor ASP.NET Core 中的 scaffold 頁面</span><span class="sxs-lookup"><span data-stu-id="1bf04-103">Part 3, scaffolded Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1bf04-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1bf04-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1bf04-105">本教學課程會檢查 Razor [先前教學](xref:tutorials/razor-pages/model)課程中的樣板所建立的頁面。</span><span class="sxs-lookup"><span data-stu-id="1bf04-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="1bf04-106">Create、Delete、Details 和 Edit 頁面</span><span class="sxs-lookup"><span data-stu-id="1bf04-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="1bf04-107">檢查 *Pages/Movies/Index.cshtml.cs* 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="1bf04-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="1bf04-108">Razor 頁面衍生自 `PageModel` 。</span><span class="sxs-lookup"><span data-stu-id="1bf04-108">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="1bf04-109">依照慣例，`PageModel` 衍生的類別稱為 `<PageName>Model`。</span><span class="sxs-lookup"><span data-stu-id="1bf04-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="1bf04-110">建構函式會使用[相依性插入](xref:fundamentals/dependency-injection)將 `RazorPagesMovieContext` 新增至頁面中。</span><span class="sxs-lookup"><span data-stu-id="1bf04-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="1bf04-111">所有 Scaffold 頁面都遵循這個模式。</span><span class="sxs-lookup"><span data-stu-id="1bf04-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="1bf04-112">如需使用 Entity Framework 進行非同步程式設計的詳細資訊，請參閱[非同步程式碼](xref:data/ef-rp/intro#asynchronous-code)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="1bf04-113">當對頁面提出要求時，方法會將 `OnGetAsync` 電影清單傳回 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="1bf04-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="1bf04-114">`OnGetAsync` 或 `OnGet` 呼叫來初始化頁面的狀態。</span><span class="sxs-lookup"><span data-stu-id="1bf04-114">`OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="1bf04-115">在此情況下，`OnGetAsync` 會取得電影清單並加以顯示。</span><span class="sxs-lookup"><span data-stu-id="1bf04-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="1bf04-116">當傳回或傳回時 `OnGet` `void` `OnGetAsync` `Task` ，不會使用 return 語句。</span><span class="sxs-lookup"><span data-stu-id="1bf04-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return statement is used.</span></span> <span data-ttu-id="1bf04-117">當傳回型別是 `IActionResult` 或 `Task<IActionResult>` 時，必須提供傳回陳述式。</span><span class="sxs-lookup"><span data-stu-id="1bf04-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="1bf04-118">例如，*Pages/Movies/Create.cshtml.cs* `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="1bf04-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="1bf04-119">檢查 *Pages/電影/Index. cshtml* Razor 頁面：</span><span class="sxs-lookup"><span data-stu-id="1bf04-119">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="1bf04-120">Razor 可以從 HTML 轉換成 c # 或 Razor 特定標記。</span><span class="sxs-lookup"><span data-stu-id="1bf04-120">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="1bf04-121">當 `@` 符號後面接著[ Razor 保留關鍵字](xref:mvc/views/razor#razor-reserved-keywords)時，它會轉換為 Razor 特定標記，否則會轉換成 c #。</span><span class="sxs-lookup"><span data-stu-id="1bf04-121">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="1bf04-122">@page 指示詞</span><span class="sxs-lookup"><span data-stu-id="1bf04-122">The @page directive</span></span>

<span data-ttu-id="1bf04-123">指示詞 `@page` Razor 會讓檔案成為 MVC 動作，這表示它可以處理要求。</span><span class="sxs-lookup"><span data-stu-id="1bf04-123">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="1bf04-124">`@page` 必須是頁面上的第一個指示詞 Razor 。</span><span class="sxs-lookup"><span data-stu-id="1bf04-124">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="1bf04-125">`@page` 是轉換為 Razor 特定標記的範例。</span><span class="sxs-lookup"><span data-stu-id="1bf04-125">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="1bf04-126">如需詳細資訊，請參閱[ Razor 語法](xref:mvc/views/razor#razor-syntax)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-126">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="1bf04-127">檢查下列 HTML 協助程式中使用的 Lambda 運算式：</span><span class="sxs-lookup"><span data-stu-id="1bf04-127">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="1bf04-128">`DisplayNameFor` HTML 協助程式會檢查 Lambda 運算式中參考的 `Title` 屬性來判斷顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="1bf04-128">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="1bf04-129">Lambda 運算式是進行檢查而不是評估。</span><span class="sxs-lookup"><span data-stu-id="1bf04-129">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="1bf04-130">這表示當 `model` 、 `model.Movie` 或 `model.Movie[0]` 為 `null` 或空白時，不會發生存取違規。</span><span class="sxs-lookup"><span data-stu-id="1bf04-130">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="1bf04-131">在評估 Lambda 運算式時 (例如，使用 `@Html.DisplayFor(modelItem => item.Title)`)，會評估模型的屬性值。</span><span class="sxs-lookup"><span data-stu-id="1bf04-131">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="1bf04-132">@model 指示詞</span><span class="sxs-lookup"><span data-stu-id="1bf04-132">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="1bf04-133">指示詞會 `@model` 指定傳遞至頁面的模型類型 Razor 。</span><span class="sxs-lookup"><span data-stu-id="1bf04-133">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="1bf04-134">在上述範例中，這 `@model` 一行讓 `PageModel` 衍生的類別可供頁面使用 Razor 。</span><span class="sxs-lookup"><span data-stu-id="1bf04-134">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="1bf04-135">此模型用於頁面上的 `@Html.DisplayNameFor` 和 `@Html.DisplayFor` [HTML 協助程式](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-135">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="1bf04-136">版面配置頁</span><span class="sxs-lookup"><span data-stu-id="1bf04-136">The layout page</span></span>

<span data-ttu-id="1bf04-137">選取 [ \*\* Razor PagesMovie**]、[**首頁**] 和 [**隱私權\*\*])  (的功能表連結。</span><span class="sxs-lookup"><span data-stu-id="1bf04-137">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="1bf04-138">每個頁面會顯示相同的功能表配置。</span><span class="sxs-lookup"><span data-stu-id="1bf04-138">Each page shows the same menu layout.</span></span> <span data-ttu-id="1bf04-139">功能表配置會在 *Pages/Shared/_Layout.cshtml* 檔案中實作。</span><span class="sxs-lookup"><span data-stu-id="1bf04-139">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="1bf04-140">開啟 *Pages/Shared/_Layout.cshtml* 檔案。</span><span class="sxs-lookup"><span data-stu-id="1bf04-140">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="1bf04-141">[版面配置](xref:mvc/views/layout)範本可讓 HTML 容器版面配置：</span><span class="sxs-lookup"><span data-stu-id="1bf04-141">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="1bf04-142">指定在一個位置。</span><span class="sxs-lookup"><span data-stu-id="1bf04-142">Specified in one place.</span></span>
* <span data-ttu-id="1bf04-143">套用於網站中的多個頁面。</span><span class="sxs-lookup"><span data-stu-id="1bf04-143">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="1bf04-144">找到 `@RenderBody()` 這行。</span><span class="sxs-lookup"><span data-stu-id="1bf04-144">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="1bf04-145">`RenderBody` 是一個預留位置，可供顯示所有頁面特定檢視 (「包裝」\*\* 在版面配置頁面中)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-145">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="1bf04-146">例如，選取 [隱私權]\*\*\*\* 連結，就會在 `RenderBody` 方法內轉譯 *Pages/Privacy.cshtml* 檢視。</span><span class="sxs-lookup"><span data-stu-id="1bf04-146">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="1bf04-147">ViewData 和 Layout</span><span class="sxs-lookup"><span data-stu-id="1bf04-147">ViewData and layout</span></span>

<span data-ttu-id="1bf04-148">請考慮來自 *Pages/Movies/Index.cshtml* 檔案的下列標記：</span><span class="sxs-lookup"><span data-stu-id="1bf04-148">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="1bf04-149">上述反白顯示的標記是 Razor 轉換成 c # 的範例。</span><span class="sxs-lookup"><span data-stu-id="1bf04-149">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="1bf04-150">`{` 和 `}` 字元中含括 C# 程式碼的區塊。</span><span class="sxs-lookup"><span data-stu-id="1bf04-150">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="1bf04-151">`PageModel`基類包含 `ViewData` 字典屬性，可用來將資料傳遞給視圖。</span><span class="sxs-lookup"><span data-stu-id="1bf04-151">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="1bf04-152">物件會使用機碼/值模式新增至 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="1bf04-152">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="1bf04-153">在上述範例中，`"Title"` 屬性會新增至 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="1bf04-153">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="1bf04-154">*Pages/Shared/_Layout.cshtml* 檔案中使用 `"Title"` 屬性。</span><span class="sxs-lookup"><span data-stu-id="1bf04-154">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="1bf04-155">下列標記會顯示 *_Layout.cshtml* 檔案的前幾行。</span><span class="sxs-lookup"><span data-stu-id="1bf04-155">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="1bf04-156">這一行 `@*Markup removed for brevity.*@` 是 Razor 批註。</span><span class="sxs-lookup"><span data-stu-id="1bf04-156">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="1bf04-157">不同于 HTML 批註 (`<!-- -->`) ， Razor 批註不會傳送至用戶端。</span><span class="sxs-lookup"><span data-stu-id="1bf04-157">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="1bf04-158">更新配置</span><span class="sxs-lookup"><span data-stu-id="1bf04-158">Update the layout</span></span>

<span data-ttu-id="1bf04-159">變更 `<title>` *Pages/Shared/_Layout cshtml*檔案中的專案，以顯示**電影**，而不是\*\* Razor PagesMovie\*\*。</span><span class="sxs-lookup"><span data-stu-id="1bf04-159">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="1bf04-160">在 *Pages/Shared/_Layout.cshtml* 檔案中尋找下列錨點元素。</span><span class="sxs-lookup"><span data-stu-id="1bf04-160">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="1bf04-161">以下列標記來取代上述元素：</span><span class="sxs-lookup"><span data-stu-id="1bf04-161">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="1bf04-162">上述的錨點項目是[標記協助程式](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-162">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="1bf04-163">在此情況下，它是[錨點標記協助程式](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-163">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="1bf04-164">標籤協助程式 `asp-page="/Movies/Index"` 屬性和值會建立頁面的連結 `/Movies/Index` Razor 。</span><span class="sxs-lookup"><span data-stu-id="1bf04-164">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="1bf04-165">`asp-area` 屬性值為空白，因此不會在連結中使用該區域。</span><span class="sxs-lookup"><span data-stu-id="1bf04-165">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="1bf04-166">如需詳細資訊，請參閱[區域](xref:mvc/controllers/areas)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-166">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="1bf04-167">儲存變更，並按一下 **RpMovie** 連結來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="1bf04-167">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="1bf04-168">如有任何問題，請參閱 GitHub 中的 [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) 檔案。</span><span class="sxs-lookup"><span data-stu-id="1bf04-168">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="1bf04-169">測試其他連結 (**Home**、**RpMovie**、**Create**、**Edit** 和 **Delete**)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-169">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="1bf04-170">每個頁面都會設定標題，您可以在 [瀏覽器] 索引標籤中看到此標題。當您將頁面加入書簽時，此標題會用於書簽。</span><span class="sxs-lookup"><span data-stu-id="1bf04-170">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="1bf04-171">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="1bf04-171">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="1bf04-172">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期欄位支援 [jQuery 驗證](https://jqueryvalidation.org/)，您必須採取將應用程式全球化的步驟。</span><span class="sxs-lookup"><span data-stu-id="1bf04-172">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="1bf04-173">請參閱此 [GitHub 問題 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)，以取得加入十進位逗號的指示。</span><span class="sxs-lookup"><span data-stu-id="1bf04-173">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="1bf04-174">`Layout` 屬性是在 *Pages/_ViewStart.cshtml* 檔案中設定：</span><span class="sxs-lookup"><span data-stu-id="1bf04-174">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="1bf04-175">上述標記會將 Pages 資料夾下的所有檔案的版面配置檔案設定為*pages/Shared/_Layout。* Razor *Pages*</span><span class="sxs-lookup"><span data-stu-id="1bf04-175">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="1bf04-176">如需詳細資訊，請參閱 [Layout](xref:razor-pages/index#layout)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-176">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="1bf04-177">Create 頁面模型</span><span class="sxs-lookup"><span data-stu-id="1bf04-177">The Create page model</span></span>

<span data-ttu-id="1bf04-178">檢查 *Pages/Movies/Create.cshtml.cs* 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="1bf04-178">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="1bf04-179">`OnGet` 方法會初始化頁面所需的任何狀態。</span><span class="sxs-lookup"><span data-stu-id="1bf04-179">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="1bf04-180">建立頁面沒有任何要初始化的狀態，所以傳回 `Page`。</span><span class="sxs-lookup"><span data-stu-id="1bf04-180">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="1bf04-181">稍後在此教學課程中，會顯示 `OnGet` 初始化狀態的範例。</span><span class="sxs-lookup"><span data-stu-id="1bf04-181">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="1bf04-182">`Page` 方法會建立 `PageResult` 物件，用以呈現 *Create.cshtml* 頁面。</span><span class="sxs-lookup"><span data-stu-id="1bf04-182">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="1bf04-183">`Movie` 屬性 (property) 使用 `[BindProperty]` 屬性 (attribute) 來加入[模型繫結](xref:mvc/models/model-binding)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-183">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="1bf04-184">當 Create 表單發佈表單值時，ASP.NET Core 執行階段會將發佈的值繫結至 `Movie` 模型。</span><span class="sxs-lookup"><span data-stu-id="1bf04-184">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="1bf04-185">當頁面發佈表單資料時，即會執行 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="1bf04-185">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="1bf04-186">如果沒有任何模型錯誤，將會重新顯示表單，以及任何發佈的表單資料。</span><span class="sxs-lookup"><span data-stu-id="1bf04-186">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="1bf04-187">大部分的模型錯誤可以在發佈表單之前，於用戶端上攔截到。</span><span class="sxs-lookup"><span data-stu-id="1bf04-187">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="1bf04-188">模型錯誤的範例為針對日期欄位發佈無法轉換為日期的值。</span><span class="sxs-lookup"><span data-stu-id="1bf04-188">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="1bf04-189">稍後的教學課程中將討論用戶端驗證和模型驗證。</span><span class="sxs-lookup"><span data-stu-id="1bf04-189">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="1bf04-190">如果沒有任何模型錯誤，就會儲存資料，而瀏覽器則會重新導向至 Index 頁面。</span><span class="sxs-lookup"><span data-stu-id="1bf04-190">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="1bf04-191">[建立] Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="1bf04-191">The Create Razor Page</span></span>

<span data-ttu-id="1bf04-192">檢查 *Pages/電影/Create. cshtml* Razor 分頁檔案：</span><span class="sxs-lookup"><span data-stu-id="1bf04-192">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="1bf04-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf04-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1bf04-194">Visual Studio 會以用於標籤協助程式的特殊粗體字型顯示下列標籤：</span><span class="sxs-lookup"><span data-stu-id="1bf04-194">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Create.cshtml 頁面的 VS17 檢視](page/_static/th3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf04-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf04-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1bf04-197">上述標記中會顯示下列標籤協助程式：</span><span class="sxs-lookup"><span data-stu-id="1bf04-197">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1bf04-198">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1bf04-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1bf04-199">Visual Studio 會以用於標籤協助程式的特殊粗體字型顯示下列標籤：</span><span class="sxs-lookup"><span data-stu-id="1bf04-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="1bf04-200">`<form method="post">` 項目是[表單標記協助程式](xref:mvc/views/working-with-forms#the-form-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-200">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="1bf04-201">表單標記協助程式會自動包含 [antiforgery 語彙基元](xref:security/anti-request-forgery)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-201">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="1bf04-202">此範例引擎會 Razor 針對模型中的每個欄位建立標記 (但識別碼) 如下所示：</span><span class="sxs-lookup"><span data-stu-id="1bf04-202">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="1bf04-203">[驗證](xref:mvc/views/working-with-forms#the-validation-tag-helpers)標籤協助程式 (`<div asp-validation-summary` ，並 `<span asp-validation-for`) 顯示驗證錯誤。</span><span class="sxs-lookup"><span data-stu-id="1bf04-203">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="1bf04-204">驗證將於本文稍後詳細討論到。</span><span class="sxs-lookup"><span data-stu-id="1bf04-204">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="1bf04-205">[標籤標記](xref:mvc/views/working-with-forms#the-label-tag-helper)協助 `<label asp-for="Movie.Title" class="control-label"></label>` 程式 () 會產生屬性的標籤標題和 `for` 屬性 `Title` 。</span><span class="sxs-lookup"><span data-stu-id="1bf04-205">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="1bf04-206">[輸入標記](xref:mvc/views/working-with-forms)協助程式 (`<input asp-for="Movie.Title" class="form-control">`) 會使用[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6)屬性，並產生在用戶端上進行 JQUERY 驗證所需的 HTML 屬性。</span><span class="sxs-lookup"><span data-stu-id="1bf04-206">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="1bf04-207">如需標籤協助程式 (例如 `<form method="post">`) 的詳細資訊，請參閱 [ASP.NET Core 中的標籤協助程式](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-207">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1bf04-208">其他資源</span><span class="sxs-lookup"><span data-stu-id="1bf04-208">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="1bf04-209">[上一步：新增模型](xref:tutorials/razor-pages/model) 
> [下一步：資料庫](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="1bf04-209">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1bf04-210">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1bf04-210">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1bf04-211">本教學課程會檢查 Razor [先前教學](xref:tutorials/razor-pages/model)課程中的樣板所建立的頁面。</span><span class="sxs-lookup"><span data-stu-id="1bf04-211">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="1bf04-212">[檢視或下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22)範例。</span><span class="sxs-lookup"><span data-stu-id="1bf04-212">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="1bf04-213">Create、Delete、Details 和 Edit 頁面</span><span class="sxs-lookup"><span data-stu-id="1bf04-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="1bf04-214">檢查 *Pages/Movies/Index.cshtml.cs* 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="1bf04-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="1bf04-215">Razor 頁面衍生自 `PageModel` 。</span><span class="sxs-lookup"><span data-stu-id="1bf04-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="1bf04-216">依照慣例，`PageModel` 衍生的類別稱為 `<PageName>Model`。</span><span class="sxs-lookup"><span data-stu-id="1bf04-216">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="1bf04-217">建構函式會使用[相依性插入](xref:fundamentals/dependency-injection)將 `RazorPagesMovieContext` 新增至頁面中。</span><span class="sxs-lookup"><span data-stu-id="1bf04-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="1bf04-218">所有 Scaffold 頁面都遵循這個模式。</span><span class="sxs-lookup"><span data-stu-id="1bf04-218">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="1bf04-219">如需使用 Entity Framework 進行非同步程式設計的詳細資訊，請參閱[非同步程式碼](xref:data/ef-rp/intro#asynchronous-code)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="1bf04-220">當對頁面提出要求時，方法會將 `OnGetAsync` 電影清單傳回 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="1bf04-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="1bf04-221">`OnGetAsync` 或 `OnGet` 在頁面上呼叫， Razor 以初始化頁面的狀態。</span><span class="sxs-lookup"><span data-stu-id="1bf04-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="1bf04-222">在此情況下，`OnGetAsync` 會取得電影清單並加以顯示。</span><span class="sxs-lookup"><span data-stu-id="1bf04-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="1bf04-223">當 `OnGet` 傳回 `void` 或 `OnGetAsync` 傳回 `Task` 時，並未使用任何傳回方法。</span><span class="sxs-lookup"><span data-stu-id="1bf04-223">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="1bf04-224">當傳回型別是 `IActionResult` 或 `Task<IActionResult>` 時，必須提供傳回陳述式。</span><span class="sxs-lookup"><span data-stu-id="1bf04-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="1bf04-225">例如，*Pages/Movies/Create.cshtml.cs* `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="1bf04-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="1bf04-226">檢查 *Pages/電影/Index. cshtml* Razor 頁面：</span><span class="sxs-lookup"><span data-stu-id="1bf04-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="1bf04-227">Razor 可以從 HTML 轉換成 c # 或 Razor 特定標記。</span><span class="sxs-lookup"><span data-stu-id="1bf04-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="1bf04-228">當 `@` 符號後面接著[ Razor 保留關鍵字](xref:mvc/views/razor#razor-reserved-keywords)時，它會轉換為 Razor 特定標記，否則會轉換成 c #。</span><span class="sxs-lookup"><span data-stu-id="1bf04-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="1bf04-229">指示詞會將檔案 `@page` Razor 變成 MVC 動作，這表示它可以處理要求。</span><span class="sxs-lookup"><span data-stu-id="1bf04-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="1bf04-230">`@page` 必須是頁面上的第一個指示詞 Razor 。</span><span class="sxs-lookup"><span data-stu-id="1bf04-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="1bf04-231">`@page` 是轉換為 Razor 特定標記的範例。</span><span class="sxs-lookup"><span data-stu-id="1bf04-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="1bf04-232">如需詳細資訊，請參閱[ Razor 語法](xref:mvc/views/razor#razor-syntax)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="1bf04-233">檢查下列 HTML 協助程式中使用的 Lambda 運算式：</span><span class="sxs-lookup"><span data-stu-id="1bf04-233">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="1bf04-234">`DisplayNameFor` HTML 協助程式會檢查 Lambda 運算式中參考的 `Title` 屬性來判斷顯示名稱。</span><span class="sxs-lookup"><span data-stu-id="1bf04-234">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="1bf04-235">Lambda 運算式是進行檢查而不是評估。</span><span class="sxs-lookup"><span data-stu-id="1bf04-235">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="1bf04-236">這表示當 `model`、`model.Movie` 或 `model.Movie[0]` 是 `null` 或空白時，不會有任何存取違規。</span><span class="sxs-lookup"><span data-stu-id="1bf04-236">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="1bf04-237">在評估 Lambda 運算式時 (例如，使用 `@Html.DisplayFor(modelItem => item.Title)`)，會評估模型的屬性值。</span><span class="sxs-lookup"><span data-stu-id="1bf04-237">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="1bf04-238">@model 指示詞</span><span class="sxs-lookup"><span data-stu-id="1bf04-238">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="1bf04-239">指示詞會 `@model` 指定傳遞至頁面的模型類型 Razor 。</span><span class="sxs-lookup"><span data-stu-id="1bf04-239">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="1bf04-240">在上述範例中，這 `@model` 一行讓 `PageModel` 衍生的類別可供頁面使用 Razor 。</span><span class="sxs-lookup"><span data-stu-id="1bf04-240">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="1bf04-241">此模型用於頁面上的 `@Html.DisplayNameFor` 和 `@Html.DisplayFor` [HTML 協助程式](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-241">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="1bf04-242">版面配置頁</span><span class="sxs-lookup"><span data-stu-id="1bf04-242">The layout page</span></span>

<span data-ttu-id="1bf04-243">選取 [ \*\* Razor PagesMovie**]、[**首頁**] 和 [**隱私權\*\*])  (的功能表連結。</span><span class="sxs-lookup"><span data-stu-id="1bf04-243">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="1bf04-244">每個頁面會顯示相同的功能表配置。</span><span class="sxs-lookup"><span data-stu-id="1bf04-244">Each page shows the same menu layout.</span></span> <span data-ttu-id="1bf04-245">功能表配置會在 *Pages/Shared/_Layout.cshtml* 檔案中實作。</span><span class="sxs-lookup"><span data-stu-id="1bf04-245">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="1bf04-246">開啟 *Pages/Shared/_Layout.cshtml* 檔案。</span><span class="sxs-lookup"><span data-stu-id="1bf04-246">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="1bf04-247">[版面配置](xref:mvc/views/layout)範本可讓您在某個位置指定網站的 HTML 容器配置，然後將它套用到網站中的多個頁面。</span><span class="sxs-lookup"><span data-stu-id="1bf04-247">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="1bf04-248">找到 `@RenderBody()` 這行。</span><span class="sxs-lookup"><span data-stu-id="1bf04-248">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="1bf04-249">`RenderBody` 是一個「包裝」\*\* 在版面配置頁中的預留位置，可供顯示您建立的所有頁面特定檢視。</span><span class="sxs-lookup"><span data-stu-id="1bf04-249">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="1bf04-250">例如，如果您選取 [Privacy]\*\*\*\* 連結，就會在 `RenderBody` 方法內呈現 **Pages/Privacy.cshtml** 檢視。</span><span class="sxs-lookup"><span data-stu-id="1bf04-250">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="1bf04-251">ViewData 和 Layout</span><span class="sxs-lookup"><span data-stu-id="1bf04-251">ViewData and layout</span></span>

<span data-ttu-id="1bf04-252">請考慮來自 *Pages/Movies/Index.cshtml* 檔案的下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="1bf04-252">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="1bf04-253">上述反白顯示的程式碼是 Razor 轉換成 c # 的範例。</span><span class="sxs-lookup"><span data-stu-id="1bf04-253">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="1bf04-254">`{` 和 `}` 字元中含括 C# 程式碼的區塊。</span><span class="sxs-lookup"><span data-stu-id="1bf04-254">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="1bf04-255">`PageModel` 基底類別有 `ViewData` 字典屬性，可用來新增至您想要傳遞到檢視的資料。</span><span class="sxs-lookup"><span data-stu-id="1bf04-255">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="1bf04-256">您可以使用索引鍵/值模式將物件新增至 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="1bf04-256">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="1bf04-257">在上述範例中，"Title" 屬性會新增至 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="1bf04-257">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="1bf04-258">"Title" 屬性是用於 *Pages/Shared/_Layout.cshtml* 檔案。</span><span class="sxs-lookup"><span data-stu-id="1bf04-258">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="1bf04-259">下列標記會顯示 *_Layout.cshtml* 檔案的前幾行。</span><span class="sxs-lookup"><span data-stu-id="1bf04-259">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="1bf04-260">這一行 `@*Markup removed for brevity.*@` 是 Razor 批註，不會出現在您的版面配置檔案中。</span><span class="sxs-lookup"><span data-stu-id="1bf04-260">The line `@*Markup removed for brevity.*@` is a Razor comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="1bf04-261">不同于 HTML 批註 (`<!-- -->`) ， Razor 批註不會傳送至用戶端。</span><span class="sxs-lookup"><span data-stu-id="1bf04-261">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="1bf04-262">更新配置</span><span class="sxs-lookup"><span data-stu-id="1bf04-262">Update the layout</span></span>

<span data-ttu-id="1bf04-263">變更 `<title>` *Pages/Shared/_Layout cshtml*檔案中的專案，以顯示**電影**，而不是\*\* Razor PagesMovie\*\*。</span><span class="sxs-lookup"><span data-stu-id="1bf04-263">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="1bf04-264">在 *Pages/Shared/_Layout.cshtml* 檔案中尋找下列錨點元素。</span><span class="sxs-lookup"><span data-stu-id="1bf04-264">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="1bf04-265">以下列標記來取代上述項目。</span><span class="sxs-lookup"><span data-stu-id="1bf04-265">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="1bf04-266">上述的錨點項目是[標記協助程式](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-266">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="1bf04-267">在此情況下，它是[錨點標記協助程式](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-267">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="1bf04-268">標籤協助程式 `asp-page="/Movies/Index"` 屬性和值會建立頁面的連結 `/Movies/Index` Razor 。</span><span class="sxs-lookup"><span data-stu-id="1bf04-268">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="1bf04-269">`asp-area` 屬性值為空白，因此不會在連結中使用該區域。</span><span class="sxs-lookup"><span data-stu-id="1bf04-269">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="1bf04-270">如需詳細資訊，請參閱[區域](xref:mvc/controllers/areas)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-270">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="1bf04-271">儲存變更，並按一下 **RpMovie** 連結來測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="1bf04-271">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="1bf04-272">如有任何問題，請參閱 GitHub 中的 [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) 檔案。</span><span class="sxs-lookup"><span data-stu-id="1bf04-272">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="1bf04-273">測試其他連結 (**Home**、**RpMovie**、**Create**、**Edit** 和 **Delete**)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-273">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="1bf04-274">每個頁面都會設定標題，您可以在 [瀏覽器] 索引標籤中看到此標題。當您將頁面加入書簽時，此標題會用於書簽。</span><span class="sxs-lookup"><span data-stu-id="1bf04-274">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="1bf04-275">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="1bf04-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="1bf04-276">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期欄位支援 [jQuery 驗證](https://jqueryvalidation.org/)，您必須採取將應用程式全球化的步驟。</span><span class="sxs-lookup"><span data-stu-id="1bf04-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="1bf04-277">這個 [GitHub 問題 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) 有加入小數逗號的指示。</span><span class="sxs-lookup"><span data-stu-id="1bf04-277">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="1bf04-278">`Layout` 屬性是在 *Pages/_ViewStart.cshtml* 檔案中設定：</span><span class="sxs-lookup"><span data-stu-id="1bf04-278">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="1bf04-279">上述標記會將 Pages 資料夾下的所有檔案的版面配置檔案設定為*pages/Shared/_Layout。* Razor *Pages*</span><span class="sxs-lookup"><span data-stu-id="1bf04-279">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="1bf04-280">如需詳細資訊，請參閱 [Layout](xref:razor-pages/index#layout)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-280">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="1bf04-281">Create 頁面模型</span><span class="sxs-lookup"><span data-stu-id="1bf04-281">The Create page model</span></span>

<span data-ttu-id="1bf04-282">檢查 *Pages/Movies/Create.cshtml.cs* 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="1bf04-282">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="1bf04-283">`OnGet` 方法會初始化頁面所需的任何狀態。</span><span class="sxs-lookup"><span data-stu-id="1bf04-283">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="1bf04-284">建立頁面沒有任何要初始化的狀態，所以傳回 `Page`。</span><span class="sxs-lookup"><span data-stu-id="1bf04-284">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="1bf04-285">稍後在本教學課程中您會看到 `OnGet` 方法初始化狀態。</span><span class="sxs-lookup"><span data-stu-id="1bf04-285">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="1bf04-286">`Page` 方法會建立 `PageResult` 物件，用以呈現 *Create.cshtml* 頁面。</span><span class="sxs-lookup"><span data-stu-id="1bf04-286">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="1bf04-287">`Movie` 屬性 (property) 使用 `[BindProperty]` 屬性 (attribute) 來加入[模型繫結](xref:mvc/models/model-binding)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-287">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="1bf04-288">當 Create 表單發佈表單值時，ASP.NET Core 執行階段會將發佈的值繫結至 `Movie` 模型。</span><span class="sxs-lookup"><span data-stu-id="1bf04-288">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="1bf04-289">當頁面發佈表單資料時，即會執行 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="1bf04-289">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="1bf04-290">如果沒有任何模型錯誤，將會重新顯示表單，以及任何發佈的表單資料。</span><span class="sxs-lookup"><span data-stu-id="1bf04-290">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="1bf04-291">大部分的模型錯誤可以在發佈表單之前，於用戶端上攔截到。</span><span class="sxs-lookup"><span data-stu-id="1bf04-291">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="1bf04-292">模型錯誤的範例為針對日期欄位發佈無法轉換為日期的值。</span><span class="sxs-lookup"><span data-stu-id="1bf04-292">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="1bf04-293">稍後的教學課程中將討論用戶端驗證和模型驗證。</span><span class="sxs-lookup"><span data-stu-id="1bf04-293">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="1bf04-294">如果沒有任何模型錯誤，就會儲存資料，而瀏覽器則會重新導向至 Index 頁面。</span><span class="sxs-lookup"><span data-stu-id="1bf04-294">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="1bf04-295">[建立] Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="1bf04-295">The Create Razor Page</span></span>

<span data-ttu-id="1bf04-296">檢查 *Pages/電影/Create. cshtml* Razor 分頁檔案：</span><span class="sxs-lookup"><span data-stu-id="1bf04-296">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="1bf04-297">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf04-297">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1bf04-298">Visual Studio 會以特別的粗體字型顯示 `<form method="post">` 標籤，用於標籤協助程式：</span><span class="sxs-lookup"><span data-stu-id="1bf04-298">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Create.cshtml 頁面的 VS17 檢視](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf04-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf04-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1bf04-301">如需標籤協助程式 (例如 `<form method="post">`) 的詳細資訊，請參閱 [ASP.NET Core 中的標籤協助程式](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-301">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1bf04-302">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1bf04-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1bf04-303">Visual Studio for Mac 會以特別的粗體字型顯示 `<form method="post">` 標籤，用於標籤協助程式。</span><span class="sxs-lookup"><span data-stu-id="1bf04-303">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="1bf04-304">`<form method="post">` 項目是[表單標記協助程式](xref:mvc/views/working-with-forms#the-form-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-304">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="1bf04-305">表單標記協助程式會自動包含 [antiforgery 語彙基元](xref:security/anti-request-forgery)。</span><span class="sxs-lookup"><span data-stu-id="1bf04-305">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="1bf04-306">此範例引擎會 Razor 針對模型中的每個欄位建立標記 (但識別碼) 如下所示：</span><span class="sxs-lookup"><span data-stu-id="1bf04-306">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="1bf04-307">[驗證](xref:mvc/views/working-with-forms#the-validation-tag-helpers)標籤協助程式 (`<div asp-validation-summary` ，並 `<span asp-validation-for`) 顯示驗證錯誤。</span><span class="sxs-lookup"><span data-stu-id="1bf04-307">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="1bf04-308">驗證將於本文稍後詳細討論到。</span><span class="sxs-lookup"><span data-stu-id="1bf04-308">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="1bf04-309">[標籤標記](xref:mvc/views/working-with-forms#the-label-tag-helper)協助 `<label asp-for="Movie.Title" class="control-label"></label>` 程式 () 會產生屬性的標籤標題和 `for` 屬性 `Title` 。</span><span class="sxs-lookup"><span data-stu-id="1bf04-309">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="1bf04-310">[輸入標記](xref:mvc/views/working-with-forms)協助程式 (`<input asp-for="Movie.Title" class="form-control">`) 會使用[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6)屬性，並產生在用戶端上進行 JQUERY 驗證所需的 HTML 屬性。</span><span class="sxs-lookup"><span data-stu-id="1bf04-310">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1bf04-311">其他資源</span><span class="sxs-lookup"><span data-stu-id="1bf04-311">Additional resources</span></span>

* [<span data-ttu-id="1bf04-312">本教學課程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="1bf04-312">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="1bf04-313">[上一步：新增模型](xref:tutorials/razor-pages/model) 
> [下一步：資料庫](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="1bf04-313">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
