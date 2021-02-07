---
title: 第3部分：將視圖新增至 ASP.NET Core MVC 應用程式
author: rick-anderson
description: ASP.NET Core MVC 之教學課程系列的第3部分。
ms.author: riande
ms.date: 01/28/2021
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
uid: tutorials/first-mvc-app/adding-view
ms.custom: contperf-fy21q3
ms.openlocfilehash: 1542e44fcc6d0ae22fb1a759ea3a3ed1d866cbc7
ms.sourcegitcommit: 19a004ff2be73876a9ef0f1ac44d0331849ad159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804553"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="25652-103">第3部分：將視圖新增至 ASP.NET Core MVC 應用程式</span><span class="sxs-lookup"><span data-stu-id="25652-103">Part 3, add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="25652-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="25652-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="25652-105">在本節中，您會將 `HelloWorldController` 類別修改為使用 [Razor](xref:mvc/views/razor) view files。</span><span class="sxs-lookup"><span data-stu-id="25652-105">In this section, you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files.</span></span> <span data-ttu-id="25652-106">這會將產生 HTML 回應的程式明確地封裝到用戶端。</span><span class="sxs-lookup"><span data-stu-id="25652-106">This cleanly encapsulates the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="25652-107">使用建立的視圖範本 Razor 。</span><span class="sxs-lookup"><span data-stu-id="25652-107">View templates are created using Razor.</span></span> <span data-ttu-id="25652-108">Razor以架構為基礎的視圖範本：</span><span class="sxs-lookup"><span data-stu-id="25652-108">Razor-based view templates:</span></span>

* <span data-ttu-id="25652-109">具有 *cshtml* 副檔名。</span><span class="sxs-lookup"><span data-stu-id="25652-109">Have a *.cshtml* file extension.</span></span>
* <span data-ttu-id="25652-110">提供使用 c # 建立 HTML 輸出的簡潔方式。</span><span class="sxs-lookup"><span data-stu-id="25652-110">Provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="25652-111">目前，此方法會傳回 `Index` 字串，其中包含控制器類別中的訊息。</span><span class="sxs-lookup"><span data-stu-id="25652-111">Currently the `Index` method returns a string with a message in the controller class.</span></span> <span data-ttu-id="25652-112">在 `HelloWorldController` 類別中，以下列程式碼取代 `Index` 方法：</span><span class="sxs-lookup"><span data-stu-id="25652-112">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="25652-113">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="25652-113">The preceding code:</span></span>

* <span data-ttu-id="25652-114">呼叫控制器的 <xref:Microsoft.AspNetCore.Mvc.Controller.View*> 方法。</span><span class="sxs-lookup"><span data-stu-id="25652-114">Calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span>
* <span data-ttu-id="25652-115">使用 view 範本來產生 HTML 回應。</span><span class="sxs-lookup"><span data-stu-id="25652-115">Uses a view template to generate an HTML response.</span></span>

<span data-ttu-id="25652-116">控制器方法：</span><span class="sxs-lookup"><span data-stu-id="25652-116">Controller methods:</span></span>

* <span data-ttu-id="25652-117">稱為 *動作方法*。</span><span class="sxs-lookup"><span data-stu-id="25652-117">Are referred to as *action methods*.</span></span>  <span data-ttu-id="25652-118">例如，上述程式 `Index` 代碼中的動作方法。</span><span class="sxs-lookup"><span data-stu-id="25652-118">For example, the `Index` action method in the preceding code.</span></span>
* <span data-ttu-id="25652-119">通常會傳回 <xref:Microsoft.AspNetCore.Mvc.IActionResult> 或衍生自的類別 <xref:Microsoft.AspNetCore.Mvc.ActionResult> ，而不是像這樣的型別 `string` 。</span><span class="sxs-lookup"><span data-stu-id="25652-119">Generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>, not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="25652-120">新增檢視</span><span class="sxs-lookup"><span data-stu-id="25652-120">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="25652-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="25652-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="25652-122">在 [ *Views* ] 資料夾上按一下滑鼠右鍵，然後新增 > 的 [ **新資料夾** ]，然後將資料夾命名為 *HelloWorld*。</span><span class="sxs-lookup"><span data-stu-id="25652-122">Right-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

<span data-ttu-id="25652-123">以滑鼠右鍵按一下 *Views/HelloWorld* 資料夾，然後新增 **> 的新專案**。</span><span class="sxs-lookup"><span data-stu-id="25652-123">Right-click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

<span data-ttu-id="25652-124">在 [ **加入新專案-MvcMovie** ] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="25652-124">In the **Add New Item - MvcMovie** dialog:</span></span>

* <span data-ttu-id="25652-125">在右上角的搜尋方塊中，輸入 *view*</span><span class="sxs-lookup"><span data-stu-id="25652-125">In the search box in the upper-right, enter *view*</span></span>
* <span data-ttu-id="25652-126">選取 **Razor 視圖**</span><span class="sxs-lookup"><span data-stu-id="25652-126">Select **Razor View**</span></span>
* <span data-ttu-id="25652-127">保留 [名稱] 方塊值 *Index.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="25652-127">Keep the **Name** box value, *Index.cshtml*.</span></span>
* <span data-ttu-id="25652-128">選取 [新增]</span><span class="sxs-lookup"><span data-stu-id="25652-128">Select **Add**</span></span>

![[新增項目] 對話方塊](adding-view/_static/add_view50.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="25652-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="25652-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="25652-131">新增 `Index` 的視圖 `HelloWorldController` ：</span><span class="sxs-lookup"><span data-stu-id="25652-131">Add an `Index` view for the `HelloWorldController`:</span></span>

* <span data-ttu-id="25652-132">新增資料夾，並命名為 *Views/HelloWorld*。</span><span class="sxs-lookup"><span data-stu-id="25652-132">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="25652-133">將檔案新增至 *Views/HelloWorld* 資料夾，並命名為 *Index.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="25652-133">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="25652-134">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="25652-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="25652-135">以 Control-按一下 *Views* 資料夾，然後新增 > 的 [ **新資料夾** ]，並將資料夾命名為 *HelloWorld*。</span><span class="sxs-lookup"><span data-stu-id="25652-135">Control-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

<span data-ttu-id="25652-136">以 Control-按一下 *Views/HelloWorld* 資料夾，然後 **新增 > 新** 的檔案。</span><span class="sxs-lookup"><span data-stu-id="25652-136">Control-click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>

<span data-ttu-id="25652-137">在 [新增檔案] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="25652-137">In the **New File** dialog:</span></span>

* <span data-ttu-id="25652-138">選取左窗格中的 [ **ASP.NET Core** ]。</span><span class="sxs-lookup"><span data-stu-id="25652-138">Select **ASP.NET Core** in the left pane.</span></span>
* <span data-ttu-id="25652-139">選取中央窗格中的 [ **Razor View-Empty** ]。</span><span class="sxs-lookup"><span data-stu-id="25652-139">Select **Razor View - Empty** in the center pane.</span></span>
* <span data-ttu-id="25652-140">在 [**名稱**] 方塊中輸入 *索引*。</span><span class="sxs-lookup"><span data-stu-id="25652-140">Type *Index* in the **Name** box.</span></span>
* <span data-ttu-id="25652-141">選取 [ **新增**]。</span><span class="sxs-lookup"><span data-stu-id="25652-141">Select **New**.</span></span>

  ![[新增項目] 對話方塊](adding-view/_static/add_view_macVSM8.9.png)

---

<span data-ttu-id="25652-143">以下列內容取代 *Views/HelloWorld/Index. cshtml* view 檔案的內容 Razor ：</span><span class="sxs-lookup"><span data-stu-id="25652-143">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="25652-144">流覽至 `https://localhost:{PORT}/HelloWorld` ：</span><span class="sxs-lookup"><span data-stu-id="25652-144">Navigate to `https://localhost:{PORT}/HelloWorld`:</span></span>

* <span data-ttu-id="25652-145">`Index`中的方法 `HelloWorldController` `return View();` 會執行語句，這會指定方法應該使用 view 範本檔案來呈現瀏覽器的回應。</span><span class="sxs-lookup"><span data-stu-id="25652-145">The `Index` method in the `HelloWorldController` ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span>
* <span data-ttu-id="25652-146">未指定視圖範本檔案名，因此 MVC 預設為使用預設的 view 檔。</span><span class="sxs-lookup"><span data-stu-id="25652-146">A view template file name wasn't specified, so MVC defaulted to using the default view file.</span></span> <span data-ttu-id="25652-147">如果未指定 view 檔案名，則會傳回預設視圖。</span><span class="sxs-lookup"><span data-stu-id="25652-147">When the view file name isn't specified, the default view is returned.</span></span> <span data-ttu-id="25652-148">預設視圖的名稱與動作方法相同， `Index` 在此範例中為。</span><span class="sxs-lookup"><span data-stu-id="25652-148">The default view has the same name as the action method, `Index` in this example.</span></span> <span data-ttu-id="25652-149">使用 [視圖] 範本 */Views/HelloWorld/Index.cshtml* 。</span><span class="sxs-lookup"><span data-stu-id="25652-149">The view template */Views/HelloWorld/Index.cshtml* is used.</span></span>
* <span data-ttu-id="25652-150">下圖顯示「Hello from 我們的視圖範本！」字串</span><span class="sxs-lookup"><span data-stu-id="25652-150">The following image shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="25652-151">以硬式編碼的方式顯示：</span><span class="sxs-lookup"><span data-stu-id="25652-151">hard-coded in the view:</span></span>

  ![瀏覽器視窗](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="25652-153">變更檢視和版面配置頁</span><span class="sxs-lookup"><span data-stu-id="25652-153">Change views and layout pages</span></span>

<span data-ttu-id="25652-154">選取功能表連結 **MvcMovie**、 **首頁** 和 **隱私權**。</span><span class="sxs-lookup"><span data-stu-id="25652-154">Select the menu links **MvcMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="25652-155">每個頁面會顯示相同的功能表配置。</span><span class="sxs-lookup"><span data-stu-id="25652-155">Each page shows the same menu layout.</span></span> <span data-ttu-id="25652-156">功能表配置是在 *Views/Shared/_Layout.cshtml* 檔案中實作。</span><span class="sxs-lookup"><span data-stu-id="25652-156">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="25652-157">開啟 *Views/Shared/_Layout.cshtml* 檔案。</span><span class="sxs-lookup"><span data-stu-id="25652-157">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="25652-158">[版面](xref:mvc/views/layout) 配置範本允許：</span><span class="sxs-lookup"><span data-stu-id="25652-158">[Layout](xref:mvc/views/layout) templates allows:</span></span>

* <span data-ttu-id="25652-159">在同一處指定網站的 HTML 容器版面配置。</span><span class="sxs-lookup"><span data-stu-id="25652-159">Specifying the HTML container layout of a site in one place.</span></span>
* <span data-ttu-id="25652-160">在網站中的多個頁面上套用 HTML 容器版面配置。</span><span class="sxs-lookup"><span data-stu-id="25652-160">Applying the HTML container layout across multiple pages in the site.</span></span>

<span data-ttu-id="25652-161">找到 `@RenderBody()` 這行。</span><span class="sxs-lookup"><span data-stu-id="25652-161">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="25652-162">`RenderBody` 是顯示您建立之所有檢視特定頁面的預留位置，「包裝」在版面配置頁中。</span><span class="sxs-lookup"><span data-stu-id="25652-162">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="25652-163">例如，如果您選取 **Privacy** 連結，**Views/Home/Privacy.cshtml** 檢視就會呈現在 `RenderBody` 方法內。</span><span class="sxs-lookup"><span data-stu-id="25652-163">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="25652-164">變更配置檔案中的標題、頁尾及功能表連結</span><span class="sxs-lookup"><span data-stu-id="25652-164">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="25652-165">以下列標記取代 *Views/Shared/_Layout. cshtml* 檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="25652-165">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="25652-166">所做的變更已醒目提示：</span><span class="sxs-lookup"><span data-stu-id="25652-166">The changes are highlighted:</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="25652-167">上述標記進行了下列變更：</span><span class="sxs-lookup"><span data-stu-id="25652-167">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="25652-168">的三個出現位置 `MvcMovie` `Movie App` 。</span><span class="sxs-lookup"><span data-stu-id="25652-168">Three occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="25652-169">錨點元素 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` 改為 `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`。</span><span class="sxs-lookup"><span data-stu-id="25652-169">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="25652-170">在上述標記中， `asp-area=""` 已省略 [錨點](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) 標籤協助程式屬性和屬性值，因為此應用程式未使用 [區域](xref:mvc/controllers/areas)。</span><span class="sxs-lookup"><span data-stu-id="25652-170">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app isn't using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="25652-171">**注意**： `Movies` 控制器尚未執行。</span><span class="sxs-lookup"><span data-stu-id="25652-171">**Note**: The `Movies` controller hasn't been implemented.</span></span> <span data-ttu-id="25652-172">此時 `Movie App` 連結無法運作。</span><span class="sxs-lookup"><span data-stu-id="25652-172">At this point, the `Movie App` link isn't functional.</span></span>

<span data-ttu-id="25652-173">儲存變更，然後選取 [ **隱私權** ] 連結。</span><span class="sxs-lookup"><span data-stu-id="25652-173">Save the changes and select the **Privacy** link.</span></span> <span data-ttu-id="25652-174">請注意，瀏覽器索引標籤上的標題會顯示 **Privacy Policy - Movie App**，而不是 **Privacy Policy - Mvc Movie**：</span><span class="sxs-lookup"><span data-stu-id="25652-174">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Privacy 索引標籤](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

<span data-ttu-id="25652-176">選取 [首頁] 連結。</span><span class="sxs-lookup"><span data-stu-id="25652-176">Select the **Home** link.</span></span>

<span data-ttu-id="25652-177">請注意標題和錨點文字會顯示 **電影應用程式**。</span><span class="sxs-lookup"><span data-stu-id="25652-177">Notice that the title and anchor text display **Movie App**.</span></span> <span data-ttu-id="25652-178">變更會在版面配置範本中進行一次，而網站上的所有頁面會反映新的連結文字和新的標題。</span><span class="sxs-lookup"><span data-stu-id="25652-178">The changes were made once in the layout template and all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="25652-179">檢查 *Views/_ViewStart.cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="25652-179">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="25652-180">*Views/_ViewStart.cshtml* 檔案會將 *Views/Shared/_Layout.cshtml* 檔案引入每一個檢視。</span><span class="sxs-lookup"><span data-stu-id="25652-180">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="25652-181">`Layout` 屬性可用來設定不同的版面配置檢視，或將它設定為 `null`，因此不會使用任何版面配置檔案。</span><span class="sxs-lookup"><span data-stu-id="25652-181">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="25652-182">開啟 *Views/HelloWorld/Index. cshtml* view 檔案。</span><span class="sxs-lookup"><span data-stu-id="25652-182">Open the *Views/HelloWorld/Index.cshtml* view file.</span></span>

<span data-ttu-id="25652-183">變更標題和 `<h2>` 元素，如下所示：</span><span class="sxs-lookup"><span data-stu-id="25652-183">Change the title and `<h2>` element as highlighted in the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="25652-184">標題和 `<h2>` 元素稍有不同，因此可清楚說明程式碼中的哪個部分會變更顯示。</span><span class="sxs-lookup"><span data-stu-id="25652-184">The title and `<h2>` element are slightly different so it's clear which part of the code changes the display.</span></span>

<span data-ttu-id="25652-185">上述程式碼中的 `ViewData["Title"] = "Movie List";` 會將 `ViewData` 字典的 `Title` 屬性設定為 "Movie List"。</span><span class="sxs-lookup"><span data-stu-id="25652-185">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="25652-186">`Title` 屬性則用於版面配置頁中的 `<title>` HTML 元素：</span><span class="sxs-lookup"><span data-stu-id="25652-186">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="25652-187">儲存變更並巡覽至 `https://localhost:{PORT}/HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="25652-187">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span>

<span data-ttu-id="25652-188">請注意，下列已變更：</span><span class="sxs-lookup"><span data-stu-id="25652-188">Notice that the following have changed:</span></span>

* <span data-ttu-id="25652-189">瀏覽器標題。</span><span class="sxs-lookup"><span data-stu-id="25652-189">Browser title.</span></span>
* <span data-ttu-id="25652-190">主要標題。</span><span class="sxs-lookup"><span data-stu-id="25652-190">Primary heading.</span></span>
* <span data-ttu-id="25652-191">次要標題。</span><span class="sxs-lookup"><span data-stu-id="25652-191">Secondary headings.</span></span>

<span data-ttu-id="25652-192">如果瀏覽器中沒有任何變更，它可以是正在查看的快取內容。</span><span class="sxs-lookup"><span data-stu-id="25652-192">If there are no changes in the browser, it could be cached content that is being viewed.</span></span> <span data-ttu-id="25652-193">在瀏覽器中按 Ctrl + F5，以強制載入來自伺服器的回應。</span><span class="sxs-lookup"><span data-stu-id="25652-193">Press Ctrl+F5 in the browser to force the response from the server to be loaded.</span></span> <span data-ttu-id="25652-194">瀏覽器標題是以我們在 *Index.cshtml* 檢視範本中設定的 `ViewData["Title"]` 和版面配置檔案中新增的額外 "- Movie App" 來建立的。</span><span class="sxs-lookup"><span data-stu-id="25652-194">The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="25652-195">*Index.cshtml* 檢視範本中的內容已與 *Views/Shared/_Layout.cshtml* 檢視範本合併。</span><span class="sxs-lookup"><span data-stu-id="25652-195">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="25652-196">單一 HTML 回應會傳送到瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="25652-196">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="25652-197">版面配置範本可讓您輕鬆進行變更，然後套用到應用程式中的所有頁面。</span><span class="sxs-lookup"><span data-stu-id="25652-197">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="25652-198">若要深入了解，請參閱[版面配置](xref:mvc/views/layout)。</span><span class="sxs-lookup"><span data-stu-id="25652-198">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![電影清單檢視](~/tutorials/first-mvc-app/adding-view/_static/hell50.png)

<span data-ttu-id="25652-200">很小的「資料」，也就是 "Hello from a View Template！"</span><span class="sxs-lookup"><span data-stu-id="25652-200">The small bit of "data", the "Hello from our View Template!"</span></span> <span data-ttu-id="25652-201">但是，訊息是硬式編碼的。</span><span class="sxs-lookup"><span data-stu-id="25652-201">message, is hard-coded however.</span></span> <span data-ttu-id="25652-202">MVC 應用程式有 "V" (view) 、"C" (控制器) ，但尚未 ("M") 模型。</span><span class="sxs-lookup"><span data-stu-id="25652-202">The MVC application has a "V" (view), a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="25652-203">將資料從控制器傳遞至檢視</span><span class="sxs-lookup"><span data-stu-id="25652-203">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="25652-204">回應傳入的 URL 要求時會叫用控制器動作。</span><span class="sxs-lookup"><span data-stu-id="25652-204">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="25652-205">控制器類別是撰寫處理傳入瀏覽器要求之程式碼的位置。</span><span class="sxs-lookup"><span data-stu-id="25652-205">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="25652-206">控制器會從資料來源擷取資料，並決定要將哪一種回應傳回瀏覽器中。</span><span class="sxs-lookup"><span data-stu-id="25652-206">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="25652-207">您可以從控制器使用檢視範本來產生並格式化瀏覽器的 HTML 回應。</span><span class="sxs-lookup"><span data-stu-id="25652-207">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="25652-208">控制器負責提供為了讓檢視範本呈現回應所需的資料。</span><span class="sxs-lookup"><span data-stu-id="25652-208">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span>

<span data-ttu-id="25652-209">視圖範本 **不** 應：</span><span class="sxs-lookup"><span data-stu-id="25652-209">View templates should **not**:</span></span>

* <span data-ttu-id="25652-210">進行商務邏輯</span><span class="sxs-lookup"><span data-stu-id="25652-210">Do business logic</span></span>
* <span data-ttu-id="25652-211">直接與資料庫互動。</span><span class="sxs-lookup"><span data-stu-id="25652-211">Interact with a database directly.</span></span>

<span data-ttu-id="25652-212">View 範本應該只適用于控制器提供給它的資料。</span><span class="sxs-lookup"><span data-stu-id="25652-212">A view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="25652-213">維護這項「關注點分離」有助於維持程式碼：</span><span class="sxs-lookup"><span data-stu-id="25652-213">Maintaining this "separation of concerns" helps keep the code:</span></span>

* <span data-ttu-id="25652-214">清潔。</span><span class="sxs-lookup"><span data-stu-id="25652-214">Clean.</span></span>
* <span data-ttu-id="25652-215">性.</span><span class="sxs-lookup"><span data-stu-id="25652-215">Testable.</span></span>
* <span data-ttu-id="25652-216">維護。</span><span class="sxs-lookup"><span data-stu-id="25652-216">Maintainable.</span></span>

<span data-ttu-id="25652-217">目前，`HelloWorldController` 類別中的 `Welcome` 方法會採用 `name` 和 `ID` 參數，然後將值直接輸出到瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="25652-217">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span>

<span data-ttu-id="25652-218">與其讓控制器以字串方式呈現這個回應，不如變更控制器以改為使用檢視範本。</span><span class="sxs-lookup"><span data-stu-id="25652-218">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="25652-219">View 範本會產生動態回應，這表示必須將適當的資料從控制器傳遞至視圖，以產生回應。</span><span class="sxs-lookup"><span data-stu-id="25652-219">The view template generates a dynamic response, which means that appropriate data must be passed from the controller to the view to generate the response.</span></span> <span data-ttu-id="25652-220">若要這麼做，請讓控制器將動態資料 (參數) 在字典中的視圖範本需要 `ViewData` 。</span><span class="sxs-lookup"><span data-stu-id="25652-220">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary.</span></span> <span data-ttu-id="25652-221">然後，view 範本就可以存取動態資料。</span><span class="sxs-lookup"><span data-stu-id="25652-221">The view template can then access the dynamic data.</span></span>

<span data-ttu-id="25652-222">在 *HelloWorldController.cs* 中變更 `Welcome` 方法，將 `Message` 和 `NumTimes` 值新增至 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="25652-222">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span>

<span data-ttu-id="25652-223">`ViewData`字典是動態物件，這表示可以使用任何型別。</span><span class="sxs-lookup"><span data-stu-id="25652-223">The `ViewData` dictionary is a dynamic object, which means any type can be used.</span></span> <span data-ttu-id="25652-224">`ViewData`物件沒有定義的屬性，直到加入某個內容為止。</span><span class="sxs-lookup"><span data-stu-id="25652-224">The `ViewData` object has no defined properties until something is added.</span></span> <span data-ttu-id="25652-225">[MVC 模型](xref:mvc/models/model-binding)系結系統會自動將具名引數 `name` 和 `numTimes` 查詢字串對應至方法中的參數。</span><span class="sxs-lookup"><span data-stu-id="25652-225">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters `name` and `numTimes` from the query string to parameters in the method.</span></span> <span data-ttu-id="25652-226">完整 `HelloWorldController` ：</span><span class="sxs-lookup"><span data-stu-id="25652-226">The complete `HelloWorldController`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5&highlight=13-19)]

<span data-ttu-id="25652-227">`ViewData` 字典物件包含將傳遞至檢視的資料。</span><span class="sxs-lookup"><span data-stu-id="25652-227">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="25652-228">建立名為 *Views/HelloWorld/Welcome.cshtml* 的 Welcome 檢視範本。</span><span class="sxs-lookup"><span data-stu-id="25652-228">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="25652-229">您將在 *Welcome.cshtml* 檢視範本中建立迴圈，以顯示 "Hello" `NumTimes` 次。</span><span class="sxs-lookup"><span data-stu-id="25652-229">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="25652-230">使用下列內容取代 *Views/HelloWorld/Welcome.cshtml* 的內容：</span><span class="sxs-lookup"><span data-stu-id="25652-230">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="25652-231">儲存變更並瀏覽至下列 URL：</span><span class="sxs-lookup"><span data-stu-id="25652-231">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="25652-232">資料取自 URL，並使用 [MVC 模型](xref:mvc/models/model-binding)系結器傳遞至控制器。</span><span class="sxs-lookup"><span data-stu-id="25652-232">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="25652-233">控制器會將資料封裝成 `ViewData` 字典，並將該物件傳遞至檢視。</span><span class="sxs-lookup"><span data-stu-id="25652-233">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="25652-234">接著，檢視會以 HTML 將資料呈現到瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="25652-234">The view then renders the data as HTML to the browser.</span></span>

![顯示一個 Welcome 標籤和顯示四次的片語 Hello Rick 的 Privacy 檢視](~/tutorials/first-mvc-app/adding-view/_static/rick2_50.png)

<span data-ttu-id="25652-236">在上述範例中， `ViewData` 字典是用來將資料從控制器傳遞至 view。</span><span class="sxs-lookup"><span data-stu-id="25652-236">In the preceding sample, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="25652-237">稍後在教學課程中，會使用檢視模型將控制器中的資料傳遞至檢視。</span><span class="sxs-lookup"><span data-stu-id="25652-237">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="25652-238">傳遞資料的視圖模型方法優於 `ViewData` 字典的方法。</span><span class="sxs-lookup"><span data-stu-id="25652-238">The view model approach to passing data is preferred over the `ViewData` dictionary approach.</span></span>

<span data-ttu-id="25652-239">在下一個教學課程中，將會建立電影資料庫。</span><span class="sxs-lookup"><span data-stu-id="25652-239">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="25652-240">[上一步：新增控制器](adding-controller.md) 
> [下一步：新增模型](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="25652-240">[Previous: Add a Controller](adding-controller.md)
[Next: Add a Model](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="25652-241">在本節中， `HelloWorldController` 會將類別修改為使用 [Razor](xref:mvc/views/razor) view files，以將產生 HTML 回應的程式完全封裝至用戶端。</span><span class="sxs-lookup"><span data-stu-id="25652-241">In this section, the `HelloWorldController` class is modified to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="25652-242">您可以使用建立視圖範本檔案 Razor 。</span><span class="sxs-lookup"><span data-stu-id="25652-242">You create a view template file using Razor.</span></span> <span data-ttu-id="25652-243">Razor型視圖範本的副檔名為 *cshtml* 。</span><span class="sxs-lookup"><span data-stu-id="25652-243">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="25652-244">它們提供了一種使用 C# 建立 HTML 輸出的簡潔方式。</span><span class="sxs-lookup"><span data-stu-id="25652-244">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="25652-245">`Index` 方法目前會傳回字串，內含在控制器類別中已直接書寫好的固定訊息。</span><span class="sxs-lookup"><span data-stu-id="25652-245">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="25652-246">在 `HelloWorldController` 類別中，以下列程式碼取代 `Index` 方法：</span><span class="sxs-lookup"><span data-stu-id="25652-246">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="25652-247">上述程式碼會呼叫控制器的 <xref:Microsoft.AspNetCore.Mvc.Controller.View*> 方法。</span><span class="sxs-lookup"><span data-stu-id="25652-247">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="25652-248">它使用檢視範本來產生 HTML 回應。</span><span class="sxs-lookup"><span data-stu-id="25652-248">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="25652-249">上述 `Index` 方法等控制器方法 (也稱為「動作方法」) 通常會傳回 <xref:Microsoft.AspNetCore.Mvc.IActionResult> (或衍生自 <xref:Microsoft.AspNetCore.Mvc.ActionResult> 的類別)，而不會傳回像是 `string` 的類型。</span><span class="sxs-lookup"><span data-stu-id="25652-249">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="25652-250">新增檢視</span><span class="sxs-lookup"><span data-stu-id="25652-250">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="25652-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="25652-251">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="25652-252">在 [ *Views* ] 資料夾上按一下滑鼠右鍵，然後新增 > 的 [ **新資料夾** ]，然後將資料夾命名為 *HelloWorld*。</span><span class="sxs-lookup"><span data-stu-id="25652-252">Right-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="25652-253">以滑鼠右鍵按一下 *Views/HelloWorld* 資料夾，然後新增 **> 的新專案**。</span><span class="sxs-lookup"><span data-stu-id="25652-253">Right-click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="25652-254">在 [新增項目 - MvcMovie] 對話方塊內</span><span class="sxs-lookup"><span data-stu-id="25652-254">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="25652-255">在右上角的搜尋方塊中，輸入 *view*</span><span class="sxs-lookup"><span data-stu-id="25652-255">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="25652-256">選取 **Razor 視圖**</span><span class="sxs-lookup"><span data-stu-id="25652-256">Select **Razor View**</span></span>

  * <span data-ttu-id="25652-257">保留 [名稱] 方塊值 *Index.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="25652-257">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="25652-258">選取 [新增]</span><span class="sxs-lookup"><span data-stu-id="25652-258">Select **Add**</span></span>

![[新增項目] 對話方塊](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="25652-260">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="25652-260">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="25652-261">為 `HelloWorldController` 新增 `Index` 檢視。</span><span class="sxs-lookup"><span data-stu-id="25652-261">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="25652-262">新增資料夾，並命名為 *Views/HelloWorld*。</span><span class="sxs-lookup"><span data-stu-id="25652-262">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="25652-263">將檔案新增至 *Views/HelloWorld* 資料夾，並命名為 *Index.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="25652-263">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="25652-264">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="25652-264">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="25652-265">依序以滑鼠右鍵按一下 *Views* 資料夾、[新增] > [新增資料夾]，然後將資料夾命名為 *HelloWorld*。</span><span class="sxs-lookup"><span data-stu-id="25652-265">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="25652-266">依序以滑鼠右鍵按一下 *Views/HelloWorld* 資料夾、[新增] > [新增檔案]。</span><span class="sxs-lookup"><span data-stu-id="25652-266">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="25652-267">在 [新增檔案] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="25652-267">In the **New File** dialog:</span></span>

  * <span data-ttu-id="25652-268">選取左窗格的 [Web]。</span><span class="sxs-lookup"><span data-stu-id="25652-268">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="25652-269">選取中間窗格的 [空的 HTML 檔案]。</span><span class="sxs-lookup"><span data-stu-id="25652-269">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="25652-270">在 [名稱] 方塊中，鍵入 **Index.cshtml**。</span><span class="sxs-lookup"><span data-stu-id="25652-270">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="25652-271">選取 [ **新增**]。</span><span class="sxs-lookup"><span data-stu-id="25652-271">Select **New**.</span></span>

![[新增項目] 對話方塊](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="25652-273">以下列內容取代 *Views/HelloWorld/Index. cshtml* view 檔案的內容 Razor ：</span><span class="sxs-lookup"><span data-stu-id="25652-273">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="25652-274">瀏覽至 `https://localhost:{PORT}/HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="25652-274">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="25652-275">`HelloWorldController` 中的 `Index` 方法不會執行什麼作業；它會執行陳述式 `return View();`，其指定方法應使用檢視範本檔案來呈現瀏覽器的回應。</span><span class="sxs-lookup"><span data-stu-id="25652-275">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="25652-276">因為未指定檢視範本檔案名稱，因此 MVC 預設為使用預設檢視檔案。</span><span class="sxs-lookup"><span data-stu-id="25652-276">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="25652-277">預設檢視檔案與方法 (`Index`) 擁有相同的名稱，因此會在 */Views/HelloWorld/Index.cshtml* 中使用。</span><span class="sxs-lookup"><span data-stu-id="25652-277">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="25652-278">下列影像顯示檢視中硬式編碼的字串 "Hello from our View Template!"</span><span class="sxs-lookup"><span data-stu-id="25652-278">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="25652-279">。</span><span class="sxs-lookup"><span data-stu-id="25652-279">hard-coded in the view.</span></span>

![瀏覽器視窗](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="25652-281">變更檢視和版面配置頁</span><span class="sxs-lookup"><span data-stu-id="25652-281">Change views and layout pages</span></span>

<span data-ttu-id="25652-282">選取功能表連結 (**MvcMovie**、**Home** 和 **Privacy**)。</span><span class="sxs-lookup"><span data-stu-id="25652-282">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="25652-283">每個頁面會顯示相同的功能表配置。</span><span class="sxs-lookup"><span data-stu-id="25652-283">Each page shows the same menu layout.</span></span> <span data-ttu-id="25652-284">功能表配置是在 *Views/Shared/_Layout.cshtml* 檔案中實作。</span><span class="sxs-lookup"><span data-stu-id="25652-284">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="25652-285">開啟 *Views/Shared/_Layout.cshtml* 檔案。</span><span class="sxs-lookup"><span data-stu-id="25652-285">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="25652-286">[版面配置](xref:mvc/views/layout)範本可讓您在某個位置指定網站的 HTML 容器配置，然後將它套用到網站中的多個頁面。</span><span class="sxs-lookup"><span data-stu-id="25652-286">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="25652-287">找到 `@RenderBody()` 這行。</span><span class="sxs-lookup"><span data-stu-id="25652-287">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="25652-288">`RenderBody` 是顯示您建立之所有檢視特定頁面的預留位置，「包裝」在版面配置頁中。</span><span class="sxs-lookup"><span data-stu-id="25652-288">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="25652-289">例如，如果您選取 **Privacy** 連結，**Views/Home/Privacy.cshtml** 檢視就會呈現在 `RenderBody` 方法內。</span><span class="sxs-lookup"><span data-stu-id="25652-289">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="25652-290">變更配置檔案中的標題、頁尾及功能表連結</span><span class="sxs-lookup"><span data-stu-id="25652-290">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="25652-291">在 title 和 footer 元素中，將 `MvcMovie` 變更為 `Movie App`。</span><span class="sxs-lookup"><span data-stu-id="25652-291">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="25652-292">將錨點元素 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` 變更為 `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`。</span><span class="sxs-lookup"><span data-stu-id="25652-292">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="25652-293">下列標記顯示醒目提示的變更：</span><span class="sxs-lookup"><span data-stu-id="25652-293">The following markup shows the highlighted changes:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="25652-294">在上述標記中，由於此應用程式未使用[區域](xref:mvc/controllers/areas)，因此已省略 `asp-area` [錨點標籤協助程式屬性](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="25652-294">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="25652-295">**注意**：尚未 `Movies` 執行控制器。</span><span class="sxs-lookup"><span data-stu-id="25652-295">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="25652-296">此時，`Movie App` 連結無法運作。</span><span class="sxs-lookup"><span data-stu-id="25652-296">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="25652-297">儲存您的變更並選取 **Privacy** 連結。</span><span class="sxs-lookup"><span data-stu-id="25652-297">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="25652-298">請注意，瀏覽器索引標籤上的標題會顯示 **Privacy Policy - Movie App**，而不是 **Privacy Policy - Mvc Movie**：</span><span class="sxs-lookup"><span data-stu-id="25652-298">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Privacy 索引標籤](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

<span data-ttu-id="25652-300">選取 **Home** 連結，並注意標題和錨點文字也要顯示 **Movie App**。</span><span class="sxs-lookup"><span data-stu-id="25652-300">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="25652-301">我們能夠在版面配置範本中一次進行變更，並讓網站上的所有頁面反映新的連結文字和新的標題。</span><span class="sxs-lookup"><span data-stu-id="25652-301">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="25652-302">檢查 *Views/_ViewStart.cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="25652-302">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="25652-303">*Views/_ViewStart.cshtml* 檔案會將 *Views/Shared/_Layout.cshtml* 檔案引入每一個檢視。</span><span class="sxs-lookup"><span data-stu-id="25652-303">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="25652-304">`Layout` 屬性可用來設定不同的版面配置檢視，或將它設定為 `null`，因此不會使用任何版面配置檔案。</span><span class="sxs-lookup"><span data-stu-id="25652-304">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="25652-305">變更 *Views/HelloWorld/Index.cshtml* 檢視檔案的標題和 `<h2>` 元素：</span><span class="sxs-lookup"><span data-stu-id="25652-305">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="25652-306">標題和 `<h2>` 元素略有不同，因此可看出哪一段程式碼變更了顯示。</span><span class="sxs-lookup"><span data-stu-id="25652-306">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="25652-307">上述程式碼中的 `ViewData["Title"] = "Movie List";` 會將 `ViewData` 字典的 `Title` 屬性設定為 "Movie List"。</span><span class="sxs-lookup"><span data-stu-id="25652-307">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="25652-308">`Title` 屬性則用於版面配置頁中的 `<title>` HTML 元素：</span><span class="sxs-lookup"><span data-stu-id="25652-308">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="25652-309">儲存變更並巡覽至 `https://localhost:{PORT}/HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="25652-309">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="25652-310">請注意，瀏覽器標題、主要標題和次要標題已變更</span><span class="sxs-lookup"><span data-stu-id="25652-310">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="25652-311">(如果您在瀏覽器中沒有看到變更，可能檢視的是快取的內容。</span><span class="sxs-lookup"><span data-stu-id="25652-311">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="25652-312">在您的瀏覽器中按 Ctrl + F5，以強制載入來自伺服器的回應。 ) 瀏覽器標題是使用 `ViewData["Title"]` 我們在 *Index. cshtml* view 範本中設定的，以及在版面配置檔案中新增的其他 "-Movie App" 所建立。</span><span class="sxs-lookup"><span data-stu-id="25652-312">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="25652-313">同時也請注意，*Index.cshtml* 檢視範本中的內容如何與 *Views/Shared/_Layout.cshtml* 檢視範本和已傳送至瀏覽器的單一 HTML 回應合併。</span><span class="sxs-lookup"><span data-stu-id="25652-313">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="25652-314">版面配置範本可讓您輕鬆進行會套用到應用程式之所有頁面的變更。</span><span class="sxs-lookup"><span data-stu-id="25652-314">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="25652-315">若要深入瞭解，請參閱 [版面](xref:mvc/views/layout)配置。</span><span class="sxs-lookup"><span data-stu-id="25652-315">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![電影清單檢視](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="25652-317">然而，我們的這一點點「資料」(在此案例中為 "Hello from our View Template!"</span><span class="sxs-lookup"><span data-stu-id="25652-317">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="25652-318">訊息) 是硬式編碼的資料。</span><span class="sxs-lookup"><span data-stu-id="25652-318">message) is hard-coded, though.</span></span> <span data-ttu-id="25652-319">MVC 應用程式具有 "V" (檢視)，並已取得 "C" (控制器)，但還沒有 "M" (模型)。</span><span class="sxs-lookup"><span data-stu-id="25652-319">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="25652-320">將資料從控制器傳遞至檢視</span><span class="sxs-lookup"><span data-stu-id="25652-320">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="25652-321">回應傳入的 URL 要求時會叫用控制器動作。</span><span class="sxs-lookup"><span data-stu-id="25652-321">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="25652-322">控制器類別是撰寫處理傳入瀏覽器要求之程式碼的位置。</span><span class="sxs-lookup"><span data-stu-id="25652-322">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="25652-323">控制器會從資料來源擷取資料，並決定要將哪一種回應傳回瀏覽器中。</span><span class="sxs-lookup"><span data-stu-id="25652-323">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="25652-324">您可以從控制器使用檢視範本來產生並格式化瀏覽器的 HTML 回應。</span><span class="sxs-lookup"><span data-stu-id="25652-324">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="25652-325">控制器負責提供為了讓檢視範本呈現回應所需的資料。</span><span class="sxs-lookup"><span data-stu-id="25652-325">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="25652-326">最佳做法：檢視範本 **不** 應該執行商務邏輯，或直接與資料庫互動。</span><span class="sxs-lookup"><span data-stu-id="25652-326">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="25652-327">相反地，檢視範本應該只使用由控制器提供給它的資料。</span><span class="sxs-lookup"><span data-stu-id="25652-327">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="25652-328">維護這項「關注點分離」，有助於保持程式碼整潔、可測試且可維護。</span><span class="sxs-lookup"><span data-stu-id="25652-328">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="25652-329">目前，`HelloWorldController` 類別中的 `Welcome` 方法會採用 `name` 和 `ID` 參數，然後將值直接輸出到瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="25652-329">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="25652-330">與其讓控制器以字串方式呈現這個回應，不如變更控制器以改為使用檢視範本。</span><span class="sxs-lookup"><span data-stu-id="25652-330">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="25652-331">檢視範本會產生動態回應，這表示必須將適當數量的資料從控制器傳遞至檢視，以便產生回應。</span><span class="sxs-lookup"><span data-stu-id="25652-331">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="25652-332">透過讓控制器將檢視範本需要的動態資料 (參數) 放置在檢視範本之後可以存取的 `ViewData` 字典，即可完成這項操作。</span><span class="sxs-lookup"><span data-stu-id="25652-332">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="25652-333">在 *HelloWorldController.cs* 中變更 `Welcome` 方法，將 `Message` 和 `NumTimes` 值新增至 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="25652-333">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="25652-334">`ViewData` 字典是動態物件，這表示您可以使用任何類型；`ViewData` 物件則要在您於其中放入某個項目之後，才會有定義的屬性。</span><span class="sxs-lookup"><span data-stu-id="25652-334">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="25652-335">MVC [模型繫結](xref:mvc/models/model-binding)系統會自動將網址列上查詢字串中的具名參數 (`name` 和 `numTimes`) 對應至方法中的參數。</span><span class="sxs-lookup"><span data-stu-id="25652-335">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="25652-336">完整的 *HelloWorldController.cs* 檔案如下所示：</span><span class="sxs-lookup"><span data-stu-id="25652-336">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="25652-337">`ViewData` 字典物件包含將傳遞至檢視的資料。</span><span class="sxs-lookup"><span data-stu-id="25652-337">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="25652-338">建立名為 *Views/HelloWorld/Welcome.cshtml* 的 Welcome 檢視範本。</span><span class="sxs-lookup"><span data-stu-id="25652-338">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="25652-339">您將在 *Welcome.cshtml* 檢視範本中建立迴圈，以顯示 "Hello" `NumTimes` 次。</span><span class="sxs-lookup"><span data-stu-id="25652-339">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="25652-340">使用下列內容取代 *Views/HelloWorld/Welcome.cshtml* 的內容：</span><span class="sxs-lookup"><span data-stu-id="25652-340">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="25652-341">儲存變更並瀏覽至下列 URL：</span><span class="sxs-lookup"><span data-stu-id="25652-341">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="25652-342">資料是使用 [MVC 模型繫結器](xref:mvc/models/model-binding)從 URL 取得並傳遞至控制站。</span><span class="sxs-lookup"><span data-stu-id="25652-342">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="25652-343">控制器會將資料封裝成 `ViewData` 字典，並將該物件傳遞至檢視。</span><span class="sxs-lookup"><span data-stu-id="25652-343">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="25652-344">接著，檢視會以 HTML 將資料呈現到瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="25652-344">The view then renders the data as HTML to the browser.</span></span>

![顯示一個 Welcome 標籤和顯示四次的片語 Hello Rick 的 Privacy 檢視](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="25652-346">在上述範例中，使用了 `ViewData` 字典將資料從控制器傳遞至檢視。</span><span class="sxs-lookup"><span data-stu-id="25652-346">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="25652-347">稍後在教學課程中，會使用檢視模型將控制器中的資料傳遞至檢視。</span><span class="sxs-lookup"><span data-stu-id="25652-347">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="25652-348">傳遞資料的檢視模型方法通常比 `ViewData` 字典方法較為慣用。</span><span class="sxs-lookup"><span data-stu-id="25652-348">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="25652-349">如需詳細資訊，請參閱[何時應該使用 ViewBag、ViewData 或 TempData ](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="25652-349">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="25652-350">在下一個教學課程中，將會建立電影資料庫。</span><span class="sxs-lookup"><span data-stu-id="25652-350">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="25652-351">[上一個](adding-controller.md) 
> [下一步](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="25652-351">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
